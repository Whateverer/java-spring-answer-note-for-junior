# study-java-spring-answer-note-for-junior
인프런 강의 - Java/Spring 주니어 개발자를 위한 오답노트 정리

# 오리엔테이션
Q001. 순차 지향 프로그래밍과 절차 지향 프로그래밍의 차이?
순차 지향 언어 - 어셈블러
절차 지향 언어 - C언어
순차 지향 프로그래밍 - Sequential oriented programming
절차 지향 프로그래밍 - Procedure oriented programming
절차 지향 프로그래밍은 함수 지향 프로그래밍

# 주니어 개발자가 많인 하는 개발 
## 실천할 수 있는 컨벤션 교정
### 1. 이름    
변수이름: camelCase    
함수이름: camelCase    
클래스이름: PascalCase    
패키지이름: alllowercase    
상수: UPPER_SNAKE_CASE    

메소드 이름은 동사로 시작해야함!    
줄여쓰기 - 가능하면 풀어서 쓰기(줄여 쓰지 말기)    
축약어 - 대문자로 표현하지X ex) memberDTO (x) -> memberDto    

### 2. 동사
- get vs find
get -> return type이 T인 경우(항상 인스턴스를 돌려받을 것을 가정)
find -> return type이 Optional<T>인 경우(null인 경우가 있음)

- get 남발하지 않기!
일반적으로 get은 내가 갖고있는 멤버변수를 그대로 돌려준다는 의미가 강함.
	ex) 모든 값을 더해서 가져오는 것 : getSumPrice(x) -> sumPrice(o)
	
### 3. 롬복
- getter setter를 남발하지 마라 : 캡슐화를 망치는 주범, 객체를 수동적이게 만듦.
수동적인 예)
```java
@Getter
@Setter
class User {
	enum Status{ACTIVE, INACTIVE}
	
	private long id;
	private String name;
	private Status status;
	private long lastLoginTimestamp;
}

class UserManager {
	public void doSomething(User user) {
		user.setStatus(Status.ACTIVE);
		user.setLastLoginTimestamp(Clock.systemUTC().millis());
	}
}
```

능동적인 예)
```java
class User {
	enum Status{ACTIVE, INACTIVE}
	
	private long id;
	private String name;
	private Status status;
	private long lastLoginTimestamp;
	
	public void inactive() {
		this.status = Status.INACTIVE;
	}
	
	public void login() {
		this.lastLoginTimestamp = clock.millis();
	}
}

class UserManager {
	public void doSomething(User user) {
		user.inactive();
		user.login(Clock.systemUTC().millis());
	}
}
```
	
### 4. 가독성
- 주석은 정말 필요할 때만 사용, 메소드 추출하기
- Optional 자주 사용하기
- Map 남발하지 않기

### 5. 관습
- start(포함) end(미포함)
	

더 알아보기
1. 검증이 필요할 때 무엇을 쓸까? verify vs validate vs check vs is     
2. 코드 스타일 - 구글 코드 스타일 가이드    
3. 클래스 생성 시 단어 조합은 3개 이하로 권장    

## 객체 지향적인 코드 짜기(1): 객체의 종류, 행동
### 1. 객체의 종류
- VO(Value Object) : 모든 변수들을 final로 선언해서 상태를 변경할 수 없는 객체, 항상 불변해야 하며 어떤 메소드를 호출해도 값이 항상 일관됨(불변성)
```java
class UserInfo {
	private final long id;
	private final String username;
	private final String email;
	
	public UserInfo(long id, String username, String email) {
		assert id > 0;
		assert StringUtils.isNotEmpty(username);
		assert EmailValidator.isValid(email);
		this.id = id;
		this.username = username;
		this.email = email;
	}
}
```
번외) 생성자의 역할     
값을 검증, 값을 할당하는 두 역할만 한다.    
- DTO(Data Transfer Object) : 데이터 전송 객체, 메소드, 클래스, 프로세스 간에 데이터를 주고 받을 때 쓰는 객체, 모든 속성을 노출함(모든 멤버변수가 public)
- Entity : 보통 데이터 베이스에 저장함
	+ 유일한 식별자가 있고,
	+ 수명 주기가 있으며, 
	+ 쓰기 모델 저장소에 저장함으로써 지속성을 가지며 나중에 저장소에 불러올 수 있다.
