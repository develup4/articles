C나 구버전의 C++에서는 기존 `NULL`처럼 숫자 0을 널 포인터로 사용하다보니 숫자와 혼용되어 문제가 될 수 있었다. 그래서 Modern C++에서는 `nullptr`이라는 키워드를 제공한다.

```cpp
#include <stdio.h>
#include <vector>

void foo(int)
{
	printf(“foo(int)\\n”);
}
void foo(int*)
{
	printf(“foo(int*)\\n”);
}

int main()
{
	//foo(NULL);
	foo(nullptr);
	return 0;
}
```

위 오버로딩 예제를 통해 사용법을 알 수 있다.

그렇다면 nullptr의 실제 구현은 어떨까? 아래와 같이 한번 만들어볼 수 있다.

```cpp
const class    // 익명 클래스
{
    public:
        template <typename T>
        operator T*(void) const
        {
            return 0;
        }

        template <typename C, typename T>
        operator T C::*(void) const
        {
            return 0;
        }
    private:
        void operator&(void) const;
} nullptr = {};    // 단 하나의 객체가 생성된다
```

nullptr은 위 구현처럼 클래스여서 숫자와 혼용될 수 없고 연산자 오버로딩을 통해 포인터처럼 사용된다.
