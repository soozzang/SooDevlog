---
title: "Mockito"
date: 2024-01-15
tags:
  - Java
  - TDD
  - 대역
  - Mockito
series: "테스트 주도 개발하기를 학습하며 정리한 내용"
---

## Mockito 기초 사용법

Mockito는 모의 객체 생성, 검증, 스텁을 지원하는 프레임워크이다. Mockito의 기본 사용법에 대해 알아보자.

## 모의 객체 선정

`Mockito.mock()` 메서드를 이용하면 특정 타입의 모의 객체를 생성할 수 있다.

```java
import static org.mockito.Mockito.mock;

public class GameGenMockTest {
    @Test
    void mockTest() {
        GameNumGen genMock = mock(GameNumGen.class);
    }
}
```

`Mockito.mock()` 메서드는 클래스, 인터페이스, 추상 클래스에 대해 모의 객체를 생성할 수 있다.    
    
이 예제에서 GameNumGen은 다음과 같은 인터페이스라고 가정한다.
```java
public interface GameNumGen {
    String generate(GameLevel level);
}
```

## 스텁 설정
모의 객체를 생성한 뒤에는 `BDDMockito` 클래스를 이용해서 모의 객체에 스텁을 구성할 수 있다.     
다음과 같이 `BDDMockito.given()` 메서드를 이용하면 모의 객체의 메서드가 특정 값을 리턴하도록 설정할 수 있다.

```java
import static org.mockito.BDDMockito.given;
//...

public class GameGenMockTest{
    @Test
    void mockTest() {
        GameNumGen genMock = mock(GameNumGen.class);
        given(genMock.generate(GameLevel.EASY)).willReturn("123");

        String num = genMock.generate(GameLevel.EASY);
        assertEquals("123", num);
    }
} 
```

이 코드에서 BDDMockito.given() 메서드는 `스텁을 정의`할 `모의 객체`의 메서드 호출을 전달한다.    
    
willReturn() 메서드는 `스텁을 정의한 메서드가 리턴할 값`을 지정한다.    
     
즉, 8행 코드는 genMock.generate(GameLevel.EASY) 메서드가 불리면 "123"을 리턴하라고 설정한다.

그래서 10행에서 num에는 "123"이 담기게된다.

Mockito를 이용한 모의 객체 생성과 스텁 설정과정만 따로 보면 아래와 같다.
```java
public class GameGenMockTest {
    @Test
    void mockTest() {
        // 모의 객체 설정
        GameNumGen genMock = mock(GameNumGen.class);

        // 스텁 설정
        given(genMock.generate(GameLevel.EASY)).willReturn("123");

        // 스텁 설정에 매칭되는 메서드 실행
        String num = genMock.generate(GameLevel.EASY);
    }
}
```

지정한 값을 리턴하는 대신에 익셉션을 발생하게 설정할 수도 있다.     
willReturn() 대신 `willThrow()` 메서드를 사용하면 된다.

```java
public class GameGenMockTest {
    @Test
    void mockThrowTest() {
        GameNumGen genMock = mock(GameNumGen.class);
        given(genMock.generate(null)).willThrow(IllegalArgumentException.class);
        
        assertThrows(
                IllegalArgumentException.class,
                () -> genMock.generate(null));
    }
}
```

> Mockito는 일치하는 스텁 설정이 없을 경우 리턴 타입의 기본 값을 리턴한다. 예를 들어 리턴 타입이 int이면 0을 리턴하고 boolean이면 false를 리턴한다. String이나 List같은 참조 타입이면 null을 리턴한다.

## 인자 매칭 처리
org.mockito.ArgumentMatchers 클래스를 사용하면 정확하게 일치하는 값 대신 임의의 값에 일치하도록 설정할 수 있다.

```java
import static org.mockito.ArgumentMatchers.any;

public class AnyMatcherTest {
    @Test
    void anyMatchTest() {
        GameNumGen genMock = mock(GameNumGen.class);
        given(genMock.generate(any())).willReturn("456");

        String num = genMock.generate(GameLevel.EASY);
        assertEquals("456", num);

        String num2 = genMock.generate(GameLevel.NORMAL);
        assertEquals("456", num2);
    }
}
```

7행에서 스텁을 설정할 때 인자에 ArgumentMatchers.any() 메서드를 전달했다. 이 메서드를 사용하면 모든 값에 일치하도록 스텁을 설정한다. 따라서 9행과 12행의 generate()는 모두 "456"을 리턴한다. 

