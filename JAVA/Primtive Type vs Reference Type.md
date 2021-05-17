# 1. 변수형

## 기본형 (Primitive type)

기본형 변수는 참조하지 않고 실제 값(리터럴)을 저장하며 컴파일 시점에서 할당 메모리의 크기를 검사합니다.

- 자바의 기본형

```java
boolean     1byte       true, false
byte        1byte       -128 ~ 127
short       2byte       -32_768 ~ 32767
int         4byte       -2147483648 ~ 2147483647
long        8byte       -9223372036854775808 ~ 9223372036854775807
float       4byte       1.4E-45 ~ 3.4028235E38 (근사값)
double      8byte       4.9E-324 ~ 1.7976931348623157E308 (근사값)
char        2byte       '\u0000' ~ '\uFFFF'
```

## 리터럴과 상수

### 리터럴

`1, 0.1, 'c'` 와 같은 변수에 저장될 수 있는 **데이터 자체를 의미합니다.** 리터럴 값들은   불변성을 지니며 값은 실제 할당 이 후 변하지 않습니다.

자바에서 각 변수형 별로 리터럴을 표기하는 법은 다음과 같습니다.

- 정수형 (short ~ long)
    - 2진수 : `0b10`
    - 8진수 : `02`
    - 10진수 : `2`
    - 16진수 : `0x2`
- 부동소수점
    - float : `2.0F`
    - double : `2.0D`
- 문자형 및 문자열
    - char : `'2'`
    - String : `"2"`

### 상수

상수도 불변성을 지닌다는 관점에서 리터럴과 비슷한 점을 갖고 있습니다. 하지만 상수는 불변성을 지니는 **변수**라는 점에서 리터럴과 차이를 갖습니다.

한번 초기화된 상수는 이 후에 다시 초기화 할 수 없으며 자바에서는 `final` 키워드로 상수를 선언합니다.

## 참조형 (Reference type)

참조형은 기본형을 제외한 모든 타입을 칭하며 실제 데이터 값이 아닌 객체의 주소값(해시코드값)을 저장하는 변수를 말합니다.

- `Class` `Interface` `array` `Enumeration`...

참조형 변수는 항상 4byte의 같은 크기를 가지며 해당 객체의 해시코드값 혹은 null 값을 저장합니다. 

- 선언 시 기본 값은 null로 초기화 됩니다.
    - null을 참조하는 레퍼런스의 객체에 접근하려하면 런타임 에러(NullPointerException)가 발생할 수 있습니다.

이 때 `new` 키워드로 생성된 객체는 힙(Heap) 메모리에 저장됩니다.

```jsx
Person man = new Person();
/* 
new를 통해 객체를 생성하고 new의 반환값으로 생성된 객체의 해시코드를 받습니다.
그 후 그 주소를 man 참조변수에 저장하게 됩니다.
*/
```

### 참조 변수 연산

자바에서는 기본형 타입끼리만 산술 연산 및 논리연산이 가능하며 레퍼런스 타입은 산술연산 및 논리연산을 할 수 없습니다.

예외적으로 `==` 연산자를 통해 가르키고 있는 해시코드의 값을 비교할 수 있습니다. 이 때는 참조하고 있는 레퍼런스가 같다면 `true` 를 반환합니다.

### equals, comparable

레퍼런스끼리 값 비교가 필요할 경우 `Object.equals` 메소드를 오버라이딩하여 원하는 값끼리 비교하게 할 수 있습니다.

- ex) `String.equals(String str2)` ⇒ 참조 해시코드가 다르더라도 저장된 문자열 값이 같다면 `true` 반환

대소 비교가 필요하다면 `Comparable` 인터페이스를 구현하여 `compareTo(object O)` 메소드를 재정의하여 레퍼런스 간 비교를 할 수 있습니다.

### String Class 연산

`String Class` 는 예외적으로 `+` 로 산술연산이 가능합니다. 어떤 연산식에 `String` 객체 혹은 `String` 리터럴이 포함된다면 해당 수식의 모든 값은 String으로 비교하여 반환됩니다.

기본형 변수는 true 혹은 그 값이 가르키고 있는 실제 값이 String으로 반환되어 연산되며 참조형 변수는 해당 객체의 `toString()` 메소드를 통해 반환된 문자열로 치환됩니다.

```java
Object o1 = new Object();
Object o2 = new Object();
System.out.println(o1 + o2);
// println 메소드는 기본적으로 레퍼런스의 toString을 호출하지만 객체 간 산술연산은 불가능하므로 에러가 발생합니다.

String str = "hello";
System.out.println(str + o1 + o2);
//hello o1.toString() o2.toString()
//산술연산식에 String이 각 레퍼런스는 toString()을 호출하여 반환된 String으로 연산합니다.

System.out.println(o1 + o2 + "");
//o1.toString() o2.toString()
//이 역시 연산식에 String이 포함되었으므로 연산이 가능합니다.
```

## 참조의 종류

각 클래스는 참조의 종류를 가지는 클래스를 상속하여 원하는 참조의 종류를 구현할 수 있습니다.

```java
static class WeakA extends WeakReference<A>{}
```

### Strong Reference

- 가장 많이 사용되는 참조로 힙 메모리의 객체를 직접적으로 참조합니다.
- 일반적으로 생성된 레퍼런스는 모두 `StrongReference` 입니다.
- 도달 가능한 범위에서 GC에 의해 수거되지 않습니다. 단 참조값이 `null` 일 경우 GC에게 collecting 될 수 있습니다.

### Weak Reference

- `java.lang.ref.WeakReference` 클래스를 통해 참조를 생성할 수 있습니다.
- `get()` 메서드를 통해 참조객체를 반환하거나, gc에 의해 수집된 경우 `null` 을 반환합니다.
- 해당 객체가 예상 수명보다 오래 남아있을 경우 메모리 누수가 발생할 수 있으므로 `Weak Reference` 로 레퍼런스를 생성해 방지할 수 있습니다.

### Soft Reference

- `java.lang.ref.SoftReference` 클래스를 통해 참조를 생성할 수 있습니다.
- `Soft Reference` 는 힙 메모리에 충분한 공간이 남아있지 않을 경우 GC에서 수집합니다.
- 힙 메모리가 충분히 사용되지 않았을 경우에는 수집되지 않기에 이점이 크지 않을 수 있습니다.
- 메모리 부족에 의한 에러가 예상될 때, 메모리를 확보하는 것이 더 중요한 레퍼런스라면 `Soft Reference` 를 통해 참조할 수 있습니다.

### Phantom Reference

- `java.lang.ref.PhantomReference` 클래스를 통해 참조를 생성할 수 있습니다.
- 객체가 `phantom` 에 도달할 수 있고 참조가 남아있지 않다면 GC 큐에 추가됩니다.
- `Phantom Reference` 는 JAVA 8 까지는 GC의 자동수행이 아닌 호출을 통해서만 객체가 수집됩니다. JAVA 9 부터는 자동 수집됩니다.

 

참고 - 참조의 종류 : [https://dzone.com/articles/understanding-type-of-references-in-java#:~:text=Strong References,eligible for the garbage collector](https://dzone.com/articles/understanding-type-of-references-in-java#:~:text=Strong%20References,eligible%20for%20the%20garbage%20collector).
