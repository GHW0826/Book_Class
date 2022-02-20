
## 18. 인터페이스 설계는 제대로 쓰기엔 쉽게, 엉터리로 쓰기엔 어렵게 하자
  - 제약을 넣어 잘못사용하기 어려운 방식으로 사용의 경우도 있다.
```cpp
  class Date
  {
    public:
      Date(int month, int day, int year);   // 그냥 숫자를 넣으면 되서, 순서 잘못넣거나, 범위 오류 생길수 있다.
  }
  
  class Date
  {
    public:
      Date(const Month& m, const Day& day, const Year& y);  // 래퍼타입을 만들어서 넘겨 오류를 줄이도록 함.
  }

```
  - 위 래퍼 타입을 사용시, 미리 유효한 집할을 static으로 만들어 준비해서 사용해도 됨.
```cpp
  class Month
  {
    static Month Jan() { return Month(1); }
    static Month Feb() { return Month(2); }
    ...
  }
```
  - shard_ptr은 사용자 정의 삭제자를 지원해 교차 DLL 문제, 뮤텍스등을 자동으로 잠금 해제하는데 쓸 수 있다.
```
  - 교차 DLL 문제
     객체 생성 시에 동적 링크 라이브러리의 new를 썻는데, 그 객체를 삭제할 떄는 이전의 DLL과 다른 DLL에 있는 delete를 썻을 경우 발생.
     (new, delete 짝이 실행되는 DLL이 달라서 꼬이게 된다.)
```

## 19. 클래스 설계는 타입 설계와 똑같이 취급하자

  - 클래스 설계는 기본적으로 새로운 타입 하나 정의하는 것과 같다.
  - 새 클래스 설계시 다음과 같은 사항 고려해 보자.
```
  - 새로 정의한 타입의 객체 생성 및 소멸 관리
  - 객체 초기화는 객체 대입과 어떻게 달라야 되는가
  - 새로운 타입으로 만든 개체가 값에 의해 전달되는 경우 어떤 의미를 줄것 인가
  - 새로운 타입이 가질수 있는 적법한 값에 대한 제약은?
  - 기존 클래스 상속 계통망에 맞출것?
  - 어떤 종류의 타입 변환을 허용할 것인가
  - 어떤 연산자와 함수를 두어야 의미가 있을까?
  - 표준 함수들 중 어떤 것을 허용하지 말 것인가?
  - 새로운 타입의 멤버에 대한 접근권한을 어느 쪽에 줄 것인가?
  - 선언되지 않은 인터페이스로 무엇을 둘 것인가?
  - 새로 만드는 타입이 얼마나 일반적인가?
  - 정말로 필요한가?
```

## 20. '값에 의한 전달'보다는 '상수객체 참조자에 의한 전달' 방식을 택하는 편이 대개 낫다
  
  - 기본적으로 C++는 함수로부터 객체를 전달받거나 할때 값에의한 전달을 사용. (복사해서 사본을 전달, 받는다.)
  - 복사하면, 해당 클래스 관련 복사생성자, 생성자, 소멸자가 모두 호출됨. 효율이 안좋음.
  - 함수 파라미터를 (const T&) 로 받으면 참조로 받게되 위와같은 비용이 안 일어난다.
  - 파생 클래스 객체가 기본 클래스 객체로 전달되는 경우 나타나는 슬라이싱 문제도 없어짐.
  - int 같은 기본타입은 값전달이 효율적일 때가 많다.


## 21. 함수에서 객체를 반환해야 할 경우에 참조자를 반환하려고 들지 말자

  - 실제하지 않은 참조자를 넘길 위험이 있다. (스택 생성된 객체 반환시 블록 나가면 객체 삭제됨)
```cpp
  // 참조 반환. 아래 경우 다 안된다.
  const TEST& operator*(const TEST& lhs, const TEST& rhs)
  {
    // 1. 지역 객체
    TEST obj(lhs, rhs); // 지역 객체라 블록 벗어나면 소멸된다.
    
    // 2. 힙 객체, 삭제를 밖에서 해줘야 해서 까다롭다.
    TEST* obj = new TEST(lhs, rhs);
    
    // 3. static 객체 반환. 스레드 세이프 하지 못하고, 참조자라 비교 연산시 항상 같을 수 있음.
     static TEST obj;
     .... // obj, lhs, rhs 작업
     
    return obj;
  }
```
  - 정도의 방법을 쓰자
```cpp
  // 생성, 소멸의 비용은 들어감. 몇몇 조건하에 컴파일러가 최적화 메커니즘을 적용함.
  // RVO (반환 값 최적화 : return value optimization)
  inline const TEST operator* (const TEST& lhs, const TEST& rhs)
  {
    return TEST(lhs, rhs);
  }
```

## 22. 데이터 멤버가 선언될 곳은 private 영역임을 명심하자

  - 데이터 멤버가 public이 아니면 사용자 쪽에서 어떤 객체를 접근할 수 있는 유일한 수단은 멤버 함수 뿐. (접근할때 일관성이 있다.)
  - 읽기, 쓰기 접근권한을 개발자가 조절 가능하다. (게터, 세터 각 부분만 구현하면 된다.)
  - public이나 protected나 비슷비슷하다. (private에 비해 변경시 생각해야하는 범위가 너무 넓어진다.)

## 23. 멤버 함수보다는 비멤버 비프렌드 함수와 더 가까워지자

  - 비멤버 비프렌드 함수 사용시, 관련 기능 패키징 유연성이 높아지고, 컴파일 의존도도 낮춰진다.
  - 유틸리티 클래스 같이 모아놔도 된다.
```cpp
  namespace TestUtill {
    class TEST { ... };
    void clear(TEST& obj);
    void init(TEST& obj);
  }
  
  // 필요한 기능만 쪼개서 관련 함수만 모아 관리도 가능.
  // TEST 자체.
  namespace TestUtill {
    class TEST { ... };
  }
  
  // TEST_init.h 헤더
  namespace TestUtill {
    void init(TEST& obj);
  }
  
  // TEST_clear.h 헤더
  namespace TestUtill {
    void clear(TEST& obj);
  }
```

## 24. 타입 변환이 모든 매개변수에 대해 적용되어야 한다면 비멤버 함수를 선언하자

  - 암시적 변환은 일반적으로 구리지만, 숫자타입 만들때는 예외이다.
  - 암시적 변환 함수는 비멤버 비프렌드 함수로 구현하자. (양쪽 자동 암시적 변환이 된다.)
```cpp
  class Num
  {
    public:
      Num(int num);
      const Num operator*(const Num& obj) const     // 1.
      { ... }
      ...
  }
  
  // 2.
  // 비멤버 함수로 구현해 양쪽 인자 모두 암시적 변환이 가능하게 한다.
  const Num operator*(const Num& lhs, const Num& rhs)
  {
    return Num(lhs.getNum() * rhs.getNum());
  }
  
  int main()
  {
    Num num1(2);
    Num num2(3);
    
    Num result = num1 * num2;
    result = Num1 * 2;        // 된다. num1.operator*(Num(2))로 암시적 변환이 일어나 가능
    result = 3 * num1;        // 3.operator(num1)과 같아 암시적 변환이 안되서 안된다. (1번 멤버 함수로 호출시는 불가능, 2번 비멤버 함수로 호출시 가능)
    return 0;
  }
```

  - 어떤 함수에 들어가는 모든 매개변수(this 포인터가 가리키는 객체도 포함)에 대해 타입 변환이 필요하다면, 그 함수는 비멤버여야 한다.

## 25. 예외를 던지지 않는 swap에 대한 지원도 생각해 보자

