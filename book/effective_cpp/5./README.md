
## 26. 변수 정의는 늦출 수 있는 데까지 늦추는 근성을 발휘하자
  
  - 생성자, 소멸자가 있는 변수 사용시, 생성자, 소멸자의 비용이 들기 때문에 사용하기 바로 전에 선언하자.
```cpp
  // 1. 생성자 1번, 소멸자 1번, 대입 n번
  TEST t;
  for (int i = 0; i < n; ++i)
    t = i;
  
  // 2. 생성자 n번, 소멸자 n번
  for (int i = 0; i < n; ++i)
    TEST t(i);
```

## 27. 캐스팅은 절약, 또 절약! 잊지 말자

  - 캐스트 방식 (C 스타일)
    1. (T) 표현식 // 표현식 부분을 T 타입으로 캐스팅
    2. T (표현식) // 표현식 부분을 T 타입으로 캐스팅
  
  - 캐스트 방식 (C++)
    1. const_cast<T>(표현식) : 객체의 상수성을 없애는 용도로 사용.
    2. dynamic_cast<T>(표현식) : 안전한 다운캐스팅을 할 떄 사용하는 연산자. 주어진 객체가 클래스 상속 계통에 속한 특정 타입인지 아닌지 결정.(런타임 비용이 높다)
    3. reinterpret_cast<T>(표현식) : 하부 수준 캐스팅을 위해 만드어진 연산자. ( 포인터 -> int 등..)
    4. static_cast<T>(표현식) : 암시적 변환을 강제로 진행할 때 사용. (double -> int, void* -> 일반 포인터, 기본 클래스 포인터 -> 파생 클래스 포인터 등...)
  
  - 캐스팅시 *this의 기본 클래스 부분에 대한 사본이 임시적으로 만들어진다. 함수 안에서 호출한 *this가 같은객체가 아닐 수 있다.
```cpp
  class TEST : public Parent
  {
    public:
      virtual void func1() {
        static_cast<Parent>(*this).func1();   // *this가 캐스팅 되면서 사본이 된다. 다른 객체의 func1() 호출.
      }
  }
```
  
## 28. 내부에서 사용하는 객체에 대한 "핸들"을 반환하는 코드는 되도록 피하자
  
  - 데이터 멤버가 private라도 그 멤버의 참조자를 반환하는 함수가 public이면 멤버 캡슐화 정도도 public이 된다.(포인터도 마찬가지)
  - const 참조자를 반환해도 됨.
  - 참조자 반환시 임시객체의 참조자를 반환해 무효참조하는 거 조심하자.
  
## 29. 예외 안전성이 확보되는 그날 위해 싸우고 또 싸우자!
  
  - 예외 안정성을 가질려면 1. 자원이 새도록 만들지 않아야 한다. 2. 로직에 알맞아야 된다.
  - 예외 안전성을 갖춘 함수는 3가지 보장 중 하나를 제공
    1. 기본적인 보장 : 함수중 예외가 발생하면 실행 중인 프로그램에 관련된 모든 것들을 유효한 상태로 유지한다는 보장.
    2. 강력한 보장 : 함수 동작중 예외 발생시, 프로그램의 상태를 절대로 변경하지 않겠다는 보장. (원자적인 동작)
    3. 예외불가 보장 : 예외를 절대로 던지지 않겠다는 보장.
```cpp
  int func() throw(); //비워 있는 예외 지정.
```
  - 강력한 예외 보장은 '복사 후 바꾸기 (copy and swap) 전략으로 구현. but 모든 함수에 대해 강력한 보장은 아님.
  - 어떤 함수가 제공하는 예외 안전성 보장의 강도는, 그 함수가 내부적으로 호출하는 함수들이 제공하는 가장 약한 보장을 넘지 않는다(최소 보장이 해당 함수 보장이다.)
  
## 30. 인라인 함수는 미주알고주알 따져서 이해해 두자
   
  - 함수처럼 보이고, 동작, 매크로보다 안전하고 쓰기 좋다. 함수 호출 시 발생하는 오버헤드도 걱정할 필요 없다.
  - 대체적으로 컴파일러 최적화는 함수 호출이 없는 코드가 연속적으로 이어지는 구간에 적용되도록 설계
  - 인라인 함수 사용시 컴파일러가 함수 본문에 대해 문맥별 최적화 걸기가 용이해짐. but 함수 호출문을 본문으로 바꿔치는 거라 목적 코드의 크기가 커진다.
  - 코드 크기가 커저, 페이징 횟수가 늘어나고, 명령어 캐시 적중률이 떨어질 가능성도 높아짐. (본문의 길이가 짧아지면 목적 코드 크기도 작아지며 캐시 적중률 높아질수 있음)
  - 인라인 함수는 대체적으로 헤더 파일에 들어 있어야 한다. (인라인은 컴파일 도중에 수행)
  - 대부분의 컴파일러의 경우, 인라인 함수로 선언되도 복잡한 함수는 인라인 대상에 넣지 않음 (루프문, 재귀 함수등.., 가상 함수 호출도 인라인 안함)
  
## 31. 파일 사이의 컴파일 의존성을 최대로 줄이자
  
  - C++의 클래스 정의는 클래스 인터페이스뿐 아니라 구현 세부사항까지 많이 지정한다.
  - #include 문은 클래스를 정의한 파일과 헤더 파일들 사이에 컴파일 의존성이 생김. (엮여있는 헤더파일이 바뀌면 컴파일 다시 해야한다.)
  - 전방 선언으로 어느 정도 해결
```cpp
  namespace std {
    class string;   // 주의 string은 typedef로 정의한 타입 동의어. 전방 선언x.
                    // 표준 라이브러리 헤더는 어지간한 경우만 아니면 컴파일시 병목 요인이 되진 않는다.
  }
  class TEST2;      // 전방 선언
  
  class TEST
  {
    private:
      string mem1_; 
      TEST2  mem2_;
  }
```
  
  - 객체 참조자 및 포인터로 충분한 경우에는 객체를 직접 쓰지 않는다.
  - 할 수 있으면 클래스 정의 대신 클래스 선언에 최대한 의존하게 만든다.
  - 선언부와 정의부에 대해 별도의 헤더 파일을 제공한다.
