---
title: "TDD시작"
date: 2023-12-01
tags:
  - Java
  - TDD
series: "테스트 주도 개발하기를 학습하며 정리한 내용"
---

> TDD의 핵심내용을 나만의 예시를 만들어 적용해본다.

## 📚 Chapter 02. TDD 시작


## TDD 이전의 개발?
TDD(테스트 주도 개발)을 시작하기 전엔 개발을 진행하다가 문제가 생기면 전에 작성한 코드를 디버깅하며 원인을 찾는 방식을 주로 택했다.    
하지만, 코드끼리의 의존성이 강하거나 / 전 단계의 코드량이 많은 등의 상황에 처하면  디버깅에 과도한 시간을 쏟아야 하는 난관이 자주 발생하곤 한다.    
숙련도가 높아진 현업자여도, 뻔한 버그를 찾는 시간은 적으나 코드에 생각치 못한 오류가 있으면 원인을 찾느라 많은 코드를 탐색해야 한다고 한다.
그러던 와중 필자는 '테스트 주도 개발'이란 책을 접했고, TDD를 접하게 되어 예전보다 더 나은 코드를 짧은 시간에 만들 수 있게 되어 개발 시간을 줄일 수 있었다고 한다.    

## TDD란?
TDD의 순서는 테스트 -> 개발이다. 나도 TDD를 접하기 전엔 내 직관에 따라 자연스럽게 개발을 하다가 어떠한 중요한 로직이 있다면 그 때 해당 기능에 해당하는 테스트를 작성하는 방식을 택하고 있었는데, 일반적인 생각과 달리 구현 코드를 작성하기도 전에 테스트 코드를 작성한다는 것이 조금 의아하게 다가왔다.

> 기능을 검증하는 테스트 코드를 먼저 작성하고 테스트를 통과시키기 위해 개발을 진행한다.
> {: .prompt-info }

위는 이 책에도 쓰여있고 유명한 TDD를 소개하는 문장 중 하나인데 유명한 방법론인 만큼 매우 흥미로운 문장이라고 생각한다.    
<br>

말한 것 처럼, TDD로 개발할 때 먼저 해야 할 것은 검증하는 테스트 코드를 작성하는 것이다.
계산기의 덧셈기능을 구현한다고 해보자.
```java
public class CalculatorTest {

    @Test
    void plus() {
        int result = Calculator.plus(1, 2);
        assertEquals(3, result);
    }
}
```
Calculator 클래스를 만들지도 않았고, 존재하지 않는 클래스의 존재하지 않는 plus 메서드를 사용하였으니 당연히 오류가 발생할 것이다.    
<br>

이 단계에서 개발자가 할 수 있는 고민을 필자가 나열하였다.
- 메서드 이름은 plus로 할까 sum으로 할까?
- 그럼 그 메서드의 파라미터가 몇개여야 할까? 파라미터의 타입은? 반환 값은?
- 메서드를 정적 메서드로 구현할까? 인스턴스 메서드로 구현할까?
- 메서드를 가진 그 클래스의 이름은 뭐가 좋을까?

만약 내가 TDD를 몰랐다면 `CalculatorTest`를 보고 기능 코드도 안짜놓고 왜 테스트 코드를 먼저 짜는거지? 라고 생각했겠지만, 이러한 고민들을 들으니 테스트 코드를 먼저 짜는 TDD의 방식으로부터 생각해 볼 수 있는 것들이 많다는 것을 느꼈고, `개발 -> 테스트`의 방식보다 더 공든 탑을 쌓을 수 있을 것 같다는 느낌을 받았다.    
<br>

계속 이어서, 이제 오류를 발생시켰으니 이 오류를 해결할 방법을 생각해야 한다.     
Calculator 클래스를 생성하고, plus() 메서드를 추가하면 위 테스트를 통과할 것이다.
```java
public class Calculator {
    public static int plus(int x, int y) {
        return 0;
    }
}
```

<br>

당연히, 또 오류가 발생한다. 3을 리턴해야 하는데 0이 나왔기 때문이다.    
하지만 여기서 중요한건 아까 발생한 `Calculator`클래스가 없어서 발생한 오류메세지는 나타나지 않았다는 것이다.

<br>

그럼 이번 오류를 해결하는 가장 간단한 방법이 무엇일까?    
그것은 바로 plus() 메서드가 3을 리턴하게 하드코딩해주면 된다.(코드생략)

> 여기서 왜 3만 리턴? 이라는 생각을 했지만, 더 읽고 나서 TDD의 하나의 방식인 '발생한 오류에 대해서만 해결을 한다.' 에 대해서 설명하는 것이라고 느꼈다.

