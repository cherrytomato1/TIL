# String의 불변성(Immutable)

- String은 불변성을 가지며 새로운 값을 할당하기 위해서 새로운 객체를 생성해야한다.
- 불변성을 갖는 String은 `a = a + "test2"` 와 같은 연산을 수행할 때 새로운 객체를 생성해 값을 복사해 그 레퍼런스를 반환한다.

    → String 간 이로 인해 연산은 비용이 크게 발생하며 그 때문에 StringBuilder, StringBuffer를 사용한다.

## 불변성 보장

### String Constant Pool

- `String a = "test"` 와 같이 스트링 리터럴을 통해 생성된 객체는 String Constant Pool에 저장된다
- 객체로 만들어진 스트링 리터럴은 String Constant Pool에 저장되어 같은 스트링 리터럴로 다시 만들어지는 객체들 역시 SCP에 존재하는 스트링 리터럴을 참조하게 된다.
    - 한번 표현된 스트링 리터럴은 마지막 사용까지 SCP에 유지된다. 더이상 사용되지 않으면 수거된다.
- String 형 레퍼런스는 SCP에 저장된 값을 가르키게 되고 두 개의 각각 다른 스트링 리퍼런스라도 같은 내용의 스트링 리터럴을 갖고 있다면 같은 곳을 참조하게 되는 것이다.

```java
String a = "test";
String b = "test";

system.out.println(a == b);
//true, 참조하는 SCP 가 같다.

```

- 하나의 객체가 참조하는 스트링 리터럴을 바꾸려고 하면 그 리터럴을 참조하는 모든 객체의 내용이 바뀌게 되므로 스트링은 Immutable 해야한다.

### String 리터럴과 new String()

- String 객체를 생성하는 방법은 두가지가 있다

```java
String a = new String("test");
String b = "test";

//비교하기
System.out.println(a == b );
//false

```

- new String은 String 객체를 생성하는 것이지만, 해당 스트링 리터럴을 참조하는 새로운 객체를 만드는 것이다.
- new를 통해 생성된 객체도 같은 SCP 내부의 메모리를 가르키므로 동작상 동일하나 불필요한 과정이 한가지 추가되는 것이다.
- 이 때 최종적으로 SCP에 접근하는 레퍼런스가 갖고 있는 값은 모두 갖게 된다.

## 불변성을 갖는 이유

### 1. 메모리 절약

String 객체는 프로그램 동작 상에서 자주 사용하게 된다. 그중 자주 사용하는 String은 계속하여 사용되는 경우가 있는데, 그럴 때마다 객체를 생성해 주기보다는 scp에 저장하여 같은 문자열이라면 같은 주소를 참조하게 해서 힙 영역을 절약할 수 있다.

### 2. 보안상 문제 제거 및 thread - safe

외부에서 DB 내의 username, password 등의 중요정보를 제어할 수 없도록 Immutable 하게 관리한다. 또한 다중쓰레드 환경에서 값의 변경가능성을 지워 동기화 이슈를 고려하지 않을 수 있다.

### 3. String 캐싱

객체의 hashcode를 통해 키로 사용할 때 String은 매번 계산하지 않고 생성 단계에서 캐싱을 한다. 이렇게 해시코드를 캐싱할 수 있는 이유는 각 String은 불변하기 때문이다.

## String의 다른 특성

### toString() 호출

- 자바에서 String이 수식에 사용되게 되면 해당 수식 내의 모든 값은 toString을 호출하여 String 형으로 수식에서 연산한다.

```java
public class StringTest {
	public static void main(String[] args) {
		String str = "hello ! ";
		Object o1 = new Object();
		String str2 = str + o1;
		
		System.out.println(str2);
		
		//출력값 : hello ! java.lang.Object@2a139a55
	}
}

```

- 위와 같이 다른 클래스의 객체와 연산하더라도 toString()을 호출해 연산한다.
