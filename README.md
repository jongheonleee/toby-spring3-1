# 🐸 Toby's Spring

## 01. ⚙️ 스프링의 이해와 원리

## 📋 목차
> ### 📌 1. 오브젝트와 의존관계 : oop를 충족하기 위해 오브젝트에 집중
> ### 📌 2. 테스트 : 
> ### 📌 3. 템플릿 : 
> ### ⭐️ 4. 예외 : 
> ### ⭐️ 5. 서비스 추상화 : 
> ### ⭐️ 6. AOP :
> ### 📌 7. 스프링 핵심 기술의 응용 :
> ### 📌 8. 스프링이란 무엇인가? :
> ### 📌 9. 스프링 프로젝트 시작하기 :

<br>
<br>

## 📌 1. 오브젝트와 의존관계 : oop를 충족하기 위해 오브젝트에 집중

### 1-1. 초난감 DAO

> ### 👉 분리하지 않은 코드, 변경에 대처하기 어려움

- [UserDao 코드] 

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/5b4c9897-878d-45c5-af48-7986ffd26638" width="500" height="500"/>


- 사진에서 보는 것과 같이, 중복된 코드들이 많이 있음
- 또한, 커넥션 조회, 쿼리문 생성, 실행, 자원 반납 등 등 여러 역할을 담당
  - SRP(단일 책임 원칙)원칙 준수 못함 -> 하나의 엑터에 대해서만 책임 져야함
- 이 코드의 문제점은 변경 포인트가 매우 많음, 즉 oop 프로그래밍이 아님


- [테스트용 main()]

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/8de2cd5c-9533-4cf3-9ad3-08dacbd4a9f4" width="500" height="500"/>


- 테스트를 main(애플리케이션 실행 함수)에서 실행함
- 테스트 또한 분리해서 관리해야함, TDD


> ### 🧑🏻‍🏫 위의 코드를 oop 원칙에 의거하여 개선. 따라서, 변경의 범위를 최소화 

### 1-2. DAO의 분리

> ### 👉 관심사의 분리, 분리와 확장을 고려한 설계
- 분리 : 변경과 발전은 한 번에 한 가지 관심사항에 집중
    - 변경이 일어났다면, 그 지점에만 작업하기 위함
    - 한 가지 관심이 한 군데에 집중되게 하는 것(SRP)
    - 핵심은 관련도 높은 것끼리 그룹핑하고 서로 다른 부분은 분리해내는 것 
      - 같은 관심에 효과적으로 집중


> ### 👉 커넥션 만들기의 추출

- [add() 메서드]

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/401f17d6-81ee-405e-8ad7-a6b836f70d11" width="500" height="500"/>


- add()는 세가지 관심사항이 존재 -> 💥 SRP 위반 
  - (1) 커넥션을 어떻게 조회
  - (2) SQL문을 담는 Statement 생성 및 실행
  - (3) 자원 반납

- get() 등등 중복 코드 발생 

- [메서드 추출]

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/9d078db7-caf1-4d2d-a4e7-cf7ffbdcf8b2" width="500" height="500"/>

- 가장 간단한 방법, 중복 코드는 한 메서드로 정의해서 사용
  - 변경이 발생하면 해당 메서드만 변경하면 됨. 즉 변경 범위 축소

> ### 👉 변화를 반기는 DAO. 즉, OCP 원칙 충족(DB 커넥션 만들기의 독립 )
- (1) 상속을 통한 확장 -> Template Method, 생성과 사용 분리 -> Factory Method 

- [상속을 통한 UserDao 확장]
<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/726f05b4-858c-4315-a7fe-7b72f093c54f" width="500" height="500"/>

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/a8e1080f-f07e-455b-bc3c-24d0538452b2" width="500" height="500"/>

- Template Method : 상위에 불변, 하위에 가변으로 분리함. 상속을 통해 코드를 완성
- Factory Method : 구체적인 객체 생성 방법을 결정, 사용과 생성을 분리 
- 위의 패턴을 적용해서 OCP 충족 시킴. 즉, 패턴을 통해 관심사항이 다른 부분을 분리하고 서로 독립적으로 변경/확장할 수 있게 설계함
- 하지만, 위 패턴의 단점이 있음 그것은 바로 상속 때문
  - (1) 상위에 변경이 발생하면 하위에도 영향이 미침
  - (2) getConnection()를 다른 Dao 클래스에 적용 못함 


