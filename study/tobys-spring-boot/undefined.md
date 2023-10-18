# 스프링 컨테이너

## Application Context

Front Controller 패턴에서, 직접 HelloController 객체를 생성해서 사용했다.

이렇게 직접 객체를 생성해서 사용하는 것보다, 스프링은 더 다양한 일을 해준다.

스프링 컨테이너는 ApplicationContext라고 하며, 직접 프로그래머가 객체를 생성하지 않아도 특정 메타 정보를 넘겨주면, 해당 메타 정보가 있는 클래스를 빈으로 등록한다.

```java
public static void main(String[] args) {
        GenericApplicationContext applicationContext = new GenericApplicationContext();
        applicationContext.registerBean(HelloController.class); // 클래스 정보를 넘긴다.
        applicationContext.refresh();
        
        // 사용할 때
        HelloController bean = applicationContext.getBean(HelloController.class);
}
```

위처럼 클래스 정보 자체를 넘겨서, 실제 HelloController 객체를 빈으로 등록해준다.

사용할 때에는 getBean() 메서드로 특정 빈을 사용할 수 있다.

이제, Front Controller는 스프링 컨테이너가 생성한 빈을 사용할 수 있다.

<figure><img src="../../.gitbook/assets/스크린샷 2023-10-18 오후 2.51.31.png" alt=""><figcaption></figcaption></figure>

위처럼 스프링 컨테이너를 사용해서 객체를 관리하는 것은 다음과 같은 장점을 가진다.

1. 객체의 생성 주기를 스프링 컨테이너가 관리해준다.
2. 요청마다 객체를 생성하지 않고, 만들어둔 객체를 반환한다.(싱글턴 패턴)

## 의존 오브젝트 추가

그리고 이제 Hello Controller는 단지 유저의 요청을 검증하고, 반환하는 역할을 하도록 책임을 분리한다.

HelloController에서 name에 "Hello "를 붙이지 않고, SimpleHelloService가 비즈니스 로직을 처리하도록 한다.

```java
public class SimpleHelloService {

    String sayHello(String name) {
        return "Hello " + name;
    }
}

public class HelloController {

    public String hello(String name) {
        SimpleHelloService helloService = new SimpleHelloService();
        return helloService.sayHello(Objects.requireNonNull(name));
    }
}
```

## Dependency Injection

스프링하면 떠오르는 핵심 개념

스프링 컨테이너를 Spring IoC/DI Container라고도 한다.

위에서 생성했던 두 클래스에 대해서 아래와 같은 의존성을 가진다.

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

의존이란, HelloController는 SimpleHelloService의 변경에 영향을 받는다는 것을 의미한다.

만약 HelloController에서 SimpleHelloService가 아닌, ComplexHelloService를 사용하고자 할 경우, 코드의 변경은 불가피하다.

```java
public class HelloController {

    public String hello(String name) {
        ComplexHelloService helloService = new ComplexHelloService();
        return helloService.sayHello(Objects.requireNonNull(name));
    }
}
```

미래에도 사용하고 싶은 클래스가 추가되면 HelloController도 같이 수정해야하지만, 자바에서는 다음과 같이 인터페이스를 사용할 수 있다.

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

이렇게 인터페이스를 의존함으로써 구현 클래스에 대해서는 HelloController에서 변경의 영향을 받지 않는다.

그러나 이는 런타임시 HelloControllr는 어떤 HelloService의 구현체를 사용할지 모르기 때문에, 이를 정해주어야한다.

이처럼 런타임시 어떤 클래스를 사용할 지 정해주는 것을 DI라고 하며, 이를 스프링 컨테이너가 대신 해주는 것이다.

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

특정 클래스가 의존하는 클래스를 사용할 수 있도록 메타정보를 통해 주입해준다.

이때 SimpleHelloService를 Bean으로 등록해야한다.

그리고 HelloController를 생성자로 생성할 때, 생성자 파라미터를 통해 SimpleHelloService를 넘겨준다.

코드로는 이제 다음과 같이 변경한다.

```java
public static void main(String[] args) {
        GenericApplicationContext applicationContext = new GenericApplicationContext();
        applicationContext.registerBean(HelloController.class);
        applicationContext.registerBean(SimpleHelloService.class);
        applicationContext.refresh();
        // ...
}
```

이제 스프링 컨테이너는 HelloController를 생성해서 빈으로 등록하게 되는데, 이 때 HelloService를 생성자에 파라미터로 넘겨주어야한다.

이때 HelloService 인터페이스를 구현한 클래스가 Bean으로 있는지 확인하게 된다.
