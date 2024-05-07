# 2주차

---

**COW L2 백엔드 과정 JAVA 객체지향프로그래밍 과제**

뱅킹 시스템 (기본계좌 & 적금 계좌 포함)

→ 다형성을 이용하여 구현

구현기능 : 계좌 생성, 출금, 입금, 송금, 전체 계좌정보 출력, 해당 계좌 이자율 출력, 각각의 계좌에 이자 넣기

1. 입금할 금액을 적으라고 했는데 한글로 입력할 경우
2. 없는 계좌로 송금, 입금, 출금을 시도할 경우
3. 계좌 생성 시 계좌 형식에 알맞지 않는 경우 -> 계좌 번호 형식 ( 00000-00-00000 ) // 정규표현식 사용
4. 적금 계좌 생성 시 목표 금액을 현재 금액보다 낮게 설정하는 경우

## BigDecimal

---

- java 언어에서 숫자를 정밀하게 저장하고 표현하는 방법
    - 돈과 소수점을 다루기 위해 사용
- 단점 : 느린 속도 & 불편한 사용법
- Double의 문제점
    - Double은 기댓값과 실제값이 다르게 저장 ( 실제 저장값은 근사치 )
        - 십진수를 이진수로 변환하여 저장할 때 근사치 저장됨.
    
- 기본 용어
    - Precision : 전체 숫자의 자릿수
        - 전체 숫자 중 가장 왼쪽의 0이 아닌 수부터 끝나는 0이 닌 수로 끝나는 위치까지의 자릿수 ( 소수점 아래 포함 ) == unscale
    - Scale : 전제 소수점 아래 자릿수
        - 소수점 아래 첫째자리부터 0이 아닌 수로 끝나는 끝자리까지
    - DECIMAL128 : 부호와 소수점 수용 ( 최대 34자리까지 가능 ) - 10진수
- 기본상수
    
    ```java
    // 0
    BigDecimal.ZERO
    
    // 1
    BigDecimal.ONE
    
    // 10
    BigDecimal.TEN
    ```
    
- **초기화**
    - double 타입을 BigDecimal로 초기화
        - 문자열의 형태로 생성자에 전달하여 초기화
        - double타입 그대로 전달하면 다른 결과값 가짐
            
            ```java
            // double 타입을 그대로 초기화하면 기대값과 다른 값을 가진다.
            // 0.01000000000000000020816681711721685132943093776702880859375
            new BigDecimal(0.01);
            
            // 문자열로 초기화하면 정상 인식
            // 0.01
            new BigDecimal("0.01");
            
            // 위와 동일한 결과, double#toString을 이용하여 문자열로 초기화
            // 0.01
            BigDecimal.valueOf(0.01);
            ```
            
- 비교연산
    - 기본 타입이 아닌 오브젝트 ( double 비교 연산자 ‘==’ 안됨 )
        
        ```java
        // 값의 비교를 위해 사용, 소수점 맨 끝의 0까지 완전히 값이 동일해야 true 반환
        // false
        a.equals(b);
        
        // 값의 비교를 위해 사용, 소수점 맨 끝의 0을 무시하고 값이 동일하면 0, 적으면 -1, 많으면 1을 반환
        // 0
        a.compareTo(b);
        ```
        
- 사칙연산
    
    ```java
    // 더하기
    a.add(b);
    
    // 빼기
    a.subtract(b);
    
    // 곱하기
    a.multiply(b);
    
    // 나누기
    // java.lang.ArithmeticException: Non-terminating decimal expansion; no exact representable decimal result.
    a.divide(b);
    
    // 나누기
    //소수점 아래 3자리까지만 출력하고 그 아래 자리수는 반올
    a.divide(b, 3, RoundingMode.HALF_EVEN);
    
    // 나누기 후 나머지
    // 전체 자리수를 34개로 제한
    a.remainder(b, MathContext.DECIMAL128);
    
    // 절대값
    new BigDecimal("#").abs();
    
    // 두 수 중 최소값
    a.min(b);
    
    // 두 수 중 최대값
    a.max(b);
    ```
    
- 소수점 처리
    - RoundingMode.HALF_EVEN은 기본 반올림을 사용하는 알고리즘 사용
    - 반올림 정책을 명확히 명시하여 개발
        
        ```java
        // 소수점 이하를 절사한다.
        // 1
        new BigDecimal("1.1234567890").setScale(0, RoundingMode.FLOOR);
        
        // 소수점 이하를 절사하고 1을 증가시킨다. ( 올림 )
        // 2
        new BigDecimal("1.1234567890").setScale(0, RoundingMode.CEILING);
        
        // 음수에서는 소수점 이하만 절사한다. ( 내림 )
        // -1
        new BigDecimal("-1.1234567890").setScale(0, RoundingMode.CEILING);
        
        // 소수점 자리수에서 오른쪽의 0 부분을 제거한 값을 반환한다.
        // 0.9999
        new BigDecimal("0.99990").stripTrailingZeros();
        
        // 소수점 자리수를 재정의한다.
        // 원래 소수점 자리수보다 작은 자리수의 소수점을 설정하면 예외가 발생한다.
        // java.lang.ArithmeticException: Rounding necessary
        new BigDecimal("0.1234").setScale(3);
        
        // 반올림 정책을 명시하면 예외가 발생하지 않는다.
        // 0.123
        new BigDecimal("0.1234").setScale(3, RoundingMode.HALF_EVEN);
        
        // 소수점을 남기지 않고 반올림한다.
        // 0
        new BigDecimal("0.1234").setScale(0, RoundingMode.HALF_EVEN);
        // 1
        new BigDecimal("0.9876").setScale(0, RoundingMode.HALF_EVEN);
        ```
        