### 1.3 Dao의 확장 

> ### 👉 클래스의 분리, 외부로부터 주입 받기 
- [인터페이스 도입]

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/e2f8ca4d-ca18-4697-8019-638b9b52f59c" width="500" height="500"/>

- [불필요한 의존관계]

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/c44ee4ac-e363-4699-9791-98a23b460aa6" width="500" height="500"/>

  - 생성자 내부에 구체 코드 존재. connectionMaker = new DConnectionMaker()
  - 이를 분리해야함(관계설정은 외부로)

- [관계설정 분리]

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/4f2e723e-353f-4666-bff7-1b05a04122a7" width="500" height="500"/>
<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/87555114-c16e-4b40-b379-86938e51b4ee" width="500" height="500"/>


- 역할이 다르거나 변경 주기가 다른 것을 클래스 단위로 재정의해서 분리
- 관심사의 분리, 관계 설정을 해주는 것을 외부로 분리
  - 상속 방식보다 더 유연함 

> ### 👉 프로그래밍에서는 둘 중에 하나를 선택해야함 - 최적화, 유연함 
- 높은 응집도 : 분리가 안되어 있음, 강결합, 직접 결합(인터페이스 x), 성능 좋음, 유연함 x
- 낮은 결합도 : 간접 결합 (인터페이스 o), 약결합, 성능 안좋음, 유연함 o, 유지 보수성 좋음 
  - 성능 향상은 약결합을 강결합으로 전환 
- 전략 패턴 : 알고리즘을 외부에서 주입 
  - (1) iv로 받음(생성자)
  - (2) 매개변수로 받음(메서드)


### 1.4 제어의 역전(IoC)

> ### 👉 오브젝트 팩토리를 통한 관심사의 분리 -> '생성'과 '사용' 분리 

- [팩토리로 '생성'과 '사용' 분리]

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/21893cc9-091e-4921-9343-6744c0f47fa3" width="500" height="500"/>

- 현재 관계설정 책임을 UserDaoTest가 갖고 있음. 이를 분리해야함
  - 해당 객체는 Test를 위해 존재함
- 이를 오브젝트 팩토리로 분리
  - 팩토리 : 객체의 생성 방법을 결정, 만들어진 객체를 반환  
  - '생성'과 '사용' 분리(관심사의 분리)

- [팩토리 설계도 구조]

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/16ee584b-f1fa-4df0-9b3f-c5eaae3468f7" width="500" height="500"/>

- UserDao 변경 영향 안 받음, DB 연결 방식 확장 가능 

> ### 👉 제어의 역전, 제어 흐름 구조가 뒤바뀜
- [제어의 역전]

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/e034ef06-5b60-443f-9c37-eb85fea0d76d" width="500" height="500"/>

- 객체 스스로 사용할 객체를 선택하지 않음, 모든 제어 권한은 다른 대상으로 위임
- 앞서 만든 UserDao, DaoFactory에도 제어의 역전 적용됨
  - UserDao : ConnectionMaker 생성했음 -> 이를 DaoFactory에 위임
- 제어의 역전에서는 컴포넌트의 생성과 관계설정, 사용, 생명주기 관리 등을 관장하는 존개가 필요

### 1.5 스프링의 IoC

> ### 👉 애플리케이션 컨테스트(빈 팩토리)로 '생성'과 '사용'을 분리

- [스프링 빈 팩토리 활용]

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/ccd88758-1dbb-486c-a8da-706e55bd59a6" width="500" height="500"/>

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/fca998a7-7832-4e1e-9012-3095e0321aea" width="500" height="500"/>


- 애플리케이션 컨테스트 : 빈의 생성과 관계설정 같은 제어를 담당하는 IoC 오브젝트 
- 위의 코드는 자바 코드이지만 실질적으로는 XML과 같은 스프링 전용 설정정보

- [애플리케이션 컨텍스트 동작 원리]

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/46d3ddd4-c74f-41a2-a271-607a837edff0" width="500" height="500"/>

- 애플리케이션 컨텍스트로 '생성'과 '사용'을 분리했을 때의 장점
  - (1) 클라는 구체 팩토리를 몰라도됨
  - (2) IoC 서비스 제공
    - 생성, 관계 설정, 생성 방식, 시점과 전략을 다르게 할 수 있음, ...
    - 오브젝트를 효과적으로 활용 가능 
  - (3) 빈을 검색하는 다양한 방식 제공
    - 이름 탐색, 타입 탐색, 애너테이션으로 탐색 
  

