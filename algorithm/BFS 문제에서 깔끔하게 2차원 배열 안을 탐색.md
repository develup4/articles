컴퓨터가 이해하기 쉬운 방식으로 구현하면 코드 역시 간단해진다.
바로 코드를 살펴보자.

```cpp
#include <stdio.h>

int direct[5][2] = {
	-1, -1,
	-1, 1,
	1, 1,
	1, -1,
	0, 0
};

int map[4][4] = {
	1, 2, 1, 1,
	3, 2, 1, 2,
	1, 2, 3, 5,
	1, 2, 1, 1
};

int main(void) {
	int dy, dx;
	int sum = 0;
	int a = 3;
	int b = 3;

	for (int t = 0; t < 5; t++) {
		dy = a + direct[t][0];
		dx = b + direct[t][1];

		if (dy >= 0 && dy < 4 && dx >= 0 && dx < 4) {
			sum += map[dy][dx];
		}
	}
	return 0;
}
```

코드를 보면 알다시피 맵의 값들을 더하는 내용이다. 은근히 이런 류가 예외처리가 까다로운데 이런 포맷을 사용하면 상당히 코드가 깔끔해진다.