<br>

하나의 케이스만으로 테스트코드를 끝낼 수 없으므로, 다른 경우를 또 추가한다.
```java
public class CalculatorTest {

    @Test
    void plus() {
        int result = Calculator.plus(1, 2);
        assertEquals(3, result);
        assertEquals(5, Calculator.plus(4,1));
    }
}
```
당연하게도, 오류가 5를 리턴해야 하는데 3을 리턴했다는 오류가 발생할 것이다.

<br>

그럼 1+2와 4+1을 모두 통과시키려면 어떻게 해야할까?
```java
public class Calculator {
    public static int plus(int x, int y) {
        return x + y;
    }
}
```
받은 두 int값을 더하는 로직으로 수정해주면 된다.

<br>

지금까지 매우 간단한 기능을 TDD로 구현하였다. 나도 이 예시를 읽으면서 이렇게까지 하는 이유가 무엇인가? 라는 의문이 들었다.  그에 필자는 이렇게 답했다.

> 우리는 실패한 테스트를 통과시킨 뒤에 새로운 테스트를 추가했고 다시 그 테스트를 통과시키기 위한 코드를 작성했다.    
> 이런 식으로 TDD는 테스트를 먼저 작성하고 테스트에 실패하면 '테스트를 통과시킬 만큼' 코드를 추가하는 과정을 반복하면서 점진적으로 기능을 완성해 나간다.

### 💡TIP
> **테스트를 하며 아직 완성이 안된 클래스들은 'src/test/java' 안에서 작업하다가, 완성이 된 후에 'src/main/java' 로 옮기면 아직 완성되지 않은 코드가 배포되는 것을 방지하는 효과가 있다.**


## TDD 핵심 정리
필자는 '암호 검사기'(8자 이상, 대문자 포함, 숫자포함)과 같은 예시로 TDD에 대해 절차적으로 잘 설명해 주었다.    
나는 나만의 예시를 만들어 적용해 봄으로 써 이해도를 높이고자 하였다.

먼저, 필자가 예시를 통해 말하고자 했던 핵심 내용들을 먼저 정리하고 이어나가고 싶다.

### 👀 1. 첫 번째 테스트를 선택할 땐 가장 쉽거나 가장 예외적인 상황을 선택해야 한다
이건 어떤 상황이냐에 따라 다를 것인데, '모든 조건을 만족하는 경우 / 모든 조건을 만족하지 않는 경우' 와 같이 가장 직관적이고, 예외적인(모두 맞거나, 모두 틀리거나) 상황을 선정하는게 이후 진행 과정을 순탄하게 해준다.    
그리고 여기서, 두 경우 중에 해당 상황에 더 테스트코드를 작성하기 쉬운 방향으로 진행하면 되겠다.


### 👀 2. TDD는 테스트를 통과시킬 만큼의 코드를 작성한다
아까 Calculator 예시에서도 말했던 내용이지만, 어떤 오류가 발생하였을 때 해당 오류를 해결하는 것에 초점을 두자.    
물론 미리 예상되는 오류를 방지하는 코드를 짤 수 도 있다. 하지만 TDD를 온전히 따라가기 위해선 필자의 말에 귀를 기울이자.

### 👀 3. 테스트코드를 정리하자
'테스트 주도 개발'인 만큼, 여러 개의 테스트케이스를 만들며 생기는 중복되는 코드를 따로 빼내서 코드의 가독성을 챙기자는 뜻이다.    
하지만, 중복을 제거한 뒤에 오히려 테스트 코드 관리가 어려워진다면, 제거했던 중복을 되돌려야 한다.

### 👀 4. 예외 상황 고려
예를 들면 비밀번호를 입력받는데, 값이 아예 없는(null)케이스 혹은 공백('')을 받은 경우 등의 예외상황을 빠뜨릴 수 있으니 항상 조심하자.

### 👀 5. 변경사항이 있을 때 마다 모든 테스트케이스를 돌려본다
2번 테스트케이스에 해당하는 클래스 내부 로직을 수정했을 때, 1,3번 테스트케이스가 통과하지 않는 사고를 막기 위해, 로직을 변경할 때 항상 모든 테스트케이스들을 실행해 보는 것이 안전하다.

### 👀 6. 테스트가 개발을 주도한다
테스트 코드를 먼저 작성하면 '테스트가 개발을 주도하게 된다'. 테스트를 작성하는 과정에서 구현을 생각하지 않았다. 단지 해당 기능이 올바르게 동작하는지 검증할 수 있는 테스트 코드를 만들었을 뿐이다.     
즉, 테스트 코드를 만들면 다음 개발 범위가 정해진다. 테스트 코드가 추가되면서  검증하는 범위가 넓어질수록 구현의 완성도가 같이 올라간다. 이렇게 테스트가 개발을 주도해 나간다.


