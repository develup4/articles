# Singleton

`싱글턴`이야 워낙 유명한 패턴이다. 그리고 안티패턴이다. 구현 방법도 인터넷에 널려 있지만 여기서는 `Android Framework`에서의 싱글턴을 다뤄보려 한다. **Binder를 하나만 여는 것**은 중요한 문제이기 때문이다.

## 안드로이드 프레임워크의 Singleton

다짜고짜 Android Framework에서 제공하는 Singleton의 코드를 살펴보자. Android Framework는 오픈소스이기에 코드를 살펴볼 수 있다.이놈의 안티패턴…기왕 써야한다면 안전하게 써야할텐데 어떻게 구현할 수 있을지 살펴보자.

```cpp
#include <stdio.h>
#include <pthread.h>
#include “StrongPointer.h”
#include “RefBase.h”
#include “Thread.h”

using namespace android;

template <typename TYPE>
class  Singleton
{
public:
    static TYPE* getInstance() {
        Mutex::Autolock _l(sLock);     // Mutex 사용
        TYPE* instance = sInstance;
        if (instance == nullptr) {
            instance = new TYPE();
            sInstance = instance;
        }
        return instance;
    }

protected:
    ~Singleton() { }
    Singleton() { }

private:
    Singleton(const Singleton&);
    static Mutex sLock;
    static TYPE* sInstance;
};

#define ANDROID_SINGLETON_STATIC_INSTANCE(TYPE)                 \\
    template<> Mutex  \\
        (Singleton< TYPE >::sLock)(Mutex::PRIVATE);  \\
    template<> TYPE* Singleton< TYPE >::sInstance(nullptr);  /* NOLINT */ \\
    template class Singleton< TYPE >;

class AAA : public RefBase, public Singleton<AAA>
{
public:
	AAA(){printf(“AAA::AAA()\\n”);}
	~AAA(){printf(“AAA::~AAA()\\n”);}
};

ANDROID_SINGLETON_STATIC_INSTANCE(AAA);

int main()
{
	sp<AAA> p1 = AAA::getInstance();    // Strong pointer
	sp<AAA> p2 = AAA::getInstance();
	return 0;
}
```

막상 살펴보면 일반적인 구현과 흡사해서 별로 볼건 없지만 공유자원이므로 `Mutex를 사용`하는 것과 일반적으로 잘 생각하지 않는 Singleton의 객체 소멸까지 고려해서 `Strong pointer를 사용`한것을 봐보자.

## 실제 사용 코드

### ProcessState에서의 사용

```cpp
#include <stdio.h>
#include <pthread.h>
#include “StrongPointer.h”
#include “RefBase.h”
#include “Thread.h”

using namespace android;

class ProcessState;
Mutex& gProcessMutex = *new Mutex;
sp<ProcessState> gProcess;

class ProcessState : public virtual RefBase
{
public:
    static  sp<ProcessState>    self();

private:
    explicit  ProcessState();
};

ProcessState::ProcessState()
{
    printf(“open(\\”/dev/binder\\”, O_RDWR | O_CLOEXEC)\\n”);    // Binder driver를 하나만 열기 위해
}

sp<ProcessState> ProcessState::self()
{
    Mutex::Autolock _l(gProcessMutex);
    if (gProcess != nullptr) {
        return gProcess;
    }
    gProcess = new ProcessState();
    return gProcess;
}

int main()
{
	sp<ProcessState>p1 = ProcessState::self();
	sp<ProcessState>p2 = ProcessState::self();
	return 0;
}
```

역시나 간략히 추린 코드이며 Android Framework에서는 더 많은 내용을 담고 있다. 말 그대로 Process의 상태를 저장하기 위한 클래스인데 Singleton을 사용해서 구현되어 있다. 이유는 **Process 내에서는 Binder driver를 단 하나만 열어야 하기 때문**이다. 단순히 의존성을 가져오기 위함이 아닌(전역이나 다름없는) 정말 **Singleton 본연의 취지에 맞는 사용법**이다 짝짝짝🎊

### IPCThreadState

```cpp
#include <stdio.h>
#include <pthread.h>
#include “StrongPointer.h”
#include “RefBase.h”
#include “Thread.h”

using namespace android;

static pthread_mutex_t gTLSMutex = PTHREAD_MUTEX_INITIALIZER;
static bool gHaveTLS(false);
static pthread_key_t gTLS = 0;

class IPCThreadState
{
	public:
		static  IPCThreadState*     self();
		static void threadDestructor(void *st)
		{
			delete (IPCThreadState*)st;    // 직접 등록한 해제함수를 호출한다
		}

	private:
		IPCThreadState();
		~IPCThreadState();
};

IPCThreadState::IPCThreadState()
{
    pthread_setspecific(gTLS, this);
	printf(“IPCThreadState::IPCThreadState()\\n”);
}

IPCThreadState::~IPCThreadState()
{
	printf(“IPCThreadState::~IPCThreadState()\\n”);
}

IPCThreadState* IPCThreadState::self()
{
    if (gHaveTLS) {
restart:
        const pthread_key_t k = gTLS;    // Thread별 고유공간 여부를 저장하기 위한 운영체제내 영역
        IPCThreadState* st = (IPCThreadState*)pthread_getspecific(k);    // 기존 Singleton을 응용
        if (st) return st;
        return new IPCThreadState;
    }

    pthread_mutex_lock(&gTLSMutex);
    if (!gHaveTLS) {
        int key_create_value = pthread_key_create(&gTLS, threadDestructor);
        if (key_create_value != 0) {
            pthread_mutex_unlock(&gTLSMutex);
            return nullptr;
        }
        gHaveTLS = true;
    }
    pthread_mutex_unlock(&gTLSMutex);
    goto restart;
}

class AAA : public Thread
{
	public :
		bool threadLoop()
		{
			IPCThreadState *t1 = IPCThreadState::self();
			IPCThreadState *t2 = IPCThreadState::self();
			IPCThreadState *t3 = IPCThreadState::self();
			return false;
		}
};

class BBB : public Thread
{
	public :
		bool threadLoop()
		{
			IPCThreadState *t1 = IPCThreadState::self();
			IPCThreadState *t2 = IPCThreadState::self();
			return false;
		}
};

int main()
{
	sp<AAA> t1 = new AAA;
	sp<AAA> t2 = new AAA;
	t1->run(“AAA”);
	t2->run(“BBB”);
	t1->join();
	t2->join();
	sleep(3);
	return 0;
}
```

`TLS(Thread Local Storage)` 개념을 활용하여 Thread별 고유한 저장공간을 구현한 것으로, Android뿐만 아니라 각종 운영체제에서 API 등을 통해서 제공한다. Android에서는 자체적인 공간에 Key를 생성하여 Self()에서 Thread당 한번의 new를 할 수 있도록 구현하였다.
