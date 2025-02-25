```cpp
#include <stdio.h>

class RefBase
{
	int mRefs;
	public:
		RefBase():mRefs(0){ printf(“RefBase::RefBase()\\n”); }
		virtual ~RefBase(){ printf(“RefBase::~RefBase()\\n”); }
		void incStrong() { mRefs++; }
		void decStrong() { if( —mRefs == 0 ) delete this; }
};

template< typename T >
class sp
{
	T *mPtr;
	public:
	    sp(T *ptr) : mPtr(ptr) { mPtr->incStrong(); }
	    sp(const sp<T> &r) : mPtr(r.mPtr) { mPtr->incStrong(); }
	    ~sp() {
			mPtr->decStrong();
		}
		T *operator->() { return mPtr; }
		T &operator*() { return *mPtr; }
};

class AAA : public RefBase
{
	public:
		AAA(){ printf(“AAA::AAA()\\n”); }
		~AAA(){ printf(“AAA::~AAA()\\n”); }
		void foo(){ printf(“AAA::foo()\\n”); }
};

int main()
{
	sp<AAA> p1 = new AAA();
	sp<AAA> p2 = p1;
	return 0;
}
```

안드로이드 네이티브에서 사용하는 실제 코드는 훨씬 더 길고 복잡하지만 핵심만 추리면 위의 코드와 같다. (실제 코드는 멀티코어 환경을 고려하여 여러가지 atomic operation도 사용되고 아무튼 복잡하다)

개념은 워낙 `Smart pointer`가 유명해서 C++ 코드를 어느정도 읽을 수 있다면 동작을 파악할 수 있을 것이다. 그 유명한 Effective C++의 첫 주제가 바로 `RAII(Resource Acquisition Is Initialization)`인데 **포인터라는 자원**에 대해서 그렇게 구현했다고 보면 될거같다.

주목해서 볼만한 부분은 `reference count`를 `sp`가 가지고 있지 않고 **pointing되는 객체가 가지고 있다는 점이다.** 예를 들어 SP에서 static member variable을 가지고 있다면(말도 안되지만) 여러 `Smart pointer`들이 카운트를 공유해버릴 것이다. 그래서 `RefCount를 상속받는 객체`만을 `sp`가 포인팅할 수 있다. 그리고 그것들 위해 인터페이스 또한 당연히 상속하는데 `IncrementCount`와 `DecrementCount`같은 동작들이 제공된다.

```cpp
#include <stdio.h>
#include “StrongPointer.h”
#include “RefBase.h”

using namespace android;

class AAA : public RefBase
{
	public:
		AAA(){ printf(“AAA::AAA()\\n”); }
		~AAA(){ printf(“AAA::~AAA()\\n”); }
		void onFirstRef() { printf(“onFirstRef()\\n”); }    // 부모의 인터페이스가 virtual 함수이다
		void foo(){ printf(“AAA::foo()\\n”); }
};

int main()
{
	sp<AAA> p1 = new AAA();
	sp<AAA> p2 = p1;
	return 0;
}
```

하나 더 살펴볼점은 `onFirstRef()` 함수이다. 가상함수이기 때문에 RefBase를 상속받는다면 자동으로 호출되는 가상함수이다. 이 함수는 처음으로 스마트포인터에 값이 할당될때 호출되는데, 그 이유는 좀 구린거 같다.

`AAA* p1 = new AAA()`

유저는 Smart pointer를 안쓰고 객체를 그냥 pointer로 사용할수도 있는 것이다. 이것을 컴파일에러 등으로 제어할 수 없다…(정말 없나? 구글이 못했으면 못한건가?)

그래서 각종 디바이스 등 중요한 객체에서 중요한 초기화는 생성자가 아니라 onFirstRef()에서 하도록 정책을 정한것이다. Smart pointer를 안쓰면 안되게끔. 좀 촌스럽지만 어쩌만 유일한 방법일지도 모른다.

```cpp
#include <stdio.h>
#include “StrongPointer.h”
#include “RefBase.h”

using namespace android;

class AAA;
class BBB;

class AAA : public RefBase
{
	public:
		sp<BBB> pb;
		AAA(){ printf(“AAA::AAA()\\n”); }
		~AAA(){ printf(“AAA::~AAA()\\n”); }
		void foo(){ printf(“AAA::foo()\\n”); }
};

class BBB : public RefBase
{
	public:
		sp<AAA> pa;
		BBB(){ printf(“BBB::BBB()\\n”); }
		~BBB(){ printf(“BBB::~BBB()\\n”); }
		void foo(){ printf(“AAA::foo()\\n”); }
};

int main()
{
	{
		sp<AAA> p1 = new AAA();
		sp<BBB> p2 = new BBB();
		p1->pb = p2;
		p2->pa = p1;
	}
	printf(“step 1.\\n”);

	return 0;
}
```

이번엔 `sp`의 상호참조 문제이다.

위 예제처럼 멤버인 `Smart pointer`가 서로를 참조할 경우 잘 생각해보면, 멤버의 참조를 통해서 객체의 RefCount가 2가 되므로 `sp`가 Scope에서 나가서 소멸되더라도 `RefCount`는 1이 되고 객체는 소멸되지 않는다. 이것은 `sp` 뿐만 아니라 Smart pointer류가 공통적으로 가지는 문제이다. 이 문제를 해결하기 위해서 다음에 소개할 wp 즉, `Weak pointer`가 도입된다.
