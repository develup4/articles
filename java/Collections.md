## ArrayList

### 깊은 복사

- ArrayList를 깊은 복사하려면 `addAll()` 메소드를 사용하면된다.

```java
ArrayList<Integer> w = new ArrayList<>();
ArrayList<Integer> copy_w = new ArrayList<>();
copy_w.addAll(w);
```

### Sort

```java
ArrayList<Integer> ArrList = new ArrayList<>();
ArrList.sort(null);
```

### Size

```java
ArrayList<Integer> ArrList = new ArrayList<Integer>();
ArrList.size();
```

## List

### set 컬렉션을 List로 변경

- 생성자에 값을 넣어주면, set에서 List로 변경할 수 있다.

```java
Set<String> set = new HashSet<>();
List<String> menuList = new ArrayList<>(set);
```

### Sort

- `Collections.sort(리스트명)` 메소드를 사용한다.

```java
List<Stirng> list = new ArrayList<>();
Collections.sort(menuList);
```

### Add

```java
ArrayList<Integer> list = new ArrayList<>();
list.add(1);
```

### remove

```java
ArrayList<Integer> list = new ArrayList<>();
list.remove(list.size() - 1); // list의 마지막 값이 리스트에서 제거된다.
```

### Size

```java
ArrayList<Integer> list = new ArrayList<Integer>();
list.size();
```

## Array

### Sort

- Array를 정렬하고자 할 때, `Arrays.sort()` 메소드를 사용한다.

```java
int[] arr = new int[5];
Arrays.sort(arr);
```

### length

- Array의 길이는 `배열명.length`를 사용한다.

```
int[] arr = new int[5];
arr.length;

```

### String to Char Array

- String을 char 배열로 변환할 때, `toCharArray()` 메소드를 사용한다.

```java
char[][] board = new char[5][5];

for (int i = 0; i < 5; i++) {
	board[i]=br.readLine().toCharArray();
}
```

### 배열 특정 범위 자르기

- 배열에서 특정 범위를 자르고, 다른 배열에 저장할 때 `Arrays.copyOfRange(배열명, 시작점, 끝점)` 메소드를 사용한다. 이때, 범위는 `[시작점,끝점)` 형식으로 시작점 이상 끝점 미만의 범위가 설정된다.

```java
int[] array = {1, 2, 3, 4, 5};
int[] temp = Arrays.copyOfRange(array, 1, 3);
System.out.println(temp);

> [2,3]
```

## Set

### 값 넣기

```java
Set<String> set = new HashSet<>();
set.add(“combMenu”);ja
```

### 값 삭제

```java
Set<String> set = new HashSet<>();
set.remove(“combMenu”);
```

### Iterator 사용

```java
Set<String> set = new HashSet<>();
set.add(“1”);
set.add(“2”);
set.add(“3”);

Iterator<String> it = set.iterator();
while(it.hasNext()){
	String a = it.next();
	System.out.println(a);
}

> 1
> 2
> 3
```

### size

```java
Set<String> set = new HashSet<String>();
set.size();
```

## Map

### 값 넣기

- Map에 `{key : value}` 값을 설정할 때, `map 이름.put(key, value)` 메소드를 사용한다.

```
Map<String,Integer> map = new HashMap<>();
map.put(“str”,1);

```

### 값 가져오기

- Map의 `{key:value}` 쌍의 value 값을 가져올 때, `map 이름.get(key값)` 메소드를 사용한다.

```java
Map<String,Integer> map = new HashMap<>();
map.get(“str”);
```

### Key 값 존재 확인

- Map 에 해당하는 key 값이 존재하는지 확인할 때, `map 이름.containsKey(key 값)` 메소드를 사용한다. Key 값이 존재하면 true, 존재하지 않으면 false를 반환한다.

```java
Map<String,Integer> map = new HashMap<>();
map.containsKey(“str”);
```

### Iterator

```java
Map<String,Integer> map = new HashMap<>();
Iterator<String> it = map.keySet().iterator();

while(it.hasNext()){
	String key = it.next();
	int value = map.get(key);
}
```

# size

```java
Map<String,Integer> map = new HashMap<>();
map.size();
```

### Map의 value 값으로 java Collection 넣어주기

- 2차원 배열을 사용하고는 싶은데, 배열의 index를 Int 형이 아닌 String 혹은 Char 형으로 지정하고 싶을 때 사용하면 된다.

```java
map[“abc”] = {1, 2, 3, 4, 5};
```

```java
Map<String, List<Integer>> map = new HashMap<>();

if (map.containsKey(str) == false) {
	List<Integer> list = new ArrayList<>();
	list.add(Integer.parseInt(info[4]));
	map.put(str, list);
} else {
	map.get(str).add(Integer.parseInt(info[4]));
}
```

## String

### 소문자, 대문자

```java
String str = “ABC”;
str = str.toLowerCase();

String str = “abc”;
str = str.toLowerCase();
```

### String to Array

- String 문자열을 Array로 만들 때, `스트링 이름.split()` 메소드를 사용한다.

```java
String str = “12345”;
String[] arr = str.split(“”); // [1,2,3,4,5]
```

### 문자열 자르기

- `스트링 이름.substring(idx)`을 수행하면, `idx를 포함한 위치부터 문자열 끝까지` 추출한다.
- `스트링 이름.substring(시작값, 끝 값)`을 수행하면, `시작값`부터 `끝 값 -1`까지의 문자열을 추출한다.