## TDD 예시
필자가 설명하면서 든 예시를 같이 따라해보며 이해가 배로 되는 걸 느껴, 필자의 예시보단 간단한 나만의 간단한 예제를 만들어서 위의 TDD 핵심 사항들을 적용해보려고 한다.

### 💵 계좌이체 하기

- 예금주명과 금액을 입력받는다.
- 검사할 규칙은 두 가지 존재한다.
    1) 예금주명은 8글자 이하의 문자열
    2) 이체금액의 한도는 1,000,000
- 두 가지 규칙을 모두 만족하면 "이체 성공"을 반환한다.
- 한 가지 규칙만 만족하면 "예금주명이 올바르지 않습니다." or "이체금액이 올바르지 않습니다."를 반환한다.
- 두 가지 규칙 모두 만족하지 않는다면 "이체 조건을 확인하시오."를 반환한다.


### ☑️ 모든 조건을 충족하는 경우
시작하기전에, 위 핵심들 중에 1번 조건('첫 번째 테스트를 선택할 땐 가장 쉽거나 가장 예외적인 상황을 선택해야 한다')에 대해 고민해봐야 한다.    
`테스트를 통과하기`자체에 가장 쉬운 상황은 `모든 규칙을 충족하는 경우`일 것이다. 아래 코드를 보면 이해가 편할 것이다.
```java
public class AccountTransferTest {
    @Test
    void meetsAllCriteria(){
        AccountTransferMeter meter = new AccountTransferMeter();
        String result = meter.meter("한수현",20000L);
        assertEquals("이체성공",result);
    }
}
```
여기서 `AccountTransferMeter`는 아직 만들지 않은 타입이다. 그렇기에 그에 대한 컴파일 에러가 먼저 발생한다.    
따라서 나는, 핵심 사항 2번(TDD는 테스트를 통과시킬 만큼의 코드를 작성한다)에 맞게 개발하기 위해, 일단 `AccountTransferMeter`클래스를 작성해야 한다.

> 발생한 오류를 해결하는 것에 집중한다.
{: .prompt-danger }

```java
public class AccountTransferMeter {
    public String meter(String name, long money) {
        return null;
    }
}
```
자, 이제 방금 발생한 `AccountTransfetMeter`가 없어서 생긴 오류를 해결하였다.     
이제 실행을 해보면, 테스트코드에서 기대한 값은 "이체성공"인데 실제 값은 null이기 때문에 테스트에 실패한다.

<br>

이 테스트를 통과시키는 법은? 간단하다. "이체성공"을 리턴하도록 수정하면 된다.
```java
public class AccountTransferMeter {
    public String meter(String name, long money) {
        return "이체성공";
    }
}
```
wow~ 이제 테스트 성공!     
이제 `모든 규칙을 만족하는`경우에 대한 테스트를 완료했으니, 다음 경우를 생각하자.

<br>

### ☑️ 예금주명의 조건은 충족하지만 이체금액의 조건은 충족하지 않는 경우
이제 다음 테스트 메서드를 추가해야 한다. 이번엔, 예금주명은 8자 이하의 문자열(만족)이지만, 이체금액이 1~1,000,000원 사이의 값이 아닌(불만족)경우이다.    
이 때의 리턴 값은 "이체금액이 올바르지 않습니다." 이어야 한다.
```java
public class AccountTransferTest {
    @Test
    void meetsAllCriteria(){...}
    
    @Test
    void meetsOnlyNameCriteria(){
        AccountTransferMeter meter = new AccountTransferMeter();
        String result = meter.meter("한수현",2000000L);
        String result2 = meter.meter("한수현",0L);
        String result3 = meter.meter("한수현",-1L);
        assertEquals("이체금액이 올바르지 않습니다.",result);
        assertEquals("이체금액이 올바르지 않습니다.",result2);
        assertEquals("이체금액이 올바르지 않습니다.",result3);
    }
}
```
지금 어떤 값들을 전달해도 `meter`가 "이체성공"을 반환하도록 코딩되어 있기 때문에, 오류가 발생한다.(기대값: "이체금액이 올바르지 않습니다.", 실제값: "이체성공")    
따라서 아래와 같이 수정해준다.
```java
public class AccountTransferMeter {
    public String meter(String name, long money) {
        if(money>1000000L || money<1L){
            return "이체금액이 올바르지 않습니다.";
        }
        return "이체성공";
    }
}
```
위와 같이 수정하였다. 방금 전의 테스트코드에서 발생하는 오류를 해결하였다!


