# 🐸 Toby's Spring

## 01. ⚙️ 스프링의 이해와 원리

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
