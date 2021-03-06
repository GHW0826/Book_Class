## 1. 생성자 대신 정적 팩터리 메서드를 고려하라

  - 클래스의 인스턴스를 얻는 수단. public 생성자, 정적 팩터리 메서드.
```
  정적 팩토리 메서드 
  장점.
    1. 이름을 가질 수 있다. : 생성자 자체로 객체의 특성을 제대로 설명하기 힘듦.
    2. 호출될 때마다 인스턴스를 새로 생성하지는 않아도 된다. : 불변 클래스, 새 인스턴스 캐싱등 불필요한 객체 생성 피할 수 있다.
    3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다. : 상속의 유연성 사용 가능.
    4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
    5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.
    
 단점
    1. 상속을 하려면 public, protected 생성자가 필요해 정적 팩터리 메서드만 제공시 하위 클래스를 만들 수 없다.
    2. 프로그래머가 찾기 어렵다. : 따로 API설명, 흔히 사용하는 명명 방식 사용해야 편함.
```

## 2. 생성자에 매개변수가 많다면 빌더를 고려하라
  
  - 매개변수가 많은 생성자, 정적 팩토리 메서드는 작성, 파악이 어렵다.
```java
  public class Test {
    private final int p1;
    private final int p2;
    ....
    
    public Test(int p1, int p2, ...) {
      this(p1, p2, ...);
    }
    .... // 매개 변수 갯수, 지원 인터페이스에 따라 달라짐.
  }
  
  {
    Test Obj = new Test(1,2,3,....);  // 매개변수 파악이 어려움.
  }
```
  - 자바 빈즈 패턴이 대안이 될수 있다.
```
    메서드 호출을 여러 번해야 하고, 변수의 일관성이 무너짐. (세터로 변수지정).
    클래스를 불변으로 만들 수 없고, 스레드 안전성 보장하려면 추가 작업을 해줘야 함.
```
  - 점층적 생성자 패턴의 안정선과 자바빈즈 패턴의 가독성을 겸비한 빌더 패턴.
  - 정적 팩터리, 생성자를 호출해 빌더 객체를 얻고, 세터 메서드들로 원하는 매개변수를 설정
  - 처리할 매개변수가 많으면 빌더 패턴 좋다.
```java
  public class Test {
    private final int size;
    private final int a;
    private final int b;
   
    public static class Builder {
      // 필수 변수
      private final int size;
      
      // 선택 변수
      private int a = 0;
      private int b = 0;
      
      public Test build() { return new Test(this); }
      public Builder(int size) { this.size = size; }
      public Builder SetA(int a) {
        this.a = a;
        return this;
      }
      public Builder SetB(int b) {
        this.b = b;
        return this;
      }
    }
    
    private Test(Builder builder) {
      this.size = builder.size;
      this.a = builder.a;
      this.b = builder.b;
    }
  }
  {
    Test Obj = new Test.Builder(10).SetA(10).SetB(5).build();
  }
```

## 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라

  - 싱글톤 : 인스턴스 하나만 생성할 수 있는 클래스
  - 방법 1. private 생성자, public static final 멤버를 마련해서 구현
  - 방법 2. 정적 팩터리 메서드를 private static final 멤버로 제공. (private 생성자)
  - 방법 3. 원소가 하나인 열거 타입 선언.
  - 리플렉션 API인 AccessibleObject.setAccessible을 사용해 private 생성자 호출 가능. -> 두번째 객체가 생성되려 할때 예외를 던지게 한다.
```java
  // 1. 싱글턴 표시가 API에 명백히 드러남.
  // 2. public static 필드가 final이라 다른 객체 참조 불가능.
  // 3. 간결함.
  public class Test {
    public static final Test INSTANCE = new TEST();
    private TEST() { ... }
  }
  
  // 1. API를 바꾸지 않고도 싱글턴이 아니게 변경 가능. (ex. 스레드별로 다른 인스턴스를 넘겨주게 할 수 있다.)
  // 2. 원하면 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다.
  // 3. 정적 팩터리의 메서드 참조를 공급자로 사용할 수 있다.
  public class Test {
    private static final Test INSTANCE = new TEST();
    private TEST() { ... }
    public static Test getInstance() { return INSTANCE; }
  }
  
  
  // 1. 직렬화, 리플렉션에서 안정.
  // 2. 다른 클래스 상속시 사용 불가능.
  // 3. thread-safe
  // 4. java enum은 클래스, c++의 enum과 다른듯?
  public enum Test {
    INSTANCE;
    ...
  }
```

