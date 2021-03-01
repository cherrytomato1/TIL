# 멀티 쓰레드의 공유자원

쓰레드가 동시성으로 실행될 때 여러 쓰레드가 동시에 접근 가능한 자원을 공유자원이라고 합니다. 

### 예제

```java
class MyClass{
    int val;	
    public void sum() {
        System.out.println("더하기 !!");
        val++;
        System.out.println("결과값 :  " + val);
    }
}

class MyThread implements Runnable{
    MyClass mc;
    public MyThread(MyClass mc) {
        this.mc = mc;
    }
    @Override
    public void run() {
        mc.sum();
    }
}

public class ThreadTest {
    public static void main(String[] args) {
        MyClass mc = new MyClass();	

        //10개의 쓰레드가 동시에 mc 속성에 접근하고 있음 
        for(int i = 0; i < 10; i ++) {
            new Thread(new MyThread(mc)).start();
        }
    }
/*  출력 :
    더하기 !!
    더하기 !!
    결과값 :  1
    더하기 !!
    결과값 :  3
    더하기 !!
    결과값 :  4
    더하기 !!
    결과값 :  5
    더하기 !!
    결과값 :  6
    더하기 !!
    결과값 :  7
    결과값 :  2
    더하기 !!
    결과값 :  8
    더하기 !!
    더하기 !!
    결과값 :  10
    결과값 :  9
*/
}
```

위와 같이 하나의 자원을 공유하는 쓰레드가 만들어질 수 있습니다. 이 때 쓰레드의 수행 순서는 우선순위가 같다면 정해지지 않은 순서로 수행됩니다. 따라서 각 `val++` 의 연산과 `System.out.print()` 간에 다른 쓰레드가 다시 `val++` 의 연산을 수행할 수 있으므로 결과는 매 실행마다 다르게 출력됩니다.

---

## 멀티 쓰레드 공유자원 접근

### 예제

공유자원에 대해 더 알아보기 위해 다음과 같은 코드를 작성해봅니다.

```java
class MyClass{
    int val;	
    public void sum() {
        if(val < 5) {
            try {
                Thread.sleep(500);
            } catch(InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("더하기 !!");
            val++;
        }
        System.out.println("결과값 :  " + val);
    }
}

class MyThread implements Runnable{
    MyClass mc;
    public MyThread(MyClass mc) {
        this.mc = mc;
    }
    @Override
    public void run() {
        mc.sum();
    }
}

public class ThreadTest {
    public static void main(String[] args) {
        MyClass mc = new MyClass();	

        //10개의 쓰레드가 동시에 mc 속성에 접근하고 있음 
        for(int i = 0; i < 10; i ++) {
            new Thread(new MyThread(mc)).start();
        }
  }
/*  출력 :
    더하기 !!
    더하기 !!
    더하기 !!
    더하기 !!
    더하기 !!
    더하기 !!
    결과값 :  6
    결과값 :  5
    결과값 :  4
    결과값 :  4
    결과값 :  4
    결과값 :  4
    더하기 !!
    결과값 :  7
    더하기 !!
    결과값 :  8
    더하기 !!
    결과값 :  9
    더하기 !!
    결과값 :  10
    */
}
```

`mc.val` 이 5 미만일 경우에만 `val++` 연산을 하도록 했는데 10 까지 결과값을 확인할 수 있습니다. 이는 각 쓰레드가 `if` 문을 통과하고 `val++`  연산 이전에 다른 쓰레드도 `if` 문을 통과할 수 있기 때문입니다.

위와 같이 멀티 쓰레드 환경에서 공유자원에 대한 작업을 하게되면 원하는 결과를 출력하지 않을 수 있습니다. 그래서 멀티쓰레드 환경에서 `synchronized` 를 통한 동기화로 공유자원에 대한 임계영역을 설정해야합니다.

---

# 쓰레드 동기화

공유자원에 대하여 원자성을 확보하기 위해 `synchronized` 를 사용할 때, `synchronized` 메서드와 `synchronized` 블럭을 사용할 수 있습니다.

## synchronized method

메서드의 제어자에 `synchronized` 키워드를 작성하여 임계영역을 설정할 수 있습니다. `synchronized` 메서드가 호출되면 해당 메서드의 객체는 호출한 쓰레드에게 Lock Flag를 전달하며 해당 쓰레드 외에 공유객체를 필요로 하는 쓰레드는 **Running** 상태가 될 수 없으며 Lock Flag 가 반납될 때까지 Waiting Pool의 Lock pool에서 대기하게 됩니다.

