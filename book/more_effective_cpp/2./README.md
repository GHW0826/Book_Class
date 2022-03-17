
## 5. 사용자 정의 타입변환 함수에 대한 주의를 놓지 말자
  
  - c++에는 c스타일 암시적 변환이 많이 남아있다.
  - 단일 인자 생성자, 암시적 타입변환 연산자.
  - 이런 암시적 변환은 비직관적이고 동작 예측이 어렵다.
  - 컴파일러는 함수 호출을 성공시키기 위해 현재 사용가능한 암시적 타입변환 함수를 찾아 호출한다.
  - 프록시 클래스로 조정하는 방법도 있다.
```cpp
  // 1. 단일 인자 생성자
  class Test {
    public:
      explict Test(int p1); // 암시적 변환을 막는다.
  }

  // 2. 암시적 타입 변환 연산자.
  class Test {
    public:
      operator double() const;    // Test를 double로 암시적 변환. (1)
      double asDouble() const;/   // Test를 double로 반환.
  }
  
  {
    Test Obj(1,2);
    
    cout << Obj;            // 암시적 변환 될 수 있다 - (1)
    cout << Obj.asDouble(); // 명시적으로 호출 구분하기도 쉽다. - (2)
    
  }
```

## 6. 증가 및 감소 연산자의 전위(prefix)/후위(postfix) 형태를 반드시 구분하자

```cpp
  class TEST 
  {
    public:
      TEST& operator++() {            // 전위
        *this += 1;
        return *this;
      }
      
      const TEST operator++(int) {     // 후위
        const TEST old = *this;
        ++(*this);
        
        return old;
      }
  }
```
  - 후위가 const 반환하는 이유는 코드 간결성, 직관성 등등..이 있다.
  - 후위 연산은 임시객체를 만들어야 하고, 반환 임시객체를 만들어야 해서 전위가 약간 더 빠르다.
  - 후위 연산자는 반드시 전위 연산자를 사용해 구현해야 한다.

## 7. &&, ||, 혹은 . 연산자는 오버로딩 대상이 절대로 아니다

  - c++은 복합적인 bool 표현식 평가시 단축평가 할 수 잇다.
  - &&, ||를 오버로딩하는 것은 단축평가 의미구조를 함수호출 의미구조로 대체하는 것이다.
```cpp
  if (expression1 && expression2) ...
  ->
  if (expression1.operator&&(expression2)) ...
  if (operator&&(expression1, expression2)) ...
```
  - 함수호출적으로 바꾸면, 단축평가가 이루어지지 않는다. 
  - 또한 매개변수 평가 순서가 정의되이 있지 않아, 무엇이 먼저 평가되는지 알기 힘들다.
  - 쉼표 연산자도 있긴함 (for문). 쉼표 앞에껄 먼저 평가후 뒤에껄 평가.
  - 마찬가지로 함수로 오버로딩시 평가 순서 애매해진다.
```cpp
  for (int i = 0, j = 10; 
        i < j; 
        ++i, --j) // 쉼표 연산
```
  - 책 기준 C++오버로딩 불가능 연산자 12가지
```cpp
  .     .*      ::      ?:
  new   delete  sizeof  typeid
  static_cast   dynamic_cast  const_cast  reinterpret_cast
```
  - 오버로딩 가능 연산자
```cpp
  operator new      operator delete
  operator new[]      operator delete[]
  +   -   *   /   %   ^   &   |   ~
  !   =   <   >   +=  -=  *=  /=  %=
  ^=  &=  |=  <<  >>  >>= <<= ==  !=
  <=  >=  &&  ||  ++  --  , ->* ->
  ()  []
  
```

## 8. new와 delete의 의미를 정확히 구분하고 이해하자
  - new, delete 연산자의 동작
``` 
  (new)
  1. 요청한 타입의 객체를 담을 수 있는 크기의 메모리 할당.
  2. 객체의 생성자를 호출하여 할당된 메모리를 객체 초기화를 수행.
  
  (delete)
  1. 소멸자 호출
  2. 메모리 해제
  이 기본동작은 바꿀 수 없다. 객체를 담을 메모리를 할당하는 방법을 바꿀 수 있다.
  new 연산자는 필수적 메모리 할당을 위해 어떤 함수를 호출하는데 그게 operator new 이다. (malloc과 마찬가지로 메모리만 할당. 오버로딩 가능)
  delete 연산자도 마찬가지로 소멸자 호출하고, 메모리 해제를 위해 함수 호출하는데 그게 operator delete.
```
  - 메모리 지정 new : 이미 잡힌 공간에 생성자를 호출해준다. 
  - new, delete 연산자는 초기화된 메모리만 가지고 일을 해야함. 미초기화 메모리는 생성자, 소멸자 호출 작업 건너 뛰어야 함.(malloc, free)
  - 메모리 지정 new를 사용한 메모리 공간은, 소멸자 메모리해제를 따로 해주어야 함.
  - operator new[], opartor delete[] 도 있고, 오버로딩 가능.
