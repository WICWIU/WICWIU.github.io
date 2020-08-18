# Google C++ Style Guide

!!! note

    아래의 내용은 [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html) 을 정리한 것입니다.

    아래의 내용에서 말하는 1인칭 "나" 또는 "우리" 등등은 제가 아니라 **Google** 이며, 타겟 독자들은 **Google** 의 개발자입니다.

---

# Functions

## Inputs and Outputs

`C++` 에서 함수의 출력은 `return` 값 또는 output parameter 로 전달된다. 이때 output parameter 보다 `return` 값으로 출력을 전달해라. 

*Why*:

:	그게 가독성이 더 좋고 성능이 output parameter 와 같거나 더 낫다.

!!! example

    ```c++ 
    #define OUT
    void calculate(int a, int b, OUT int& c) {
        c = a + b;
    }
    ```

    위의 함수는 output parameter 로 출력을 전달한다. 이렇게 하지 말고 다음과 같이 `return` 을 사용해라.

    ```c++ 
    int calculate(int a, int b) {
        return a + b;
    }
    ```

입력 파라미터는 값을 사용하거나 `const` 참조자를 사용해라.

옵션 입력을 나타내기 위하여 `absl::optional` 을 사용해라. 그리고 옵션 출력을 나타내기 위하여 `const` 가 아닌 포인터를 사용해라.

파라미터로 들어온 `const` 참조자가 함수의 호출보다 오래 남아있길 바라는 함수는 정의하지 마라. 왜냐하면 `const` 참조자는 임시적으로 생성되기 때문이다. 그 대신 lifetime 요구를 없앨 수 있는 방법을 찾아라. 가령 파라미터를 복사하거나 `const` 포인터를 사용해라.

모든 input parameter 를 output parameter 보다 먼저 선언해라. 파라미터가 단지 새롭게 추가되었다는 이유로 가장 마지막에 선언하지 말아라. 

!!! example

    새롭게 추가된 input parameter 라면 output parameter 이전에 선언해라.

input parameter 이면서 output parameter 인 파라미터는 함수를 혼란스럽게 만든다.

## Write Short Functions

함수를 작게 만들어라.

함수가 하나의 기능만 하도록 만들어라.

우리는 가끔 큰 함수가 필요하다는 것을 알고 있으므로 함수 크기에 대한 엄격한 길이 제한을 두지 않을 것이다. 그러나 함수의 길이가 `40` 줄이 넘어간다면 함수를 세부 기능으로 쪼개서 더 작은 함수로 만들 수 있는지 검토해봐라.

함수를 짧고 단순하게 유지시키고, 다른 사람이 읽었을 때 쉽게 이해하고 바꿀 수 있도록 만들어라.

*Why*:

:	당신이 만든 함수가 엄청 큰데도 잘 기능한다고 하자. 그래도 후임 개발자가 나중에 와서 당신이 만든 함수에 새로운 기능을 추가해야할 때 시간이 오래걸리고 버그를 발생시킬 가능성이 크다.

    또 작은 함수를 만들면 테스트 하기에도 편하다.

## Function Overloading

오버로딩은 코드를 읽는 사람이 호출문만 보고 왜 이렇게 오버로딩했는지 이해할 수 있을 때에만 사용해라. 즉, 의미적으로 별 차이가 없는 오버로딩만 사용해라.

!!! example

    `const std::string&` 을 파라미터로 받는 함수와 `const char*` 를 파라미터로 받는 함수를 오버로딩 할 수 있다.

    하지만 `std::string_view` 로 통합하는 것이 더 낫다.

## Default Arguments

`virtual` 이 아닌 메소드에 디폴트 값이 항상 같은 값을 가질 때에만 디폴트 파라미터를 사용해라.

`virtual` 메소드에서는 디폴트 값을 사용하지 마라.

물론 디폴트 파라미터도 가끔 적절하고 가독성을 높혀준다. 만약 디폴트 파라미터를 써야할지 말지 혼란스럽다면 그냥 오버로딩해라.

## Trailing Return Type Syntax

람다 표현식을 제외하면 대부분의 경우에서 trailing return type 을 사용하지 마라.

!!! example

    ```c++
    int foo(int x);
    ```

    위와 같은 함수 선언이 일반적인 함수 선언이다. 하지만 trailing return type 을 사용하면 다음과 같이 함수를 선언할 수 있다.

    ```c++ 
    auto foo(int x) -> int;
    ```

    이 둘은 완전히 똑같은 선언이다. 하지만 람다 표현식 같은 예외적인 경우를 제외하고 기존의 함수 선언을 사용해라.


