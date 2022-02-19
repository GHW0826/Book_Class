
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

  - RAII 클래스의 객체를 직접 접근하기 위해, 명시적 변환, 암시적 변환이 있다.
  - 명시적 변환
```cpp
  int test = getTTest(test.get());    // 명시적 변환. get이라는 함수를 제공하여 접근 가능하게 한다.
```
  - 암시적 변환
```cpp
  class TEST
  {
    public:
      operator T() const { return pResource_; }   // 암시적 변환.
    private:
      T pResource_;
  }
```

## 16. new 및 delete를 사용할 때는 형태를 반드시 맞추자

  - delete 사용시 기존 메모리에 대해 한 개 이상의 소멸자가 호출되고, 그후 그 메모리가 해제된다.
```cpp
  delete test;      // 객체 1개를 삭제
  delete[] test2;   // 객체 배열을 삭제.
```
  - new에 []을 사용했으면 delete도 []를 쓰자.
```cpp
  std::string* test1 = new std::string       <->  delete test1;
  std::string* test2 = new std::string[100]  <->  delete[] test2;
```

## 17. new로 생성한 객체를 스마트 포인터에 저장하는 코드는 별도의 한 문장으로 만들자

  - 자바, C#은 매개변수의 평가 순서가 특정하게 고정되어 있지만, C++은 아님.
```cpp
  void Test(std::shared_ptr<T>, int);
   
   void func()
  {
    ...
    /*  아래 호출 순서가 컴파일러마다 다르다.
        new, shard_ptr 생성자 호출은 순차적이지만 사이에 getNum() 순서가 섞일수 있다.
        2 -> 1 -> 3 순서로 호출시 getNum()에서 예외를 던지면 new 했던 포인터가 유실될 수 있다.
      1. getNum() 호출
      2. new T 호출
      3. shared_ptr 생성자 호출
    */
    Test(std::shared_ptr<T>(new T), getNum());
    ...
   }
```
  - new 로 생성한 객체를 스마트 포인터로 넣는 코드는 한문장으로 별도로 만든다. 예외 발생시 디버깅하기 힘듦.
```cpp
  void func()
  {
    ...
    std::shared_ptr<T> test(new T);
     
    Test(test, getNum());
    ...
  }
```
