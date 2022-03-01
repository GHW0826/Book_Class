
## 41. 템플릿 프로그래밍의 천릿길도 암시적 인터페이스와 컴파일 타임 다형성부터
  
  - 객체지향 기본은 명시적 인터페이스, 런타임 다형성.
  - 템플릿, 일반화 프로그래밍 기본은 암시적 인터페이스, 컴파일 타임 다형성.
  - 암시적 인터페이스는 템플릿 안에서 몇 몇 표현식이 유효해야 한다는 것.
```cpp
  template<typename T>
  void execute(T& obj)
  {
    if (obj.size() > 10 && obj != something()) {    // size 함수 지원, >, != 부등 비교 지원
      T tmp(obj);                                   // 복사생성자 지원
      tmp.normalize();                              // normalize 함수 지원
      tmp.swap(obj);                                // swap 함수 지원
    }
  }
```
  - 위 함수에서 operator>, operator!= 가 호출될떄 해당 호출을 성공시키기 위해 템플릿의 인스턴스화가 일어남 (컴파일 도중) -> 컴파일 타임 다형성
  - 템플릿 매개변수의 경우, 인터페이슨느 암시적, 유효 표현식에 기반을 두어 구성. 다형성은 컴파일 중에 템플릿 인스턴스화와 함수 오버로딩 모호성 해결을 통해 나타난다.

## 42. typename의 두 가지 의미를 제대로 파악하자

  - 템플릿 선언문 내에서 class, typename의 차이점은 없다
```cpp
  template<class T>
  template<typename T>
```
   
    - 템플릿 내의 이름중 템플릿 매개변수에 종속된 것 : 의존 이름. 
    - 의존이름이 중첩되어 있는 경우 : 중첩 의존 이름
    - 템플릿 매개변수와 상관없는 타입 이름 : 비의존 이름
```cpp
  // 그냥 예시
  template<typename T>
  void getSecondObj(const T& container)
  {
    if (container.size() >= 2) {
      T::const_iterator iter(container.begin());    // T::const_iterator는 중첩 의존 이름
      ++iter;
      int value = *iter;              // int 는 비의존 이름
      std::cout << value;       
    }
  }
```
  - 위 예제에서 T에 const_iterator라는 이름을 가진 정적 데이터 멤버가 있을수 있다. but..
  - c++ 구문 분석기는 템플릿 안에서 중첩 의존 이름을 만나면 개발자가 타입이라고 알려주지 않는 한 그 이름이 타입이 아니라고 가정하게 되어있다. (타입으로 지정하는 방법 : 객체앞에 typename 지정)
```cpp
    template<typename T>
  void getSecondObj(const T& container)
  {
    if (container.size() >= 2) {
      typename T::const_iterator iter(container.begin()); 
      ...
    }
  }
```

- 중첩 의존 타입 이름을 식별하는 용도에는 반드시 typename을 사용한다. 중첩 의존 이름이 기본 클래스 리스트에 있거나 멤버 초기화 리스트 내의 기본 클래스 식별자있을 때는 예외.
- 너무 길면  typedef로 줄이기 가능 (ex. typedef typename std::iterator_traits<T>::value_type value_type; )
  
## 43. 템플릿으로 만들어진 기본 클래스 안의 이름에 접근하는 방법을 알아 두자
  
  - 파생 클래스 템플릿에서 기본 클래스 템플릿의 이름 참조시 'this->', using, 기본 클래스 한정문을 명시적으로 써준다.
  - 템플릿 매개변수가 인스턴스로 만들어 질때까지 무엇이 될지 알 수 없다. (완전 템플릿 특수화 등.. 예외 경우가 있다)
  - 컴파일러가 파악하는 과정에 템플릿 정의 구문 분석할 수 있게 미리 파악하게 해준다.
```cpp
  class Info { ... };
  
  template<typename T>
  class TEST
  {
    public:
      void clear(const Info& info)
      {
        T obj;
        obj.clear(info);    // 
      }
  }
  
  tempalte<typename T>
  class TESTInfo : public TEST<T>
  {
    public:
      // using TEST<T>::clear;
  
      void clearInfo(const Info& info)
      {
        ...
        clear(info);
        // this->clear(info);
        // TEST<T>::clear(info);    // 호출되는 함수가 가상함수인 경우, 명시적 한정시 가상 함수 바인딩이 무시된다.
        ...
      }
  }
```
  
