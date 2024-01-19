---
title: "DispatcherServlet"
date: 2024-01-16
tags:
  - Spring
  - DispatcherServlet
series: "독립 실행 가능한 애플리케이션 만들기"
---
우리는 지금 프론트 컨트롤러라는 서블릿을 만든 상태이다. 목표는 서블릿 컨테이너를 관리하지 않는 Containerless의 형태로 구축하고 싶은 것이다.

일단 지금까지의 코드는 이러하다.

```java
public class HellobootApplication {

    public static void main(String[] args) {
        GenericApplicationContext applicationContext = new GenericApplicationContext();
        applicationContext.registerBean(HelloController.class); // 어떤 클래스로 빈을 만들 것인가
        applicationContext.registerBean(SimpleHelloService.class); // 어떤 클래스로 빈을 만들 것인가
        applicationContext.refresh(); // 초기화
        
        TomcatServletWebServerFactory serverFactory = new TomcatServletWebServerFactory();
        WebServer webServer = serverFactory.getWebServer(ServletContext -> {

            servletContext.addServlet("frontcontroller", new HttpServlet() {
                @Override
                protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
                    // 인증, 보안, 다국어, 공통 기능 처리 (프론트 컨트롤러)
                    if (req.getRequestURI().equals("/hello")) { // "/hello"에 대한 매핑
                        String name = req.getParameter("name");

                        HelloController helloController = applicationContext.getBean(HelloController.class); // 스프링 컨테이너에서 helloController 가져옴
                        String ret = helloController.hello(name); // 컨트롤러의 메서드 호출

                        resp.setContentType(MediaType.TEXT_PLAIN_VALUE);
                        resp.getWriter().println(ret); // 컨트롤러의 리턴값을 응답에 사용
                    } else { // 404
                        resp.setStatus(HttpStatus.NOT_FOUND.value());
                    }

                }
            }).addMapping("/*"); // 프론트 컨트롤러는 중앙에서 모든 매핑을 받아야 함.
        });
        webServer.start();
    }
}
```
지금 코드에서는 애플리케이션의 로직과 긴밀하게 연결되있는 로직들이 서블릿 코드안에 등장하고 있는 것이 문제상황이다.

첫 번째는 매핑이다.
매핑은 웹 요청을 갖고 이것을 처리할 컨트롤러, 컨트롤러 메서드가 어떤 것인가를 연결시켜주는 작업이다.
ex) "/hello"로 요청이 들어오면 HelloController의 hello 메서드가 담당하게 하고 있음
이러한 매핑 작업이 하드코딩되어 들어가 있다.

두 번쨰는 바인딩이다.
요청의 파라미터값을 추출해서 hello 메서드의 파라미터로 넘겨주는 작업이다.
hello라는 메서드의 첫 번째 파라미터가 name 이라고 코드로 직접 지정을 하고 있다.

이런 과정들을 서블릿이 담당하지 않게 되어야 Containerless 애플리케이션이라고 말할 수 있다.

이제 Spring으로 위 문제들을 해결해보자.

## DispatcherServlet으로 전환
DispatcherServlet은 프론트 컨트롤러가 담당하는 많은 기능들을 맡아주는 서블릿 클래스이다.

```java
public class HellobootApplication {

    public static void main(String[] args) {
        GenericWebApplicationContext applicationContext = new GenericWebApplicationContext(); //WebApplicationContext로 전환
        applicationContext.registerBean(HelloController.class);
        applicationContext.registerBean(SimpleHelloService.class); 
        applicationContext.refresh(); 
        
        TomcatServletWebServerFactory serverFactory = new TomcatServletWebServerFactory();
        WebServer webServer = serverFactory.getWebServer(ServletContext -> {
            servletContext.addServlet("dispatcherServlet",
                    new DispatcherServlet(applicationContext)
                ).addMapping("/*"); 
        });
        webServer.start();
    }
}
```

초기에 스프링은 xml에 어떤 경로로 들어오면 어떤 Bean이 처리해달라고 직접 지정을 해줘야 했다.
가장 각광을 받았던 방식은 매핑 정보를 서블릿에 코드로 직접 넣는 방법(우리가 지금까지 했던 방법)대신에, 그 요청을 처리할 컨트롤러 클래스 안에 매핑정보를 집어넣는 방식이다.
그 방법은 애노테이션을 넣는 방법이다.

## 애노테이션 매핑 정보 사용
애노테이션을 사용하여 HelloController를 수정하자.

```java
@RequestMapping("/hello")
public class HelloController {
    private final HelloService helloService;

    public HelloController(HelloService helloService) {
        this.helloService = helloService;
    }
    
    @GetMapping
    @ResponseBody
    public String hello(String name) {
        return helloService.sayHello(Objects.requireNonNull(name));
    }
}
```
그럼 여기서 애노테이션을 가지고 DispatcherServlet은 무엇을 담당하는 것일까?
- 서블릿 컨테이너인 ApplicationContext를 생성자로 받은 상태에서,
- Bean을 다 뒤진다.
- 이 중에서 웹 요청을 처리할 수 있는 매핑정보를 가지고 있는 클래스를 찾는다. (ex. GetMapping)
  - 이 클래스는 웹 요청을 처리할 수 있도록 만들어진 웹 컨트롤러라는 것을 인지함.
- 여기서 웹 요청 정보들을 추출해냄 ("/hello", GET -> hello() 메서드) 
- 추출한 정보들로 매핑에 사용할 매핑 테이블을 만들어 둔다.
- 그 이후에 요청이 들어오면 매핑 테이블을 참고하여 해당 요청을 담당할 Bean 오브젝트와 메서드를 확인한다.

Dispatcher Servlet을 도입한 후에 main메서드가 아래와 같이 엄청나게 간략해졌다.
```java
public class HellobootApplication {

    public static void main(String[] args) {
        GenericWebApplicationContext applicationContext = new GenericWebApplicationContext(); //WebApplicationContext로 전환
        applicationContext.registerBean(HelloController.class);
        applicationContext.registerBean(SimpleHelloService.class); 
        applicationContext.refresh(); 
        
        TomcatServletWebServerFactory serverFactory = new TomcatServletWebServerFactory();
        WebServer webServer = serverFactory.getWebServer(ServletContext -> {
            servletContext.addServlet("dispatcherServlet",
                    new DispatcherServlet(applicationContext)
                ).addMapping("/*"); 
        });
        webServer.start();
    }
}
```

main 에서는 먼저 스프링 컨테이너를 만들고, 서블릿 컨테이너를 코드로 실행하면서 서블릿을 등록하고 있다.

다음엔 이 두가지의 분리된 작업을 하나의 통합된 작업으로 만들어보겠다.