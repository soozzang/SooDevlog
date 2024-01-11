---
title: "TDD와 Junit,Assertj"
date: 2023-10-01
tags:
  - Java
  - TDD
---


> 테스트 주도 개발의 필요성과 장단점, Junit과 Assertj에 대한 정리글

## TDD란?
- 테스트 주도 개발(test-driven development, TDD).    
- 개발 방법론 중의 하나로, 테스트를 먼저하고 개발을 하는 프로그래밍 방식이다.    
- 작은 단위의 테스트 케이스를 작성하고 이를 통과하는 코드를 추가하는 방식을 반복하여 구현한다.
- 짧은 개발 주기의 반복에 의존하는 개발 프로세스이다.

## TDD의 개발주기
![TDD의 개발주기](/Users/soohyun/Desktop/blog/my-hoodie-blog/contents/posts/TDD/image/TDD-개발주기.png)
위는 TDD의 개발주기를 그림으로 표현한 것이다.    
- `Red` 단계에서는 실패하는 테스트 코드를 먼저 작성한다.
- `Green` 단계에서는 테스트 코드를 성공시키기 위한 실제 코드를 작성한다.
- `Blue` 단계에서는 중복 코드 제거, 일반화 등의 리펙토링을 수행한다.

핵심은, 실패하는 테스트 코드를 작성할 때 까지 실제 코드를 작성하지 않는 것과, 실패하는 테스트를 통과할 정도의 최소 실제 코드를 작성해야하는 것이다.    
이를 통해 실제 코드에 대해 기대되는 바를 보다 명확하게 정의해서 불필요한 설계를 피할 수 있고, 정확한 요구 사항에 집중할 수 있다.

## 일반 개발 방식과 TDD 개발 방식의 비교

### 1) 일반 개발 방식
보통 개발 방식은 `'요구사항 분석 -> 설계 -> 개발 -> 테스트 -> 배포'`의 개발 주기를 갖는다.     
이러한 방식은 소프트웨어 개발을 느리게 하는 잠재적 위험을 갖고 있는데, 그 이유는 아래와 같다    
- 1.소비자의 요구사항이 처음부터 `명확하지 않은` 경우가 많기 때문에 처음부터 완벽한 설계는 어렵다.
- 2.자체 버그 검출 능력 저하 or 소스코드의 품질이 저하될 수 있다.
- 3.자체 테스트 비용이 증가할 수 있다.

이러한 문제가 발생하는 이유는 어느 프로젝트든 `초기 설계`가 완벽하기는 힘들기 때문이다.    
<br>
고객의 요구나 기획의 변화로 인해 계속 재설계를 하게되면, 개발자는 코드를 삽입,수정,삭제 하는 과정에서 `불필요한 코드를 남길 가능성`이 존재한다.    
<br>
결론적으로 이러한 코드들은 재사용이 어려워 `유지보수성`이 떨어진다.    
<br>
`작은 부분`의 기능 수정에도 `모든 부분`을 테스트해야 하므로 전체적인 버그를 검출하기 어려워진다. 이렇게 되면, 작은 수정에도 모든 기능을 다시 테스트해야 하는 문제가 발생해 자체 테스트 비용이 증가한다.

### 2) TDD 개발 방식
- TDD와 일반적인 개발 방식의 가장 큰 차이점은 테스트 코드를 작성한 뒤에 실제 코드를 작성한다는 것이다.
<br>
- 디자인 단계에서 프로그래밍 목적을 반드시 미리 정해야하고, 무엇보다 테스트 케이스를 작성해야만 한다.
<br>
- 테스트 코드를 작성하는 도중 발생하는 예외 사항은 테스트 케이스에 새로 추가하고 설계를 개선한다.
<br>
- 이후 테스트가 통과된 코드만을 코드 개발 단계에서 실제 코드로 작성한다.
<br>
- 이러한 반복적인 단계가 진행되며 자연스럽게 코드의 버그가 줄어들고 소스코드는 간결해진다.
<br>
- 또한 테스트 케이스 작성을 하며 자연스럽게 설계가 개선되기 때문에 재설계 시간이 절감된다.