## 44. 매개변수에 독립적인 코드는 템플릿으로부터 분리시키자
  
  - 템플릿 사용시 코드 비대화 될 수 있다.
  - 공통성 및 가변성 분석 (그냥 공통적인 부분을 함수로 뽑고, 호출하도록 수정하는것) 똑같이 템플릿에 적용.
  - 템플릿은 코드 중복이 암시적(인스턴스화될때 발생함), 코드중복을 감각으로 알아채야 한다.
  - 비타입 템플릿 매개변수로 생기는 코드 비대화는, 템플릿 매개변수를 함수 매개변수 or 클래스 데이터 멤버로 대체해 비대화를 종종 없앤다.
  - 타입 매개변수로 생기는 코드 비대화의 경우, 동일한 이진 표현구조를 가지고 인스턴스화되는 타입들이 한 가지 함수 구현을 공유하게 해 비대화 감소 가능. (int, long..)
  - 포인터의 경우 하단에서 타입 미정 포인터 (void*)로 동작하는 버전을 호출하는 식으로 만든다. 
```cpp
  // size_t에 따라 인스턴스화됨.
  template<typenamt T, std::size_t n>
  class TEST 
  {
    public:
      void fn();
  };
  
  {
    TEST<double, 5> obj1;
    TEST<double, 10> obj2;
  
    // size_t가 작동하는 부분 뺴곤 일치.
    obj1.fn();    
    obj2.fn();
  }
  
  // 1. 함수에 매개변수를 받게해 인스턴스화를 줄임.
  template<typenamt T>
  class TESTBase
  {
    protected:
      void fn(std::size_t num);
  }
  template<typenamt T, std::size_t n>
  class TEST : private TEST<T>
  {
    private:
      using TEST<T>::invert;
    public:
      void fn() { this->invert(n); }
  };
```

## 45. "호환되는 모든 타입"을 받아들이는 데는 멤버 함수 템플릿이 직방!
  
  - 포인터는 암시적 변환 가능. 스마트 포인터는 불가능. (상속 관계 변환. 상수, 비상수 관계 변환)
  - 스마트 포인터 계열은 직접 변환 방법을 작성해야 함. -> 멤버 함수 템플릿 (생성자 템플릿)을 쓴다.
```cpp
  // 1.
  template<typename T>
  class ptr
  {
    public:
      template<typename U>      // 일반화된 복사 생성자를 위한 멤버 템플릿.  (일반화 복사 생성자 라고 부른다.)
      ptr(const ptr<U>& obj);   // but 상속 관계 역행등 더 다양한 케이스가 많아짐.
  }
  
  
  // 2.
  // 초기화를 이용해 T* 타입을 U*타입으로 초기화함. 암시적 변환이 가능할 떄만 컴파일 에러가 나지 않는다.
  // 이런건 대입 연산에서 많이 사용.
  template<typename T>
  class ptr
  {
    public:
      template<typename U>     
      ptr(const ptr<U>& obj)
      : heldptr_(other.get()) {...}
      
      T* get() const { return heldptr; }
    private:
      T* heldptr;
  }
```
  - 템플릿도 기본적으로 c++. 컴파일러 기본 생성 규칙에 따라 복사생성자도 기본으로 생김. (일반화 복사 생성자는 기본생성자가 아니다)
  - 일반화된 복사 생성자, 대입 연산을 선언 했더라도 보통의 복사 생성자, 복사 대입 연산자는 직접 선언하자.
  
## 46. 타입 변환이 바람직할 경우에는 비멤버 함수를 클래스 템플릿 안에 정의해 두자
  
  - 템플릿 인자 추론 과정에서는 암시적 타입 변환이 고려되지 않는다.
  - 클래스 템플릿 안에 프렌드 함수를 넣어둬 함수 템플릿 성격을 주지 않고 특정 함수 하나를 나타낼 수 있다.
  - 클래스 템플릿 내부에서는 템플릿의 이름(<> 뗸 것)을 그 템플릿 및 매개변수의 줄임말로 쓸 수 있다.
