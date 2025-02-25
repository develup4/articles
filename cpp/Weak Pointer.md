**상호참조 문제**가 있던 strong pointer의 마지막 예제에서 멤버의 `sp`글자를 `wp`로만 바꿔보면,

```cpp
using namespace android;

class AAA;
class BBB;

class AAA : public RefBase
{
	public:
		wp<BBB> pb;
		AAA(){ printf(“AAA::AAA()\\n”); }
		~AAA(){ printf(“AAA::~AAA()\\n”); }
		void foo(){ printf(“AAA::foo()\\n”); }
};

class BBB : public RefBase
{
	public:
		wp<AAA> pa;
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

상호참조를 했음에도 소멸자가 잘불리며 객체가 소멸하는 것을 확인할 수 있다. 문제해결이 된 것인가? 내용을 좀 더 살펴보도록 하자.

우선 `wp`가 바로 `weak pointer`이다. 뭔가 이름만 봐도 `Strong pointer`와 대비되는 느낌이다.

```cpp
int main()
{
	{
		wp<AAA> p1 = new AAA();
		p1->foo();       // Compile error!
	}
	printf(“step 1.\\n”);

	return 0;
}
```

`wp`는 `->`연산자가 오버로딩되지 않았기 때문에 객체에 접근할 수 없다. 포인터라면서...이..무슨..??? 그리고 `wp`는 스코프에서 벗어나서 사라지더라도 객체를 해제하지 않는다. 스마트포인터라면서...?

그래서 `wp`는 정말로 상호참조 문제**만을** 위해서 나온거 같다. 따라서 실제 사용을 위해서는 sp로 변환할 필요가 있는데 이때는 `promote()`라는 멤버함수 사용이 필요하다.

```cpp
int main() {
	{
		wp<AAA> p1 = new AAA;
		sp<AAA> p2 = p1.promote();
		p2->foo();     // OK
	}
	printf(“step 1.\\n”);

	return 0;
}
```

이렇게 변환하여 사용하면 잘 호출된다. 반대방향으로 `sp`에서 `wp`로 변환할때는 별도의 함수호출없이 바로 대입하면 된다는 점도 기억한다.

```cpp
#include <stdio.h>
#include “StrongPointer.h”
#include “RefBase.h”

using namespace android;

class AAA : public RefBase
{
	int data;
	public:
		AAA(){ printf(“AAA::AAA()\\n”); }
		~AAA(){ printf(“AAA::~AAA()\\n”); }
		void foo()
		{
			printf(“AAA::foo()\\n”);
			//data = 20;
		}
};

int main()
{
	{
		wp<AAA> p1 = new AAA;
		{
			sp<AAA> p2 = p1.promote();
		} // ~AAA() 소멸됨
		printf(“step 1.\\n”);
		p1.promote()->foo(); // AAA::foo(this);
	}
	printf(“step 2.\\n”);

	return 0;
}
```

좀 작위적이지만 위험할 수 있는 예제이다. brace를 나가면서 `sp`를 통해 객체가 소멸되었으나 밖의 `wp`를 사용하려는 예제이다. 이건 100% 크래시가 발생한다.

```cpp
int main()
{
	{
		wp<AAA> p1 = new AAA;
		{
			sp<AAA> p2 = p1.promote();
		} // ~AAA()
		printf(“step 1.\\n”);
		if( p1.promote() == 0 )
			printf(“이미 객체가 소멸됨\\n”);
		else
			p1.promote()->foo(); // AAA::foo(this);
	}
	printf(“step 2.\\n”);

	return 0;
}
```

다만 `promote()` 함수는 객체의 유효성 검사까지 가능하기 때문에, 이것을 이용하면 문제를 방지할 수 있다. 근데 뭔가 그냥 포인터를 쓰는거만큼 귀찮은 일이 되어버린 느낌이다. 하지만 안드로이드의 방대한 코드가 모두 이것들을 사용하고 있으니 분석이나 사용, 수정을 위해서는 반드시 알아둘 필요가 있다.