### 예제

```java
class MyClass{
    int val;	
//synchronized 추가
    public synchronized void sum() {
        if(val < 5) {
            try {
                Thread.sleep(500);
            } catch(InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("더하기 !!");
            val++;
        }
        System.out.println("결과값 :  " + val);
    }
}

class MyThread implements Runnable{
    MyClass mc;
    public MyThread(MyClass mc) {
        this.mc = mc;
    }
    @Override
    public void run() {
        mc.sum();
    }
}

public class ThreadTest {
    public static void main(String[] args) {
        MyClass mc = new MyClass();	

        //10개의 쓰레드가 동시에 mc 속성에 접근하고 있음 
        for(int i = 0; i < 10; i ++) {
            new Thread(new MyThread(mc)).start();
        }
    }
/*  출력 :
    더하기 !!
    결과값 :  1
    더하기 !!
    결과값 :  2
    더하기 !!
    결과값 :  3
    더하기 !!
    결과값 :  4
    더하기 !!
    결과값 :  5
    결과값 :  5
    결과값 :  5
    결과값 :  5
    결과값 :  5
    결과값 :  5
    */
}
```

어떤 쓰레드가 `sum()` 메서드에 접근하게 되면 해당 쓰레드는 `mc` 객체에 대한 Lock Flag 를 갖게되고 다른 쓰레드는 `mc` 에 대해 접근할 수 없습니다.

---

```java

class MyClass{
    int val;	
}

class MyThread implements Runnable{
    MyClass mc;
    public MyThread(MyClass mc) {
        this.mc = mc;
    }
    @Override
    public void run() {
        sum();
    }

    public synchronized void sum() {
        if(mc.val < 5) {
        try {
            Thread.sleep(500);
          } catch(InterruptedException e) {
            e.printStackTrace();
          }
          System.out.println("더하기 !!");
          mc.val++;
        }
        System.out.println("결과값 :  " + mc.val);
    }
}

public class ThreadTest {
    public static void main(String[] args) {
        MyClass mc = new MyClass();	

        //10개의 쓰레드가 동시에 mc 속성에 접근하고 있음 
        for(int i = 0; i < 10; i ++) {
            new Thread(new MyThread(mc)).start();
        }
    }

/*  출력 :
    더하기 !!
    더하기 !!
    더하기 !!
    더하기 !!
    더하기 !!
    결과값 :  4
    결과값 :  5
    더하기 !!
    결과값 :  6
    더하기 !!
    결과값 :  7
    더하기 !!
    결과값 :  8
    더하기 !!
    결과값 :  9
    더하기 !!
    결과값 :  4
    결과값 :  5
    결과값 :  5
    결과값 :  10
*/
}
```

**하지만 위와 같이 사용하게 된다면 `sum()` 메서드는 `mc` 객체에 임계영역을 설정하는 것이 아닌 `synchronized` 가 작성된 `sun()` 의 객체인 쓰레드에게 임계영역을 지정하는 것이므로 `mc` 객체에 모든 쓰레드가 한번에 접근할 수 있습니다.**

## synchronized 블럭

`synchronized(공유 객체){}` 로 블럭을 설정하면 블럭이 수행되는 동안 해당 쓰레드가 공유 객체에 대한 Lock flag를 가져갈 수 있습니다. 메서드 전체에 임계영역을 설정하는 것이 아닌 블럭 내에만 설정하므로 Lock flag 점유 시간을 더 효율적으로 사용할 수 있습니다.

### 예제

```java

class MyClass{
    int val;	
}

class MyThread implements Runnable{
    MyClass mc;
    public MyThread(MyClass mc) {
        this.mc = mc;
    }
    @Override
    public void run() {
        synchronized (mc) {
            if(mc.val < 5) {
                try {
                    Thread.sleep(500);
                } catch(InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("더하기 !!");
                mc.val++;
            }
            System.out.println("결과값 :  " + mc.val);
        }
    }
}

public class ThreadTest {
    public static void main(String[] args) {
        MyClass mc = new MyClass();	

        //10개의 쓰레드가 동시에 mc 속성에 접근하고 있음 
        for(int i = 0; i < 10; i ++) {
            new Thread(new MyThread(mc)).start();
        }
    }
//  출력 생략.. 
}

```

`synchronized` 블럭으로 `mc` 객체에 대한 lock flag 를 하나의 쓰레드만 보유할 수 있습니다. 따라서 블럭 내의 동작들은 순차적으로 수행됩니다.

