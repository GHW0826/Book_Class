
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
## 7. &&, ||, 혹은 . 연산자는 오버로딩 대상이 절대로 아니다
## 8. new와 delete의 의미를 정확히 구분하고 이해하자