> ### 👉 스프링 IoC 용어 정리
- 빈 : 스프링이 IoC 방식으로 관리하는 오브젝트
- 빈 팩토리 : 스프링의 IoC를 담당하는 핵심 컨테이너, 이를 확장한 것이 애플리케이션 컨텍스트
- 애플리케이션 컨텍스트 : 빈 팩토리 확장버전, 스프링이 제공하는 서비스 추가로 제공 
- 설정정보/설정 메타정보 : 애플리케이션 컨텍스트가 IoC를 적용하기 위한 메타 정보 
- 컨테이너/ IoC 컨테이너 : IoC 방식으로 빈을 관리하는 컨테이너를 의미
- 스프링 프레임워크 : 스프링이 제공하는 모든 기능을 통틀어 말함

### 1.6 싱글톤 레지스트리와 오브젝트 스코프 


> ### 👉 스프링 IoC 컨테이너는 기본적으로 빈을 싱글톤으로 관리 
- 스프링의 애플리케이션 컨텍스트의 특징, "빈을 기본적으로 싱글톤 타입으로 등록"
- 싱글톤 레지스트리 : 싱글톤을 저장하고 관리하는 컨테스트를 지칭
- 매번 클라가 요청할 때마다 빈을 생성하는 것은 비효율적임, 서버 과부화 걸릴 수 있음. 이를 해결하고자 빈을 싱글톤으로 등록해서 공유
  - 멀티 쓰레드 환경에서 iv를 가지는 빈은 위험함. 따라서 4가지 방식으로 대처
    - (1) 동기화 처리된 객체를 iv로 갖어야함
    - (2) 상수 사용, 메서드로 계산된 값 반환
    - (3) iv 없애기
    - (4) Copy On Write(읽기 전용)

> ### 👉 싱글톤 패턴은 안티 패턴, 즉 사용에 주의해라 
- 싱글톤 패턴의 단점
  - (1) private 사용 -> 상속 x
  - (2) 테스트 어려움 
  - (3) 서버 환경에서 하나만 생성되는 것을 보장하지 못함
  - (4) 싱글톤을 전역 상태로 만들 수 있음

- 멀티 쓰레드 환경에서 주의 
  - 싱글톤은 iv 없는 게 좋음 
  - 싱글톤은 iv를 read-only로 만듦

> ### 👉 스프링 빈의 스코프 -> 싱글톤 스코프, 프로토타입 스코프 
- 스코프 : 빈이 생성, 존재, 적용되는 범위를 의미함
  - 싱글톤 스코프 : 컨테이너 내에 한 개의 오브젝트만 만듦, 한 스프링 컨테이너가 존재하는 동안 계속 유지 
  - 프로토타입 스코프 : 빈을 요청할 때마다 새로운 오브젝트 만듦

### 1.7 의존관계 주입(DI)

> ### 👉 스프링이 제공하는 IoC 방식의 핵심을 짚어주는 용어, 의존관계 주입(DI)
- 의존관계 주입 : 외부에서 오브젝트 주입하는 방식, 보다 명료한 표현은 "의존관계 설정"
  - (1) 인터페이스에만 의존
  - (2) 의존관계는 제3의 존재가 결정(팩토리, 컨테이너)
  - (3) 참조를 외부에서 제공(주입)해줌으로써 만들어짐 

- 핵심은, 관심사의 분리 -> '사용'과 '생성'

- [의존관계]
  
<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/21943eb3-22e7-4dc1-aa05-810cbf4d94a5" width="500" height="500"/>

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/672d9bbf-4c7a-4402-b509-0d3faa9ac2d0" width="500" height="500"/>

- A가 B를 알고 있음, 의존하고 있음. 이는 B가 변경되면 A에도 영향이 미침을 의미함 

- [의존관계 주입]

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/c314bb43-c200-47a9-a83e-d87325554b0b" width="500" height="500"/>

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/56d593c9-6b00-4f24-90eb-bde0f0c1813f" width="500" height="500"/>



- DI : 의존관계 주입, DL : 객체를 찾는 것 
- 애플리케이션 컨텍스트에서는 빈을 검색하는 방법이 2가지가 있음
  - 이름 - 맵의 등록된 이름 확인 
  - 타입 - 맵의 등록된 빈의 타입 확인 


