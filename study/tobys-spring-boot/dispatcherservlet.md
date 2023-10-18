# DispatcherServlet

이전에 Bean 객체를 직접 생성해서 서블릿에서 사용했던 것을, 스프링 컨테이너인 ApplicationContext를 정의함으로써 싱글턴 패턴으로 빈을 사용하고, 의존하는 객체의 경우 생성자를 통해 객체를 생성할 수 있도록 했다.

```java
public class HellobootApplication {

    public static void main(String[] args) {
        GenericApplicationContext applicationContext = new GenericApplicationContext();
        applicationContext.registerBean(HelloController.class);
        applicationContext.registerBean(SimpleHelloService.class);
        applicationContext.refresh();

        ServletWebServerFactory serverFactory = new TomcatServletWebServerFactory();
        WebServer webServer = serverFactory.getWebServer(servletContext -> {
            servletContext.addServlet("frontcontroller", new HttpServlet() {
                @Override
                public void service(HttpServletRequest req, HttpServletResponse resp) throws IOException {
                    // 인증, 인가, 다국어 처리 등의 공통 기능
                    if (req.getRequestURI().equals("/hello") && req.getMethod().equals(HttpMethod.GET.name())) {
                        String name = req.getParameter("name");

                        HelloController helloController = applicationContext.getBean(HelloController.class);
                        String ret = helloController.hello(name);

                        resp.setContentType(MediaType.TEXT_PLAIN_VALUE);
                        resp.getWriter().println(ret);
                    } else {
                        resp.setStatus(HttpStatus.NOT_FOUND.value());
                    }
                }
            }).addMapping("/*");
        });
        webServer.start();
    }
}
```

그러나 아직, 서블릿 내에 불편한 점이 있는데, 바로 매핑 정보이다.

HTTP Method와 URL의 path 정보로 필터링 하여, HelloController의 hello() 메서드로 로직을 처리하는 것이 하드 코딩 되어 있다.

또한 Parameter를 직접 꺼내어 HelloController의 메서드 파라미터로 바인딩하는 작업까지.

이를 개선해보고자 한다.

## DispatcherServlet

DisipatcherServlet이 들어온 요청을 받아 적절한 path와 HTTP Method를 수행할 로직을 ApplicationContext에서 찾게 만든다.

```java
public class HellobootApplication {

    public static void main(String[] args) {
        GenericWebApplicationContext applicationContext = new GenericWebApplicationContext();
        applicationContext.registerBean(HelloController.class);
        applicationContext.registerBean(SimpleHelloService.class);
        applicationContext.refresh();

        ServletWebServerFactory serverFactory = new TomcatServletWebServerFactory();
        WebServer webServer = serverFactory.getWebServer(servletContext -> {
            servletContext.addServlet("dispatcherServlet",
                    new DispatcherServlet(applicationContext)
            ).addMapping("/*");
        });
        webServer.start();
    }
}
```

기존의 GenericApplicationContext보다 Web과 관련된 기능이 추가된 GenericWebApplicationContext를 파라미터로 넘겨주어야 한다.

여기에 HelloController가 어떤 path와 HTTP Method를 처리할 지를 정의해주어야한다.

## 어노테이션

HelloController의 특정 메서드가 어떤 HTTP Method와 path를 수행할 지 결정해주기 위해서 어노테이션을 사용할 수 있다.

<pre class="language-java"><code class="lang-java"><strong>@RequestMapping
</strong><strong>public class HelloController {
</strong>    private final HelloService helloService;

    public HelloController(HelloService helloService) {
        this.helloService = helloService;
    }

    @GetMapping("/hello")
    public String hello(String name) {
        return helloService.sayHello(Objects.requireNonNull(name));
    }
}
</code></pre>

@RequestMapping에서 HTTP Method를 추가한 @GetMapping 어노테이션을 사용하면, path가 "/hello"이면서 HTTP Method가 GET인 url에 대한 웹 요청을 처리할 수 있다.



이제 DispatcherServlet은 웹 요청이 들어오면 ApplicationContext에서 들어온 요청의 path와 HTTP Method를 적절히 수행할 수 있는 매핑 정보를 가지고 있는 클래스를 찾는다.

