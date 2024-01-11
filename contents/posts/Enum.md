---
title: "Enum"
date: 2023-10-15
tags:
  - java
---

> 원래 막연하게 개념정도만 알고 있었는데 직접 프로그래밍하며 사용해보니 쓰이는 이유에 대해 알게 되어 개념이 머리속에서 구체화된 것 같다.   

## Enum 이란?
관련이 있는 `상수`들의 집합이다. 자바에서는 final로 String과 같은 문자열이나 숫자들을 나타내는 기본 자료형의 값을 고정할 수 있다. 이렇게 고정된 값을 `상수`라고 한다. 그래서 상수만으로 구성된 클래스가 있다면 반드시 `class`로 선언할 필요없이, `enum`으로 선언하여 해당 객체는 상수의 집합이라는 것을 나타낼 수 있고 그에 특화된 기능들을 사용할 수 있다.


## Enum의 선언
일반 클래스와 동일하게 `.java`확장자를 갖는다. 그대신, 아래와 같이 `class`키워드 대신 `enum`키워드로 정의한다.
```java
public enum Rank {
    FIRST,
    SECOND,
    THIRD,
    FOURTH,
    FIFTH
}
```
위는 내가 구현했던 로또 미션의 일부를 가져온건데, 1등부터 5등까지의 Rank를 상수화 해놓은 것이다.


## Enum 타입 변수
enum 타입도 변수를 선언하고 대입할 수 있다.
```java
Rank myRank = Rank.FIRST;

Rank who = null;
```
위처럼 변수를 선언할 수 있고, `null`을 저장할 수 있다.(참조 타입이기 때문에)    


## Enum 타입 메서드

enum 타입은 `Enum` 이라는 클래스를 상속하여 기본 메서드를 제공한다.    

### name

- enum 객체가 가지고 있는 문자열을 반환한다. 이때 문자열은 객체를 선언할 때 명시한 상수 이름이다.
  
```java
Rank myRank = Rank.FIRST;
System.out.println(myRank.name());  // FIRST
```

### ordinal
- 해당 enum 객체가 enum 타입에서 몇 번째 순번인지 반환한다. 0부터 시작한다.    
- Rank의 순번은 아래와 같다.
  
```java
public enum Rank {
    FIRST, // 0
    SECOND, // 1
    THIRD, // 2
    FOURTH, // 3
    FIFTH // 4
}
```
그래서 아래의 결과를 확인할 수 있다.
```java
System.out.println(Rank.FIRST.ordinal()); // 0
System.out.println(Rank.THIRD.ordinal()); // 2
```

### compareTo
- 두 enum 객체간의 `순번`을 비교하여 순번의 `상대적 차이`를 반환하는 메서드이다. 
- 상대 객체보다 순번이 빠르면 `음수`, 아니면 `양수`가 반환된다.

```java
System.out.println(Rank.FIRST.compareTo(Week.THIRD)); // -2
System.out.println(Rank.FIFTH.compareTo(Week.SECOND)); // 3
```

### valueOf
- `name` 메서드와 반대로, 입력받은 문자열이 특정 enum객체의 상수명과 같은지 확인 후 그 객체를 반환한다.

```java
Rank myRank = Rank.valueOf("THIRD");
System.out.println(myRank); // THIRD
```

### values
- enum 타입에 선언된 모든 객체를 순번대로 `배열`로 만든다. 이번 로또 미션에서 유용하게 썼다.

```java
Rank[] ranks = Rank.values();
for (Rank rank : ranks) {
    System.out.println(rank);
}

/*
    FIRST
    SECOND
    THIRD
    FOURTH
    FIFTH
*/
```