## HashMap

---

- Hashmap은 데이터를 저장할 때에 Key와 Value를 짝을 이루어 저장.
- Key 값으로 Hash함수를 실행한 결과를 통해 저장위치 결정
- 데이터의 저장 위치를 해시 함수를 통해 알 수 있어 추가, 삭제, 검색이 빠르다.

값 추가

```java
HashMap<Integer,String> map = new HashMap<>();//new에서 타입 파라미터 생략가능
map.put(1,"사과"); key : 1 // Value : 사과
```

값 삭제

```java
map.remove(1); //key값 1 제거
map.clear(); //모든 값 제거
```

값 출력

```java
map.get(1) //key값 1의 value얻기 // 특정 값 반환

getKey(); // 현재 Hashmap의 Key값 출력
getValue() // 현재 Hashmap의 value값 출력

map.entrySet() // HashMap의 전체 반환 // Key && Value
map.keySet() // HashMap의 전체 Key값 반환 // Only Key
map.values() // HashMap의 전체 Value값 반환 // Only Value
```

값 변환

```java
map.replace(1, "바나나"); // 해당 key값에 해당하는 Value값 변환
```

값 존재여부 확인

```java
map.containsKey(1); // HashMap에 해당 key 값 존재히면 true 반환, else false 반환
map.comtainsValue("사과"); // HashMap에 해당 Value 값 존재히면 true 반환, else false 반환
```

## 정규표현식 ( Regular Expressiong)

---

특정 문자열의 규칙을 가지는 문자열의 집합을 표현하는데 사용되는 언어

| 기호        | 설명                                  | 예제                                                               |
|-----------|:------------------------------------|------------------------------------------------------------------|
| .         | 임의의 문자 1개                           |                                                                  |
| ^         | 시작을 의미, [ ] 안에 있으면 부정의 의미           | ^a : a로 시작하는 단어, [^a] : a가 아닌 철자 문자 1개                           |
| $         | $앞의 문자열로 문자가 끝나는지를 의미               | a$ : a로 끝나는 단어                                                   |
| ?         | 앞의 표현식이 없거나 or 최대 한개만               | a1? : 1이 최대 한개만 있거나 없을수도 있다. → a(o), a1(o), a11(x), a111(x)      |
| *         | 앞의 표현식이 없거나 or 있거나 (여러개)            | a1* : 1이 있을수도 없을수도 있다. → a(o), a1(o), a11(o), a111(o)            |
| +         | 앞의 표현식이 1개 이상 or 여러개                | a1* : 1이 1개 이상있다. → a(x), a1(o), a11(o), a111(o)                 |
| [ ]       | [ ] 안의 문자가 존재하는지를 의미                | [ab][cd] : a,b 중 한 문자와 c,d 중 한 문자                                |
| [^]       | [] 대괄호 안에 ^ 문자가 있으면, 제외를 뜻함         | [^a-z] : 알파벳 소문자 a부터 z까지를 제외한 모든 문자                              |
| -         | 사이의 문자 혹은 숫자를 의미한다                  | [a-z] : 알파벳 소문자 a부터 z까지 하나, [a-z0-9] : 알파벳 소문자 전체,0~9 중 한 문자     |
| shift + \ | 또는                                  | [a                                                               |b] : a 혹은 b |
| ( )       | 그룹                                  | 01(0                                                             |1) : 01뒤에 0 또는 1이 들어간다→ 010(o), 011(o), 012(x) |
| (?:)      | 찾지만 그룹에 포함 안됨                       |                                                                  |
| (?=)      | =앞 문자를 기준으로 전방 탐색                   |                                                                  |
| (?<=)     | =뒤 문자를 기준으로 후방 탐색                   |                                                                  |
| { n }     | 딱 n 개 존재                            | a{3}b : a가 3번 온 후 b가 온다. → aab(x), aaab(o), aaaab(o)             |
| { n, m}   | n개 이상 m개 이하                         | a{3,5} : a가 3개 이상 5개 이하 있다. → aa(x), aaa(o), aaaa(o), aaaaaaa(x) |
| { n, }    | n개 이상 존재                            | a{3,} : a가 3개 이상 있다. → aa(x), aaa(o), aaaa(o), aaaaaaa(o)        |
| \b        | 공백, 탭, ",", "/" 등을 의미한다             | apple\b : apple뒤에 공백 탭등이 있다. → apple juice (o), apple.com (x)    |
| \B        | \b의 부정, 공백 탭등이 아닌 문자인 경우 매치한다       | apple\B → apple.com (o)                                          |
| \d        | 0~9 사이의 숫자 // [0-9]와 동일             |                                                                  |
| \D        | \d의 부정숫자가 아닌 어떤 문자 // [^0-9]와 동일    |                                                                  |
| \s        | 공백, 탭                               |                                                                  |
| \S        | 공백, 탭이 아닌 문자                        |                                                                  |
| \w        | 알파벳 대소문자+숫자+"_" // [a-zA-Z_0-9]와 동일 |                                                                  |
| \W        | \w의 부정 // [^a-zA-Z_0-9]             |                                                                  |

