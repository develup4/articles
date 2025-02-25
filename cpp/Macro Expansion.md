# 도입

파일 시스템이 없는 경우를 상상해볼 수 있는가? 임베디드 세계에서는 충분히 가능한 이야기이다. 그렇다면 미리 입력할 데이터가 필요한 경우 우리에게 남은 답은 **하드코딩**뿐이다. 그리고 그 하드코딩을 그나마 세련되게 할 수 있는 방법이 `Macro Expansion`이다. 매크로의 문법일 뿐이지만 위와 같은 경우에서 사용하는 경우를 이 글에서는 다루겠다.

## 문제 설정

시스템에 쓰이는 타이머를 관리하고 싶다고 가상의 문제를 생각해보자. 타이머의 목록을 설정 파일에 넣어두고 시스템이 시작할 때 불러와서 생성하면 좋겠지만, 처음 말한 것과 같이 파일 시스템이 없다면 설정 파일을 어디에 둘 것인가? 답은 소스코드 뿐이다.

# Descriptor

마치 설정파일처럼 타이머의 목록을 나타낼 C++ 코드를 `Descriptor`라고 한다. 형태는 아래 코드와 같다.

```cpp
#ifdef TIMER_DEFINE_START
TIMER_DEFINE_START
		TIMER_ENTITY(TIMER1, 1000, timer_handler1)
    TIMER_ENTITY(TIMER2, 2000, timer_handler2)
TIMER_DEFINE_END
#endif
```

# Macro Expansion

이 방식의 장점은 저 Descriptor를 통해 여러가지 코드를 생성해낼 수 있다는 점이다. 해석방법에 따라 다양한 활용이 가능하다.

- 타이머의 목록을 enum으로 만들어내기

```cpp
enum Timer_e {
    #define TIMER_DEFINE_START()
		#define TIMER_ENTITY(TIMER_NAME, DURATION, HANDLER) \
				TIMER_NAME
		#define TIMER_DEFINE_END()
		#include "descriptor.h"

		TIMER_COUNT

		#undef TIMER_DEFINE_START
		#undef TIMER_ENTITY
		#undef TIMER_DEFINE_END
};
```

위 코드를 자세히 살펴보자.

1. descriptor는 매크로의 덩어리일 뿐이다. 이 매크로는 `#define`을 통해 전처리기에서 변환되는데 이를 통해 매크로의 해석방식을 구현할 수 있다.
2. 매크로 인자를 이용해 코드를 생성해내는데 활용할 수 있다.
3. TIMER_COUNT는 enum의 성질을 이용한 것이다.
4. 해석방식을 `#define`을 통해 정의했다면 그것을 통해 디스크립터를 해석하도록 `#include`를 진행하자. `#include`는 **단순한 붙여넣기**이다. 위 디스크립터가 `#include` 부분에 붙여넣기 되었을 때, 어떤 일이 일어날지 상상해보자.
5. 중요한 점은 사용 후에는 `#undef`를 통해 해제를 해줘야 한다는 점이다. 디스크립터는 다양하게 해석될 수 있기 때문에 새로운 정의를 위하여 정의를 해제시켜주자.

- 타이머를 생성해내기

```cpp
#define TIMER_DEFINE_START()
#define TIMER_ENTITY(TIMER_NAME, DURATION, HANDLER) \
		timers.append(new Timer(TIMER_NAME, DURATION, HANDLER));
#define TIMER_DEFINE_END()
#include "descriptor.h"

#undef TIMER_DEFINE_START
#undef TIMER_ENTITY
#undef TIMER_DEFINE_END
```

# 장점

- 파일 시스템이 없는 경우 거의 유일하게 설정 파일을 관리할 수 있다.
- 위 예에서처럼 하나의 디스크립터로 여러가지 코드를 생성해낼 수 있다.

# 단점

- 매크로이기 때문에 코드의 가독성이 떨어진다. (이건 경우에 따라 반대가 될 수도 있다.)
- 더 결정적인 단점은 매크로이기 때문에 Code Tracking이 어려워진다.