## Enum 타입 필드
enum 타입의 객체도 인스턴스 이므로 `인스턴스 필드`를 가질 수 있다. 이를 통해 상수와 관련된 추가적인 데이터를 상수안에 포함하여 관리할 수 있다.    
상수 이름뒤에 `()`를 붙여 사용한다. 각 순위의 출력용 한국어 스트링과 상금을 추가해보겠다.
```java
public enum Rank {
    FIRST("1등", 5000),
    SECOND("2등", 4000),
    THRID("3등", 3000),
    FOURTH("4등", 2000),
    FIFTH("5등", 1000);

    private String korean;
    private int prize;

    Rank(String korean, int prize) {
        this.korean = korean;
        this.prize = prize;
    }
}
```

`Rank` enum 타입에 korean, prize 의 `private` 필드를 추가하였다. 아래와 같이 유용하게 사용 가능하다. (이 부분이 참 편한 것 같다고 생각했다.)

```java
Rank[] ranks = Rank.values();
for (Rank rank : ranks) {
    System.out.println(rank.korean+" (상금: " + rank.prize + "원)")
}

/*
    1등 (상금: 5000원)
    2등 (상금: 4000원)
    3등 (상금: 3000원)
    4등 (상금: 2000원)
    5등 (상금: 1000원)
*/
```

`private`로 필드들을 설정하여 캡슐화를 지키려고 하였고, enum 타입은 일반 클래스와 마찬가지로 메서드를 지원하므로, `getter`를 넣어주었다.
```java
public enum Rank {
    // ..

    public String getKorean() {
        return korean;
    }

    public int getPrize() {
        return prize;
    }
}
```

위와 같이 메서드를 정의하면, 아래와 같이 한 enum 객체에서 메서드를 호출할 수 있다.
```java
System.out.println(Rank.SECOND.getkorean()); // 2등
```

## Enum 생성자는 왜 private 인가?
> **로또 미션을 진행하면서, 생성자를 public으로 설정하면 IDE에서 빨간줄이 그어지길래 궁금해서 찾아보았다.**

자바에서 enum 타입은 열거형을 의미하는 특별한 형태의 클래스이다. 그렇기 때문에 일반 클래스와 같이 `생성자`가 필요하다. 물론, 생성자 없이도 자바가 default 생성자를 만들어주지만, enum의 경우엔 다른 클래스들과 달리 생성자의 접근 제어자를 `private`로 지정해야 한다.    
<br>
enum타입은 고정된 상수들의 집합으로서, 런타임이 아닌 `컴파일타임`에 모든 값을 알고 있어야 한다. 즉, 다른 패키지나 클래스에서 enum 타입에 접근해서 `동적으로 어떤 값을 정해줄 수 없다`. 따라서 컴파일 시에 타입 안정성이 보장된다.    
<br>
그러기에 해당 enum클래스 내에서 new 키워드로 인스턴스 생성이 불가능하다. 이러한 이유 때문에 생성자의 접근제어자를 private으로 설정해야 한다. 이렇게 되면 외부에서 접근 가능한 생성자가 없으므로 enum타입은 실제적으로 `final`과 다름이 없다. 결국 enum 타입은 인스턴스 생성을 제어하며, `싱글톤`을 일반화 한다. 이러한 특성 때문에 enum 타입은 싱글톤을 구현하는 하나의 방법으로도 사용된다고 한다.

<br>

## Enum의 사용으로 인한 if문 사용 줄이기
이 부분도 로또 미션을 하며 편리하다고 느낀 점인데, 원래는 5등이면 출력용 스트링 "5등" 을 반환하거나 출력하고, 해당하는 상금 1000 원을 반환하는 등의 로직을 `if문`으로 처리했을텐데, enum으로 세트로 묶어서 관리하니 코드의 가독성이 올라감과 동시에 유지보수에 용이해질 것이라고 생각했다.

### 🔗 Reference 

**[Java Enum 활용기](https://techblog.woowahan.com/2527/)**    
**[Java: enum의 뿌리를 찾아서...](https://www.nextree.co.kr/p11686/)**    
**[[Java] 열거 타입 (Enum)](https://hudi.blog/java-enum/)**    