### ☑️ 이체금액의 조건은 충족하지만 예금주명의 조건은 충족하지 않는 경우
세 번째 테스트 메서드를 추가하겠다. 이번엔, 이체금액은 1~1,000,000 사이지만(만족), 예금주명이 조건에 맞지 않는 경우이다.
```java
public class AccountTransferTest {
    @Test
    void meetsAllCriteria(){...}
    
    @Test
    void meetsOnlyNameCriteria(){...}
    
    @Test
    // string이 아닌 값을 입력하는 경우는 생략함.
    void meetsOnlyMoneyCriteria(){        
        AccountTransferMeter meter = new AccountTransferMeter();
        String result = meter.meter("비바리퍼블리카(토스)",10000L);
        String result2 = meter.meter("",10000L);
        assertEquals("예금주명이 올바르지 않습니다.",result);
        assertEquals("예금주명이 올바르지 않습니다.",result2);
    }
}
```
당연히 해당 조건에 대한 로직이 `AccountTransferMeter`에 없기 때문에, 기대한 값은 "예금주명이 올바르지 않습니다." 이지만, 실제 값은 "이체성공"이라 오류가 발생할 것 이다.    
따라서 아래와 같이 수정해준다.
```java
public class AccountTransferMeter {
    public String meter(String name, long money) {
        if(money>1000000L || money<1L){
            return "이체금액이 올바르지 않습니다.";
        }
        
        boolean isBlank = false; //true여야 공백이 아닌 예금주명
        if(!name.trim().isEmpty()) {
            isBlank = true;
        }
        if(!(name.length()<=8 && isBlank)) {
            return "예금주명이 올바르지 않습니다.";
        }
    }
}
```
위와 같이 수정해주면 방금 발생한 테스트코드의 오류를 해결하였다 !

### ☑️ 두 조건 모두 충족하지 않는 경우
이번엔 두 조건 모두 충족하지 않는 경우에 대해 테스트를 해보려고 한다.
```java
public class AccountTransferTest {
    @Test
    void meetsAllCriteria(){...}
    
    @Test
    void meetsOnlyNameCriteria(){...}
    
    @Test
    // string이 아닌 값을 입력하는 경우는 생략함.
    void meetsOnlyMoneyCriteria(){...}
    
    @Test
    void meetsNoCriteria(){        
        AccountTransferMeter meter = new AccountTransferMeter();
        String result = meter.meter("비바리퍼블리카(토스)",2000000L);
        assertEquals("이체 조건을 확인하시오.",result);
    }
}
```
위의 테스트 코드를 실행해보면, 기대하는 값은 "이체 조건을 확인하시오." 인데, 실제 값은 "이체금액이 올바르지 않습니다." 이기 때문에 오류가 발생할 것이다.
하나의 조건만 충족하지 않을 때와, 모든 조건을 충족하지 않을 때의 리턴 값이 달라야하기 때문이다.    
아래와 같이 수정가능하다.
```java
public class AccountTransferMeter {
    public String meter(String name, long money) {
        boolean moneyCriteria = false;
        boolean nameCriteria = false;
        
        if(money<=1000000L && money>=1L){
            moneyCriteria = true;
        }
        
        boolean blankValidate = false; //true여야 공백이 아닌 예금주명
        if(!name.trim().isEmpty()) { // 공백이 아니면,
            blankValidate = true;
        }
        if(name.length()<=8 && blankValidate) {
            nameCriteria = true;
        }
        
        if(!moneyCriteria && nameCriteria) return "이체금액이 올바르지 않습니다.";
        if(moneyCriteria && !nameCriteria) return "예금주명이 올바르지 않습니다.";
        if(!moneyCriteria && !nameCriteria) return "이체 조건을 확인하시오.";
        
        return "이체 성공";
    }
}
```
위와 같이 수정해주면 방금 작성한 테스트 코드를 통과할 수 있다!

