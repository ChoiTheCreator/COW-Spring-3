1. 잔액이 출금요청액보다 작을 경우를 조건문으로 처리할까, 예외를 발생시켜 처리할까
    조건문으로 처리할 경우 가독성이 증가하고 빠름 / 예외발생은 구조적 통일성?
2. 생각해보니 1)의 내용을 판단하는 주체는 계좌가 아닌 은행이어야함.
  -> 이 경우를 생각해보니 예외처리가 맞는것 같음. ∵ 입금액을 한글로 입력한 것과 같이 입력값의 오류이므로, 또한 다시 입력기회를 줘야하므로 동일하게 처리해야할듯
   -> 더 생각해보니 예외사항이 맞나..? 충분히 예상가능한값이므로 정규표현식으로 유효성검증을 해야하지않을까?
4. 아예 BigDecimal의 서브클래스를 만들어 subtract()를 재정의하고 compareTo<0인 경우 예외처리를 하면 어떤가?
  -> 코드가 간결해지고 예외 발생의 위치가 논리적으로 옳은방향인듯 하지만 추후 마이너스통장이 추가되거나 기타 상황들에서 문제가 발생할 수 있어 좋은구조는 아닌듯함
5. 입,출금 메서드는 데이터무결성이 중요한 파트임 -> 여러 은행 객체가 동시에 계좌에 접근하면 안되고, 입출금 과정중 문제가 발생한 부분이 생기면 실행 이전의 상태를 복구해놔야함. 1) 아직 쓰레드를 사용하지 않으므로 synchronized를 사용하진 않아도됨. 2) 입출금메서드가 boolean을 리턴해서 성공여부를 파악 가능하도록함. 
6. 이자율 테이블을 변동가능하게 하고 싶은데, 어렴풋하게만 그려져서 일단 구현은 뒤로미룸.
7. AccountNum을 객체로 만드는게 좋을거같음. => Branch ∵ 1) 앞의자리 0이 생략되지 않아야하고, 2) 각 부분이 의미를 가짐 3) 옳은지 검증하는 checkDigit (verifyNum)이 존재


---
생성형 AI사용

1. 사용쿼리 : 프로그래밍을 연습하면서 가상의 은행시스템을 만들거야. 계좌번호를 발급하는 규칙을 만들어줘.
    가상 은행 시스템을 만들 때 사용할 수 있는 일반적인 계좌번호 발급 규칙은 다음과 같습니다. 이러한 규칙은 보안과 중복을 방지하기 위해 설계됩니다.
    
    1. **고유성 보장**: 모든 계좌 번호는 고유해야 합니다. 중복된 계좌 번호가 있으면 안 됩니다.
    
    2. **길이와 형식 지정**: 계좌 번호의 길이와 형식을 지정합니다. 일반적으로 숫자로만 이루어진 길이가 일정한 형식을 가집니다.
    
    3. **지역 식별자**: 계좌 번호의 일부는 해당 은행이나 지역을 식별할 수 있는 정보를 포함할 수 있습니다. 이는 보안을 강화하고 은행 시스템을 운영하는 데 도움이 될 수 있습니다.
    
    4. **체크 디지트**: 계좌 번호의 유효성을 검사하기 위한 체크 디지트를 포함할 수 있습니다. 이를 통해 입력 오류를 방지하고 데이터 정합성을 유지할 수 있습니다.
    
    5. **랜덤 발급**: 계좌 번호를 무작위로 발급하여 보안성을 높일 수 있습니다. 또한 일련 번호를 통해 계좌 번호를 쉽게 예측할 수 없도록 합니다.
    
    예를 들어, 다음은 일반적인 계좌 번호 형식의 예입니다:
    
    - **은행 코드**: 3자리 숫자로 구성됩니다.
    - **고객 번호**: 7자리 숫자로 구성됩니다.
    - **체크 디지트**: 1자리 숫자로 구성됩니다.
    
    이러한 경우, 계좌 번호는 총 11자리로 구성됩니다. 이 숫자는 각 은행에서 고유하게 할당됩니다. 각 은행은 자체적으로 이러한 계좌 번호를 생성하고 관리할 수 있습니다.

2. 사용쿼리 : 계좌번호에 올바른 검증숫자(11번째 수)를 만들어주는 메소드
    String createVerifyNum(String accountNum)를 완성해줘

    ```
    private String createVerifyNum(String accountNum) {
    int[] weights = {2, 3, 4, 5, 6, 7, 8, 9, 2, 3, 4}; // 각 숫자에 곱해질 가중치

    int sum = 0;
    for (int i = 0; i < accountNum.length(); i++) {
        char digit = accountNum.charAt(i);
        if (Character.isDigit(digit)) {
            int num = Character.getNumericValue(digit);
            sum += num * weights[i];
        } else {
            // 숫자가 아닌 경우 처리
            // 여기서는 예외처리 없이 그냥 패스하도록 함
        }
    }

    int remainder = sum % 11;
    int verifyNum;
    if (remainder == 0)
        verifyNum = 0;
    else if (remainder == 10)
        verifyNum = 'X';
    else
        verifyNum = 11 - remainder;

    return String.valueOf(verifyNum);
    }
```