이때 등록된 Bean이 많을 경우 탐색 속도가 오래 걸리기 때문에, 클래스 레벨에도 매핑 정보 어노테이션을 붙여준다.

**그러나 이대로 서버를 실행하고, http://localhost:8080/hello로 접속해보면 404 에러가 발생한다.**

@GetMapping("/hello")를 통해 매핑이 되었지만, 해당 메서드의 반환값이 String인 것이 문제이다.

DispatcherServlet은 반환 값을 그대로 노출하는 것이 아니라, 반환 값에 대한 HTML 템플릿을 찾는다.

즉,위 로직에서 Hello Spring이라는 문자열을 helloService.sayHello()부터 받으면, Hello String.html이라는 HTML 템플릿을 찾는 것인데, 없어서 404 에러가 발생하는 것이다.

이를 뷰 이름이 아닌, 반환된 String 값을 바디에 TEXT/PLAIN으로 넣으려면 @ResponseBody 어노테이션을 메서드에 추가한다.

```java
@RequestMapping
public class HelloController {
    private final HelloService helloService;

    public HelloController(HelloService helloService) {
        this.helloService = helloService;
    }

    @GetMapping("/hello")
    @ResponseBody
    public String hello(String name) {
        return helloService.sayHello(Objects.requireNonNull(name));
    }
}
```

이를 메서드별로 붙이지 않고, 클래스 단위에 **@RestController를 붙이면, 해당 어노테이션이 붙은 클래스의 메서드는 @ResponseBody가 붙어 있는 것으로 판단하게 된다.**

## 스프링 컨테이너로 통합

현재 psvm 메인 메서드에서는 크게 두가지, 스프링 컨테이너 초기화와 서블릿 컨테이너 초기화 작업들이 존재한다.

```java
public class HellobootApplication {

    public static void main(String[] args) {
        GenericWebApplicationContext applicationContext = new GenericWebApplicationContext();
        applicationContext.registerBean(HelloController.class);
        applicationContext.registerBean(SimpleHelloService.class);
        applicationContext.refresh(); // 스프링 컨테이너 초기화

        ServletWebServerFactory serverFactory = new TomcatServletWebServerFactory();
        WebServer webServer = serverFactory.getWebServer(servletContext -> {
            servletContext.addServlet("dispatcherServlet",
                    new DispatcherServlet(applicationContext)
            ).addMapping("/*");
        });
        webServer.start(); // 서블릿 컨테이너 초기화
    }
}
```

이제 Servlet Container를 초기화하고 DispatcherServlet을 등록하는 과정을 스프링 컨테이너로 통합해보자.

applicationContext.refresh()를 통해 스프링 컨테이너를 초기화하는데, refresh() 메서드는 템플릿 메서드 패턴으로 구현되어있다.

특히 가장 중요한 메서드는 onRefresh()이다.

<pre class="language-java"><code class="lang-java">@Override
public void refresh() throws BeansException, IllegalStateException {
	synchronized (this.startupShutdownMonitor) {
	    StartupStep contextRefresh = this.applicationStartup.start("spring.context.refresh");

	    // Prepare this context for refreshing.
	    prepareRefresh();

	    // Initialize other special beans in specific context subclasses.
<strong>	    onRefresh();
</strong>	}
</code></pre>

이를 상속해서 DispatcherServlet을 생성해서 서블릿으로 등록하는 로직을 추가해보면,

```java
public class HellobootApplication {

    public static void main(String[] args) {
        GenericWebApplicationContext applicationContext = new GenericWebApplicationContext() {
            @Override
            protected void onRefresh() {
                super.onRefresh();

                ServletWebServerFactory serverFactory = new TomcatServletWebServerFactory();
                WebServer webServer = serverFactory.getWebServer(servletContext -> {
                    servletContext.addServlet("dispatcherServlet",
                            new DispatcherServlet(this)
                    ).addMapping("/*");
                });
                webServer.start();
            }
        };
        applicationContext.registerBean(HelloController.class);
        applicationContext.registerBean(SimpleHelloService.class);
        applicationContext.refresh();
    }
}
```

이제 위와 같이 onRefresh() 메서드에 추가되어 간단해졌다.

super.onRefresh()의 경우, 스프링이 훅 메서드를 통해 별도의 과정을 붙여놓았기 때문에 삭제하면 안된다.
