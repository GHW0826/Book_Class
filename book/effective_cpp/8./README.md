  
## 49. new 처리자의 동작 원리를 제대로 이해하자  

  - 할당할 메모리가 없을떄 new는 예외를 던진다. (옛날에는 null 반환)
  - 예외를 던지기 전에 사용자 지정 에러 처리 함수를 우선적으로 호출한다 (new 처리자). 메모리 고갈 상황을 처리할 함수 set_new_handler함수가 있음.
  - new 처리자에서 new 예외발생시, 메모리 확보될때 까지 반복 호출.
  - new 처리가 기본 동작 처리해줘야 할 것. 
    + 사용할 수 있는 메모리를 더 많이 확보 : new 시도 이후 메모리 확보 성공 전략 (ex. 프로그램 시작시 메모리 블록을 크게 하나 할당해 놓았다가 new 처리가자 처음 호출될 때 그 메모리를 쓰게 허용)
    + 다른 new 처리자를 설치  : 현재 new 처리자가 가용 메모리 확보할 수 없으면, 
    + new 처리자의 설치를 제거 : set_new_handler에 널포인터를 넘긴다. new 처리자가 없으면 new는 메모리 할당 실패시 예외를 던짐.
    + 예외를 던짐  : bad_alloc or bad_alloc에서 파생된 타입의 예외를 던진다. operator new에는 이쪽 종류 에러를 받아서 처리는 부분이 없다. 이 예외는 메모리 할당을 요청한 원래의 위치로 전파됌.
    + 복귀하지 않는다. : abort, exit를 호출.
  - c++에는 특정 클래스를 위한 할당에러 처리자를 두는 기능이 없다. (직접 구현 가능. 각 클래스에서 자체 버전의 set_new_handler, new를 제공하도록 만들면 Ok)
```cpp
  class TEST
  {
    public:
      static void outOfMemory();  // 메모리 할당 실패시 호출.
      ...
  }
```
  - 에외불가(nothrow) new는 영향력이 제한되어 있다. 메모리 할당 자체에만 적용되서. 이후 호출되는 생성자는 얼마든지 예외를 던질 수 있다.

## 50. new 및 delete를 언제 바꿔야 좋은 소리를 들을지를 파악해 두자
   + 잘못된 힙 사용을 탐지하기 위해
```
    할당된 메모리 주소 목록을 new가 유지하고, delete가 목록에서 주소를 제거등.. 구현하면 이중 해제, 메모리 릭 방지가 될 수 있다.
    
    또한 데이터 오버런, 언더런 발생시를 대비해 사용자 정의 new를 활용하면 요구된 크기보다 약간 더 메모리를 할당 후 
    사용자가 실제로 사용할 메모리의 앞,뒤에 오버런/언더런 탐지용 바이트패턴[경계 표지]를 적어두도록 만들 수 있을 것.
    delete는 누군가 이 경계표지에 손을 댓는지 점검하도록 만듦. 이 경계표지 부분에 원래와 다른 정보가 적혀있으면 메모리 사용 도중 
    오버,언더런이 발생 delete는 로그로 기록해 문제의 포인터 값을 남길 수 있다.
``` 
   + 효율을 향상시키기 위해
```
    컴파일러 기본 제공 버전 new, delete는 일반적임 쓰임새에 맞춰짐.
    프로그램에 알맞게 메모리 관리자에 대한 요구사항에 맞춰 설계시 성능이 높아진다.
```
   + 동적 할당 메모리의 실제 사용에 관한 통계 정보를 수집하기 위해
```
    할당된 메모리 블록의 크기 분포, 메모리 할당 해제 순서 (FIFO, LIFO..), 한번에 실제로 쓰이는 동적 할당 메모리의 최대량등.. 
    정보를 사용자 정의 new, delete를 사용하면 쉽게 수집 가능하다.
    경계표지 작업등에서 바이트 정렬 제약 주의해야 한다.
    일반적인 컴파일러 잘되있어서 만들지 말고, 컴파일러 문서를 찾아서 기능을 사용하는것도 좋다.
    오픈소스도 방법중 하나. (메모리 관리자 패키지)
```
   + 할당 및 해제 속력을 높이기 위해
   + 기본 메모리 관리자의 공간 오버헤드를 줄이기 위해
   + 적당히 타협한 기본 할당자의 바이트 정렬 동작을 보장하기 위해
   + 임의의 관계를 맺고 있는 객체들을 한 군데에 나란히 모아 놓기 위해
   + 그때그떄 원하는 동작을 수행하도록 하기 위해