## TDD 개발 방식의 장점

### 보다 튼튼한 객체 지향적 코드 생산
TDD는 코드의 재사용이 가능함을 보장하므로, 개발 시 기능 별 철저한 모듈화가 이뤄진다.    
이는 종속성과 의존성이 낮은 모듈과 조합된 소프트웨어를 개발할 수 있게 끔 하며, 모듈을 추가/삭제/수정 해도 프로그램의 전체 구조에 영향을 미치지 않게 된다.    

### 재설계 시간의 단축
테스트 코드를 먼저 작성하기 때문에 개발자가 지금 무엇을 해야하는지 명백하게 정의하고 개발을 시작할 수 있게된다. 그 과정에서 다양한 예외사항에 대한 처리까지도 생각하게 되고, 이는 결과적으로 재설계 시간을 단축시킨다.    

### 디버깅 시간의 단축
단위 테스트를 진행하면, 어떠한 문제가 발생했을 때 어디서 터진 문제인지를 전부 디버깅할 필요없이 특정 버그를 손 쉽게 찾아낼 수 있다.    

### 추가 구현의 용이함
추가적인 기능을 구현할 때 해당 기능이 기존 코드에 어떤 영향을 미칠지가 어렵고 꾸준히 고민해야할 사항인데, TDD의 경우 자동화된 단위 테스트를 전제하므로 테스트 기간을 획기적으로 단축시킬 수 있다.

## TDD 개발 방식의 단점

### 생산성 저하
TDD를 했을 때 `개발 속도가 느려진다고` 느낀다는 의견이 많기 때문에 TDD에 대해 반신반의한다고 한다.      
당연하게도, 기능하나를 구현하는 것 뿐만아니라 그에 대한 테스트 코드작성까지 총 두 가지 작업이 요구되기 때문이다.    
TDD 방식의 개발 시간은 일반적인 개방 방식에 비해 대략 10~30% 정도로 늘어난다고 한다.


## TDD를 하기 어려운 이유

### 1) 지금까지 자신이 개발하던 방식을 많이 바꿔야 한다.
TDD가 아닌 방식으로 오랜기간 개발을 했을수록, 바꾸기 어려울 것이다.    
오히려 개발을 별로 안해본 사람들에겐 적용하기 쉽다.
### 2) TDD는 이렇게 해야한다는 이미지(틀)이 있다.
반드시 `툴`(단위 테스트 프레임워크)을 써서 개발해야 된다고 생각한다고 한다.    
이러한 규칙에 얽매이는 것은 에자일 방식이 아니고, `도구/규칙`에 집착하다보니 TDD가 더 어렵게 느껴지게 된다.

> 이제 TDD의 대표적인 툴인 Junit과 Assertj에 대해 정리하고자 한다.

## Junit5의 어노테이션

테스트를 구성하고, 프레임워크를 상속하기 위해 다음과 같은 어노테이션을 사용한다.    
따로 명시하지 않으면 대부분 `junit-jupiter-api` 모듈 안의 `org.junit.jupiter.api` 패키지 안에 존재한다.    

### @DisplayName
테스트 클래스나 테스트 메소드에 이름을 붙여줄 때 사용한다.    
```java
@Test
@DisplayName("~~ 잘 되는지 테스트")
void 테스트(){
    ...
}
```
기본적으로 테스트 이름은 메소드 이름을 따라간다. 하지만 메소드 이름은 그대로 둔 채 `테스트 이름`을 바꾸고 싶을 때 이 어노테이션을 사용한다.    