### ☑️ 값이 없는 경우
값이 없는 경우와 같이 특수한 케이스에 대한 테스트도 항상 넣어주는 것이 좋다.
```java
public class AccountTransferTest {
    @Test
    void meetsAllCriteria(){...}
    
    @Test
    void meetsOnlyNameCriteria(){...}
    
    @Test
    // string이 아닌 값을 입력하는 경우는 생략함.
    void meetsOnlyMoneyCriteria(){...}
    
    @Test
    void meetsNoCriteria(){...}
    
    @Test
    void inputIsNull(){
        AccountTransferMeter meter = new AccountTransferMeter();
        String result = meter.meter("한수현",null);
        String result2 = meter.meter(null,10000L);
        String result3 = meter.meter(null,null);
        assertEquals("값을 입력하세요.",result);
        assertEquals("값을 입력하세요.",result2);
        assertEquals("값을 입력하세요.",result3);
    }
}
```
위의 테스트는 우리가 null 값에 대한 처리를 안해주었기 때문에 NPE(NullPointerException)이 발생하게 된다.    
아래와 같이 처리해 주자.
```java
public class AccountTransferMeter {
    public String meter(String name, long money) {
        boolean moneyCriteria = false;
        boolean nameCriteria = false;
        
        if(name==null || money==null) return "값을 입력하세요.";
        
        if(money<=1000000L && money>=1L) moneyCriteria = true;
        
        boolean blankValidate = false; //true여야 공백이 아닌 예금주명
        if(!name.trim().isEmpty()) blankValidate = true; // 공백이 아니면,
            
        if(name.length()<=8 && blankValidate) nameCriteria = true;
            
        if(!moneyCriteria && nameCriteria) return "이체금액이 올바르지 않습니다.";
        if(moneyCriteria && !nameCriteria) return "예금주명이 올바르지 않습니다.";
        if(!moneyCriteria && !nameCriteria) return "이체 조건을 확인하시오.";
        
        return "이체 성공";
    }
}
```

### ☑️ 코드 리펙터링
이제 모든 상황에 대한 테스트코드를 작성하고 -> 개발하기의 순서를 지켜 코드를 완성하였다.      
테스트코드도 코드이기 때문에 유지보수 대상이다. 리펙터링을 해주면 깔끔하게 정리를 해주는 것이 좋다.
```java
public class AccountTransferTest {
    private AccountTransferMeter meter = new AccountTransferMeter();
    
    private void accountTransferValidator(String name, long money, String message) {
        String result = meter.meter(name,money);
        assertEquals(message,result);
    }
    
    @Test
    void meetsAllCriteria(){
        String result = meter.meter("한수현", 20000L);
        accountTransferValidator("한수현", 20000L, "이체성공");
    }

    @Test
    void meetsOnlyNameCriteria(){
        accountTransferValidator("한수현", 2000000L, "이체금액이 올바르지 않습니다.");
        accountTransferValidator("한수현", 0L, "이체금액이 올바르지 않습니다.");
        accountTransferValidator("한수현", -1L, "이체금액이 올바르지 않습니다.");
    }

    @Test //string이 아닌 값을 입력하는 경우는 생략함.
    void meetsOnlyMoneyCriteria(){
        accountTransferValidator("비바리퍼블리카(토스)", 10000L, "예금주명이 올바르지 않습니다.");
        accountTransferValidator(" ", 10000L, "예금주명이 올바르지 않습니다.");
    }

    @Test
    void meetsNoCriteria(){
        accountTransferValidator("비바리퍼블리카(토스)", 2000000L, "이체 조건을 확인하시오.");
    }

    @Test
    void inputIsNull(){
        accountTransferValidator("한수현", null, "값을 입력하세요.");
        accountTransferValidator(null, 10000L, "값을 입력하세요.");
        accountTransferValidator(null, null, "값을 입력하세요.");
    }
}
```
위 처럼 정리를 해주었다.
- 원래 테스트 코드에서는 AccountTransferMeter 객체를 생성하는 코드가 모든 메서드에 중복되어 들어가 있으므로 밖으로 빼낸다.
- 원래 result에 리턴 메세지를 담고 그것을 assertEquals를 통해 검증하는 방식이었는데, 이 과정을 하나의 메서드(`accountTransferValidator`)로 추출하여 코드중복을 줄였다.
```java
public class AccountTransferTest {
    private AccountTransferMeter meter = new AccountTransferMeter();
    
    private void accountTransferValidator(String name, long money, String message) {
        String result = meter.meter(name,money);
        assertEquals(message,result);
    }
    
    @Test
    void ...
```

> 항상 로직을 수정하였을 때는 모든 테스트케이스를 돌려보는 것을 잊지말자!

> src/test/java에 있던 클래스 파일을 구현 후 src/main/java에 옮겨주는 것도 잊지말자!

## 🌊 마무리 한마디
직접 작은 기능을 구현해보며 TDD를 적용해보니 체감이 확실히 되었다. 지금까지의 나의 개발방식에서 놓칠 수 있었던 부분들을 TDD를 적용하면 많은 점들을 커버할 수 있다고 느껴져 인상깊었다.  