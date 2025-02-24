```cpp
#include <stdio.h>

char history[10];

void run(int level) {
	if (level == 3) {
		print(“%s”, history);
		return;
	}

	for (int i = 0; i < 3; i++) {
		history[level] = ‘A’ + i;
		run(level + 1);
		history[level] = 0;
	}
}
```

재귀함수 호출 중 현재까지의 루트를 확인할 필요가 있을때 매우 유용하다. 지나간적이 있는지 확인할때는 `check 배열`과 함께 사용하기도 한다.