---

# Google-Specific Magic

우리는 우리의 `C++` 프로젝트를 더욱 견고하게 만들기 위하여 많은 트릭과 유틸리티를 사용한다. 

## Ownership and Smart Pointers 

Ownership 이 고정된, 동적으로 할당된 객체를 사용해라.

Ownership 을 스마트 포인터로 넘겨주어라.

!!! note

    Ownership 이란 동적 할당 객체를 관리하기 위한 기술이다. Owner 란 동적으로 할당된 객체가 더 이상 필요가 없어졌을 때 그것의 할당을 해제해줄 의무가 있는 객체를 뜻한다. 

    Ownership 은 다른 객체에게 넘겨줄 수 있다. 마지막으로 Ownership 을 넘겨 받은 객체가 동적 할당을 해제해줄 의무를 갖게 된다.

    스마트 포인터는 `*` 와 `->` 연산자를 오버로딩하여 포인터처럼 행동하는 클래스이다. 스마트 포인터의 장점은 Ownership 관리를 자동으로 해준다는 것이다. `std::unique_ptr` 이 스마트 포인터의 한 예시이다. `std::shared_ptr` 도 스마트 포인터의 예시이다.

동적 할당 객체가 필요하다면 Ownership 을 그것을 할당한 코드가 계속 가지고 있을 수 있도록 해라. 

!!! example

    만약 다른 코드가 그 객체에 접근해야 한다면 Ownership 을 넘겨주지 않으면서 복사하거나 포인터를 전달해줄 수 있도록 해라.

`std::unique_ptr` 을 스마트 포인터로써 사용해라.

!!! example

    ```c++ 
    std::unique_ptr<Foo> FooFactory();
    void FooConsumer(std::unique_ptr<Foo> ptr);
    ```

합리적인 이유가 없다면 Ownership 을 공유할 수 없도록 코딩해라.

*Why*:

:	Ownership 을 공유하면서 복사하는 비용이 많이 든다. 만약 성능 향상이 기대되고 `std::shared_ptr<const Foo>` 처럼 객체의 불변성이 보장된다면 Ownership 공유를 해도 좋다.

Ownership 을 공유해야 한다면 `std::shared_ptr` 을 사용해라.

절대로 `std::auto_ptr` 을 사용하지 말아라. 그 대신 `std::unique_ptr` 를 사용해라.

## cpplint