```
  싱글 스레드, 멀티 스레드 환경, 튜닝,  바이트 정렬 보장, 페이지 부재를 최소화하기 위한 위치 지정 new 및 delete 등..
  프로그램에 알맞는 사용자 정의 new, delete 기법이 있다.
```

## 51. new 및 delete를 작성할 때 따라야 할 기존의 관례를 잘 알아 두자

  - new 구현 요구 사항
```
  1. 반환 값이 제대로 되어 있어야 한다.
  2. 가용 메모리 부족시 new 처리자 함수를 호출해야 한다.
  3. 크기가 없는(0 바이트) 메모리 요청에 대한 대비책을 갖춰야 한다.
  4. 기본 형태의 new가 가려지지 않도록 한다.
  5. 관례적으로 메모리 할당을 반복해서 시도하는 무한 루프를 가져야 한다. 
    할당 요구를 만족시킬 수 없으면, new 처리자 호출.
```
  - new 멤버 함수는 자식 클래스쪽으로 상속 가능.
  - 특정 클래스 전용 사용자 정의 메모리 관리자 작성시 자식 클래스가 포함되지 않는 것에 유의하자.
```cpp
  class B
  {
    public:
      static void* operator new(std::size_t size) throw(std::bad_alloc);
    ...
  }
  
  class D : public B
  {}
  
  D* p = new D;
  
  void* B::operator new(std::size_t size) throw(std::bad_alloc)
  {
    if (size != sizeof(B))          // 자식 클래스 경우가 설계 되지않았다면. 기본 new 호출하도록 설정.
      return ::operator new(size);
  }
```
  - new[]의 경우 호출시 배열안에 몇개 객체가 들어갈지 계산도 안된다. 단순히 메모리 덩어리를 할당해 주면 된다. 
    또한 객체의 갯수를 넘어서 (갯수 정보.. 등 헤더) 할당이 된다.
  - delete 함수는 널 포인터가 들어오면 아무 일도 하지 않아야 한다. 클래스 전용 버전은 예정 크기보다 더 큰 블록을 처리해야 한다.

## 52. 위치지정 new를 작성한다면 위치지정 delete도 같이 준비하자

  - new 함수의 위치지정 버전을 만들떄는, 짝에 맞춰 delete 위치 지정 버전도 꼭 만들자.
  - 추가 매개변수 new호출시 똑같이 추가 매개변수 delete가 없다면 메모리 해제시 delete가 호출되지 않는다. (생성자에서 예외를 던지면..)
  - 일반 delete시 추가 매개변수 delete호출 말고 기본형이 호출됨.
  - 어떤 형태든 new가 클래스 안에 선언되는 순간, 표준 형태들이 다 가려진다.
  - 위치 지정 new, delete 선언시 표준 버전이 가려지지 않게 하자.
```cpp
  class StdNewDelete
  {
    public:
      // 기본형 
      static void* operator new(std::size_t size) throw (std::bad_alloc)
      { return ::operator new(size);
      static void* operator delete(void* pMemory) throw()
      { return ::operator delete(pMemory);
      
      // 위치 지정
      static void* operator new(std::size_t size, void* ptr) throw (std::bad_alloc)
      { return ::operator new(size, ptr);
      static void* operator delete(void* pMemory, void* ptr) throw()
      { return ::operator delete(pMemory, ptr);
      
      // 예외불가
      static void* operator new(std::size_t size, const std::nothrow_t& nt) throw()
      { return ::operator new(size, nt);
      static void* operator delete(void* pMemory, const std::nothrow_t& nt) throw()
      { return ::operator delete(pMemory, nt);
  }
  
  class TEST : public StdNewDelete
  {
    public:
      using StdMewDelete::operator new;       // 표준 형태가 보이도록 만든다.
      using StdMewDelete::operator delete;
      
      // 사용자 정의 new, delete
      static void* operator new(std::size_t size, ...);
      static void* operator delete(void* pMemory, ...);
  }
```