- [의존관계가 추가된 DI 설정용 클래스]

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/89885159-dd74-451d-8d80-768d6d6fb6e6" width="500" height="500"/>

> ### 👉 메서드 주입 방법 3가지 - 생성자, setter(), 일반 메서드 




### 1.8 XML을 이용한 설정 

> ### 👉 애플리케이션 컨텍스트는 XML을 DI 정보로 활용 가능
- [connectionMaker() -> <bean> 전환]

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/2d1a516a-c75a-451c-8ee0-95f84195c6db" width="500" height="500"/>


<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/7a9cb779-d663-438b-905f-d5c24da54a88" width="500" height="500"/>

- @Configuration = <beans>
- @Bean methodName() = <bean id="methodName"..
- return new BeanClass() = class = 'a.b.c...BeanClass'>

> ### 👉 프로퍼티 값의 주입 

- [DB 연결정보 설정]

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/207ece15-a2f8-41a0-9996-4b396b4735cf" width="500" height="500"/>

- XML에 있는 값은 모두 문자열


> ### 🧑🏻‍🏫 정리
> ### 초난감 코드 -> 관심사의 분리, 리팩토링
> ### 변경에 대한 분리 -> 전략 패턴 -> OCP 충족 
> ### 서로 관련 있으면 그룹핑 아니면 분리 -> SRP 충족
> ### 생성/사용의 분리 -> 제어권을 팩토리를 만들어서 넘김
> ### 싱글톤은 안티패턴 -> 싱글톤 레지스트리 적용
> ### 런타임에 의존관계 형성 -> DI
> ### 오브젝트 주입 -> 생성자, setter
> ### XML을 통해 DI 처리 


## 📌 2. 테스트 : 내 코드의 확신과 개발 시간을 단축해줌(스프링의 핵심은 OOP 와 테스트)

### 2.1 의존관계 주입(DI)


> ### 👉 테스트의 의미? "내 코드에 대한 확신"
- 테스트란? 내가 예상하고 의도했던 대로 코드가 정확히 동작하는지를 확인해서, 만든 코드를 확신할 수 있게 해주는 작업



> ### 👉 테스트 코드에서 중요점 
- 테스트의 핵심 
  - (1) 꼼꼼함
  - (2) 자동화
- 테스트는 단계별로 이루어져야함, 테스트 코드 -> 구현 -> 검증 -> 리팩토링
- 단위 테스트 : 작은 단위로 쪼개서 특정 기능에 대해서 테스트 진행
  - 의도한 대로 코드가 동작하는지 빠르게 피드백 받기 위함
- 통합 테스트 : 단위 테스트 전체를 통해해서 테스트, 단위 테스트 -> 통합 테스트
- 테스트 커버리지 : 테스트에서 사용되는 코드가 얼마나 되는지 확인(꼼꼼함)
- 자동수행 테스트 코드 : TDD의 핵심, 테스트 코드 자동화, 버튼 하나로 간단하게 테스트 진행, 범위가 넓어야함
- 지속적인 개선 : 리팩토링, 점진적인 개발 : 단계적 개발

> ### 👉 UserDaoTest 문제점과 개선 
- [main() 메서드로 작성된 테스트]

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/5bb59e5a-df2e-4a97-b0b6-acd162097ea9" width="500" height="500"/>


- 위 코드의 문제점
  - (1) 수동 확인 
  - (2) 실행 불편함 

- 개선 -> 테스트 검증의 자동화
- [수정전 테스트 코드]

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/0fa0784d-4345-41e0-947a-5afc564fe89d" width="500" height="500"/>


- [수정후 테스트 코드]

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/75c96a06-b953-4a0d-b3ed-5bca995b717d" width="500" height="500"/>



> ### 👉 Junit -> 테스트의 효율적 수행과 결과 관리
- Junit : 자바로 단위 테스트를 만들 때 유용함 
- 테스트의 중요 규칙
  - (1) 테스트 결과의 일관성 : 외부 환경에 독립적, 코드 변동 없으면 항상 동일한 결과 
  - (2) 동일한 결과를 보장 : 단위 테스트는 항상 일관성 있는 결과가 보장되야함, 테스트 환경, 순서 ,, 이 변동되도 테스트 결과는 동일 
  - (3) 포괄적인 테스트 : 한번에 한 가지 검증 목적에 충실, 매실행마다 검증, 
    - 성공하는 경우/실패하는 경우 
  - (4) 예외에 대한 테스트 : 발생 가능한 예외들을 분석하고 테스트
  