- PO(Persistence Object) : DB의 Entity

### 2. 디미터 법칙
최소 지식의 법칙
: 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다.    
TDA 원칙 (Tell Don't Ask) 물어보지 말고 일을 시키라

### 3. 행동
struct와 class는 다르다.
데이터 위주의 사고 < 행동 위주의 사고
일반적으로 행동 위주의 사고가 객체지향일 확률이 높다.
- duck typing : 어떤 새가 오리처럼 걷고, 날고, 오리처럼 꽥꽥거린다면 오리라고 부르겠다. (행동이 같다면 같은 클래스로 부르겠다.)    
ex) 자동차 클래스를 만든다고 했을 때 단순히 필드만 나열하는 것은 데이터 위주의 사고, 자동차가 하는 일을 method로 정의하면 행동 위주의 사고.

### 4. 순환 참조
순환참조, 양방향 참조 만들지 말기.
1. 순환 의존성 자체가 결합도를 높이는 원인이 된다.
2. 순환참조 때문에 serialize가 불가해진다.
- 간접 참조로 해결하기
- 컴포넌트 분리(중간에 한 클래스를 생성)

더 알아보기
1. 항상 하면 좋은 고민
- final이어야 할까?
- 변수명
2. VO의 변경자
- 새로운 VO를 반환한다.
- VO의 변경자 이름(ex. changePassword < withNewPasswird)

## 설계(1) : 의존성이란 무엇인지?(DI vs DIP)
### 1. SOLID
- Single reponse (단일 책임 원칙) : 클래스는 하나의 목적만을 가져야 한다. (응집도와 연관이 있는 원칙)
- Open-Closed (개방-폐쇄의 원칙) : 확장에 대해 열려 있어야 하고, 수정에 대해서는 닫혀 있어야 한다. => 추상화(인터페이스화)가 부족한 경우
- Liskov substitution (리스코프 치환 원칙) : 상위 클래스와 하위 클래스 사이의 계약이 깨지지 않아야 한다.
public 메소드는 다른 말로 인터페이스라고 한다. (인터페이스 == 계약)
- Interface-Segregation (인터페이스 분리의 원칙) : 클라이언트가 자신이 이용하지 않는 메서드에 의존하지 않아야 한다는 원칙 => 인터페이스를 분리해서 조립하듯이 개발하지 않을 경우    
인터페이스란? : 이 기능을 사용하고 싶다면 이 방법을 사용하라고 알려주는 것    
API도 큰 범주에서는 인터페이스다.    
- Dependency inversion (의존성 역전 원칙) : 상위 모듈은 하위 모듈에 의존해서는 안된다. 추상화에 의존해야 한다. 추상화는 세부 사항에 의존해서는 안된다. 세부사항이 추상화에 의존해야 한다.
	
### 2. 의존성
의존성이란 무엇인가?     
의존성은 결합도와 같은 말이고 다른 객체나 함수를 사용하는 상태이다. A객체가 B객체를 사용하면 A는 B에 의존한다고 볼 수 있다.
- 의존성 주입 : 필요한 값을 외부에서 의존성을 넣어주면 의존성 주입.
1. 파라미터 주입
2. 필드 주입
3. 생성자 주입
의존성 주입을 받는다고 의존성이 사라진 것은 아님. => 의존성을 약하게 하는 역할.
	
Dependency Injection은 의존성 주입(DI)    
Dependency Inversion은 의존성 역전(SOILD-DIP)    
- 의존성 역전 : 의존성 역전은 화살표의 방향을 반대로 바꾸는 기술
ex) MacDonald -> HamburgerChef 를    
 MacDonald -> (interface) Chef <- HamburgerChef 로 바꾸는 것
- 의존성 역전과 스프링 
### 3. 의존성 조언
### 4. CQRS
