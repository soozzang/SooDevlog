---
title: "SpringBootApplication의 구조"
date: 2024-01-19
tags:
  - 독립 실행 가능한 애플리케이션 제작
  - Spring
series: "독립 실행 가능한 애플리케이션 만들기"
---
(지금까지의 코드)
```java
@Configuration
@ComponentScan
public class HellobootApplication {

    public static void main(String[] args) {
        AnnotationConfigWebApplication applicationContext = new AnnotationConfigWebApplicationContext() {
            @Override
            protected void onRefresh() {
                super.onRefresh();

                TomcatServletWebServerFactory serverFactory = new TomcatServletWebServerFactory();
                WebServer webServer = serverFactory.getWebServer(ServletContext -> {
                    servletContext.addServlet("dispatcherServlet",
                            new DispatcherServlet(applicationContext)
                    ).addMapping("/*");
                });
                webServer.start();
            }
        };
        applicationContext.register(HellobootApplication.class);
        applicationContext.refresh();
    }
}
```
지금까지 만든 오브젝트 중에서 실제 애플리케이션의 기능을 담당하는 클래스는 HelloController와 SimpleHelloService 두 개가 존재한다.

그리고, 오브젝트를 생성하는 것은 크게 TomcatServletWebServerFactory() 와 DispatcherServlet() 으로 두 가지가 존재한다.

이번엔 TomcatServletWebServerFactory() 와 DispatcherServlet()도 스프링 빈으로 등록을 하여 스프링 컨테이너가 이들을 관리하게 만들어 유연한 구성을 만들 수 있도록 할 것이다.

아래처럼 팩토리 메서드로 지정하도록 하겠다.
```java
@Configuration
@ComponentScan
public class HellobootApplication {
    @Bean
    public ServletWebServerFactory servletWebServerFactory() {
        return new TomcatServletWebServerFactory();
    }

    @Bean
    public DispatcherServlet dispatcherServlet() {
        return new DispatcherServlet();
    }

    public static void main(String[] args) {
        AnnotationConfigWebApplication applicationContext = new AnnotationConfigWebApplicationContext() {
            @Override
            protected void onRefresh() {
                super.onRefresh();
                
                // Bean 가져옴
                ServletWebServerFactory serverFactory = this.getBean(ServletWebServerFactory.class);
                DispatcherServlet dispatcherServlet = this.getBean(DispatcherServlet.class); 
                
                WebServer webServer = serverFactory.getWebServer(ServletContext -> {
                    servletContext.addServlet("dispatcherServlet", dispatcherServlet)
                        .addMapping("/*");
                });
                webServer.start();
            }
        };
        applicationContext.register(HellobootApplication.class);
        applicationContext.refresh();
    }
}
```

## SpringBootApplication
main() 메서드의 내용을 run()메서드로 추출해보자.
```java
@Configuration
@ComponentScan
public class HellobootApplication {
    @Bean
    public ServletWebServerFactory servletWebServerFactory() {
        return new TomcatServletWebServerFactory();
    }

    @Bean
    public DispatcherServlet dispatcherServlet() {
        return new DispatcherServlet();
    }

    public static void main(String[] args) {
        run(HellobootApplication.class, args);
    }

    public static void run(Class<?> applicationClass, String... args) {
        AnnotationConfigWebApplication applicationContext = new AnnotationConfigWebApplicationContext() {
            @Override
            protected void onRefresh() {
                super.onRefresh();

                ServletWebServerFactory serverFactory = this.getBean(ServletWebServerFactory.class);
                DispatcherServlet dispatcherServlet = this.getBean(DispatcherServlet.class);

                WebServer webServer = serverFactory.getWebServer(ServletContext -> {
                    servletContext.addServlet("dispatcherServlet", dispatcherServlet)
                            .addMapping("/*");
                });
                webServer.start();
            }
        };
        applicationContext.register(applicationClass);
        applicationContext.refresh();
    }
}
```

추가로, applicationContext.register()로 등록할때, applicationClass로 클래스 타입을 인자로 받게 끔 설정해 두었다.

그 후, run() 메서드를 MySpringApplication 이라는 클래스로 옮기면, 아래와 같은 모습을 보인다.
```java
@Configuration
@ComponentScan
public class HellobootApplication {
    @Bean
    public ServletWebServerFactory servletWebServerFactory() {
        return new TomcatServletWebServerFactory();
    }

    @Bean
    public DispatcherServlet dispatcherServlet() {
        return new DispatcherServlet();
    }

    public static void main(String[] args) {
        MySpringApplication.run(HellobootApplication.class, args);
    }
}
```

이렇게 보니, 스프링 부트 프로젝트를 처음 만들었을 때의 기본 코드의 main 메서드와 동일해진 것을 볼 수 있다!

이제 MySpringApplication 대신 스프링부트에 원래 들어있던 SpringApplication을 사용하자.

```java
@Configuration
@ComponentScan
public class HellobootApplication {
    @Bean
    public ServletWebServerFactory servletWebServerFactory() {
        return new TomcatServletWebServerFactory();
    }

    @Bean
    public DispatcherServlet dispatcherServlet() {
        return new DispatcherServlet();
    }

    public static void main(String[] args) {
        SpringApplication.run(HellobootApplication.class, args);
    }
}
```