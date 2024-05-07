# 7주차 과제 - Spring 사전 조사

5/7 화요일 23:59 까지 제출해주시면 되겠습니다.

## 스프링의 장점 / 사용 이유
1. POJO기반의 구성(Plain Old Java Object)
- 코드개발시 개발자가 특정한 라이브러리나 컨테이너의 기술에 종속되지 않고 java코드를 이용해 객체를 구성하는 방식 그대로 스프링에서 사용 가능하다
따라서 자유럽게 객체지향적 설계를 구현할 수 있다. 높은 생산성과 유연한 테스트를 할 수 있는 장점을 가지게 된다.

2. DI(Dependency Injection, 의존성 주입)을 통한 객체 관계 구성
제어의 역전이 일어나는 것을 전제로 스프링 내부의 객체들 간의 관계를 관리할 때 사용. 자바에서는 인터페이스를 사용해 의존적 관계를 처리
-메소드나 객체(bean)의 호출 작업은 제어의 역전을 통해 외부에서 이루어진다
-제어의 역행을 전제조건으로 의존성 주입이 일어난다.
-의존성을 가진 객체에 대해 스프링에서 의존성 주입이 발생하도록 한다.
-의존성 주입 특징으로 인해 개발자가 POJO개발이 가능하게 된다.

컴포넌트 스캔과 자동 의존관계 설정
생성자에 @Autowired가 있으면 스프링이 연관된 객체를 스프링 컨테이너에서 찾아서 넣어준다
스프링이 시작될 때 @Controller, @Service, @Repository와 같은 컴포넌트들을 스캔하고 스플링에 빈으로 등록한다. 기본적으로 @Component가 있으면 스프링 빈으로 자동 등록 되며, 위의 세 어노테이션도 포함아고 있기에 스프링 빈으로 자동 등록 된다. 스프링 빈으로 등록된다면 "스프링 컨테이너에서 빈이 관리된다" 라고 말한다 이후에 @Autowired 어노테이ㅣ션으로 등록된 빈들의 의존관계 연결한다

의존성 주입의 방법 
1. 필드주입
2. setter 주입
3. 생성자 주입 (권장)

생성자 주입 권장 이유
1. 순환 의존성 확인: 필드 주입으로는 순환 의존성을 파악하기 어렵다. 생성자 주입하게되면 에러메세지가 뜨면서 서버 기동이 되지 않는다
2.부렵ㄴ성: 필드 주입은 final을 선언할 수 없지만 생성자 주입은 final을 선언함으로써 객체가 변하지 않도록 방지해준다
3. 단일 책임 원칙 위반 확인
@TrequriedArgsConstrusctor라는 어노테이션을 붙이면 final 필드나 @NonNull이 붙은 필드에 대해 생성자를 생성해 준다. 주로 의존성 주입의 편의성을 위해 사용된다

3.AOP(관점지향 프로그래밍) 지원
유지보수를 쉽게 하고 반복적인 코드를 줄이고 개발자가 핵심 비즈니스 로직에만 집중할 수 있도록 지원

4. 편리한 MVC구조

5. WAS에 독립적인 개발 환경