> Mockito 클래스와 BDDMockito 클래스는 ArgumentMatchers 클래스를 상속하고 있으므로 ArgumentMatchers.any() 대신에 Mockito.any()나 BDDMockito.any()를 사용해도 된다.

ArgumentMatchers 클래스는 any() 외에도 다음의 메서드를 제공한다.

- anyInt(), anyLong(), anyChar() ... : 기본 데이터 타입에 대한 임의 값 일치
- anyString() : 문자열에 대한 임의 값 일치
- any() : 임의 타입에 대한 일치
- anyList(), anySet(), anyMap(), anyCollection() : 임의 콜렉션에 대한 일치
- matches(String), matches(Pattern) : 정규 표현식을 이용한 String값 일치 여부
- eq(값) : 특정 값과 일치 여부

스텁을 설정할 메서드의 인자가 두 개 이상인 경우, ArgumentMatchers의 anyInt()나 any()등의 메서드는 내부적으로 인자의 일치 여부를 판단하기 위해 ArgumentMatcher를 등록한다. Mockito는 한 인자라도 ArgumentMatcher를 사용해서 설정한 경우, 모든 인자를 ArgumentMatcher를 이용해서 설정해야 한다.      
    
따라서 임의의 값과 일치하는 인자와 정확하게 일치하는 인자를 함께 사용하고 싶다면 아래와 같이 사용해야 한다. (eq(값) 사용)
```java
given(mockList.set(anyInt(), eq("123"))).willReturn("456");
```

## 행위 검증
모의 객체으 ㅣ역할 중 하나는 실제로 모의 객체가 `불렸는지` 검증하는 것이다. 아래는 모의 객체의 특정 메서드가 불렸는지 확인하는 코드이다.

```java
import static org.mockito.BDDMockito.then;

public class GameTest {
    @Test
    void init() {
        GameNumGen genMock = mock(GameNumGen.class);
        Game game = new Game(genMock);
        game.init(GameLevel.EASY);

        then(genMock).should().generate(GameLevel.EASY);
    }
}
```
10행에서 genMock 모의 객체의 generate()메서드가 GameLevel.EASY `인자를 사용`해서 `호출되었는지` 검증한다.
    
정확한 값이 아니라 메서드가 불렸는지 여부가 중요하다면 any(), anyInt()등을 사용해서 인자를 지정하면 된다. 아래 예시를 보자

```java
then(genMock).should().generate(any());
```

정확하게 한 번만 호출된 것을 검증하고 싶다면 should() 메서드에 Mockito.only()를 인자로 전달한다.

```java
then(genMock).should(only()).generate(any());
```

메서드 호출 횟수를 검증하기 위해 Mockito 클래스가 제공하는 메서드는 다음과 같다.

- only() : 한 번만 호출
- times(int) : 지정한 횟수만큼 호출
- never() : 호출되지 않음
- atLeast(int) : 적어도 지정한 횟수만큼 호출
- atLeastOnce : atLeast(1)과 동일
- atMost(int): 최대 지정한 횟수만큼 호출

## 인자 캡쳐
단위 테스트를 실행하다보면 모의 객체를 호출할 때 `사용한 인자`를 검증해야 할 때가 있다. 
    
String이나 int 같은 타입은 쉽게 검증할 수 있지만 많은 속성을 가진 객체는 쉽게 검증하기 어렵다. 이럴 때 인자 캡쳐를 사용한다.   
    
Mockito의 ArgumentCaptor를 사용하면 메서드 호출 여부를 검증하는 과정에서 실제 호출할 때 전달한 인자를 보관할 수 있다. 사용법은 아래와 같다
```java
import org.mockito.ArgumentCaptor;

public class UserRegisterMockTest {
    private UserRegister userRegister;
    private EmailNotifier mockEmailNotifier = mock(Email.Notifier.class);

    @DisplayName("가입하면 메일을 전송함")
    @Test
    void whenRegisterThenSendMail() {
        userRegister.register("id", "pw", "email@email.com");

        ArgumentCaptor<String> captor = ArgumentCaptor.forClass(String.class);
        then(mockEmailNotifier)
                .should().sendRegisterEmail(captor.capture());

        String realEmail = captor.getValue();
        assertEquals("email@email.com", realEmail);
    }
}
```
12행에서 String 타입의 인자를 보관할 수 있는 ArgumentCaptor를 생성한다. 그 후, 14행에서 처럼 capture() 메서드를 사용해 인자를 캡쳐해 가져올 수 있다.





