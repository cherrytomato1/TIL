## 기본형 (Primitive type)

- 기본형 변수는 참조하지 않고 실제 값을 저장합니다.
- 컴파일 시점에서 할당 메모리의 크기를 검사합니다.
- 총 8 개의 변수형이 존재합니다.

```java
// 자바의 기본형 타입
    boolean, byte, short, int, long, float, double, char
// 1byte, 1byte, 2byte, 4byte, 8byte, 4byte, 8byte, 2byte
```

- 기본형 타입의 산술 연산에서는 더 큰 범위를 갖는 타입으로 자동 형변환되어 실행됩니다. 하지만 반대의 경우는 명시적 형변환을 해야합니다.

```java
int a = 10;
long b = a;
// b에 자동형변환 된 a 가 저장됩니다.
a = b;
// 이 경우는 명시적형변환을 하지 않으면 컴파일 에러가 발생합니다.
a = (int) b;
char c = 'a';
a = c;
// a == 61
//char type도 unsigned short와 같은 범위를 갖고 있기 때문에 자동형변환됩니다.
```

- `boolean` 타입은 다른 타입과 치환될 수 없으므로 형변환이 불가능합니다.

### 로컬 기본형 타입

- 로컬 기본형 변수는 메서드, 초기화 블럭 내에 존재하는 기본형 타입 변수를 칭합니다.
- 로컬 기본형 변수는 **실제 값**을 저장할 수 있어 **스택** 메모리에 저장됩니다.
- 로컬 기본형 변수는 기본값을 초기화하지 않습니다. (쓰레기 값으로 사용할 수 없음) 따라서 반드시 명시적으로 초기화해야하고, 그렇지 않으면 컴파일 에러가 발생합니다.

### 속성 기본형 타입

- 속성 기본형 타입은 static 변수, 인스턴스 변수를 칭합니다.
- 속성 기본형 변수는 힙 메모리에 저장됩니다.
- 클래스 기본형 변수는 0 혹은 0에 준하는 값이 저장됩니다. **Null**이 존재하지 않으며, **래퍼 클래스를 통해 기본형 변수를 참조하는 클래스를 만들 수 있습니다. 래퍼클래스는 객체이므로 참조변수로써 null을 참조할 수 있습니다.**

## 참조형 (Reference type)

- 기본형을 제외한 모든 타입을 칭합니다.
    - `Class` `Interface` `array` `Enumeration`...
- 객체의 주소값(해시코드값)을 저장하며 힙(Heap) 메모리에 저장됩니다.
- 항상 4byte의 같은 크기를 가지며 해당 객체의 해시코드값 혹은 null 값을 저장합니다.
- 선언 시 기본 값은 null로 초기화 됩니다.
    - null을 참조하는 레퍼런스의 객체에 접근하려하면 런타임 에러(NullPointerException)가 발생할 수 있습니다.
- **Java에서는 배열또한 참조형으로 사용하며 다중 배열은 해당 배열보다 한단계 낮은 배열의 해시코드를 참조합니다. 직접적으로 배열의 차원 크기만큼 메모리를 할당하지 않기 때문에 다중배열은 물리적으로 정렬되어 있지 않습니다.**
    - **이와 같이 Java에서는 직접적으로 다중 배열의 메모리를 할당하지 않기 때문에 배열을 참조형으로 사용합니다.**

```jsx
Person man = new Person();
/* 
new를 통해 객체를 생성하고 new의 반환값으로 생성된 객체의 해시코드를 받습니다.
그 후 그 주소를 man 참조변수에 저장하게 됩니다.
*/
```

### 참조 변수 연산

- Java에서는 기본형 타입끼리만 산술 연산 및 논리연산이 가능합니다. 레퍼런스 타입은 산술연산 및 논리연산을 할 수 없습니다.
    - 예외적으로 `==` 연산자를 통해 가르키고 있는 해시코드의 값을 비교할 수 있습니다.
    - 비교가 필요할 경우 `Object.equals` 메소드를 오버라이딩하여 원하는 값끼리 비교하게 할 수 있습니다.
        - ex) `String.equals(String str2)` ⇒ 참조 해시코드가 다르더라도 저장된 문자열 값이 같다면 `true` 반환
    - 혹은 `Comparable` 인터페이스를 구현하여 `compareTo(object O)` 메소드를 재정의하여 레퍼런스 간 비교를 할 수 있습니다.

### String Class 연산

- `String Class` 는 예외적으로 `+` 로 산술연산이 가능합니다.
- 어떤 연산식에 `String` 레퍼런스 혹은 `String` 상수가 포함된다면 해당 수식의 모든 값은 String으로 비교하여 반환됩니다.
- 기본형 변수는 true 혹은 그 값이 가르키고 있는 실제 값이 String으로 반환되어 연산됩니다.
- 참조형 변수는 해당 객체의 `toString()` 메소드를 통해 반환된 문자열로 치환됩니다.

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
