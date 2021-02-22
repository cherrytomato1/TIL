# JAVA의 Thread

## Concurrent vs Parallel - 동시성과 병렬성

자바의 쓰레드는 Concurrent하게 동작합니다. Concurrent 는 논리적으로 여러 작업을 동시에 처리하는 멀티쓰레드 동작이지만 여러 쓰레드가 물리적으로 동시에 실행되지는 않습니다. 실제 동작은 여러 쓰레드의 수행시간을 쪼개어 동작합니다. Parallel도 어떤 작업을 여러 작업으로 쪼개고 동시에 수행하는 것을 말합니다. 하지만 Parrallel은 Concurrent와 다르게 물리적으로 동시에 여러쓰레드를 병렬적으로 수행하는 것을 말합니다. 

## Thread

쓰레드는 프로세스를 구성하는 세부 실행단위(Unit)를 말합니다. 자바는 JVM을 통해 멀티 쓰레드를 구성하여 concurrent 하게 동작하여 동시에 여러 작업을 수행할 수 있습니다. 하지만 동시에 수행되는 쓰레드의 수행순서는 보장되어 있지 않습니다.

### Main Thread

 JVM은 main() 메소드를 찾아 Main Thread를 동작시킵니다. 기본적으로 Main Thread는 다른 쓰레드와 차별화 되어 있는게 아니라 처음에 수행되는 쓰레드입니다. 그러므로 메인 쓰레드의 종료와 다른 쓰레드의 종료는 관련없이 별개로 동작합니다. 메인 쓰레드가 종료되어도 다른 모든 쓰레드가 종료되어야 JVM의 동작이 멈춥니다.

# Thread Class

JAVA에서 쓰레드는 `java.lang.Thread` 클래스를 통해 사용합니다. `Thread` 클래스의 속성인 `Runnable target`  구현객체의 `run()` 메소드의 구현부에서 해당 쓰레드의 실제 동작을 정의하며 `start()` 메소드를 통해 쓰레드를 스케쥴러를 통해 동작시킬 수 있습니다.

## Thread Class의 주요 메소드

### Thread 생성자

- `Thread` 객체를 생성할 때 `Runnable` 객체, 쓰레드 명, 쓰레드 그룹 등을 받아 객체를 생성합니다.
- `private void init(...)` 메서드를 통해 쓰레드를 생성합니다.

```java
private Runnable target;

public Thread() {
        init(null, null, "Thread-" + nextThreadNum(), 0);
}

//runnable 구현 객체를 매개로 받은 후 init() 메서드로 쓰레드 생성
public Thread(Runnable target) {
    init(null, target, "Thread-" + nextThreadNum(), 0);
}

//쓰레드의 이름을 부여. 부여하지 않으면 0번 부터 차례로 인덱싱
public Thread(String name) {
        init(null, null, name, 0);
}
// ....
```

### start()

- `start()` 메서드를 수행하면 native 메서드인 `start0()` 를 통해 쓰레드를 스케쥴러에 포함시킬 수 있습니다.
- 스케쥴러에 포함된 쓰레드는 쓰레드 큐 내에서 runnable로 대기하다가 수행 차례가 되면 `run()` 메서드를 호출하여 동작을 수행합니다.
- **쓰레드는 단 한번만 `start()` 메서드가 호출 될 수 있으며 Terminate 되었거나 Waiting 인 상태여도 `start()` 메서드를 호출 할 수 없습니다.**

```java
public synchronized void start() {
		// 해당 쓰레드는 시작되지 않은 상태여야 한다. 한번 수행된 쓰레드는 다시 수행할 수 없음.
    if (threadStatus != 0)
        throw new IllegalThreadStateException();

    group.add(this);

		// started 플래그를 통해 예외를 받는다.
    boolean started = false;
    try {
        start0();
        started = true;
    } finally {
        try {
            if (!started) {
                group.threadStartFailed(this);
            }
        } catch (Throwable ignore) {
            /* do nothing. If start0 threw a Throwable then
              it will be passed up the call stack */
        }
    }
}

private native void start0();
```

### run()

- `start()` 메서드를 실행하면 스케쥴러 내에서 실제 동작하는 부분입니다. `Thread` 클래스는 `Runnable` 인터페이스의 `run()` 메소드를 구현했습니다.
- `target.run()` 메서드를 수행하기 때문에 생성자를 통해 `target` 을 지정하거나 `Thread` 클래스를 상속해 `run()`  메서드를 재정의해야합니다.
- **쓰레드의 `start()` 메서드를 호출하지 않고 `run()` 메서드를 직접 호출하면 쓰레드는 스케쥴러에 포함되지 않고 일반 메서드를 호출한 것처럼 동작합니다. 그렇기 때문에 `run()` 메서드를 수행하는 쓰레드는 호출한 쓰레드가 됩니다.**

```java
private Runnable target;
// 
@Override
public void run() {
    if (target != null) {
        target.run();
    }
}
```

### sleep()

- `sleep()` 메서드를 통해 쓰레드를 Waiting pool 로 보내 대기시킬 수 있습니다.
- **static 메서드이므로 호출한 쓰레드가 대기상태에 들어갑니다.**
- ms 단위의 시간을 매개변수로 받아 해당 시간만큼 쓰레드를 대기시킵니다.
- 인터럽트가 발생하면 다시 Runnable 상태가 되며 `InterruptedException` 을 발생시킵니다.

```java
public static native void sleep(long millis) throws InterruptedException;
```

### 기타 메서드

- `public static native Thread currentThread();`
    - static 메서드로 호출한 쓰레드의 객체를 반환
- `String getName()`,  `Void setName()`
    - 쓰레드명에 대한 게터 및 세터
- `public final native boolean isAlive();`
    - 쓰레드가 현재 수행되고 있는지를 `boolean` 반환

## Thread의 구현

자바에서는 `Runnable` 인터페이스를 구현한 객체의 `run()` 메서드를 호출하여 쓰레드를 동작시킬 수 있습니다. `Thread` 클래스도 `Runnable` 을 구현했기 때문에 `Thread` 클래스를 상속받아 `run()` 메소드를 재정의 하는 것으로도 쓰레드를 구현할 수 있습니다.

### Runnable 인터페이스

- `Interface Runnable` 을 구현하는 클래스를 생성
- `void run()` 메소드를 오버라이딩하여 쓰레드의 동작 정의
- 쓰레드 객체를 만들어서  `Runnable` 을 구현한 객체를 생성자의 매개변수로 넘김
- 해당 쓰레드 객체의 `start()` 메소드로 쓰레드 수행

```java
public class myRunnable implements Runnable{
    @Override
    public void run(){
    //... do something...
    }
}

//...
public static main(String[] args){
    myRunnable mr = new myRunnable();
    Thread myThread = new Thread(mr);
    myThread.start();
}
```

### Thread 클래스 상속

- `Thread` 클래스를 상속한 클래스 구현
- `void run()` 메소드를 오버라이딩
- 구현 객체를 생성하고 객체의 `start()` 메소드로 `run()` 메소드 수행 시작

```java
public class myThread extends Thread{
    @Override
    public void run(){
    //... do something...
    }
}

//...
public static main(String[] args){
    Thread myThread = new myThread();
    // upcasting
    myThread.start();
}

```

 `Thread` 클래스를 상속하면 구현이 더 간단하지만 다른 클래스를 상속할 수 없기 때문에 상황에 맞게 쓰레드를 구현합니다.

---

→ 스케쥴링과 동기화 관련 내용은 다음 포스팅에 ...
