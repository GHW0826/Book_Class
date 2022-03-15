
## 15. 클래스와 멤버의 접근 권한을 최소화하라
  
  - 컴포넌트 설계의 가장 큰 차이는 내부 데이터, 구현정보를 잘 숨기는것.
  - 정보은닉의 장점.
```
  1. 개발 속도를 높임. 여러 컴포넌트를 병력로 개발 하기 때문.
  2. 관리 비용을 낮춤. 각 컴포넌트 파악이 빨라지고 디버깅, 교체 부담이 적어짐.
  3. 특정 컴포넌트 최적화를 통해 다른 컴포넌트 영향을 주지 않고 최적화 가능.
  4. 독립적인 컴포넌트라면 재사용성이 올라감.
  5. 큰 시스템을 제작하는 난이도를 낮춰줌.
```
  - 가장 바깥 클래스(톱 레벨 클래스), 인터페이스에 부여할 수 잇는 접근 수준은 package-private(default), public.
  - public클래스는 패키지의 api, package-private는 내부 구현에 속함.
  - 모든 멤버는 기본적으로 private. 이후 접근이 필요한 것들에멘 권한을 풀어준다.
  - 권한을 풀어주는일이 많아지면, 컴포넌트 분해를 생각해보자.
  - 테스트 목적은 접근 범위를 적당히 넓혀도 됨. (public 클래스의 private멤버를 package-private까지 푸는것)
  - 일반적으로 public 가변 필드는 thread-safe하지 않음.
  - 클래스에서 public static final 배열 필드를 두거나, 이 필드를 반환하는 메서드는 제공하면 안된다. (제공시 해결책은 2가지)
```java
  public static final Thing[] VALUES= { ... };
  
  1. private로 만들고 public 불변 리스트 추가.
  private static final Thing[] PRIVATE_VALUES = { ... };
  pulic static final List<Thing> VALUES = 
      Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));
      
  2. 배열을 private로 만들고 복사본 반환하는 public메서드 추가(방어적 복사)
  private static final Thing[] PRIVATE_VALUES = { ... };
  public static final Thing[] values() {
      return PRIVATE_VALUES.clone();
  }
```


## 16. public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라
## 17. 변경 가능성을 최소화하라
## 18. 상속보다는 컴포지션을 사용하라
## 19. 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라
## 20. 추상 클래스보다는 인터페이스를 우선하라
## 21. 인터페이스는 구현하는 쪽을 생각해 설계하라
## 22. 인터페이스는 타입을 정의하는 용도로만 사용하라
## 23. 태그 달린 클래스보다는 클래스 계층구조를 활용하라
## 24. 멤버 클래스는 되도록 static으로 만들라
## 25. 톱레벨 클래스는 한 파일에 하나만 담으라
