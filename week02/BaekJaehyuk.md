# 1. 구조 

## 1.1 MVC 패턴

- `View`
- `Controller`
- `Service`
- `Repository`
- `Domain`

이 외 `Exception`, `DTO`, `계좌별 이자 정책` 등 각각의 역할을 분리하여 기능을 구현하도록 설계.

## 1.2 싱글톤 패턴

다음과 같이 의존관계를 생성

```java
    private static final Service bankservice = new BankService();
    private Repository centralBankRepo = CentralBank.getInstance();
```
## 1.3 메시지 코드

메시지를 관리하는 클래스를 생성하여 유지보수 및 코드변경에 유리하도록 설계

```java
    public static final String CHOICE = "아래 기능 중 하나를 선택하세요.";
    public static final String MENU= "(1) : 계좌 생성  (2) : 입금 (3) : 출금 (4) : 송금 (5) 계좌조회 (6) 프로그램 종료" ;
    public static final String ACCOUNT_TYPE= "(1) : 예금 계좌 , (2) : 적금 계좌";
    public static final String ACCOUNT_NUMBER = "계좌번호를 입력하세요. (8자리 입력)";
    public static final String ACCOUNT_OWNER = "이름을 입력하세요.";
    public static final String BALANCE = "금액을 입력하세요.";
```

## 1.4 예외처리

`Checked Exception`이 아닌 `Unchecked Exception`을 사용한 예외처리 구현

---

# 2. 클래스


View - `BankView` 
: 

1.  `AccountDTO` 을 통해 각각의 도메인에 값을 할당
2.  `DecimalFormat` 을 통해 계좌번호의 패턴을 설정.
3. 입력값을 받아 해당하는 기능을 실행시키도록 구현.


---

Repository (Interface) - `CentralBank(Impl)`
: 
1. `ArrayList`를 사용하지 않고, 계좌를 배열에 관리하기 위한`HashMap` 활용하여 계좌를 관리

---

InterestCalculator (Interface) : `NormalInterestCalculator(Impl)`, `SavingInterestCalculator(Impl)`
: 

1. SavingInterestCalculator
: 삼항연산자를 사용하여 잔액에 따른 이자율 책정

2. NormalInterestCalculator
: 숫자 범위에 따른 값 할당을 할 경우 사용하면 좋은 `NavigableMap<Object, Object>`을 사용하여 
금액에 따른 이자율 책정 로직을 구현 +  (`floor.Entry()` 사용하여 범위 값 할당)


---

BasicAccount, SavingAccount
: 

1. 요구사항에 맞춰 적금 계좌 클래스(`SavingAccount`)는 예금 계좌 클래스(`BasicAccount`)에서 상속을 받는다.

---

# 3. 기능

* 입금
* 출금 (적금계좌일 경우,목표금액을 달성했을 경우에 출금 가능)
* 송금
* 계좌 조회


---

# 4. 고민

이번 `Banking Service` 구현을 MVC 패턴을 지키면서 구현하면서 MVC 패턴을 지키는 것이 좋을까라는 의문이 생겼다. 관련해서 서치를 해보니 서비스 규모에 따라 MVC 패턴을 적용할 것인지 아닌지 중요하다는 점을 알게 되었다. 이번 4월 1일까지 구현한 코드가 지저분하고
관심사에 따른 역할이 완벽하게 구현이 되지 않아 크게 아쉬움을 가지고 있다. 해당 코드를 리팩토링을 통해 SOLID 원칙을 최대한 위반하지 않은 코드로 수정하고 싶다. 또한, 기능 요구사항 외의 예외처리를 다양하게 하고 싶다

또한, `View`에서 `switch-case` 문을 통해 기능을 선택하도록 구현하였다. HashMap을 사용하여 원하는 기능을 선택할까도 하였지만, 이 역시 관련하여 구글링을 하니 고정된 값일 경우 switch-case가 더 좋다고 알게 되었다.
추후에 `View` 관련 코드도 같이 수정할 예정이다.

기능 요구사항을 구현하면서 해당 기능을 단위로 테스트를 진행하고자 했지만, 조금 급하게 짜는 경향이 있어 `TDD 방식`의 기반의 개발을 하지 못한 점이 아쉽게 생각한다.