### @DisplayNameGeneration
클래스에 해당 애노테이션을 붙이면 `@Test` 메소드 이름에 `언더바(_)`로 표시한 모든 부분이 공백으로 바뀌어 나타난다.    
```java
@DisplayNameGeneration(DisplayNameGenerator.ReplaceUnderscores.class)
class blablaTest {
    @Test
    ...
}
```
### @BeforeEach
각각 테스트 메소드가 실행되기전에 실행되어야 하는 메소드를 명시해준다.    
`@Test`,`@RepeatedTest`,`@ParameterizedTest`,`@TestFactory`가 붙은 테스트 메소드가 실행하기 전에 실행된다.
테스트하기 전에 필요한 데이터를 미리 세팅해주기 위해 주로 사용한다.

### @AfterEach
`@Test`,`@RepeatedTest`,`@ParameterizedTest`,`@TestFactory`가 붙은 테스트 메소드가 실행되고 난 후 실행된다.

### @BeforeAll
`@BeforeEach`는 `각 테스트 메소드 마다` 실행되지만, 이 어노테이션은 테스트가 시작하기 전 딱 `한 번만` 실행 된다.

### @AfterAll
위와 동일하게 `테스트가 완전히 끝난 후` 딱 `한 번만` 실행 된다.

### @Nested
테스트 클래스안에 `Nested 테스트 클래스`를 작성할 때 사용 된다.    
static이 아닌 중첩 클래스, 즉 Inner 클래스여야만 한다. 테스트 인스턴스 라이플사이클이 per-class로 설정되어 있지 않다면 `@BeforeAll`,`@AfterAll` 가 동작하지 않으니 주의할 필요가 있다.

### @Tag
테스트를 필터링할 때 사용한다. 클래스또는 메소드레벨에 사용한다.

### @Disabled
테스트 클래스나, 메소드의 테스트를 비활성화 한다.

### @Timeout
주어진 시간안에 테스트가 끝나지 않으면 실패한다.


## Assertions
모든 JUnit Jupiter assertion은 정적 메소드이며, `org.junit.jupiter.api.Assertions` 클래스 안에 있다.    

### assertEquals
두 값을 비교하여 일치 여부를 판단한다.     
```java
@Test
public void test(){
    String expected = "수현";
    String actual = "수현";

    assertEquals(expected, actual);
}
```

### assertArrayEquals
- 예상 배열과 실제 배열이 동일한지 확인한다.    
- 배열이 같지 않으면 마지막 인자로 들어간 메세지가 출력된다.
```java
@Test
public void test() {
    char[] expected = { 'S', 'o', 'o', 'h', 'y', 'u', 'n' };
    char[] actual = "Soohyun".toCharArray();

    assertArrayEquals(expected, actual, "같은 배열이 아님.");
}
```

### assertTrue & assertFalse
특정 조건이 true인지 false인지 판단한다.
```java
@Test
public void test() {
    BooleanSupplier condition = () -> 1 > 2;

    assertFalse(condition, "1은 2보다 크지 않습니다.");
}
```

### assertNull & assertNotNull
객체의 null 여부를 확인한다.
```java
@Test
public void test() {
    Object wallet = null;

    assertNull(wallet, () -> "wallet은 null이어야 합니다.");
}
```

### assertSame & assertNotSame
`예상되는 값`과 `실제 값`이 `동일한 객체`를 참조하는지 확인한다.
```java
@Test
public void test() {
    String language = "Java";
    Optional<String> optional = Optional.of(language);

    assertSame(language, optional.get());
}
```

### fail
- 제공된 실패 메시지와 기본 원인으로 테스트에 실패.    
- 개발이 완료되지 않은 테스트를 표시하는 데 유용하다.
```java
@Test
public void test() {
    // Test not completed
    fail("개발이 완료되지 않은 테스트입니다.");
}
```

### assertIterableEquals
- 예상 반복 가능 항목과 실제 반복 가능 항목이 동일한지 확인한다.    
- 두 Iterable은 동일한 순서로 동일한 요소를 반환해야 한다.    
- 두 Iterable이 동일한 유형일 필요는 없다.    
```java
@Test
public void test() {
    Iterable<String> al = new ArrayList<>(asList("Java", "Junit", "Test"));
    Iterable<String> ll = new LinkedList<>(asList("Java", "Junit", "Test"));

    assertIterableEquals(al, ll);
}
```

