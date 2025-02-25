# POSIX Thread

이렇게 쌩으로 사용하는 경우는 흔치 않겠지만 우선 기초인 `POSIX`의 Thread부터 살펴보자. 사용법은 아래와 같다. 직관적이라 따로 설명이 필요없다.

```cpp
#include <stdio.h>
#include <pthread.h>
#include “StrongPointer.h”
#include “RefBase.h”

using namespace android;

void *foo(void* data)
{
	printf(“foo()\\n”);
	return 0;
}

int main()
{
	pthread_t thread;
	pthread_create( &thread, 0, foo, 0 );
	pthread_join( thread, 0 );
	return 0;
}
```

하지만 위 코드를 보고 있지면 클래스로 만들고 싶은 마음이 자연스레 생긴다. 그렇다면 아래와 같이 만들어볼 수 있을 것이다.

```cpp
#include <stdio.h>
#include <pthread.h>
#include “StrongPointer.h”
#include “RefBase.h”

using namespace android;

class Thread
{
	pthread_t thread;
public:
	virtual void foo(void) = 0;
	static void *_foo(void* data)
	{
		Thread *self = static_cast<Thread*>(data);
		self->foo();
		return 0;
	}
	void run() { pthread_create( &thread, 0, _foo, this ); }
	void join() { pthread_join( thread, 0 ); }
};

class MyThread : public Thread
{
public:
	void foo(void)
	{
		printf("MyThread::foo()\\n");
	}
};

int main()
{
	MyThread thread;
	thread.run();
	thread.join();
	return 0;
}
```

# 안드로이드 프레임워크의 Thread

이와 같이 POSIX Thread는 Wrapping해서 많이 사용하는데 그 중 잘만들어진 버전 중 하나가 `Android Framework`에서 구현된 Thread라고 생각한다. 안드로이드 프레임워크는 오픈소스로 코드를 살펴볼 수 있는데, 아래에서 한번 살펴보자.

```cpp
#ifndef _LIBS_UTILS_THREAD_H
#define _LIBS_UTILS_THREAD_H

#include <stdint.h>
#include <sys/types.h>
#include <time.h>

#if !defined(_WIN32)
# include <pthread.h>
#endif

#include "Condition.h"
#include "Errors.h"
#include “Mutex.h”
#include “RefBase.h”
#include “Timers.h”
#include “ThreadDefs.h”

// —————————————————————————————————————
namespace android {
// —————————————————————————————————————

// DO NOT USE: please use std::thread

class Thread : virtual public RefBase
{
public:
    // Create a Thread object, but doesn’t create or start the associated
    // thread. See the run() method.
    explicit            Thread(bool canCallJava = true);
    virtual             ~Thread();

    // Start the thread in threadLoop() which needs to be implemented.
    // NOLINTNEXTLINE(google-default-arguments)
    virtual status_t    run(    const char* name,
                                int32_t priority = PRIORITY_DEFAULT,
                                size_t stack = 0);

    // Ask this object’s thread to exit. This function is asynchronous, when the
    // function returns the thread might still be running. Of course, this
    // function can be called from a different thread.
    virtual void        requestExit();

    // Good place to do one-time initializations
    virtual status_t    readyToRun();

    // Call requestExit() and wait until this object’s thread exits.
    // BE VERY CAREFUL of deadlocks. In particular, it would be silly to call
    // this function from this object’s thread. Will return WOULD_BLOCK in
    // that case.
            status_t    requestExitAndWait();

    // Wait until this object’s thread exits. Returns immediately if not yet running.
    // Do not call from this object’s thread; will return WOULD_BLOCK in that case.
            status_t    join();

    // Indicates whether this thread is running or not.
            bool        isRunning() const;

#if defined(__ANDROID__)
    // Return the thread’s kernel ID, same as the thread itself calling gettid(),
    // or -1 if the thread is not running.
            pid_t       getTid() const;
#endif

protected:
    // exitPending() returns true if requestExit() has been called.
            bool        exitPending() const;

private:
    // Derived class must implement threadLoop(). The thread starts its life
    // here. There are two ways of using the Thread object:
    // 1) loop: if threadLoop() returns true, it will be called again if
    //          requestExit() wasn’t called.
    // 2) once: if threadLoop() returns false, the thread will exit upon return.
    virtual bool        threadLoop() = 0;

private:
    Thread& operator=(const Thread&);
    static  int             _threadLoop(void* user);
    const   bool            mCanCallJava;
    // always hold mLock when reading or writing
            thread_id_t     mThread;
    mutable Mutex           mLock;
            Condition       mThreadExitedCondition;
            status_t        mStatus;
    // note that all accesses of mExitPending and mRunning need to hold mLock
    volatile bool           mExitPending;
    volatile bool           mRunning;
            sp<Thread>      mHoldSelf;
#if defined(__ANDROID__)
    // legacy for debugging, not used by getTid() as it is set by the child thread
    // and so is not initialized until the child reaches that point
            pid_t           mTid;
#endif
};

}  // namespace android

// —————————————————————————————————————
#endif // _LIBS_UTILS_THREAD_H
// —————————————————————————————————————
```

이것저것 구현사항은 많지만 기본적인 동작은 인터페이스만 봐도 짐작할 수 있다. 주의할만한 사항은 **쓰레드 객체를 내부에서 strong pointer로 만든 뒤, refCount를 자체적으로 1 감소시킨다는 점**이다. 그래서 일반 포인터로 Android 쓰레드를 사용하면 refCount가 0이 되어버리므로 정상적으로 돌지않는다.