---

## wait() - notify() - notifyAll()

### wait()

해당 쓰레드가 공유 자원에 대한 Lock을 소유하더라도 현재 수행이 불가능하다면 Lock을 잠시 반납할 수 있습니다. `wait()` 메소드 호출을 통해 잠시 반납합니다. 

쓰레드 내에서 `synchornized` 블럭 혹은 메서드에서 Lock 된 어떤 공유 객체에 대하여  `wait()` 메서드를 통해 Lock을 잠시 해제합니다.

```java
public final native void wait(long timeout) throws InterruptedException;
public final void wait(long timeout, int nanos) throws InterruptedException {
	//...
}
public final void wait() throws InterruptedException {
    wait(0);
}
```

쓰레드 객체의 `wait()` 메서드를 호출하게되면 해당 쓰레드는 Lock flag 를 반납하고 Wating Pool에서 대기하며, 대기 하는 동안 Interrupt가 발생할 수도 있기 때문에 인터럽트 예외를 처리해야합니다. 또한 `wait()` 메서드의 매개변수를 통해서 얼마만큼의 시간을 대기한 후 깨어날 지 설정할 수 있으며 `wait(0)` 인 경우 `notify()` 메서드가 호출될 때까지 계속 대기합니다.

### notify(), notifyAll()

`notify()` , `notifyAll()` 메서드는  `wait()` 메서드로 공유자원에 Lock을 잠시 해제하고 Waiting Pool에 대기하고 있는 쓰레드에게 작업이 완료되었음을 알립니다. 

```java
public final native void notify();
public final native void notifyAll();
```

`notify()`는 Wating Pool에 대기하는 임의의 쓰레드를 깨우며, `notifyAll()`은 대기중인 Waiting Pool의 모든 쓰레드에게 알립니다. `notify()` 는 해당 쓰레드의 우선순위 값과 관련없이 임의의 쓰레드를 깨우므로 높은 우선순위의 쓰레드가 계속 대기할 수 있으니 `notifyAll()` 메서드를 통해 JVM 스케줄러가 우선순위에 따라 Running 상태로 이동시키게 하는 것이 더 효율적일 수 있습니다.

### 사용

`wait()`, `notify()` 그리고 `notifyAll()` 메서드는 `Object` 클래스와 `Thread` 클래스에 정의되어 있습니다. `Object` 클래스의 메서드를 호출하게 되면 메서드를 수행한 쓰레드에 대해서 `wait()` 을 동작하게 되고 `Thread` 클래스의 `wait()` 으로 임의의 쓰레드에게 대기 명령을 줄 수 있습니다.

---

# Dead-Lock, Starvation

## Dead - Lock

교착 상태, 이중 잠금. 서로 공유 객체를 필요로 하는데, Lock flag를 서로가 갖고 있어 접근이 제한되어 서로 무한 대기상태가 되는 것을 말합니다.

![img](https://github.com/cherrytomato1/TIL/blob/main/img/dead-lock.png)

## Starvation

기아 상태, 쓰레드 간 우선순위의 차이로 인해서 높은 우선순위를 가진 쓰레드가 Lock Flag 를 계속 획득하여 낮은 우선순위를 가진 쓰레드는 Lock flag를 계속해서 얻지 못하고 고갈되는 상태를 말합니다.

## Deprecated Method

Java 8 기준으로 `Thread` 클래스의 `stop()`, `suspend()`, `resume()`, `destroy()` 메서드는 *deprecated*  되었습니다. 각 메서드는 쓰레드의 lock flag 및 다른 점에서 문제점을 야기합니다.

### stop()

`stop()` 메서드는 수행되면 모든 lock을 해제하고 쓰레드를 강제 종료합니다. 이 과정에서 공유객체는 불완전해질 수 있습니다.

### suspend(), resume()

`suspend()` 메서드가 호출되면 해당 쓰레드는 `resume()` 메서드가 호출될 때까지 Waiting Pool에서 대기합니다. 이 때 쓰레드는 갖고 있던 Lock Flag 를 반환하지 않으므로 `resume()` 메서드를 수행하는 쓰레드가 해당 Lock Flag를 필요로 한다면 **Dead - Lock** 상태에 빠지게 됩니다.

### destroy()

`destroy()` 메서드는 쓰레드를 강제로 종료시킵니다. 이 때 Lock Flag를 반환하지 않기 때문에 Lock Flag를 필요로 하는 다른 쓰레드에게 **Starvation** 이 발생할 수 있습니다.

---