- [Junit을 적용한 UserDaoTest]

<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/3b9594d4-0a85-438a-b4ae-281dc9062312" width="500" height="500"/>

> ### 👉 TDD -> 테스트 주도 개발(테스트 -> 구현)
- 테스트 코드를 먼저 작성, 테스트를 성공하게 해주는 코드를 작성하는 방식
- TDD에선 테스트 작성하고 성공 시키는 코드를 만드는 작업의 주기를 가능한 짧게 가지는 것을 권장
- TDD의 장점 중 하나는 코드를 만들어 테스트를 실행하는 그 사이 간격이 매우 짧음 

- [given / when / then]
<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/d752067f-58f3-4d1f-898f-499ead602000" width="500" height="500"/>

- 테스트를 작성할 때 요령, 하나의 흐름(시나리오)을 테스트
  - given : 전제 조건
  - when : 실행
  - then : 결과 

- 요즘 DDD 각광 받음
  - 도메인 주도 개발
  - 핵심 로직 분리, 디테일 한 것은 최대한 미룸
  - oop, 비즈니스 로직에 먼저 집중

> ### 👉 Junit 테스트 코드 흐름
- [Junit의 테스트 메서드 실행 방법]
<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/390107e9-ee9a-4faf-97fd-c902bd379cb3" width="500" height="500"/>

- Junit에서는 테스트 독립성을 위해 매번 새로운 오브젝트를 만듦
- 테스트 코드 흐름 
  - (1) 테스트 클래스에 @Test와 public이고 void이며 파라미터가 없는 메서드 찾음
  - (2) 테스트 클래스의 오브젝트를 하나 만듦
  - (3) @Before 붙은 메서드부터 실행
  - (4) @Test 붙은 메서드를 호출, 테스트 결과 저장
  - (5) @After 붙은 메서드 실행
  - (6) 나머지 테스트 메서드 2~5 반복
  - (7) 모든 테스트 결과 반환 


> ### 👉 픽스처 -> 테스트를 수행하는 데 필요한 정보나 오브젝트 
- @Before 로 묶어서 한번에 초기화 하는게 좋음 

> ### 👉 테스트를 위한 애플리케이션 컨텍스트 관리

- [스프링 테스트 컨텍스트를 적용한 UserDaoTest]
<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/d5302e7b-7486-4404-9fd1-1b56e52a2d9b" width="500" height="500"/>

- [빈과 테스트 클래스]
<img src="https://github.com/jongheonleee/toby-spring3-1/assets/87258372/90aa6c30-5317-4f69-bb5b-fbcbfe6a5256" width="500" height="500"/>

- 빈은 기본적으로 싱글톤으로 관리, 하지만 테스트는 매번 생성됨


> ### 👉 DI와 테스트
- @Autowired 를 통해서 픽스처를 자동 주입, 이는 브릿지 패턴 적용됨
  - 외부에서 주입
- DI 적용한 이유
  - (1) 변경에 유리하게 대처
  - (2) 다른 차원의 서비스 기능을 도입할 수 있음
    - 예를들어, DB 커넥션 개수 카운팅 같은 부가기능 
  - (3) 효율적인 테스트 작성
    - 테스트 작성 용이하게 하려고
- 브릿지 패턴으로 테스트 코드의 재사용성 증가


> ### 👉 컨테이너 없는 DI 테스트 
- 컨테이너 자체는 생성하는 데 큰 부담
- DI를 테스트에 이용하는 방법은 스프링 컨테이너를 사용하지 않고 테스트를 만드는 것
- 스프링 DI 컨테이너에 의존하지 않음


> ### 🧑🏻‍🏫 정리
> ### 테스트의 핵심 "자동화", "편리함"
> ### 테스트는 "일관성"이 있고 "포괄적"이여야함 
> ### 코드 작성과 수행이 짧을 수록 좋음, 물론 꼼꼼하고 넓은 범위는 기본
> ### 테스트하기 쉬운 코드여야함 
> ### TDD 유용함, 테스트 -> 구현 -> 검증 
> ### 테스트 코드도 관리해야함