**즉, 아래와 같이 반드시 `sp`를 써서 사용해야한다.**

```cpp
using namespace android;

class MyThread : public Thread
{
public:
	bool threadLoop()
	{
		printf(“MyThread::foo()\\n”);
		sleep(2);
		return true;
	}
};

int main()
{
	sp<Thread> thread = new MyThread;
	thread->run(“MyThread”);
	thread->join();
	return 0;
}
```

# Mutex

## POSIX Mutex

Thread에 대해 다뤘다면 다음은 `Mutex`에 대해서 다뤄볼 차례이다. 위와 마찬가지로 POSIX Mutex부터 살펴보자.

```cpp
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
int sum = 0;

class MyThread : public Thread
{
public:
	bool threadLoop()
	{
		int local;
		for(int i=0; i<10000000; i++)
		{
			pthread_mutex_lock(&mutex);
			local = sum;
			local = local + 1;
			sum = local;
			pthread_mutex_unlock(&mutex);
		}

		return false;
	}
};

int main()
{
	sp<Thread> thread1 = new MyThread;
	thread1->run(“MyThread1”);
	sp<Thread> thread2 = new MyThread;
	thread2->run(“MyThread2”);

	thread1->join();
	thread2->join();
	printf(“sum = %d\\n”, sum );
	return 0;
}
```

POSIX Thread의 Wrapper class에도 POSIX Mutex를 적용해보자.

```cpp
using namespace android;

Mutex mutex;
int sum = 0;

class MyThread : public Thread
{
public:
	bool threadLoop()
	{
		int local;
		for(int i=0; i<10000000; i++)
		{
			mutex.lock();
			local = sum;
			local = local + 1;
			sum = local;
			mutex.unlock();
		}

		return false;
	}
};
```

## 안드로이드 프레임워크의 Mutex

그렇다면 역시나 마찬가지 순서로 Android Framework에서 제공하는 Mutex를 살펴볼 차례이다. 역시나 더 편하게 사용하기 위해 Android Framework에서 Wrapper 클래스를 제공하지만, 여기서 주의할 사항이 있다. 이 **임계영역(Critical section)에서 예외발생시 unlock이 안되는 문제가 있을 수 있다**는것이다. 그렇다고 모든 catch에서 unlock을 하는 것도 뭔가 불합리하다.

그래서 Android Framework에서는 `autolock`이라는 것을 제공을 한다. 이것도 역시 `RAII` 개념(자원의 관리는 객체)이다.

```cpp
Mutex mutex;
int sum = 0;

class MyThread : public Thread
{
public:
	bool threadLoop()
	{
		int local;
		for(int i=0; i<10000000; i++)
		{
			Mutex::Autolock ll(mutex);
			local = sum;
			local = local + 1;
			sum = local;
		}

		return false;
	}
};
```

**Autolock은 예외가 발생하더라도 자동으로 unlock을 해준다.**

# Conditional

## POSIX Conditional

이번엔 `Conditional`에 대해 알아보자. 일반적인 생산자 소비자 모델이 있을때 각각이 별개의 쓰레드에서 동작한다면, 생산도 안했는데 소비자가 작동하는 문제가 있을 수 있다. **일의 순서가 있는 것이다.** 이런 문제를 위해 POSIX는 `pthread_cond_wait`, `pthread_cond_signal` 등의 api를 제공한다.

```cpp
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t cond = PTHREAD_COND_INITIALIZER;

class Producer: public Thread
{
public:
	bool threadLoop()
	{
		pthread_mutex_lock(&mutex);
		for(int i=0; i<3; i++)
		{
			sleep(1);
			printf(“생산중…\\n”);
		}
		printf(“생산완료\\n”);
		pthread_cond_signal(&cond);
		pthread_mutex_unlock(&mutex);

		return false;
	}
};

class Consumer : public Thread
{
public:
	bool threadLoop()
	{
		pthread_mutex_lock(&mutex);
		pthread_cond_wait(&cond, &mutex);
		for(int i=0; i<3; i++)
		{
			sleep(1);
			printf(“소비중…\\n”);
		}
		printf(“소비완료\\n”);
		pthread_mutex_unlock(&mutex);

		return false;
	}
};

int main()
{
	sp<Thread> pro = new Producer;
	pro->run(“Producer”);
	sp<Thread> con = new Consumer;
	con->run(“Consumer”);

	pro->join();
	con->join();
	return 0;
}
```

## 안드로이드 프레임워크의 Conditional

그리고 역시나 Android Framework에서 제공하는 Class가 존재한다.

```cpp
using namespace android;

Mutex mutex;
Condition cond;
class Producer: public Thread
{
public:
	bool threadLoop()
	{
		Mutex::Autolock ll(mutex);
		for(int i=0; i<3; i++)
		{
			sleep(1);
			printf(“생산중…\\n”);
		}
		printf(“생산완료\\n”);
		cond.signal();

		return false;
	}
};

class Consumer : public Thread
{
public:
	bool threadLoop()
	{
		Mutex::Autolock ll(mutex);
		cond.wait(mutex);
		for(int i=0; i<3; i++)
		{
			sleep(1);
			printf(“소비중…\\n”);
		}
		printf(“소비완료\\n”);

		return false;
	}
};
```

특이한 점은 `signal()`과 `broadcast()`가 있다는 점이다. broadcast API는 여러 쓰레드에게 신호를 보낼 수 있어 편리하다.
