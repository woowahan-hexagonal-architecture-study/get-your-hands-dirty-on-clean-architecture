# 02. 의존성 역전하기
계층형 아키텍처에 대한 대안
* 단일 책임 원칙(Single Responsibility Principle, SRP)
* 의존성 역전 원칙(Dependency Inversion Principle, DIP)  
cf. SOLID 원칙

## 단일 책임 원칙
> 하나의 컴포넌트는 오로지 한 가지 일만 해야 하고, 그것을 올바르게 수행해야 한다.  

위의 문장으로 많이 알려져있지만, 사실 아래 정의가 더 정확하다.  
단일 변경 이유 원칙(Single Reason to Change Principle)이 나을지도..
> 컴포넌트를 변경하는 이유는 오직 하나뿐이어야한다.  

컴포넌트를 변경할 이유가 한 가지라면 우리가 어떤 다른 이유로 소프트웨어를 변경하더라도 이 컴포넌트에 대해서는 전혀 신경 쓸 필요가 없다.

변경 이유는 의존성에 의해 전파가 빠르다
![image](/images/chapter2/pic2-1.png) 
의존도가 높은 컴포넌트 A는 어떤 컴포넌트가 변경되든지 같이 변경되어야함  
➡️ 변경 비용 증가

## 부수효과에 관한 이야기
코드의 한 영역이 바뀌면 다른 부분에 영향도가 큼  
기획 담당자에게도 이상한 요구사항을 내도록 악영향을 줄 수도 있다  
(영향도가 큰 컴포넌트를 변경해야하는 기획을 회피하여 더 이상한 기획을 낼 수 있음...!)

## 의존성 역전 원칙
* 계층형 아키텍처에서 계층 간 의존성은 항상 다음 계층인 아래 방향을 가리킴  
* 단일 책임 원칙을 고수준에서 적용할 때, 상위 계층들이 하위 계층에 비해 변경할 이유가 더 많음  
영속성 코드가 변경된다해도 도메인 코드에는 변경이 없게 할 수 있을까? 
> 의존성 역전 원칙(Dependency Inversion Principle, DIP)  
> : 코드상의 어떤 의존성이든 그 방향을 바꿀수(역전시킬 수 )있다.

* 양쪽 코드를 모두 제어할 수 있을 때만 의존성을 역전시킬 수 있다.  
(서드파티 라이브러리 의존성도 제거해야함)  
* 영속성 코드가 도메인 코드에 의존하고, 도메인 코드를 변경할 이유의 개수를 줄이자!  

![image](/images/chapter2/pic2-2.png)
* 영속성 계층의 리포지토리가 도메인 계층에 있는 엔티티에 의존
* 두 계층 사이의 순환 의존성(circular dependency)에 DIP 적용
* 도메인 계층에 리포지토리에 대한 인터페이스를 만들고, 실제 리포지토리는 영속성 계층에서 구현

## 클린 아키텍처
> 설계가 비즈니스 규칙의 테스트를 용이하게 하고, 비즈니스 규칙은 프레임워크, 데이터베이스, UI기술, 그 밖의 외부 애플리케이션이나 인터페이스로부터 독립적일 수 있음  

* 도메인 코드가 바깥으로 향하는 어떤 의존성도 없어야 함
* 모든 의존성은 도메인 코드를 향하고 있어야함 (의존성 역전 원칙)
![image](/images/chapter2/pic2-3.png)
  * 아키텍처의 코어에는 주변 유스케이스(서비스)에서 접근하는 도메인 엔티티
  * 유스케이스(서비스)는 단일 책임을 갖기 위해 세분화됨
  * 바깥쪽 계층은 다른 서드파티 컴포넌트에 어댑터 제공 가능
* 특정 프레임워크에 특화된 코드가 없기에 비즈니스 규칙에 집중할 수 있음 (Domain-Driven Design DDD, 도메인 주도 설계)
* 도메인 계층이 영속성이나 UI같은 외부 계층에 철저하게 분리돼야함 
    * 애플리케이션의 엔티티에 대한 모델을 각 계층에서 유지보수함
    * ex) ORM 프레임워크를 사용할 때, 도메인은 영속성 계층을 모르기때문에 각 계층에서 사용할 엔티티 클래스를 따로 만들어야함
 
## 육각형 아키텍처(헥사고날 아키텍처)
![image](/images/chapter2/pic2-4.png)
* 애플리케이션이 다른 시스템이나 어댑터와 연결되는 4개 이상의 면을 가질 수 있음
    * 도메인 엔티티와 상호작용하는 유스케이스
    * 육각형에서 외부로 향하는 의존성 없음
    * 모든 의존성은 내부로 향함
    * 육각형 바깥에는 애플리케이셔노가 상호작용하는 어댑터들이 있음
* 외부 어댑터
    * 왼쪽 : 애플리케이션을 주도하는 어댑터(애플리케이션 코어 호출)
    * 오른쪽 : 애플리케이션에 의해 주도되는 어댑터(애플리케이션 코어에 의해 호출)
* 어댑터와 코어 간 통신을 위한 애플리케이션 코어
    * 주도하는 어댑터(driving adapter) - 왼쪽
    * 주도되는 어댑터(driven adapter) - 오른쪽  

<code>포트와 어댑터(ports-and-adapters) 아키텍처</code>라고도 함
* 어댑터 : 애플리케이션과 다른 시스템 간의 번역을 담당하는 가장 바깥층
* 애플리케이션 계층 : 포트와 유스케이스 구현체 결합 (애플리케이션의 인터페이스 정의)
* 도메인 엔티티

## 유지보수 가능한 소프트웨어를 만드는 데 어떻게 도움이 될까?   
* 어쨋든 포인트는 의존성을 역전시켜 도메인 코드가 다른 바깥쪽 코드에 의존하지 않도록 함
* 도메인 로직의 결합을 제거하고 코드 변경 이유의 수를 줄인다
* 유지보수성이 좋아짐