

### DI란

Dependency Injection : 의존성 주입

두 객체 사이에 의존성이 존재할 때, 객체의 생성을 의존하는 객체에서 직접하는 게 아니라 외부에서 생성한 후 주입시켜주는 것

* 안드로이드의 경우 Koin, Dagger, Hilt와 같은 라이브러리를 이용

* DI Framework : DI를 구현하기 위해서는 객체를 생성하고 넘겨주는 외부 역할이 필요하다. Dagger에서는 Component와 Module이라 명칭

### IOC(제어가 역전되었다)

서로 알 필요가 없다 == 서로 의존하지 않는다

쉽게 말해 외부 컨테이너가 객체를 생성하여 주입하는 경우

DI의 장점

- Boilerplate Code(보일러 플레이트 코드)를 줄일 수 있다
- 테스트 용이

