
## 5. C++가 은근슬쩍 만들어 호출해 버리는 함수들에 촉각을 세우자
  - 클래스 안에 명시하지 않으면 (복사 생성자, 복사 대입 연산자, 생성자, 소멸자)는 기본형으로 컴파일러가 자동 선언 (public, inline)해준다.
  - 기본형으로 만들기 때문에, 소멸자는 비가상 소멸자로 만들어 진다.
  - 생성자 선언시 기본형 생성자를 자동으로 만들지는 않는다.
  - 컴파일러가 적합하다고 판단되야 자동 생성됌.

## 6. 컴파일러가 만들어낸 함수가 필요 없으면 확실히 이들의 사용을 금해 버리자

  - 요구 사항에 필요하지 않는 자동 생성 기능은 막아 놓자.
  - private로 막아도 friend class는 이용가능. so
  1. 선언만 해놓는다.
```cpp
  class TEST {
    private:
      TEST(const TEST&);
      TEST& operator=(const TEST&);
  }
```
  2. 상속을 이용해 막는다. (아래와 비슷한 클래스를 상속해서 막는다.)
```cpp
  class Uncopyable {
    public:
      Uncopyable() {}
      ~Uncopyable() {}
     private:
      Uncopyable(const Uncopyable&);
      Uncopyable& operator=(const Uncopyable&);
  }
```
  3. 키워드 이용.
```cpp
  class TEST {
    public:
      TEST(const TEST&) = delete;
      TEST& operator=(const TEST&) = delete;
  }
```

## 7. 다형성을 가진 기본 클래스에서는 소멸자를 반드시 가상 소멸자로 선언하자

  - 상속시 소멸자를 비가상으로 선언하면, 자식 클래스 소멸자가 호출 안된다.
  - 가상 함수는 사용시 가상 함수 호출 결정 정보 테이블이 생김. (가상함수 테이블 vtbl. 포인터 형태(vptr)로 가상함수 주소를 가리킴) so 다형성 설계를 염두해 두지 않은 클래스는 조심해야 한다.
  - 가상 소멸자가 없는 클래스 상속은 조심해야 한다 final 키워드 사용시 상속 방지 가능.
  - 순수 가상 소멸자 사용도 가능
```cpp
  class TEST {
  public:
    virtual ~TEST() = 0;
  };
  
  TEST::~TEST() {}  // 순수 가상 소멸자 정의도 해야된다.
  
```

## 8. 예외가 소멸자를 떠나지 못하도록 붙들어 놓자


## 9. 객체 생성 및 소멸 과정 중에는 절대로 가상 함수를 호출하지 말자
## 10. 대입 연산자는 * this의 참조자를 반환하게 하자
## 11. operator=에서는 자기대입에 대한 처리가 빠지지 않도록 하자
## 12. 객체의 모든 부분을 빠짐없이 복사하자
