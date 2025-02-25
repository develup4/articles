```cpp
#include <iostream>
#include <vector>
using namespace std;

int main()
{
	vector<int> v(5, 0);
	vector<int>::iterator p1 = v.begin();

	auto p2 = v.begin();    	// 초기값으로 타입을 추론
	decltype(v.begin()) p3;   	// return값으로 타입을 추론

	for (auto n : v)    		// ranged-for에 많이 활용한다
  {
	  cout << n << endl;
  }
	return 0;
}
```

`auto`와 `decltype`은 둘 다 **타입을 추론**하는데 사용하는 Modern C++ 키워드이다. 주석으로 설명한 것처럼 초기값에서 추론할지 혹은 return 값으로 추론할지에 대한 차이가 있으며, 상황에 따라(초기값이 없거나) 사용하면 된다.
