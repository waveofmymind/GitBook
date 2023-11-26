---
description: String vs StringBuilder vs StringBuffer
---

# 자바의 String Poole

## String

String 클래스는 불변 객체로, 내부 구현을 보면 final을 통해 객체를 생성함을 알 수 있다.

또한 만들어진 문자열의 문자를 재배열하는 함수도 제공하지 않는다.

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. **/
    private final char value[];
    ...
}
```

그렇기 때문에 한번 생성한 문자열 값을 변경하는 것은 불가능하며, + 연산으로 문자열을 이어붙일 때에도 새로운 객체를 반환한다.

```java
String s = "hello";
System.out.println(s.hashCode());  // 99162322
s += " world";
System.out.println(s.hashCode());  // 1776255224
```

String 클래스는 동등성을 보장하기 때문에 값이 같을 경우 같은 객체로 hashcode() 값이 돌일하다.

그렇기 때문에 hashcode()의 값이 다르면 다른 참조 객체인 것이다.

이와 같이 반복적으로 문자열을 이어붙일 경우 힙에 참조를 잃은 객체들이 쌓이게 되는데, 이는 GC에 의해 수거되지만 메모리 관리 측면에서는 주의해야한다. 또한 연산 속도 측면에서 객체를 생성하기 때문에 성능이 떨어진다.

## String Pool

<figure><img src="https://camo.githubusercontent.com/6694a44e803b389ff8583afb23e0443debc16ec3ad6f168273d1de4f8cc9eabb/68747470733a2f2f7777772e6e6f74696f6e2e736f2f696d6167652f687474707325334125324625324673332d75732d776573742d322e616d617a6f6e6177732e636f6d2532467365637572652e6e6f74696f6e2d7374617469632e636f6d25324661636338393534342d633238382d343139362d383938612d626636383933623439356261253246556e7469746c65642e706e673f7461626c653d626c6f636b2669643d37643534386138392d616366612d343962342d393963652d39363266346430336632373326737061636549643d62343533626438352d636231352d343462352d626632652d3538306165646138303734652677696474683d32303030267573657249643d38303335326331322d363561342d343536322d396133362d3231373965643064666666622663616368653d7632" alt=""><figcaption></figcaption></figure>

<mark style="color:blue;">=</mark> 연산자로 String 객체에 값을 넣으면, 힙 내의 String Pool에서 관리된다.

그리고 마찬가지로 객체를 new로 생성할 경우 일반적으로 새로운 객체를 생성하는 것이기 때문에 String Pool의 관리에서 벗어나 별도의 힙 영역에 객체가 저장된다.

둘다 힙에 저장되는데 전자의 경우에는 더 효율적이다.

String literal 방식이라고 하는데, String Pool의 메모리 주소를 참조하게 되고, 같은 메모리 주소를 가르키기 때문에 하나의 메모리를 재사용할 수 있다.

```java
String a = "Cat";
String b = "Cat";
String c = new String("Cat");
System.out.println(a == b);  // true
System.out.println(a == c);  // false
```

a와 b는 String literal 방식으로 String Pool의 "Cat"이라는 값을 가르키는 메모리 주소를 공유한다.

그렇기 때문에 동일한 객체이다.

그러나 new로 생성한 "Cat"은 String Pool에 "Cat"이 존재하더라도 별도의 객체가 되는 것이다.

그리고 String Pool을 이용하기 위해, String이 가변 객체가 되어서는 안된다.

만약 String이 가변 객체라면, String a = "Cat", String b = "Cat" 에서 a = "Dog"로 변경할 경우, b 객체도 "Dog"로 변한다.(메모리 값이 같기 때문이다.) 즉, String Pool의 재사용성을 위한 것이라고도 할 수 있다.

## String Builder와 String Buffer

그렇다면 문자열을 이어붙이거나 많이 연산할 때는 어떻게 해야할까? 앞전에서는 String에 문자열을 이어붙이는 것은 메모리 낭비라고 말했다.

그래서 자바에서는 StringBuilder와 StringBuffer로, 내부에 가변 객체로 두어 변경에 용이하게 지원하고 있다.

### String Builder

StringBuilder는 내부에 char\[] 배열을 둔다.

```java
abstract class AbstractStringBuilder implements Appendable, CharSequence {
​
    /** The value is used for character storage **/
    char[] value;
    ...
}
```

그리고 문자를 이어붙일 때에는 append() 메서드로 할 수 있는데, char\[] 배열의 길이를 늘리고 해당 배열에 인자로 받은 문자열을 더하는 식이다.

만약, char\[] 배열에 인자로 받은 문자열 길이만큼의 공간 여유가 있으면 길이는 늘리지 않는다.

`append()` 함수를 한번 보자.

```java
public AbstractStringBuilder append(String str) {
        if (str == null) str = "null";
        int len = str.length();
        ensureCapacityInternal(count + len);
        str.getChars(0, len, value, count);
        count += len;
        return this;
}
    
// (기존 문자열 길이 + 새로 들어 갈 문자열 길이)와 현재 value 길이를 비교
private void ensureCapacityInternal(int minimumCapacity) {
    // overflow-conscious code
    if (minimumCapacity - value.length > 0)
        expandCapacity(minimumCapacity);
}
​
void expandCapacity(int minimumCapacity) {
    int newCapacity = value.length * 2 + 2;
    if (newCapacity - minimumCapacity < 0)
        newCapacity = minimumCapacity;
    if (newCapacity < 0) {
        if (minimumCapacity < 0) // overflow
            throw new OutOfMemoryError();
        newCapacity = Integer.MAX_VALUE;
    }
    value = Arrays.copyOf(value, newCapacity);
}
```

그리고 이어붙여서 생성한 새로운 객체는 StringBuilder 내의 배열에 값을 변경한 것이지, StringBuilder를 새로 생성한 것이 아니기 때문에 같은 메모리 주소를 가진다.

```java
StringBuilder s = new StringBuilder("hello");
System.out.println(s.hashCode());  // 859417998
s.append("world");
System.out.println(s.hashCode());  // 859417998
```

### StringBuffer

StringBuffer는 StringBuilder에서 synchronized가 적용되어 동기화를 지원한다. 특히 append() 메서드를 보자.

```java
@Override
public synchronized StringBuffer append(CharSequence s) {
    toStringCache = null;
    super.append(s);
    return this;
}
```

\