```java
String str = “1234567”;
str.substring(3); // “4567”
str.substring(2, 5) // “345”
```

### 문자열 뒤집기

- String 문자열을 뒤집기 할 때, `StringBuilder(문자열).reverse().toString()` 메소드를 사용한다.

```java
String str = “Reverse”;
String str = new StringBuilder(words).reverse().toString();
System.out.println(str);

> esreveR
```

### length

```java
String str = “123”;
str.length();
```

### String 값 변경하기

- Java에서 String은 `immutable`하다. 즉, 한번 할당되면 변경이 불가능하다. 따라서, 특정 문자열을 변경하려면 `substring()` 메소드를 활용해 변경된 새로운 문자열을 생성해야한다.

```java
String name = “starfucks”;
String newname = name.substring(0, 4) + ’b’ + name.substring(5);
System.out.println(newname);

> starbucks
```

## StringBuilder

### 추가(append)

```java
StringBuilder sb = new StringBuilder();
sb.append(‘a’);
sb.append(‘b’);
sb.append(‘c’);

System.out.println(sb);
> abc
```

### 삭제(deleteChartAt)

- 삭제할 때는, `빌더 이름.deletCharAt(삭제할 문자의 인덱스)` 메소드를 사용한다.

```java
StringBuilder sb=new StringBuilder();
sb.append(‘a’);
sb.append(‘b’);
sb.append(‘c’);
System.out.println(sb);

sb.deleteCharAt(1);
System.out.println(sb);

> abc
> ac
```

### StringBuilder 값 변경하기

- StringBuilder 객체의 특정 값을 변경할 때, `빌더 이름.setChartAt(인덱스, 문자)` 메소드를 활용한다.

```java
StringBuilder name = new StringBuilder(“starfucks”);
name.setCharAt(4, ‘b’);
System.out.println(name);

> starbucks
```

## 입/출력

### BufferdReader

- `BuffedReader` 클래스는 **Enter를 경계**로 입력값을 인식한다.
- `readLine()` 메소드는 개행문자(Enter)를 포함해 String 형식으로 입력을 받아온다. 따라서, int 형으로 `readLine()`을 받아오려면 `Integer.parseInt()`로 형변환이 필요하다.

```java
import java.io.InputStreamReader;
import java.io.BufferedReader;
import java.io.IOException;

// Input : 100
public class InputOuputTest
	public static void main(String args[])throws IOException{
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in))
		int n = Integer.parseInt(br.readLine());
		System.out.print(n);
	}
}

> 100
```

### StringTokenizer

- `StringTokenizer` 클래스는 지정한 형식에 따라 문자열을 쪼개주는 클래스이다. `new StringTokenizer(문자열, 기준)` 형식으로StringTokenizer 객체를 생성할 수 있다.

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

// Input : 123 456
public class InputOuputTest {
	public static void main(String args[]) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st = new StringTokenizer(br.readLine());
      int n = Integer.parseInt(st.nextToken());
      int m = Integer.parseInt(st.nextToken());
        System.out.print(n + ” “ + m);
    }
}

> 123 456
```

## Queue

### 생성

```java
import java.util.Queue;

Queue<Integer> queue =new LinkedList<>();
Queue<String> queue = new LinkedList<>();
```

### 삽입

```java
Queue<Integer> queue = new LinkedList<>();
queue.add(1);
queue.add(2);
```

### 꺼내기

- Queue의 가장 앞의 원소를 꺼낼때는, `큐 이름.poll()` 메소드를 사용한다.

```java
Queue<Integer> queue = new LinkedList<>();
queue.add(1);
int first = queue.poll();
```

## Priority Queue

### 생성

- 생성자에 Collections를 사용해 `오름차순`, `내림차순`을 설정할 수 있다. defaults는 오름차순이다.

```java
import java.util.PriorityQueue;

// 오름차순
PriorityQueue<Integer> pq = PriorityQueue<>();
PriorityQueue<String> pq = PriorityQueue<>();

// 내림차순
PriorityQeueu<Integer> pq = PriorityQueue<>(Collections.reverseOrder());
```

### 삽입

```java
PriorityQueue<Integer> pq = PriorityQueue<>();
pq.add(1);
pq.add(2);
```

### 제거

```java
PriorityQueue<Integer> pq = new PriorityQueue<>(Collections.reverseOrder());
pq.add(2);
pq.add(12);
pq.add(13);

System.out.println(pq);
pq.remove();
System.out.println(pq);

> [13, 2, 12]
> [12, 2]
```

### `minHeap`과 `maxHeap`

- PriorityQueue의 minHeap, maxHeap에 접근할 때, `priorityQueue 이름.peek()` 메소드를 사용한다.
- Primitive 타입이 아니라면 Comparable 인터페이스를 구현해야만 정렬이 가능하다.

```java
PriorityQueue<Integer> pq = PriorityQueue<>();
pq.add(1);
pq.add(2);
pq.add(13);

System.out.println(pq.peek()); // 1
PriorityQueue<Integer> pq = PriorityQueue<>(Collections.reverseOrder());
pq.add(1);
pq.add(2);
pq.add(13);

System.out.println(pq.peek()); // 13
```

---

[1] [Java - 자바 코딩테스트 문법 정리 - 오늘코딩](https://gwang920.github.io/java/Java-condingGrammer/)