## 싱글톤, 싱글톤 컨테이너
싱글톤 이란 클래스의 인스턴스가 딱 `1개만 생성되는 것을 보장하는 디자인 패턴이다
따라서 객체 인스턴스가 1개여야하고 private 생성자를 사용해서 외부에서 임의로 new키워드를 사용하지 못하게 막아야 한다.
싱글톤 문제로는 구현 코드가 많이들어간다는 점. DIP위반, OCP위반, 테스트 어려움, 내부속성 변경or 초기화 어려움, private생성자로 자식 클래스 만들기 어려움, 결론적으로 유연성 떨어짐.


싱글톤 컨테이너 
스프릥 컨테이너는 싱글톤의 문제점 해결, 객체 인스턴스를 싱글톤(1개만 생성)으로 관리한다. 지금까지 우리가 학습한 스프링 빈이 바로 싱글톤으로 관리되는 빈이다.

스프링 컨테이너는  싱글톤 패전 적용 하지 않아도 객체 인스턴스 싱글톤으로 관리
싱글톤 관리하는 기능= 싱글톤 레지스트리
모든 단점을 해결하면서 객체를 싱글톤으로 유지할 수 있다.
스프링 컨테이너 덕분에 고객의 요청이 올 때마다 객체를 생성하는 것이 아니라, 이미 만들어진 객체를 공유해서 효율적으로 재사용할 수 있다.

주의점으로는 멀티쓰레드 환경에서의 동시성 문제 해결해야 함.
따라서 
1.상태를 유지하게 설계하면 안되고 무상태로 설계해야한다.
2. 의존적 필드가 있으면 안됨
3. 값을 변경할 수 잇는 필드가 있으면 안됨
4. 공유필드 대신 지역변수, 파라미터, ThreadLocal등을 사용해야한다
5. 스프링 빈의 필드에 공유 값을 설정하면 정말 큰 장애 발생 가능.

## 레이어드 아키텍쳐 패턴
백엔드 API를 구현할때 널리 쓰이는 패턴
레이어드 아키텍쳐로 API를 구현할때는 역할에 따라 독립된 모듈로 나누어서 코드를 구현한다.
일반적으로 presentation layer, business layer, persistence layer 이렇게 3개의 레이어가 있으며, 3개의 레이어가 층층히 의존도에 따라 연결되어 전체 시스템을 구성한다.

presentation layer
-클라이언트와 직접적으로 연결되는 부분, 웹사이트에서는 UI에 해단, 백엔드 API에서는 엔드포인트에 해당한다
-API의 엔드포인트를 정의하고 전송된 HTTP요청들을 읽어 들이는 로직 구현

business layer
-실제 시스템이 구형해야 하는 로직들 구현
- 필요한 데이터 생성, 수정, 읽기, 삭제 처리

persistence layer
- 데이터베이스와 관련된 로직 구현
- business layer에서 필요한 데이터의 생성, 수정, 읽기, 삭제를 처리하고 실제 데이터베이스에서 데이터 저장, 수정 읽어 들이는 역할.

레이어드 아키텍쳐 패턴 핵심 요소
1. 단방향적 의존성
presentation layer - business layer에만 의존
business layer- persistence layer에만 의존

같은 의미로
business layer - presentation layer에 독립적
persistence layer - business layer에 독립적

2.레이어드 역할의 분리
presentation layer에는 비즈니스 로직이 전혀 구현 되어있지 않으며,
비즈니스 로직을 처리하기 위해서는 presentation layer에서 business layer를 호출해야 한다.

## 스프링 어노테이션 10개 이상 정리
@SpringBootApplication
Srping Boot를 자동으로 실행시켜주는 어노테이션으로 Bean 등록은 두 단계로 진행된다.

@ComponentScan을 통해 Component들을 Bean으로 등록한다.
@EnableAutoConfiguration을 통해 미리 정의해둔 자바 설정 파일들을 Bean으로 등록한다.
(Bean은 스프링 IoC 컨테이너에 의해 인스턴스화되어 조립되거나 관리되는 객체)
@Configuration
스프링 IoC Container에게 해당 클래스가 Bean 구성 Class임을 알려주는 어노테이션이다.
@Bean을 해당 클래스의 메소드에 적용하면 @Autowired로 빈을 부를 수 있다.


@EnableAutoConfiguration
Spring Application Context를 만들 때 자동으로 설정하는 기능을 켠다.
classpath의 내용에 기반해서 자동 생성해 준다.
만약 tomcat-embed-core.jar가 존재하면 톰캣 서버가 setting된다.


@ComponentScan
@Component, @Service, @Repository, @Controller, @Configuration이 붙은 빈들을 찾아서
Context에 빈을 등록해 주는 어노테이션이다.
@Component 어노테이션이 있는 클래스에 대하여 bean 인스턴스를 생성한다.

Spring에서 @Component로 다 쓰지 않고 @Repository, @Service, @Controller등을 사용하는 이유는, 예를들어 @Repository는 DAO의 메소드에서 발생할 수 있는 unchecked exception들을 스프링의 DataAccessException으로 처리할 수 있기 때문이다.

또한 가독성에서도 해당 애노테이션을 갖는 클래스가 무엇을 하는지 단 번에 알 수 있다.


@Component
개발자가 직접 작성한 Class를 Bean으로 등록하기 위한 어노테이션이다.
@ComponentScan선언에 의해 특정 패키지 안의 클래스들을 자동 스캔하여 @Component 어노테이션이 있는 클래스들에 대하여 Bean 인스턴스를 생성한다.

@Bean
@Bean은 개발자가 직접 제어가 불가능한 외부 라이브러리 등을 Bean으로 만드려할 때 사용되는 어노테이션이다.

@Autowired
필드, setter 메소드, 생성자에 사용하며 Type에 따라 알아서 Bean을 주입해주는 역할을 한다.
객체에 대한 의존성을 주입시킨다.
@Autowired을 사용하면 스프링이 자동적으로 값을 할당한다.
Controller 클래스에서 DAO나 Service에 관한 객체들을 주입 시킬 때 많이 사용한다.

Bean을 주입받는 방식(3가지)
@Autowired
setter
생성자: @AllArgsConstructor 사용, 스프링에서는 생성자를 통한 방식을 권장한다.

@Controller
Spring MVC의 Controller로 사용되는, 클래스 선언을 단순화 시켜주는 어노테이션이다.


@RestController
Spring에서 Controller 중 View로 응답하지 않는 Controller를 의미한다.
method의 반환 결과를 JSON 형태로 반환한다.

@RestController가 적혀있는 Controller의 method는 HttpResponse로 바로 응답이 가능하다.
@ResponseBody 역할을 자동적으로 해주는 어노테이션이다.
@RestController  =  @Controller  +  @ResponseBody

@Service
Service class에서 쓰이는 어노테이션으로, 비즈니스 로직을 수행하는 Class라는 것을 나타내는 용도이다.

## Postman 설치해오기
완료
