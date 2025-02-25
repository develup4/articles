## JVM과 Thread

### 프로세스와 쓰레드

- JVM이 시작되면 Java 프로세스가 시작된다.
- 그리고 이 프로세스 안에서 여러 개의 **쓰레드**라는 것이 아둥바둥 살게된다.
- Tomcat과 같은 WAS도 역시나 똑같이 main() 메소드에서 생성한 쓰레드들이 수행되는 것이다.

### Java에서 기본적으로 존재하는 Thread들

- 아무런 쓰레드를 생성하지 않아도 **JVM을 관리하기 위한 여러 쓰레드가 존재**한다.
- 예를 들면 Java의 쓰레기 객체를 청소하는 `GC 관련 쓰레드`가 여기에 속한다.

### Thread와 메모리 공간

- JVM은 기본적으로 아무런 옵션 없이 실행하면 OS마다 다르지만 적어도 32MB~64MB의 물리 메모리를 점유한다.
- 그에 반해서, 쓰레드를 하나 추가하면 1MB 이내의 메모리를 점유한다.
- 그래서 쓰레드를 `경량 프로세스`라고도 부른다.

## Java와 Thread

### 생성

- `Runnable 인터페이스`나 `Thread 클래스`를 통해 쓰레드를 생성한다.
- 둘다 `java.lang`에 있다.
- 둘다 `run()`으로 실행한다.
- 또 다른 클래스를 상속할 필요가 있을때는 Runnable 인터페이스를 사용한다.

### Thread의 이름

- 모든 쓰레드는 이름이 있다.
- 이름을 지정하지 않으면 그 쓰레드의 이름은 `Thread-n`이다.

### 우선순위 지정

- 우선순위 관련 상수가 정의되어 있다.
- `MAX_PRIORITY`, `NORM_PRIORITY`, `MIN_PRIORITY`
- 하지만 기본값으로 사용하는 것을 권장한다.

### Stack 사이즈

- 생성시 stackSize를 지정할 수 있다. **경우에 따라서는 이 값이 무시될 수도** 있다.

### Sleep 메소드

- Thread 클래스에는 sleep() 메소드가 있다.
- `Thread.sleep()`을 쓸때는 반드시 `try-catch`로 묶고, 적어도 `InterruptedExceiption`으로 catch해주어야한다.

### 데몬 쓰레드

- 데몬 쓰레드가 아닌 사용자 쓰레드는 JVM이 해당 쓰레드가 끝날 때까지 기다린다.
- `thread.setDaemon(true);`
- 예를 들어 모니터링하는 쓰레드를 별도로 띄워 모니터링할때 사용할 수 있다.

### join 메소드

- 인자가 없다면 시간제한없이 수행 중인 쓰레드가 중지할 때까지 대기한다.
- `join(6000);`과 같이 인자가 있을 경우 특정 시간만큼만 기다린다.

### interrupt 메소드

- `InterruptedException`을 발생시킨다.

### Synchronized

- 메소드 자체를 synchronized로 선언할 수 있다.
  - synchronized methods라고 부른다.
  - `public synchronized void plus(int value) { ... }`
- 블록으로 감싸는 방법
  - synchronized statements라고 부른다.
  - `synchronized(this) { ... }`
  - **잠금처리를 위한 객체를 선언**해야한다.
  - `Object lock = new Object; synchronized(lock) { ... }`

## 예제 코드

```java
public class StateThread extends Thread {
	private Object monitor;
	public StateThread(Object monitor) {
		this.monitor = monitor;
	}

	public void run() {
		try {
			for (int loop = 0; loop < 10000; loop++) {
				String a = "A";
			}
			synchronzied(monitor) {
				// 다른 쓰레드가 Object 객체에 대한 notify() 메소드나 notifyAll() 메소드를 호출할 때까지
				// 현재 쓰레드가 대기하고 있도록 한다.
				monitor.wait();
			}
			Thread.sleep(1000);
		} catch(InterruptedException ie) {
			ie.printStackTrace();
		}
	}
}
```

---

[1] 이상민, 25장 “쓰레드는 개발자라면 알아두는 것이 좋아요”, 자바의神 2nd Edition
