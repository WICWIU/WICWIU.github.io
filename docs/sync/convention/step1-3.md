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

## Friends

## Exceptions

## noexcept

## Run-Time Type Information (RTTI)

## Casting

## Streams

## Preincrement and Predecrement

## Use of const

### Where to put the const

## Use of constexpr

## Integer Types

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

## General Naming Rules

## File Names

## Type Names

## Variable Names

### Common Variable names

### Class Data Members

### Struct Data Members

## Constant Names

## Function Names

## Namespace Names

## Enumerator Names

## Macro Names

## Exceptions to Naming Rules

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