### assertNotEquals
예상 값과 실제 값이 다름을 확인
```java
@Test
public void test() {
    Integer value = 5; 
    
    assertNotEquals(0, value, "value는 0이 될 수 없습니다.");
}
```

### assertThrows
특정 예외가 발생했는지 확인한다.    
`assertThrows(확인할 예외 클래스, 테스트하려는 코드)`
```java
@Test
public void Test() {
    Assertions.assertThrows(RuntimeException.class, () -> {
        DoSomething.func();
    });
}
```

## AssertJ
Junit5의 공식 문서에서 AssertJ 라이브러리 사용을 권장하였고, 주위 개발하시는 분들이나 강의를 보아도 AssertJ를 주로 사용하시는 경우가 많았다. 그 이유를 먼저 알아보았더니,    

### 1. 가독성
깔끔하고 읽기 쉬운 테스트 코드를 작성할 수 있다.
```java
// 1. Junit
assertEquals(a, b);

// 2. AssertJ
assertThat(a).isEqualTo(b);
```
위의 간단한 두 테스트를 보았을 때 AssertJ의 메소드가 왼쪽에서 오른쪽으로 더 자연스럽게 읽히는 느낌을 받을 수 있다.  


### 2. 자세한 실패메세지를 제공해 실패 원인 파악 용이함

### 1) Junit5
```java
// JUnit
assertTrue(name.contains("o"));
```
위의 테스트를 Junit으로 돌렸을 때, 실패 메세지를 확인해보면,    
```bash
expected: <true> but was: <false>
Expected :true
Actual   :false
```
이러하다. 이 실패 메세지는 예상값이 true지만 실제 값은 false라는 사실을 알 수 있지만, 무슨 테스트를 실패한건지(name이 "o"를 포함하지 않음)를 포함해 알려주지 않아서 명확하지 않다.      

### 2) AssertJ
```java
// AssertJ
assertThat(name).contains("o");
```
위의 테스트를 AssertJ로 돌렸을 때, 실패 메세지를 확인해보면,
```bash
java.lang.AssertionError:
Expecting actual:
  "han"
to contain:
  "o"
```
실패 메세지만 보고도 어떤 로직의 테스트를 해서 실패한건지 + 실패원인을 바로 파악할 수 있다.

### 3. 다양한 검증 메서드 제공으로 인한 편리함
```java
// JUnit
assertTrue(winners.containsAll(List.of("수현1", "수현2")) && winners.size() == 2);
assertArrayEquals(winners.toArray(), new String[]{"수현1", "수현2"});
assertTrue(winners.containsAll(List.of("수현1", "수현2")));

// AssertJ
assertThat(winners).containsExactlyInAnyOrder("수현1", "수현2");
assertThat(winners).containsExactly("수현1", "수현2");
assertThat(winners).contains("수현1", "수현2");
```
위의 예시를 보았을 때 알 수 있듯이, Junit은 간단한 메서드만 제공하기 때문에 비교를 위해 많은 작업들이 필요시된다.   
하지만, AssertJ는 다양한 메서드를 제공해주어 개발자가 할 일을 덜어준다.


### 4. 메서드 체이닝
AssertJ는 메서드 체이닝을 제공한다.
```java
assertThat("[TITLE] Hello, My Name is sh")
        .isNotEmpty()
        .contains("[TITLE]")
        .containsOnlyOnce("sh")
        .doesNotStartWith("[ERROR]");
```
```java
assertThat(score)
        .isPositive()
        .isGreaterThan(60)
        .isLessThanOrEqualTo(75);
```
위와 같이, 메서드 체이닝을 통해 여러 테스트를 한 번에 할 수 있다.


> 그럼 본격적으로 AssertJ의 사용법을 알아보자.

## AssertJ 기본 검증 메서드