```cpp
  template<typename T>
  class TEST
  {
    public:
      // 여기서 정의해야 링크 문제 해결.
      friend const TEST operator*  (const TEST& lhs, const TEST& rhs)
      {
        // 헬퍼 함수 이용.
        return fn(lhs, rhs);
      }
      // firend const TEST<T> operator* (const TEST<T>& lhs, const TEST<T>& rhs);
  }
```
  - 헬퍼 함수를 써서 프렌드 함수가 호출하게 해도 됌.
```cpp
  template<typename T>
  const TEST<T> fn(const TEST<T>& lhs, const TEST<T>& rhs);
```
  
## 47. 타입에 대한 정보가 필요하다면 특성정보 클래스를 사용하자
  
  - STL 반복자 종류 
    + 입력 반복자 : 전진만 가능. 한번에 한 칸씩만 이동. 읽기만 한번 가능. (ex. istream_iterator )
    + 출력 반복자 : 입력 반복자와 비슷하지만 출력만 가능. 쓸 수 있는 횟수가 한번. (ex. ostream_iterator)
    + 순방향 반복자 : 입출력 반복자 하는 일 기본적으로 가능. + 자신이 가리키는 위치에서 읽기, 쓰기 여러 번 가능. (ex. 해시 컨테이너 반복자)
    + 양방향 반복자 : 순방향 반복자에 뒤로 갈 수 있는 기능 추가. (ex. list, set, multiset, map, multimap 등 컨테이너 반복자)
    + 임의 접근 반복자 : 양방향 반복자에 "반복자 산술 연산" 수행 기능 추가. (ex. vector, deque, string 반복자)
  
  - C++ 표준 라이브러리는 위 반복자 식별 태크 구조체가 정의되어 있다. 
```cpp
  struct input_iterator_tag {};
  struct output_iterator_tag {};
  struct forward_iterator_tag : public input_iterator_tag {};
  struct bidrectional_iterator_tag : public forward_iterator_tag {};
  struct random_access_iterator_tag : public bidirectional_iterator_tag {};
```
  
  - 반복자의 타입에 따라 함수 구현이 달라질 수 있다. 이때 반복자의 타입을 알려면 특성 정보 사용.
  - 컴파일 타임시 적용되는것과 런타임시 적용되는 것 구분해서 코드 설계.
  - 특성 정보를 이용해 오버로드해서 사용.
```cpp
  
  typdef random_access_iterator_tag iterator_category;
  typdef bidrectional_iterator_tag iterator_category;
  ...
  
  template<typenamt IterT>
  struct iterrator_traits    // 특성정보는 구조체로 구현이 관계
  {
    tpyedef typename IterT::iterator_category iterator_category;
  }
  
  template<typenamt IterT>
  struct iterrator_traits<IterT*>   // 포인터 타입에 대한 템플릿 특수화
  {
    tpyedef random_access_iterator_tag iterator_category;
  }
  
  // 함수 오버로드.
  template<typename IterT, typename DistT>
  void doAdvance(IterT& iter, DistT d, std::random_access_iterator_tag);
  
  template<typename IterT, typename DistT>
  void doAdvance(IterT& iter, DistT d, std::bidirectional_iterator_tag);
  ...
  
  
  template<typename IterT, typename DistT>
  void advance(IterT& iter, DistT d)
  {
    doAdvance(iter, d,
      typename std::iterator_traits<IterT>::iterator_category()); // 반복자 타입에 따라 오버로드 된 함수 호출.
  }
```
  
## 48. 템플릿 메타프로그래밍, 하지 않겠는가?

  - 템플릿 메타프로그래밍 : 컴파일 도중에 실행되는 템플릿 기반 프로그램 작성.
  - 템플릿은 컴파일 타임 영역이라 몇몇 에러들을 컴파일 도중에 찾을 수 있다.
  - 템플릿의 반복 의미는 재귀를 사용함. (재귀식 템플릿 인스턴스화)
  - TMP 주요 사항 
    + 치수 단위의 정확성 확인
    + 행렬 연산의 최적화 : 표현식 템플릿 사용시 덩치 큰 임시 객체를 없애고 루프를 합칠 수 있다.
    + 맞춤식 디자인 패턴 구현 생성
