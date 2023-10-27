# Front Controller

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

`Servlet`은 요청 path별로 생성해야한다는 번거로움이 있었고, 각 Servlet이 요청을 처리할 때 사용되는 코드가 대부분 중복이였다.

이를 개선하고자, `Front Controller`가 도입되었다.

모든 `Servlet`에 공통적으로 필요한 코드를 중앙화된 Controller에서 처리하고, 요청의 path에 맞는 객체에게 위임하기 위함이다. (ex. 인증/인가, 다국어 처리 등)

## 구현

```java
public static void main(String[] args) {
        ServletWebServerFactory serverFactory = new TomcatServletWebServerFactory();
        WebServer webServer = serverFactory.getWebServer(servletContext -> {
            servletContext.addServlet("frontcontroller", new HttpServlet() {
                @Override
                public void service(HttpServletRequest req, HttpServletResponse resp) throws IOException {
                    // 인증, 인가, 다국어 처리 등의 공통 기능
                    if (req.getRequestURI().equals("/hello") && req.getMethod().equals(HttpMethod.GET.name())) {
                        String name = req.getParameter("name");
                        resp.setStatus(HttpStatus.OK.value());
                        resp.setHeader(HttpHeaders.CONTENT_TYPE, MediaType.TEXT_PLAIN_VALUE);
                        resp.getWriter().println("Hello " + name);
                    } else if (req.getRequestURI().equals("/user")) {
                        // 유저 로직
                    } else {
                        resp.setStatus(HttpStatus.NOT_FOUND.value());
                    }
                }
            }).addMapping("/*");
        });
        webServer.start();
    }
```

즉 위와 같다.

특정 서블릿을 모든 Path에 대해 매핑시킨 후, if문으로 Path와 HTTP Method 별로 요청을 수행한다.

그러나 Path가 늘어날수록 위 코드가 변경되어야 하는 것이 단점일 것이다.

그렇다면 별도의 클래스를 생성해서, 컴포지션을 활용해보자.

```java
public class HelloController {

    public String hello(String name) {
        return "Hello " + name;
    }
}

public static void main(String[] args) {
        ServletWebServerFactory serverFactory = new TomcatServletWebServerFactory();
        WebServer webServer = serverFactory.getWebServer(servletContext -> {
            HelloController helloController = new HelloController();
            servletContext.addServlet("frontcontroller", new HttpServlet() {
                @Override
                public void service(HttpServletRequest req, HttpServletResponse resp) throws IOException {
                    // 인증, 인가, 다국어 처리 등의 공통 기능
                    if (req.getRequestURI().equals("/hello") && req.getMethod().equals(HttpMethod.GET.name())) {
                        String name = req.getParameter("name");

                        String ret = helloController.hello(name);

                        resp.setStatus(HttpStatus.OK.value());
                        resp.setHeader(HttpHeaders.CONTENT_TYPE, MediaType.TEXT_PLAIN_VALUE);
                        resp.getWriter().println(ret);
                   // ... 이전과 같으므로 생략
            }).addMapping("/*");
        });
        webServer.start();
    }
```

HelloController를 생성하고, 서블릿이 초기화 될 때 객체를 생성해두면, 요청마다 객체가 생성되지 않고 서블릿의 생명주기에는 인스턴스가 1개임이 보장된다.

이제 Path가 `"/hello"`이고 HTTP 메서드가 `GET`이면, HelloController가 로직을 수행하고 결과를 응답하게 되었다.

&#x20;이를 **바인딩**이라고 한다.
