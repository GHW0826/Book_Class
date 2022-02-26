
## 32. public 상속 모형은 반드시 "is-a(...는 ...의 일종이다)"를 따르도록 만들자
   
   - 부모 클래스 (Base), 자식 클래스 (Derived).  D타입으로 만들어진 모든 객체는 B타입의 객체지만, 그 역은 아니다.
   - 참조자, 포인터를 받아들이는 것 또한 마찬가지.
   - 클래스 설계에 따라 상속 관련 함수를 처리 지정하는 기준이 달라질 수 있다.

## 33. 상속된 이름을 숨기는 일은 피하자

   - 변수, 함수등 네이밍 관련 대입, 호출은 우선순위에 따른 유효범위순으로 진행된다.
   - 함수의 경우 오버로드 되어도 자식에 같은 네이밍 함수 선언이 되있으면 해당 함수부터 호출해 부모 클래스의 오버로드된 함수는 호출되지 않는다.
```cpp
   class B
   {
      private:
         int x_;
       public:
         virtual void f1() = 0;
         virtual void f1(int);
         void f2();
         void f2(double);
   }
   
   calss D : public B
   {
      public:
         virtual void f1();
         void f2();
         void f3();  
   }
   
   int main()
   {
      ...
      D d;
      d.f1();        // D의 f1();
      d.f1(5);       // 에러 D f1이 B의 f1을 가림.
      d.f2();        // 문제 없음.
      d.f2(3.14);    // 에러 D의 f2가 B의 f2를 가림.
      return 0;
   }
   
   // using을 사용해 B클래스의 함수를 끄집어 낼 수 있다. (위 main문의 문제가 사라져 그대로 사용 가능해짐)
   calss D : public B
   {
      public:
         using B::f1;
         using B::f2;
         
         virtual void f1();
         void f2();
         void f3();  
   }
```
   - private 상속시 전달 함수를 만들어 사용하자.

## 34. 인터페이스 상속과 구현 상속의 차이를 제대로 파악하고 구별하자

   @ 추상 클래스 인스턴스 안만들어짐.
   - 상속의 개념.  1. 인터페이스 상속,  2. 함수구현의 상속.
   - 순수 가상 함수에도 구현 붙일수 있다. (호출시 반드시 클래스 이름을 한정자로 붙여 주어야 함)
   - 순수 가상, 가상 함수의 차이는 해당 함수의 기본 구현이 통합적인가 아닌가 정도인듯.
   - 비가상 함수를 선언하는 목적은 파생 클래스가 함수 인터페이스와 더불어 그 함수의 필수적인 구현을 물려받게 하는 것.

## 35. 가상 함수 대신 쓸 것들도 생각해 두는 자세를 시시때때로 길러 두자
   
   - 비가상 인터페이스 관용구를 통한 템플릿 메서트 패턴 (NVI) : public 비가상 멤버 함수를 통해 private 가상 함수를 간접적으로 호출하는 방법.
   - 사전 동작, 사후 동장 관리가 편하다.
```cpp
   class TEST
   {
      public:
         int f1()
         {
            ...   // 사전 동작들
            int val = f2();
            ...   // 사후 동작들
            
            return val;
         }
       private:
         virtual int f2()
         {
            ...
            return val;
         }
   }
```

   - 함수 포인터로 구현한 전략 패턴 (비 멤버 함수, 함수 포인터를 이용하여 로직을 비 멤버 함수에 맡김)
   - 같은 타입 객체의 로직을 다양하게 가져갈 수 있다. 
   - 비 멤버 함수라 private 데이터는 접근 못함. 공개된 인터페이스를 통해서만 작업 가능.
```cpp
   int f1_cal(const TEST& obj);
   
   class TEST
   {
      public:
         typedef int (*pfn)(const TEST&);
         
         explict TEST(pfn fn = f1_cal)
         : pfn(fn)
         {}
         
         int execute() const
         {  return pfnc_(*this); }
      private:
         pfn pfn_;  
   }
```

   - function 으로 구현한 전략 패턴
   - function 타입 객체는  다음 예제에서 int (const TEST&)의 시그니처와 호환되는 함수 호출성 개체 어떤 것도 가질 수 있다. (ex: int (const TEST_부모클래스&)... 암시적 변환이 가능해짐. )
   - 다양한 방식이 가능해짐.
```cpp
   int f1_cal(const TEST& obj);
   
   class TEST
   {
      public:
         typedef std::function<int (const TEST&)> pfn;
         
         explict TEST(pfn fn = f1_cal)
         : pfn(fn)
         {}
         
         int execute() const
         {  return pfnc_(*this); }
      private:
         pfn pfn_;  
   }
```

   - 고전적 전략 패턴
```cpp
   class Obj;
   class Fun
   {
      public:
         virtual int cal(const Obj& obj) const
         { ... }
   }
   
   class Fun1 : public Fun
   {...}
   class Fun2 : public Fun
   {...}
   
   Fun defaultFn;
   
   class Obj
   {
      public:
         explicit Obj(Fun* fn = &defaultFn)
         : fn_(fn)
         {}
         
         int execute() const
         { return fn_->cal(*this); }
         
      private:
         Fun* fn_;
   }
```

## 36. 상속받은 비가상 함수를 파생 클래스에서 재정의하는 것은 절대 금물!
   
   - 부모 클래스에서 정의한 함수를 자식 클래스에서 재정의 하면, 포인터 대입후 호출시 자체적으로 부모 클래스 함수가 가려짐.
   - 비가상 함수는 정적 바인딩으로 묶이기 때문.(타입 포인터는 해당 타입에 해당 함수가 정의되어 있을 것이라고 결정해 버림)
   - 가상 함수였으면 동적 바인딩으로 묶여 항상 D::fn() 호출 (아래 예제)
```cpp
   class B
   {
      public:
         void fn();
   }
   
   class D : public B
   {
      public:
         void fn();
   }
   
   {
      D x;
      B* pB = &x;
      D* pD = &x;
      
      pB->fn();   // B::fn 호출
      pD->fn();   // D::fn 호출
   }
```

## 37. 어떤 함수에 대해서도 상속받은 기본 매개변수 값은 절대로 재정의하지 말자

## 38. "has-a(...는 ...를 가짐)" 혹은 "is-implemented-in-terms-of(...는 ...를 써서 구현됨)"를 모형화할 때는 객체 합성을 사용하자

## 39. private 상속은 심사숙고해서 구사하자

## 40. 다중 상속은 심사숙고해서 사용하자