| 메서드            | 설명                              |
|------------------|----------------------------------|
| `isEqualTo(값)`   | 검증대상과 동일한 값인지 비교한다.  |
| `isSameAs(값)`    | 검증대상과 값을 == 비교한다.       |
| `isNotNull()`    | 검증대상이 Not Null 인지 확인한다.  |
| `isNull()`       | 검증대상이 Null 인지 확인한다.     |
| `isNotEmpty()`   | 검증대상이 Not Empty 인지 확인한다. |
| `isEmpty()`      | 검증대상이 Empty 인지 확인한다.    |
| `isIn()`         | 검증대상이 값 목록에 포함되어 있는지 검증한다.  |
| `isNotIn()`      | 검증대상이 값 목록에 포함되어 있지 않는지 검증한다. |

`isIn()`과 `isNotIn()`의 값 목록은 `가변 인자`로 주거나 List와 타입을 이용해 전달한다.    

### 사용 예시
```java
import static org.assertj.core.api.Assertions.assertThat;

class JUnitTest {
    @Test
    void 기본_테스트_메서드() {
        // isEqualTo()
        String str1 = new String("hello");
        String str2 = new String("hello");
        assertThat(str1).isEqualTo(str2); // 비교 대상의 내용이 같은지 확인
        // isSameAs()
        String str3 = str1;
        assertThat(str1).isSameAs(str3); // 같은 객체를 참조하고 있는지 확인

        // isNotNull()
        assertThat(str1).isNotNull(); // 객체가 null이 아닌지 확인

        // isNull()
        String str4 = null;
        assertThat(str4).isNull(); // 객체가 null인지 확인

        // isNotEmpty() and isEmpty()
        List<String> emptyList = new ArrayList<>();
        List<String> nonEmptyList = Arrays.asList("apple", "banana");
        assertThat(emptyList).isEmpty(); // 비어있는지 확인
        assertThat(nonEmptyList).isNotEmpty(); // 비어있지 않은지 확인

        // isIn() and isNotIn()
        String fruit1 = "apple";
        String fruit2 = "orange";
        assertThat(fruit1).isIn(nonEmptyList); // "apple"이 리스트에 포함되어 있는지 확인
        assertThat(fruit2).isNotIn(nonEmptyList); // "orange"가 리스트에 포함되어 있지 않은지 확인
    }
}
```


## AssertJ 문자열 검증 메서드

| 메서드            | 설명                              |
|------------------|----------------------------------|
| `contains(값)` | 검증대상에 (값)이 포함되어있는지 확인한다. |
| `containsOnlyOnce(값)`| 검증대상에 (값)이 딱 한 번만 포함되어있는지 확인한다.|
| `containsOnlyDigits()` | 숫자만 포함하는지 검증한다.  |
| `containsWhitespaces()` | 공백 문자를 포함하고 있는지 검증한다. |
| `containsOnlyWhitespaces()`   | 공백 문자만 포함하는지 검증한다. |
| `doesNotContain(값)`  | 검증대상의 공백 문자만 포함하는지 검증한다. |
| `doesNotContainAnyWhitespaces()` | 검증대상의 공백 문자를 포함하고 있지 않은지를 검증한다. |
| `doesNotContainPattern(패턴)` | 검증대상의 정규 표현식에 일치하는 문자를 포함하고 있지 않은지를 검증한다.|
| `startsWith(값)` | 검증대상의 시작 값이 (값)과 동일한지 비교한다.|
| `endsWith(값)` | 검증대상의 마지막 값이 (값)과 동일한지 비교한다.|
| `doesNotStartWith(값)` | 검증대상의 (값)이 지정한 문자열로 시작하지 않는지를 검증한다.|
| `doesNotEndWith(값)` | 검증대상의 (값)이 지정한 문자열로 끝나지 않는지를 검증한다.|

