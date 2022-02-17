
## 13. 자원 관리에는 객체가 그만!

  - 스마트 포인터 등. 자원 해제가 필요한 경우 객체에 넣어서 관리하자. 코드 블럭 중간에 return 등이 호출되도 알아서 해제된다.
  - shared_ptr, unique_ptr, weak_ptr... 등등 스마트 포인터 사용.
  - 자원을 획득한 후 자원 관리 객체에 넘기고, 소멸자에서 자원이 해제되도록하면 된다. (RAII)
  - 이런 느낌
```cpp
  class Mange
  {
    public:
      Mange::Mange(const T* resource)
      {
        resource_ = resource;
      }
      Mange::~Mange()
      {
        delete resource_;
      }
    private:
      T* resource_;
  }
```

## 14. 자원 관리 클래스의 복사 동작에 대해 진지하게 고찰하자
  - RAII클래스에 구현하는 일반적인 복사 동작 (1, 2번을 주로 사용)
    1. 복사 금지
    2. 관리하는 자원에 대해 참조 카운팅 수행.
    3. 진짜로 복사
    4. 자원의 소유권을 옮긴다. (unique_ptr 느낌)

## 15. 자원 관리 클래스에서 관리되는 자원은 외부에서 접근할 수 있도록 하자

## 16. new 및 delete를 사용할 때는 형태를 반드시 맞추자

## 17. new로 생성한 객체를 스마트 포인터에 저장하는 코드는 별도의 한 문장으로 만들자
