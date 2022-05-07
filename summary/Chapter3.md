# 3.코드 구성하기

---

## 계층으로 구성하기

---

- 코드 구조화의 접근법 중 하나는 계층을 이용하는 것이다.

```json
buckpal
| - domain
    - Account
    - Activity
    - AccountRepository
    - AccountService
  - persistence
    - AccountRepositoryImpl
  - web
    - AccountController
```

> `의존성 주입의 역활?` : 애플리케이션 계층이 `인커밍/아웃고잉 어댑터`에 의존성을 갖지 않는 것이 중요하다. Outgoing 어댑터의 경우에는 제어 흐름의 반대 방향으로 의존성을 돌리기 위해 의존성 원칙을 이용해야 한다.
>

## 기능으로 구성하기

---

```json
buckpal
| - account
    - Account
    - AccountController
    - AccountRepository
    - AccountRepositoryImpl
    - SendMoneyService
```

### 계층형 아키텍처

---

- 애플리케이션 계층에 인터페이스를 만들고, 어댑터에 해당 인터페이스를 구현하는 클래스를 둔다

### 육각형 아키텍처

---

- 모든 계층에 의존성을 가진 중립적인 컴포넌트를 도입해 해당 아키텍처를 구성하는 대부분의 클래스를 초기화 한다.

> `중립적 컴포넌트` : AccountController, SendMoneyService, AccountPersistenceAdapter
>
- AccountController가 SendMoneyUseCase 인터페이스를 필요로 하기 때문에, 의존성 주입을 통해 SendMoneyService 클래스의 인스턴스를 주입한다.
    - 컨트롤러는 인터페이스만 알면 되기 때문에 자신이 SendMoneyService 가진지 모름

- SendMoneyService 인스턴스를 만들 때에도 DI가 LoadAccountPort 인터페이스를 위한 AccountPersistenceAdapter 인스턴스를 주입할 것.

### 사용하는 이유

---

> 아키텍처 - 코드 갭을 효과적으로 다룰 수 있는 강력한 요소다.
>

어댑터 코드를 자체 패키지로 이동시키면 필요한 경우 `하나의 어댑터를 다른 구현으로 쉽게 교체`할 수도 있다.

### `EX)` Key-value 데이터베이스에서 SQL 데이터베이스로 바꾼다고 하면 관련 아웃고잉 포트만 바꾸면 된다

- `DDD` 개념의 직접적 대응도 가능하다. 위 코드에서는 account와 같은 상위 레벨 패키지는 다른 bounded context 통신하는 기점이 된다.

### 핵심 요소

---

육각형 아키텍처에서 구조적으로 핵심이 되는 요소는 `엔티티`, `유스케이스`, `incoming`, `out coming adapter`가 있다.

## ****아키텍처적으로 표현력이 있는 패키지 구조****

---

```json
📦 buckpal
└─ account
   ├─ adapter
   │  ├─ in
   │  │  └─ AccountController
   │  └─ out
   │     └─ persistence
   │        ├─ AccountPersistenveAdapter
   │        └─ SpringDataAccountRepository
   ├─ domain
   │  └─ Acoount
   │     └─ Activity
   └─ application
      ├─ SendMoneyService
      └─ port
         ├─ in
         │  └─ SendMoneyUseCase
         └─ out
            └─ LoadAccountPort
               └─ UpdateAccountStatePort
```

해당 패키지 구조를 사용 하면 어떤 작업을 할 때 패키지 구조만 보고 바로 파악 할수 있도록 도와준다.

<aside>
🔥 이 패키지 구조는 `architecture-code gap` (아키텍처-코드 갭) 혹은 `model-code gap` (모델 코드 갭)울 효과적으로 다룰 수 있는 강력한 요소.

</aside>

### Screaming Architecture

> 어플리케이션의 기능을 코드를 통해서 볼 수 있게 만드는 것
>

## 기능으로 구성한 패키지 구조의 단점

<aside>
🔥 단저엄

- 기능에 의한 패키징 방식은 가시성을 떨어뜨린다
- 어댑터를 나타내는 패키지명이 없다
- 인커밍 포트, 아웃커밍 포트는 여전히 확인할 수 없다.
- 기능을 기준으로 코드를 구성하면 기반 아킽텍처가 명확하게 보이지 않는다.
</aside>