### 사용 예시
```java
import static org.assertj.core.api.Assertions.*;

public class AssertJTest {

    @Test
    void 문자열_테스트() {
        assertThat("Hello, world! Nice to meet you.") // 주어진 "Hello, world! Nice to meet you."라는 문자열은
                .isNotEmpty() // 비어있지 않고
                .contains("Nice") // "Nice"를 포함하고
                .contains("world") // "world"도 포함하고
                .doesNotContain("ZZZ") // "ZZZ"는 포함하지 않으며
                .startsWith("Hell") // "Hell"로 시작하고
                .endsWith("u.") // "u."로 끝나며
                .isEqualTo("Hello, world! Nice to meet you."); // "Hello, world! Nice to meet you."과 일치
    }
}

```

## AssertJ 숫자 검증 메서드

| 메서드            | 설명                              |
|------------------|----------------------------------|
| `isPositive()`   | 검증대상이 양수인지 확인한다.  |
| `isNotPositive()`    | 검증대상이 양수가 아닌지 확인한다.       |
| `isNegative()`    | 검증대상이 음수인지 확인한다.  |
| `isNotNegative()`       | 검증대상이 음수가 아닌지 확인한다.     |
| `isZero()`   | 검증대상이 0 인지 확인한다. |
| `isNotZero()`      | 검증대상이 0 이 아닌지 확인한다.   |
| `isGraterThan(값)`         | 검증대상이 값을 초과한지 확인한다.  |
| `isLessThan(값)`      | 검증대상이 값보다 미만인지 확인한다. |
| `isGraterThanOrEqualTo(값)`      | 검증대상이 값 이상인지 확인한다. |
| `isLessThanOrEqualTo(값)`      | 검증대상이 값 이하인지 확인한다. |
| `isBetween(값1, 값2)`      | 값1과 값2 사이에 포함되는지 검증한다. |

### 사용 예시
```java
import static org.assertj.core.api.Assertions.*;

public class AssertJTest {

    @Test
    void 숫자_테스트() {
        assertThat(3.14d) // 주어진 3.14라는 숫자는
                .isPositive() // 양수이고
                .isGreaterThan(3) // 3보다 크며
                .isLessThan(4) // 4보다 작습니다
                .isEqualTo(3, offset(1d)) // 오프셋 1 기준으로 3과 같고
                .isEqualTo(3.1, offset(0.1d)) // 오프셋 0.1 기준으로 3.1과 같으며
                .isEqualTo(3.14); // 오프셋 없이는 3.14와 같습니다
    }
}
```

## assertThatThrownBy() : 예외처리

### 사용 예시 1
  
```java
@Test
public void testException_01() {
    assertThatThrownBy(() -> {
        throw new Exception("예외 발생!");
    })
            .isInstanceOf(Exception.class)
            .hasMessageContaining("예외 발생!");
}
```

### 사용 예시 2
아래의 대표적인 네 가지 예외를 사용한다면 아래와 같이도 작성 가능하다.
1. assertThatNullPointerException
2. assertThatIllegalArgumentException
3. assertThatIllegalStateException
4. assertThatIOException
  
```java
@Test
public void testException_02() {
    assertThatIOException().isThrownBy(() -> {
                throw new IOException("예외 발생!");
            })
            .withMessage("%s!", "예외 발생!")
            .withMessageContaining("예외 발생!")
            .withNoCause();
}
```

### 🔗 Reference
**[TDD란 무엇이며 왜 필요한가?](https://jay-flow.medium.com/tdd란-무엇이며-왜-필요한가-18cb5979629c)**    
**[TDD란? 테스트 주도 개발](https://hanamon.kr/tdd란-테스트-주도-개발/)**    
**[JUnit 5](https://junit.org/junit5/)**    
**[JUnit5 완벽 가이드](https://donghyeon.dev/junit/2021/04/11/JUnit5-완벽-가이드/)**    
**[AssertJ를 사용하는 이유](https://xxeol.tistory.com/12)**    
**[AssertJ 핵심 기능 알아보기](https://dkswnkk.tistory.com/595)**    
**[[JUnit5] JUnit5 AssertJ](https://thisiswoo.github.io/development/junit5-assertj.html)**    