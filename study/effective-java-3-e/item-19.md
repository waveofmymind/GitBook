---
description: 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라.
---

# Item 19

**상속용 클래스는 재정의할 수 있는 메서드들을 내부적으로 문서로 남겨야한다.**

{% hint style="warning" %}
재정의 가능이란, public과 protected 메서드 중 final이 아닌 모든 메서드이다.
{% endhint %}

그러나 이러한 방법은 상속에 의한 설계의 일부가 아님을 유의하자.

**클래스의 내부 동작 과정 중간에 끼어들 수 있는 훅(hook)을 잘 선별하여 protected 메서드 형태로 공개해야 할 수도 있다.**

설계할 때 어떤 메서드를 protected로 노출할 지 정하는 것은 하위 클래스를 만들어 시험해보아야 한다.

그리고 다음으로 상속을 허용하는 클래스가 지켜야 할 제약이  몇 개 있다.

### 상속용 클래스의 생성자는 직접적으로든 간접적으로든 재정의 가능 메서드를 호출해서는 안된다.

상위 클래스의 생성자가 하위 클래스의 생성자보다 먼저 실행되므로 하위 클래스에서 재정의한 메서드가 하위 클래스의 생성자보다 먼저 호출된다.

아래 예시를 보자

```java
public class Car {
    public Car() {
        engineStart();
    }
    
    public void engineStart() {
    }
}

public final class Porsche extends Car {
    private final Instant instant; // 생성자에서 초기화될 필드
    
    Porsche() {
        instance = Instant.now();
    }
    
    @Override public void engineStart() {
        System.out.println(instant):
    }
    
    public static void main(String[] args) {
        Porsche porsche = new Porsche();
        porsche.engineStart();
    }
}
```

engineStart() 함수가 Car 클래스의 생성자에서도 호출되기 때문에 instant가 두 번 호출될 것이라고 기대하지만,

첫번째의 경우 null을 출력한다.

**상위 클래스의 생성자가 하위 클래스의 재정의된 engineStart()를 호출**하기 때문이다.

마찬가지로, Clonable과 Serializable 인터페이스를 상속한 클래스를 다시 상속할 수 있게 하는 것도 조심해야한다.

하게된다면 각 인터페이스의 재정의된 메서드를 생성자에서 재정의하지 않도록 해야한다.

정리하면, 클래스 내부에서는 재정의 가능 메서드를 사용하지 않도록 하고, 이를 꼭 설명을 붙여 문서화하자.

