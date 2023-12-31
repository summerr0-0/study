# ch.8

## 1. 의존성 이해하기

### 변경과 의존성
* 의존성은 실행시점과 구현시점이 다르다
* 실행시점 : 정상 동작을 위해 실행시점에 객체가 필요하다
* 구현시점 : 의존 대상 객체가 변경될 경우 의존하는 객체도 같이 변경된다

* 의존성이 존재한다의 의미
  * 객체가 작업을 수행할 때 다른 객체가 필요한 경우
  * 의존성은 방향성이 있으며 단방향이다
  * 자신이 의존하는 대상이 바뀌면 나도 바뀔 수 있다
    * Screening 이 변경되면 PeriodCondition 도 같이 바뀔 수 있다

### 의존성 전이
* 직접 의존성
  * 한 요소가 다른 요소에 직접 의존
* 간접 의존성
  * 직접적인 관계는 없음
  * 의존성 전이에 의해 영향이 전파된다
  * PeriodCondition 은 Screening 에 의존
  * Screening 은 Movie 에 의존
  * PeriodCondition 은 Movie 에 간접적으로 의존한다 

### 런타임 의존성과 컴파일타임 의존성
* 런타임 의존성
  * 애플리케이션이 실행되는 시점
  * 객체사이의 의존성
* 컴파일타임 의존성
  * 작성된 코드를 컴파일하는 시점
  * 클래스 사이의 의존성
* 컴파일 타임 구조와 런타임 구조 사이의 거리가 멀수록 설계가 유연해진다
  * 구현체에 의존하면 설계가 유연하지 못하다는 뜻 
  * DiscountPolity - 컴파일 시점 의존
  * PercentDiscountPolicy, AmountDiscountPolicy - 런타임 시점 의존

### 컨텍스트 독립성
* 특정한 문맥에 최소한의 가정으로 이루어져 있으면 다른 문맥에서 재사용하기 쉽다
  * 퍼센트로 할인율 계산해줘(x)
  * 할인율 계산해줘 (o)
* 유연한 설계를 위해서는 가능한 구체적인 정보를 적게 알아야 한다
* 구체클래스에 의존하지 말자

### 의존성 해결하기
* 의존성 해결
  * 컴파일타임 의존성은 구체적인 런타임 의존성으로 바뀌는 것
  * 컴파일 시점에는 DiscountPolicy 런타임 시점에는 PercentDiscountPolicy, AmountDiscountPolicy 로 교체
* 의존성 해결 방법
  * 생성자로 해결 : 객체를 생성하는 시점에 생성자를 통해
    * 생성자에 인스턴스를 전달해서 의존성을 해결한다
  * 수정자로 해결 : 객체 생성 후 setter 메서드를 통해
    * setter를 이용해 실행시점에 의존대상을 바꾼다 - 유연한 변경 가능
    * set 하기 전에 해당 인스턴스 변수를 사용하면 NPE가 발생함
    * 안정을 위해 생성자로 먼저 생성 후 set으로 변경하기
  * 메서드로 해결 : 메서드 실행 시 인자를 이용해 해결
    * 파라미터를 통해 전달되어서 메서드가 실행되는 동안만 일시적으로 의존이 생기는 것
    * 자주 사용하는 객체는 생성자/수정자를 이용하는 것이 좋다

## 2. 유연한 설계
### 의존성과 결합도
* 협력은 의존성을 만들고 과한 의존성은 재사용을 어렵게 만든다
* 바람직한 의존성은 재사용할 수 있는 의존성이다
  * 특정 컨텍스트에 강하게 의존하면 재사용 불가능
    * PercentDiscountPolicy, AmountDiscountPolicy 에 강하게 결합하면 후에 재사용이어려움
      * 할인정책을 바꿀 때 수정이 무조건 필요해짐
* 느슨한 결합도를 유지하도록 하자
* 의존성과 결합도
  * 의존성 - 두 객체의 관계유무
    * 의존성이 있다 / 없다
  * 결합도 - 의존성의 정도를 상대적으로 표현
    * 결합도가 강핟 / 느슨하다

### 지식이 결합을 낳는다
* 많이 알 수록 많이 결합된다
* 추상화를 통해서 정보를 감출 수 있다
  * PercentDiscountPolicy 로 계산해줘 (x)
  * DiscountPolicy 계산해줘 (o)
  * 어떤식으로 할인을 계산하는지 알필요 없다. 그냥 할인 요금을 계산하는 것만 알면 된다

### 추상화에 의존하라
* 추상화 : 절차나 물체를 감추면서 복잡도를 줄이는 것
* 불필요한 정보를 감춰 결합도를 느슨하게 유지할 수 있다
* 의존대상 구분법
  * 구체 클래스 의존성
  * 추상 클래스 의존성
  * 인터페이스 의존성
* 구체 클래스보다 추상클래스, 추상클래스보다 인터페이스 의존성이 결합도가 더 낮다

### 명시적인 의존성
* 숨겨진 의존성
  * `Movie` 생성자에서 `this.discountPolicy = new AmountDiscountPolicy`
  * DiscountPolicy 와 AmountDiscountPolicy 둘 다 의존이 생기는 것

* 명시적 의존성
  * 퍼블릭 인터페이스에 의존성을 노출하는 것
  * `Movie` 생성자에서 `this.discountPolicy = discountPolicy`
    * 외부에서 discountPolicy의 구현체를 인자로 전달받는다

* 의존성을 숨기지 마라
  * 숨기면 내부 구현을 직접 봐야한다
  * 재사용을 하기가 어려워진다

### new는 해롭다
* new가 결합도측면에서 어려운 이유
  * `new AmountDiscountPolicy` 처럼 구체 클래스 이름을 직접게 된다
    * 구체 클래스에 의존도가 생긴다
  * `new AmountDiscountPolicy(인자1, 인자2...)`
    * 인자와 생성자 호출도 알게 된다 -> 지식의 양이 늘어난다
* 인스턴스를 생성하는 로직과 인스턴스를 사용하는 로직을 분리하기
  * 외부에서 이미 생성된 인스턴스를 전달받기
  * Movie 는 discountPolicy 를 사용하는 책임만 있고 discountPolicy 생성 책임은 Movie의 클라이언트가 가진다

### 가끔은 생성해도 무방하다
* 협력하고 싶은 기본 객체를 설정할 경우 안에서 생성해도 된다
  * 대부분 `AmountDiscountPolicy` 를 사용하고 가끔만 `PercentDiscountPolicy` 를 사용하는 경우
    * 매번 생성자에 같은 값을 넣게된다 -> 코드 중복이 발생한다
  * 생성자에 `new AmountDiscountPolicy` 를 넣는다.
    * 생성자 체이닝을 사용하자
      * 생성자 안에서 기본 생성자 추가

### 표준 클래스에 대한 의존은 해롭지 않다
### 컨텍스트 확장하기
### 조합 가능한 행동