## 4. 인스턴스화를 막으려거든 private 생성자를 사용하라

  - 정적 메서드와 정적 필드만 담은 클래스 생성.
  - final 클래스, 관련 메서드를 모아 놓을때 사용.
  - 생성자 명시 안하면 기본 생성자를 만듬 -> c++과 똑같음.
  - 추상 클래스로 만들어도 하위 클래스를 만들어 인스턴스화 하는것 같다.
  - 생성자를 private로 선언해서 이런 클래스들 인스턴스화 하는 걸 막자.

## 5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

  - Spring DI 개념인것 같다.
  - 클래스가 여러 자원 인스턴스를 지원해야 하고, 클라이언트가 원하는 자원을 사용함.
  - 클래스의 유연성, 재사용성, 테스트 용이성이 좋다.
```java
  public class DIClass {
    private final Item obj;
    
    public DIClass(Item obj) {
      this.obj = obj;
    }
  }
```

## 6. 불필요한 객체 생성을 피하라

  - 불변 객체는 재사용.
```java
  String s = new String("test");  // 실행될 떄마다 인스턴스를 새로 만듦.
  String s = "test";              // 하나의 인스턴스를 사용.
```

  - 비싼 객체는 캐싱해서 재사용한다.
  - 박싱 타입, 오토박싱 조심하자.
```java
  Long sum = 0L;
  for (long i = 0; i < Integer.MAX_VALUE; ++i)
    sum += i; // Long 인스턴스가 더해질때마다 
```

## 7. 다 쓴 객체 참조를 해제하라

  - GC 있어도 메모리 관리에 신경 써야 한다.
  - 메모리 누수가 계속되면, GC활동, 메모리 사용량이 늘어남 결국 성능저하. 심하면 디스크 페이징, OutOfMemoryError
  - 다 쓴 참조 조심. 앞으로 다시 쓰지 않을 참조들.
  - 다 쓴 참조들은 null처리 하자. NullPointerException으로 실수로 사용 방지도 가능.
  - 이런 처리도 좋은데, 참조의 범위를 벗어나게 밀어내는 것도 좋다.
  - 리스너, 콜백 등도 등록후 명확히 해지하지 않으면 계속 쌓여간다. 이때 콜백을 약한 참조하면 GC가 수거해간다.

## 8. finalizer와 cleaner 사용을 피하라

  - 자바는 2가지 객체 소멸자 제공. finalizer,cleaner
  - c++의 destructor와 다른 개념인듯.
  - 자바의 메모리 회수는 try-with-resource, try-finally
  - finalizer는 수행할 스레드 제어 불가능. cleaner는 수행할 스레드 제어 가능.
  - 상태를 영구적으로 수정하는 작업에서는 finalizer, cleaner 절대 의존하면 안됨.
```
  둘다 전체적으로 안좋음.but 
  1. 자원의 소유자가 close 메서드를 호출하지 않는 것에 안전망 역할을 함.
  2. 네이티브 피어와 연결된 객체에서 GC는 네이티브 티어를 몰라 가비지 컬렉션이 안일어남. 이거 처리는 적당함.
    (이것도 성능 저하 감당 가능하거나, 자원을 즉시회수 애햐하면 close사용 해야 한다.)
  이것도 생각해보고 사용.
  - finalizer
    예측할 수 없고, 상황에 따라 위험할 수 있어 일반적으로 불필요. 쓰임새가 있지만 기본적으론 사용 안함.
    
  - cleaner
    덜 위험 하지만 예측할 수없고, 위험한건 마찬가지.
```
  - cleaner, finalizer는 안전망 역할이나 중요하지 않은 네이티브 자원 회수용으로만 사용하자. (이것도 불확실성, 성능 저하 주의)

## 9. try-finally보다는 try-with-resources를 사용하라

  - 자원이 둘 이상이면 try-finally 방식은 지저분함.
  - try-with-resource가 더 간결하교, 분명하고 보기도 좋음.
```java
  static String firstLineOfFile(String path, String default) {
    try (BufferedReader br = new BufferedReader(new FileReader(path))) {
      return br.readLine();
    }
    catch (IOException e) {
      return default;
    }
  }
```