자주 사용하는 정규식

| 정규 표현식                     | 설명     |
|----------------------------|--------|
| ^[0-9]*$                   | 숫자     |
| ^[a-zA-Z]*$                | 영문자    |
| ^[가-힣]*$                   | 한글     |
| \\w+@\\w+\\.\\w+(\\.\\w+)? | E-Mail |
| ^\d{2,3}-\d{3,4}-\d{4}$    | 전화번호   |
| \d{6} \- [1-4]\d{6}        | 주민등록번호 |
| ^\d{3}-\d{2}$              | 우편번호   |

### String 클래스의 정규식 문법

---

| String 정규식 메소드                                      | 설명                                                           |
|-----------------------------------------------------|--------------------------------------------------------------|
| boolean matches(String regex)                       | 인자로 주어진 정규식에 매칭되는 값이 있는지 확인                                  |
| String replaceAll(String regex, String replacement) | 문자열내에 있는 정규식 regex와 매치되는 모든 문자열을 replacement 문자열로 바꾼 문자열을 반환 |
| String[] split(String regex)                        | 인자로 주어진 정규식과 매치되는 문자열을 구분자로 분할                               |

EX

```java
// matches (일치하는지 확인)
String txt = "123456";

boolean result1 = txt.matches("[0-9]+"); // 숫자로 이루어져 있는지

System.out.println(result1); // true
```

```java
// replaceAll (정규표현식과 일치하는 모든 값 치환)
String txt2 = "power987*-;";

// 영문자와 숫자를 제외한 문자를 모두 0으로 치환
String result2 = txt2.replaceAll("[^a-z0-9]","0"); 

System.out.println(result2); // power987000
```

```java
// split (정규표현식과 일치하는 값 기준으로 나누기)
String txt3 = "power987*-;";

String[] txts = txt3.split("[0-9]+"); // 숫자부분을 기준으로 분할

System.out.println(txts[0]); // power
System.out.println(txts[1]); // *-;
```

### Pattern 클래스

---
문자열을 정규 표현식 패턴 객체로 변환

| Pattern 클래스 메소드                                      | 설명                                   |
|------------------------------------------------------|--------------------------------------|
| compile(String regex)                                | 정규표현식의 패턴을 작성                        |
| matches(String regex, CharSequence input)            | 정규표현식의 패턴과 문자열이 일치하는지 체크일치할 경우 true, |
| 일치하지 않는 경우 false를 리턴(일부 문자열이 아닌 전체 문자열과 완벽히 일치 해야한다) |                                      |
| asPredicate()                                        | 문자열을 일치시키는 데 사용할 수있는 술어를 작성          |
| pattern()                                            | 컴파일된 정규표현식을 String 형태로 반환            |
| split(CharSequence input)                            | 문자열을 주어진 인자값 CharSequence 패턴에 따라 분리  |

```java
// 문자열 형태의 정규표현식 문법을 정규식 패턴으로 변환
String patternString = "^[0-9]*$";

// Pattern 객체로 컴파일된 정규식은 뒤의 Matcher 클래스에서 사용된다
Pattern pattern = Pattern.compile(patternString);
```

```java
// 샘플 문자열
String txt1 = "123123";
String txt2 = "123이것은숫자입니다00";

// 첫번째 매개값은 정규표현식이고 두번째 매개값은 검증 대상 문자열
boolean result = Pattern.matches("^[0-9]*$", txt1);
System.out.println(result); // true
boolean result2 = Pattern.matches("^[0-9]*$", txt2);
System.out.println(result2);// false
```

### Matcher 클래스

---
대상 문자열의 패턴을 해석하고 주어진 패턴과 일치하는지 판별하고 반환된 필터링된 결과값들을 가짐

| Matcher 클래스 메소드  | 설명                                                         |
|------------------|------------------------------------------------------------|
| find(int start)  | start 위치 이후부터 매칭검색, 패턴이 일치하는 경우 true를 반환, 불일치하는 경우 false반환 |
| start(int group) | 지정된 그룹이 매칭되는 시작위치 반환                                       |
| end(int group)   | 지정된 그룹이 매칭되는 끝위치의 다음 문자위치 반환                               |
| group(int group) | 그룹화되어 매칭된 패턴중  group 번째 부분 반환                              |
| groupCount( )    | 괄호로 지정해서 그룹핑한 패턴의 전체 개수 반환                                 |
| matches( )       | 패턴이 전체 문자열과 일치할 경우 true반환                                  |