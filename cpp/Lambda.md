번역하길 **익명 함수**이다. `functor`처럼 간단하게 사용할 수 있으면서도 클래스를 선언하지 않아도 되는 편리한 기능이다. 가독성을 해친다는 견해도 있지만 이제는 지원 안하는 언어를 찾기 어려울 지경이라 눈이 받아들일 때가 됐다. 다른 언어에 비해 복잡한 경향은 있는 것 같다. (같은 시스템 프로그래밍 언어인 Rust를 보라.)

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

int main()
{
	int v1 = 2;
	int v2 = 3;
	int v3 = 4;
	//auto f1 = [](int n) { return n + v1 + v2; };
	auto f2 = [v1, v2](int n) { return n + v1 + v2; };
	auto f3 = [=](int n) { return n + v1 + v2 + v3; };

	//auto f4 = [v1](int n) { return v1 = n; };
	auto f5 = [&v1](int n) { return v1 = n; };
	auto f6 = [&](int n) { return v1 = n; v2 = n; };
	return 0;
}
```

위 처럼 간단하게 사용할 수 있으나, 주의할 점 그리고 획기적인 점은 `Capture`이다. **첨자([ ]) 안에 외부의 context(변수나 this 등)를 넣으면** lambda 내에서 사용할 수 있는 것이다. 전달하는 방식은 복사 혹은 참조가 모두 가능하다. 이는 다른 언어에서도 제공하는 `Closure`의 개념이라고 할 수 있다.

클로저를 직접 구현해본다면 아래와 같은 클래스의 형태가 될 수 있다.

```cpp
class closure_object
{
	private: const int value1, value2;
	public:
		closure_object(int n1, int n2) : value1(n1), value2(n2) {}
		int operator()(int n) const { return n + value1 + value2; }
};

int main()
{
	int v1 = 1, v2 = 2;
	auto f1 = [v1, v2](int n) { return n + v1 + v2; };
	cout << f1(10) << endl;

	auto f2 = closure_object(v1, v2);   // [v1, v2](int n) { n + v1 + v2 }와 동일
	cout << f2(10) << endl;    // 갑자기 함수형 프로그래밍이 되어버렸다
	return 0;
}
```

다만 C++에서 Lambda의 단점을 꼽자면,

```cpp
int main()
{
	auto f1 = [](int a, int b) { return a + b; };
	auto f2 = [](int a, int b) { return a + b; };
	cout << typeid(f1).name() << endl;
	cout << typeid(f2).name() << endl;
	return 0;
}
```

위 예제에서 `f1`, `f2`는 **서로 다른 타입**이며, 위 구현처럼 일종의 다른 클래스가 된다. 따라서 같은 기능임에도 **별도의 기계어 코드를 가지며 Code Size가 늘어날 수 있다.**