스타일을 점검하기 위하여 [cpplint.py](https://raw.githubusercontent.com/google/styleguide/gh-pages/cpplint/cpplint.py) 를 사용해라.

*Why*:

:	[cpplint.py](https://raw.githubusercontent.com/google/styleguide/gh-pages/cpplint/cpplint.py) 는 파일을 읽고 스타일 에러를 점검해주는 툴이다.

!!! note

    구글의 프로젝트는 저마다 `cpplint.py` 를 어떻게 사용해야 하는지 메뉴얼을 갖고 있다. 만약 당신의 프로젝트에 `cpplint.py` 가 없다면 당신이 따로 [cpplint.py](https://raw.githubusercontent.com/google/styleguide/gh-pages/cpplint/cpplint.py) 를 다운로드해라.

---

# Other C++ Features

## Rvalue References

Rvalue 참조자를 아래에서 정의하는 특별한 경우에서만 사용해라. Rvalue 참조자는 오직 임시 객체에만 사용되는 참조자이다.

(https://google.github.io/styleguide/cppguide.html#Rvalue_references)

## Friends

`friend` 키워드는 반드시 같은 파일 내에서 사용해라. 

*Why:*

:	이로써 코드를 읽는 사람이 다른 파일을 찾아볼 필요가 없도록 해라.

## Exceptions

^^`C++` 의 예외를 사용하지 마라.^^ 즉, `try-catch` 문과 `throw` 키워드를 사용하지 마라.

*Why:*

:	`C++` 의 예외는 고수준에서 일어나서는 안되는 상황을 처리할 수 있게 해준다. **Python**, **Java** 같은 다른 언어에서도 예외가 사용되므로 **C++** 에서 예외를 사용하면 코드가 일관성이 있어진다. 

    하지만 만약 당신이 예외를 발생시키기 위하여 `throw` 를 사용한다면 그것을 호출하는 모든 함수를 점검해야만 한다. 그 결과 모든 호출 함수들이 기본적인 예외 처리문이라도 지니고 있어야 한다. 그렇지 않으면 프로그램은 곧바로 종료된다.

    일반적으로 예외는 프로그램의 실행 흐름을 어렵게 만들어서 코드를 읽는 사람이 코드를 이해하기 어려워 진다. 예외는 함수의 실행 흐름을 당신이 예측하지 못하는 곳으로 넘어가게 한다. 이는 곧바로 유지보수와 디버깅을 어렵게 만들고 개발 비용을 높인다. 아마 당신은 이 문제를 어떤 상황에서 언제 예외를 사용해야 할지 정함으로써 해결할 수도 있을 거라고 생각할 것이다. 하지만 그것을 정립하고 그것을 다른 사람에게 이해시키고 기억시키는 비용이 더 많이 들어갈 것이다.

    또 예외를 사용하면 바이너리 프로시져에 데이터가 추가되고 컴파일 시간이 늘어나며 주소 공간도 커진다.

    또 예외를 사용하도록 하면 개발자는 `throw` 를 적절하지 못한 때에 사용할 수도 있고, 안전하지 못한 상황에서 사용할 수도 있다. 가령 적절하지 못한 파라미터가 입력되었을 때 그것은 예외로 던져져서는 안된다. 

    그리고 예외에 관련된 제한 규칙을 만들려면 이 코딩 컨벤션 문서가 막대하게 더 커질 것이다.

즉, 예외를 사용했을 때 발생하는 효용보다 발생하는 비용이 압도적으로 더 크다는 것이다. 우리는 에러처리 코드나 `assert` 를 사용하는 것이 더 좋다고 본다. 이것은 철학적이나 도의적인 결론이 아니라 순전히 우리(**Google**)의 경험적이고 실용적인 결론이다. 왜냐하면 지금까지 예외를 사용하는 프로젝트를 사용해야 할 때면 우리는 매번 막대한 어려움을 겪었기 때문이다.

이 금지 규칙은 앞으로 계속 추가되는 `C++` 의 예외 처리 문법에도 똑같이 적용된다. 앞으로도 `C++` 의 예외를 절대로 사용하지 말아라.

!!! example

    `C++11` 에서는 `std::exception_ptr` 과 `std::nested_exception` 이 추가되었다. 하지만 사용하지 마라.

## noexcept

(일단 Pass)

## Run-Time Type Information (RTTI)

RTTI 를 사용하지 마라. 즉, `typeid` 키워드와 `dynamic_cast` 형변환을 사용하지 마라.

RTTI 를 유닛 테스트, 테스트 코드에서는 사용해라.

*Why:*

:	물론 `dynamic_cast` 는 다음과 같이 추상 클래스의 포인터를 하위 클래스로 형변환 할 때 적절하게 사용될 수 있다.

    ```cpp 
    bool Base::Equal(Base* other) = 0;
    bool Derived::Equal(Base* other) {
    Derived* that = dynamic_cast<Derived*>(other);
    if (that == nullptr)
        return false;
    ...
    }
    ```

    하지만 RTTI 는 런타임 상에서 객체의 타입을 조사하는데 이것은 곧 설계 문제를 뜻한다. 런타임에서 당신의 클래스 구조를 알 수 있다는 말은 당신이 클래스를 잘못 설계했다는 것이다.

    또한 무분별한 RTTI 으 사용은 프로젝트의 유지보수성을 떨어뜨린다. 왜냐하면 코드를 읽는 사람이 프로젝트를 분석하면서 `typeid` 나 `dynamic_cast` 가 나올 때마다 포인터가 실제로 갖고 있는 객체에 따라 분기를 나눠가면서 분석해야 하기 때문이다. 가령 다음과 같은 코드를 보자.

    ```cpp 
    if (typeid(*data) == typeid(D1)) {
    ...
    } else if (typeid(*data) == typeid(D2)) {
    ...
    } else if (typeid(*data) == typeid(D3)) {
    ...
    ```

    위와 같은 코드의 `typeid` 와 `dynamic_cast` 는 객체의 타입이 런타임 상에서 결정되므로 프로그램 로직에 따라 완전히 결정되지 않는다는 것을 의미한다. 이것은 코드를 읽는 사람 입장에서는 정말 개똥같은 상황이다. 심지어 하위 클래스를 이후에 확장했다면 위와 같은 모든 코드를 다 수정해야만 한다.

만약 RTTI 를 사용해야겠다는 생각이 들거든 다음의 대안을 고려해봐라. `virtual` 메소드를 사용하는 것은 클래스 타입에 따라 코드 실행을 바꿔주는 적절한 방법이다.

만약 프로그램의 로직 자체가 상위 클래스의 포인터에 하위 클래스의 객체가 저장되었음을 완벽하게 보장한다면, 그때는 코드 분석이 모호해지지 않으므로 `dynamic_cast` 를 사용해도 상관없다. 하지만 당신은 이런 경우 성능 향상을 위하여 `static_cast` 를 사용하게 될 것이다. 

## Casting

`C` 스타일 형변환을 사용하지 말고 `C++` 스타일의 형변환을 사용해라.

*Why:*

:	`C` 스타일 형변환은 매우 모호하다. 이로써 여러가지 문제가 발생한다. 데이터가 손실되는 것은 물론이고 어떤 의도로 작성된 코드인지 이해하기 힘들 수도 있다.

!!! example

    가령 `C++` 스타일 형변환은 `static_cast<float>(double_value)` 이다. 
    
    또 `int64 y = int64{1} << 42` 와 같은 brace initialization 도 `C++` 형변환이다.

    이러한 `C++` 스타일 형변환을 사용해라.

    하지만 `(int)x` 같은 `C` 스타일 형변환은 사용하지 마라.

산술 타입의 데이터를 형변환 할 때 `int64{x}` 와 같은 brace initialization 을 사용해라.

*Why:*

:	brace initialization 은 데이터 손실이 일어나면 컴파일이 되지 않도록 해주기 때문에 가장 안전하다.

`static_cast` 는 상위 클래스의 포인터를 하위 클래스로 형변환할 때, 또는 하위 클래스의 포인터를 상위 클래스로 형변환할 때 사용해라. 단, 이때 상위 클래스 포인터를 하위 클래스 포인터로 형변환 한다면 객체가 반드시 하위 클래스이어야 한다.

`const` 의 의미를 제거해야 하는 상황에서 `const_case` 형변환을 사용해라.

위험한 형변환이나 `int` 를 포인터로 형변환 하는 등 가끔 말도 안 되는 형변환이 필요할 때 `  reinterpret_cast` 를 사용해라. 단, `reinterpret_cast` 는 당신이 지금 하고 있는 행동이 무엇인지 완벽하게 이해하고 있을 때 사용해라. 또한 `reinterpret_cast` 를 사용하기 전에 `absl::bit_cast` 를 사용할 수 있는 상황인지 점검해봐라.

비트수준의 데이터를 다른 타입으로 바꾸어야 할 때 `absl::bit_cast` 를 사용해라.

!!! example

    `double` 타입의 데이터를 `int64` 로 바꾸고 싶을 때 `absl::bit_cast` 를 사용해라.

`dynamic_cast` 에 대한 가이드는 RTTI 섹션을 보아라.

## Streams

상황에 따라 스트림을 적절히 사용해라. 스트림이란 `<iostream>` 헤더로 추가되는 `C++`의 표준 I/O 기능이다. 구글의 많은 프로젝트가 디버깅 로그와 테스트 코드 진단을 위하여 스트림을 사용한다.

`printf` 를 사용하지말고 `<<` 또는 `>>` 를 사용해라.

*Why:*

:	`printf` 는 사용하기 어렵고 이식성이 매우 떨어진다. 심지어 `std::string` 을 사용할 수도 없고 user-defined 타입은 당연히 사용 불가능하다. 스트림은 콘솔 I/O 를 위하여 `std::cin`, `std::cout`, `std::cerr`, `std::clog` 를 제공하는데 이것을 사용해라.

`<<` 를 오버로딩 한다면 스트림을 위하여서만 오버로딩해라.

하지만 `std::cerr` 나 `std:clog` 를 사용하는 것보다 로그 라이브러리를 사용해라.

또한 `std::stringstream` 을 사용하지 말고 `absl/strings` 나 그와 비슷한 라이브러리를 사용해라.

end-user 를 위하여 I/O 스트림을 사용하지 말고 개발자를 위하여, 진단을 위하여, 테스트를 위하여 사용해라.

## Preincrement and Predecrement

후위 증감 연산자(`i++`, `i--`)가 꼭 필요하지 않은 이상 전위 증감 연산자(`++i`, `--i`) 를 사용해라.

*Why*:

:	전위 증감 연산자가 일반적으로 가독성이 더 좋고, 좀 더 효율적이다.

## Use of const

의미가 적절하다면 언제 어디에서나 `const` 를 사용해라. 이로써 가능한 모든 코드에서 `const` 변수와 `const` 메소드를 만들어라.

*Why*:

:	코드를 읽는 사람이 변수가 어떻게 쓰이는지 쉽게 이해할 수 있다. 

    컴파일러가 더 빠르게 컴파일을 할 수 있다. 
    
    멀티 스레딩을 해야 하는 사람에게 어떤 메소드를 `lock` 하지 않고도 thread-safe 하게 사용할 수 있는지 알려준다.

가끔은 `constexpr` 가 더 좋은 선택일 수도 있으니 고려해봐라.

우리는 의미가 적절하다면 모든 함수의 파라미터, 모든 메소드, 모든 전역 변수 등에 `const` 를 쓰는 것을 강력하게 권고한다.

함수가 파라미터로 전달된 참조자와 포인터의 값을 변경하지 않는다면 각각 `const T&` 와 `const T*` 를 사용해라.

값으로 파라미터를 전달한다면 `const` 를 붙이는 것은 의미없기 때문에 이것은 권고하지 않는다. 

!!! example

    다음 코드에서 `int b` 는 값을 전달하는 파라미터이므로 `const int b` 는 의미가 없다.

    ```c++ 
    int add(int a, const int b) {
        return a + b
    }
    ```

클래스의 메소드가 클래스의 데이터를 변경하지 않는 한 모든 메소드를 `const` 메소드로 선언해라. 이때 모든 클래스의 `const` 메소드는 thread-safe 해야 한다. 그렇지 않다면 주석이나 API 에 분명하게 thread-unsafe 하다고 명시해라.

지역변수에 `const` 키워드를 붙이는 것은 권고하지도 않고, 권고하지 않지도 않는다.

### Where to put the const

(일단 Pass)

## Use of constexpr

적절한 상황이라면 모든 코드에서 `constexpr` 을 반드시 사용해라.

*Why*:

:	`constexpr` 을 사용하면 프로젝트 성능이 막대하게 향상된다. `constexpr` 은 실행 결과를 런타임 상에서 계산하게 하지 않고 컴파일 상에서 계산하게 만들기 때문이다. 가령 다음의 코드는 피보나치 수열의 `30` 번째 수를 구하는 코드이다. 

    ```c++ 
    constexpr long int fib(int n) { 
        return (n <= 1)? n : fib(n-1) + fib(n-2); 
    } 
    int main () { 
        // value of res is computed at compile time.  
        const long int res = fib(30); 
        cout << res; 
        return 0; 
    } 
    ```

    `fib` 함수가 `constexpr` 로 선언되었다. 이 **프로그램의 실행시간은 단지 `0.003` 초** 이다. 성능이 무지막지하게 향상된 것이다. 만약 `res` 를 `const` 가 아니라 `long int res` 로 선언한다면 실행시간이 `0.017` 로 올라간다. `fib(5)` 가 컴파일 상에서 계산되지 않기 때문이다.

따라서 함수에서, 생성자에서, 메소드에서 가능하다면 모든 곳에서 `constexpr` 을 사용해라. 단, `constexpr` 은 몇 가지 제약사항이 있으므로 주의해야 한다.

## Integer Types

당신은 보통 `int` 를 사용해왔을 것이다. 그러나 상황에 따라서 정확한 사이즈의 정수 타입을 사용해라.

!!! example

    `16` 비트만 있어도 된다면 `<stdint.h>` 의 `int16_t` 를 사용해라.

    어떤 데이터가 $2 ^{31}$ 와 같거나 큰 데이터를 표현한다면 `int64_t` 같은 `64` 비트 변수타입을 사용해라.

당신이 사용하는 변수 타입이 너무 크지 않은지 항상 확인해라. 하지만 만약 의심된다면 그냥 큰 타입을 사용해라.

지금까지 당신은 `16` 비트를 위하여 `short` 를 사용하고, `32` 비트를 위하여 `int` 를 사용하고, `32` 비트를 위하여 `long` 을 사용하고, `64` 비트를 위하여 `long long` 을 사용했을 것이다. 하지만 이것을 사용하지 말고 `<cstdint>` 를 통하여 `int16_t`, `uint32_t`, `int64_t` 등등을 사용해라.

*Why*:

:	`int` 타입을 보통 `4` 바이트로 생각하지만, 아키텍쳐와 컴파일러에 따라서 사이즈가 달라질 수도 있다. 그러므로 `<cstdint>` 에서 제공하는 정확한 사이즈의 변수 타입(`int16_t`, `uint32_t`, `int64_t`)을 사용해라.

사이즈를 저장하기 위하여 `size_t` 나 `ptrdiff_t` 를 사용해라.

물론 우리도 `for-loop` 같은 곳에서는 변수가 너무 커지지 않는다는 것을 알기 때문에 `int` 를 자주 사용한다. 그런 사소한 부분에서는 그냥 `int` 를 사용해라.

### On Unsigned Integers

## 64-bit Portability

## Preprocessor Macros

## 0 and nullptr/NULL 

## sizeof

## Type deduction

### Function template argument deduction

### Local variable type deduction

### Return type deduction

### Parameter type deduction

### Lambda init captures

### Structured bindings

## Class template argument deduction

## Designated initializers

## Lambda expressions

## Template metaprogramming

## Boost

## std::hash

## Other C++ Features

## Nonstandard Extensions

## Aliases

---

# Naming

가장 중요한 컨벤션이 바로 네이밍 컨벤션이다. 네이밍 스타일로써 코드를 읽는 사람은 곧바로 이것이 타입인지 변수인지 함수인지 상수인지 매크로인지 이해할 수 있기 때문이다. 따라서 개개인의 네이밍 선호에 자유를 주는 것보다 네이밍을 통일하는 것이 압도적으로 더 좋다.

## General Naming Rules

^^일반적인 네이밍 규칙은 그 변수의 의미를 영어로 작성한 후 공백을 지우는 것^^ 이다. 그리고 아래에서 설명해줄 네이밍의 타입에 따라서 camel case 또는 Pascal case 를 사용해라.

후임 개발자가 읽었을 때 이해하기 어려운 약칭과 줄임말로 네이밍을 하지 마라.

만약 Wikipedia 에 존재하는 약칭이나 줄임말이라면 써도 좋다.

일반적으로 네이밍의 정확성은 그 네이밍이 사용되는 스코프에 비례하여 지어져야 한다. 

!!! example

    `3` 줄의 코드에서 쓰이고 말 변수라면 `n` 이라는 이름을 붙혀도 좋다.

    하지만 전반적인 프로젝트에서 계속 사용되는 변수라면 `n` 이라는 이름은 너무 모호하기에 사용해서는 안된다.

물론 널리 알려인 약칭으로 네이밍을 해도 된다.

!!! example

    `i` 는 iteration 변수로 널리 알려져 있으니 써도 된다.

    `T` 는 템플릿 변수로 널리 알려져 있기 때문에 써도 좋다.

## File Names

`C++` 파일은 반드시 `.cc` 로 끝나야 하고 헤더 파일은 반드시 `.h` 로 끝나야 한다.

파일 이름은 반드시 소문자여야 한다. 그리고 `_` 와 `-` 를 포함시킬 수 있다. 하지만 `_` 를 쓰는 게 더 좋다.

!!! example

    다음과 같이 파일 이름을 지어라.

    ```shell
    my_useful_class.cc
    my-useful-class.cc
    myusefulclass.cc
    myusefulclass_test.cc // _unittest and _regtest are deprecated.
    ```

`/usr/include` 에 이미 존재하는 이름을 절대로 쓰지 말아라.

!!! example

    유닉스 시스템 헤더 파일의 디렉토리인 `/usr/include` 에는 `db.h` 가 있다. 그러므로 절대로 `db.h` 라는 파일 이름을 짓지 말아라.

파일 이름을 매우 자세하게 지어라.

!!! example

    `logs.h` 이 아니라 `http_server_logs.h` 으로 파일 이름을 지어라. 

일반적으로 대표적인 클래스를 파일 이름으로 지어라.

!!! example

    `FooBar` 클래스를 선언하는 헤더 파일의 이름을 `foo_bar.h` 로 짓고 구현하는 파일 이름을 `foo_bar.cc` 로 지어라.

## Type Names

타입 이름은 Pascal case 로 지어라. 타입 이름에는 `class`, `struct`, `type aliase`, `enum`, `template parameter` 등이 있다.

!!! example

    다음과 같이 타입 이름을 지어라.

    ```cpp 
    // classes and structs
    class UrlTable { ...
    class UrlTableTester { ...
    struct UrlTableProperties { ...

    // typedefs
    typedef hash_map<UrlTableProperties *, std::string> PropertiesMap;

    // using aliases
    using PropertiesMap = hash_map<UrlTableProperties *, std::string>;

    // enums
    enum class UrlTableError { ...
    ```

## Variable Names

변수 이름은 snake case 로 지어라. 변수 이름에는 함수의 변수, 함수의 파라미터, 클래스 멤버 변수 등이 포함된다. 

### Common Variable names

!!! example

    ```cpp 
    std::string table_name;  // OK - lowercase with underscore.`
    ```

    변수 이름을 위와 같이 짓고 다음과 같이 짓지 마라.

    ```cpp 
    std::string tableName;   // Bad - mixed case.
    ```

### Class Data Members

클래스의 멤버 변수의 이름도 snake case 로 짓되 마지막에 `_` 를 하나 더 붙혀라.

!!! example

    클래스의 데이터 멤버는 다음과 같이 이름을 지어라. 이름의 끝에 꼭 `_` 를 붙혀라.

    ```cpp 
    class TableInfo {
        ...
    private:
        std::string table_name_;  // OK - underscore at end.
        static Pool<TableInfo>* pool_;  // OK.
    };
    ```

    `static` 이든 `static` 이 아니든 이렇게 지어라.
    
### Struct Data Members

!!! example

    `struct` 의 멤버 변수의 이름을 다음과 같이 지어라.

    ```cpp 
    struct UrlTableProperties {
        std::string name;
        int num_entries;
        static Pool<UrlTableProperties>* pool;
    };
    ```

## Constant Names

상수로 선언된 변수 즉, `constexpr` 이나 `const` 로 선언된 변수의 이름에는 `k` 를 맨 앞에 붙이고 Pascal case 로 지어라.

모든 [Static storage duration](https://en.cppreference.com/w/cpp/language/storage_duration#Storage_duration) 을 갖는 변수, 즉 `static` 변수나 전역 변수를 이렇게 네이밍 해라.

단, 대문자로 단어를 구분할 수 없는 문자라면 `_` 를 사용해도 좋다.

!!! example

    ```cpp
    const int kDaysInAWeek = 7;
    const int kAndroid8_0_0 = 24;  // Android 8.0.0
    ```

## Function, Method Names

함수와 메소드의 이름은 camel case 로 네이밍해라.

!!! example

    다음과 같이 함수 이름을 지어라.

    ```cpp 
    addTableEntry()
    deleteUrl()
    openFileOrDie()
    ```

    다음과 같이 메소드 이름을 지어라.

    ```cpp 
    class MyAwesomeClass {
    ...
    void myAwesomeMethod { ... }
    ...
    }
    ```

## Namespace Names

`namespace` 의 이름은 snake case 로 지어라. 하지만 `namespace` 의 이름은 `_` 가 필요없도록 짧고 간단할수록 좋다.

가장 최상위 `namespace` 는 프로젝트의 이름으로 지어라.

하위 `namespace` 는 세부 프로젝트의 이름이나 팀의 이름으로 지어라.

!!! example

    `websearch::index` 이나 `websearch::index_util` 와 같이 네이밍해라.

## Enumerator Names

Enumerator 변수의 이름은 상수와 같이 네이밍해라. 즉 `k` 를 맨 앞에 쓰고 Pascal case 로 네이밍해라. 

!!! example

    ```cpp 
    enum class UrlTableError {
        kOk = 0,
        kOutOfMemory,
        kMalformedInput,
    };
    ```

    위와 같이 네이밍하고 다음과 같이 네이밍하지 마라.

    ```cpp 
    enum class AlternateUrlTableError {
        OK = 0,
        OUT_OF_MEMORY = 1,
        MALFORMED_INPUT = 2,
    };
    ```

*Why:*

:	2009년 1월까지, `enum` 변수의 이름은 매크로의 네이밍과 같았다. 하지만 이 네이밍은 충돌을 일으켰고 막대한 비용이 소모되었다. 그러므로 이렇게 네이밍 하라는 것이다. 

## Macro Names

일반적으로 매크로는 사용되어서는 안된다. 하지만 정말로 필요한 경우 매크로를 모두 대문자로 네이밍하고 단어 중간을 `_` 로 구분지어라. 

!!! example

    정말로 매크로가 필요하다면 다음과 같이 네이밍해라.

    ```cpp 
    #define ROUND(x) ...
    #define PI_ROUNDED 3.0
    ```

## Exceptions to Naming Rules

당신이 기존의 `C` 와 `C++` 에 이미 존재하는 비슷한 무언가를 네이밍한다면 기존의 `C` 와 `C++` 에서 따르는 네이밍 컨벤션을 따라도 좋다.

!!! example
    
    `bigopen()` : function name, follows form of `open()`

    `uint` : typedef

    `bigpos` : struct or class, follows form of pos

    `sparse_hash_map` : STL-like entity; follows STL naming conventions

    `LONGLONG_MAX` : a constant, as in INT_MAX 

---

# Comments

## Comment Style

## File Comments

### Legal Notice and Author Line

### File Contents

## Class Comments

## Function Comments

### Function Declarations

### Function Definitions

## Variable Comments

### Class Data Members

### Global Variables

## Implementation Comments

### Explanatory Comments

### Line-end Comments

### Function Argument Comments

### Don'ts

## Punctuation, Spelling, and Grammar

## TODO Comments

---

# Formatting

## Line Length

## Non-ASCII Characters

## Spaces vs. Tabs

## Function Declarations and Definitions

## Lambda Expressions

## Floating-point Literals

## Function Calls

## Braced Initializer List Format

## Conditionals

## Loops and Switch Statements

## Pointer and Reference Expressions

## Boolean Expressions

## Return Values

## Variable and Array Initialization

## Preprocessor Directives

## Class Format

## Constructor Initializer Lists

## Namespace Formatting

## Horizontal Whitespace

### General

### Loops and Conditionals

### Operators

### Templates and Casts

## Vertical Whitespace

---

# Exceptions to the Rules

## Existing Non-conformant Code

## Windows Code

---

# Parting Words

---

# 총평 

Google C++ Style Guide 는 코딩 경험이 부족한 우리에게 전 세계 1티어 개발자들의 경험을 흡수할 수 있게 해주었다. 다른 언어 개발자도 한번쯤 읽어서 구글 개발자들의 경험적 산물을 배워갈만한 정말 좋은 가이드이다. 우리는 곧바로 [**WICWIU**](https://github.com/WICWIU/WICWIU) 에 Google C++ Style Guide 를 적용시키고 싶다.

하지만 Google C++ Style Guide 는 이미 언급했듯이 너무 단순하고 당연해서 굳이 언급하지 않아도 되는 코딩 컨벤션은 정말로 언급하지 않았다. 가령 `goto` 문이 매우 안좋은 코드라는 것은 널리 알려져 있으므로 언급하지 않았다. 하지만 `goto` 문을 쓰지 말라는 규칙을 명시해두어야 할 정도로 우리 학부생들은 코딩 경험이 극히 부족하다. 

그렇기 때문에 사소하고 불필요해 보여도 안좋은 코드를 안좋은 코드라고 알리기 위하여 몇 가지 코딩 컨벤션을 추가할 필요가 있다. 

- 변수를 선언할 때 모든 변수를 mutable 로 하고 예외적으로 immutable 로 선언하는 것이 아니라, 모든 변수를 immutable 로 선언하고 예외적으로 mutable 로 선언해라.

- 함수와 메소드를 선언할 때 모든 함수를 non-const 로 선언하고 예외적으로 const 를 선언하는 것이 아니라 모든 함수를 const 로 선언하고 예외적으로 non-const 를 선언해라.

- 클래스를 선언할 때 모든 멤버 변수를 public 으로 선언하고 예외적으로 private 으로 선언하는 것이 아니라 모든 것을 private 으로 선언하고 예외적으로 public 으로 선언해라. 

- 에러 처리 메시지는 표준 출력이 아니라 표준 에러로 보내라. 즉, `std::cout` 이 아니라 `std::cerr` 로 보내라.

- 추상 클래스의 추상 메소드라면 `= 0;` 또는 `= delete;` 또는 `= default;` 로써 클래스를 생성 불가능하게 만들어라.

- 하나의 함수는 하나의 기능만 해야 한다.

- 매직 넘버를 사용하면 안된다. `0` 이나 `1` 까지는 허용한다. 물론 테스트 코드에서는 매직 넘버를 사용해도 좋다.