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
## 4. 인스턴스화를 막으려거든 private 생성자를 사용하라
## 5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라
## 6. 불필요한 객체 생성을 피하라
## 7. 다 쓴 객체 참조를 해제하라
## 8. finalizer와 cleaner 사용을 피하라
## 9. try-finally보다는 try-with-resources를 사용하라
