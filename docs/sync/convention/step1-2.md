# Google C++ Style Guide

!!! note

    아래의 내용은 [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html) 을 정리한 것입니다.

    아래의 내용에서 말하는 1인칭 "나" 또는 "우리" 등등은 제가 아니라 **Google** 이며, 타겟 독자들은 **Google** 

---

# C++ Version

C++17 을 써라. 아직 C++2x 는 쓰지 마라. 비표준 C++ 확장은 쓰지 마라. 

비표준 C++ 확장에 대해서는 아래에서 더 자세히 설명한다.

---

# Header Files

모든 `.cc` 파일은 `.h` 헤더 파일과 연관되어 있다. 아주 예외적으로 유닛 테스트에 사용되는 작은 `.cc` 파일들은 단지 `main()` 함수만 갖고 있다.

!!! note

    `C++` 프로그램을 작성하기 위한 파일의 확장자는 `.cc`, `.cpp`, `.cxx`, `.c++` 등이 있는데 [**WICWIU**](https://github.com/WICWIU/WICWIU) 는 `.cpp` 확장자를 사용하고 있습니다.

헤더 파일의 올바른 사용은 가독성, 프로젝트 크기, 프로젝트의 성능에 큰 영향을 미친다. 다음의 규칙들은 헤더 파일을 어떻게 올바르게 사용할 수 있는지 가이드해준다.

## Self-contained Headers

헤더 파일은 반드시 독립적이어야 한다. 즉, 그 자체로 컴파일 되어야 한다. 그리고 반드시 `.h` 확장자를 가져야 한다. 포함되어야 하는 비헤더 파일은 `.inc` 확장자를 가져야 하며 아주 예외적으로 사용되어야 한다. 헤더 파일이 반드시 독립적이어야 한다는 것은 그것이 필요한 모든 것을 지니고 있어야 한다는 것이다. 

헤더 파일은 아래에서 설명할 헤더 가드를 가져야만 한다.

template 과 inline 함수는 선언과 동시에 정의해라. 그렇지 않으면 link 단계에서 에러가 발생한다. 예외적으로 template 구현을 특수화시켜야 할 경우 오직 해당 `.cc` 파일에서 구현하는 것이 허락된다.

## The `#define` Guard

모든 헤더 파일은 중복 선언을 막기 위해 `#define` 가드를 가져야 한다. `#define` 가드의 형식은 다음과 같이 하라.

```c++ 
<PROJECT>_<PATH>_<FILE>_H_
```

유일성을 보장하기 위하여 경로는 전체 경로여야 한다. 

!!! example

    가령 파일이 `foo/src/bar/baz.h` 라면 다음과 같이 `#define` 가드를 생성하라.

    ```c++ 
    #ifndef FOO_BAR_BAZ_H_
    #define FOO_BAR_BAZ_H_
    ...
    #endif // FOO_BAR_BAZ_H_
    ```

## Forward Declarations

가능한 한 forward declarations 을 피해라. forward declarations 는 정의 없이 선언하는 것이다.

!!! example

    함수의 forward declarations 의 예시이다.

    ```c++ 
    void printThisInteger(int);
    ```

    클래스의 forward declarations 의 예시이다.

    ```c++ 
    class MyClass;
    struct MyStruct;
    ```

## Inline Functions

inline 함수는 오직 코드가 작을 때, 즉 코드가 10 줄 이하일 때 사용하라.

- 장점

    inline 함수는 그 코드가 작으면 작을수록 효율적이다.

- 단점 

    inline 함수를 남용하면 프로그램이 오히려 느려진다. 함수의 크기가 커지면 inlining 은 코드의 크기를 매우 크게 한다.

`virtual` 이나 리커시브 함수 같은 함수들은 보통 inline 되지 않는다. 특히 리커시브 함수는 절대 inline 화 시키면 안 된다.

## Order of Includes

헤더를 다음의 순서로 포함시켜라. 그리고 각각의 헤더 파트들을 꼭 한 칸 띄워줘라.

1. 파일과 관련된 헤더

2. `C` 시스템 헤더 

3. `C++` STL 헤더

4. 다른 라이브러리 헤더 

5. 당신의 프로젝트의 헤더

각각의 헤더 파트 내부에서는 알파벳 순서로 헤더를 정렬해라.

!!! example

    `dir2/foo2.h` 를 구현하거나 테스트하는 것이 목적인 `dir/foo.cc` 와 `dir/foo_test.cc` 에서는 다음과 같이 헤더를 포함시키면 된다.

    1. `dir2/foo2.h`

    2. A blank line

    3. C system headers, 가령 `<unistd.h>`, `<stdlib.h>`

    4. A blank line

    5. C++ standard library headers, 가령 `<algorithm>`, `<cstddef>`

    6. A blank line

    7. 다른 라이브러리의 `.h` 파일

    9. 당신의 프로젝트의 `.h` 파일

!!! note

    "파일과 관련된 헤더" 란 그것이 빠지면 전체 프로젝트의 컴파일 빌드가 되지 않는 헤더를 뜻한다. 가령 위의 예시에서 `dir2/foo2.h` 헤더가 `dir/foo.cc` 와 `dir/foo_test.cc` 파일에서 빠지면 프로젝트가 빌드에 실패한다.

!!! note

    `stddef.h` 같은 `C` 헤더는 `cstddef` 의 `C++` 스타일로 바꿀 수 있다. 두 스타일 다 가능하지만 기존에 있는 코드와 일관성을 지키면 된다.

!!! example

    `google-awesome-project/src/foo/internal/fooserver.cc` 의 헤더 포함은 다음과 같을 수 있다.

    ```c++ 
    #include "foo/server/fooserver.h"

    #include <sys/types.h>
    #include <unistd.h>

    #include <string>
    #include <vector>

    #include "base/basictypes.h"
    #include "base/commandlineflags.h"
    #include "foo/server/bar.h"
    ```

    먼저 `fooserver.cc` 가 구현해야 할 `foo/server/fooserver.h` 를 맨 처음에 포함시켰다. 그 다음에 `C` 시스템 헤더, `C++` 헤더, 다른 라이브러리 헤더, 그리고 해당 프로젝트의 헤더를 포함시킨다.
    
## Names of Includes

모든 헤더 파일은 `.` 이나 `..` 이 없이 포함되어야 한다. 

!!! example

    `google-awesome-project/src/base/logging.h` 헤더 파일은 다음과 같이 포함되어야 한다.

    ```c++ 
    #include "base/logging.h"
    ```

## 헤더 포함 규칙

필요한 모든 헤더를 포함시켜라.

!!! example

    만약 당신이 `bar.h` 와 `foo.h` 헤더를 포함해야 하는데 `bar.h` 가 `foo.h` 를 포함하고 있다면 당신의 파일에서 실제로 `bar.h` 만 포함해도 된다. 하지만 `bar.h` 와 `foo.h` 를 포함시켜라.

## 조건부 헤더 

조건부 헤더가 필요하다면 모든 헤더 밑에 포함시켜라.

!!! example

    가끔 다음과 같이 시스템 사양을 체크해야 하는 조건부 헤더가 필요하다. 그러면 모든 헤더들보다 밑에 선언하면 된다.

    ```c++ 
    #include "foo/public/fooserver.h"

    #include "base/port.h"  // For LANG_CXX11.

    #ifdef LANG_CXX11
    #include <initializer_list>
    #endif  // LANG_CXX11
    ```

---

# Scoping

## Namespaces 

예외적인 상황을 제외하고 모든 코드를 `namespace` 내부에 작성해라. 이름공간은 프로젝트의 이름이나 경로처럼 유니크해야 한다.

`namespace` 의 `using` 을 사용하지 마라.

!!! example

    `using namespace std;` 를 사용하지 않는 것이다.

`inline namespace` 를 사용하지 마라.

`namespace` 를 어떤 이름공간인지 알려주는 코멘트로 닫아라.

!!! example

    다음과 같이 `namespace` 가 끝날 때 코멘트를 달아라.

    ```c++ linenums="1"
    namespace mynamespace {
    class MyClass {
    public:
    ...
    void Foo();
    };

    }  // namespace mynamespace
    ```

`namespace` 의 `alias` 를 사용하지 마라.

!!! example

    `namespace baz = ::foo::bar::baz;` 를 사용하지 마라.
    
## Unnamed Namespaces and Static Variables

만약 어떤 `.cc` 파일이 외부에서 참조되지 않는다면 코드를 이름없는 `namespace` 로 묶거나 `static` 으로 선언해라.

!!! note

    이름없는 `namespace` 로 묶거나 함수를 `static` 으로 선언하면 외부에서 접근할 수 없게 된다.

!!! example

    외부에서 참조되지 않는 `.cc` 파일이라면 모든 코드를 다음의 이름없는 `namespace` 로 묶어라.

    ```c++ 
    namespace {
    ...
    }   // namespace
    ```

## Nonmember, Static Member, and Global Functions

함수를 `namespace` 에 두는 것을 선호해라. 완전한 전역함수를 사용하지 마라. 

!!! note

    **"메소드"** 는 클래스의 멤버 함수이고, **"함수"** 는 클래스에 속하지 않은 일반적인 함수입니다.

그렇다고 모든 함수를 어떤 클래스의 `static` 메소드로 선언하지는 마라. `static` 메소드는 반드시 그 클래스와 밀접하게 연관되어 있어야 한다.

## Local Variables

변수를 가능한 한 가장 좁은 scope 에 배치해라. 그리고 변수를 변수의 첫 사용과 가능한 한 가까이 배치해라.

변수를 가능한 한 선언과 동시에 초기화해라.

!!! example

    *Don't:*

    ```c++ 
    int i;
    i = f();      // Bad -- initialization separate from declaration.
    ```

    *Do:*

    ```c++ 
    int j = g();  // Good -- declaration has initialization.
    ```

    *Don't:*

    ```c++ 
    std::vector<int> v;
    v.push_back(1);  // Prefer initializing using brace initialization.
    v.push_back(2);
    ```

    *Do:*

    ```c++ 
    std::vector<int> v = {1, 2};  // Good -- v start
    ```

`if` 나 `while` 이나 `for` 문에서 사용될 변수는 그 scope 내부에서 선언하여 바깥에서 사용될 수 없게 하라. 

!!! example

    *Do:*

    ```c++ 
    while (const char* p = strchr(str, '/')) str = p + 1;
    ```

단, 변수가 객체라면 매번 생성되고 소멸되기를 반복하게 된다. 그러므로 이 경우 반복문 바깥에 선언해라.

!!! example

    *Don't:*

    ```c++ 
    // Inefficient implementation:
    for (int i = 0; i < 1000000; ++i) {
    Foo f;  // My ctor and dtor get called 1000000 times each.
    f.DoSomething(i);
    }
    ```

    *Do:*

    ```c++
    Foo f;  // My ctor and dtor get called once each.
    for (int i = 0; i < 1000000; ++i) {
    f.DoSomething(i);
    }
    ```

## Static and Global Variables

`static` 변수를 갖는 객체를 만들지 마라.

!!! info

    자세한 설명은 https://google.github.io/styleguide/cppguide.html#Static_and_Global_Variables 참고

(일단 Pass)

### Decision on destruction

(일단 Pass)

### Decision on initialization

(일단 Pass)

### Common patterns

(일단 Pass)

## thread_local Variables

(일단 Pass)

---

# Classes

우리는 클래스를 광범위하게 사용한다. 이 섹션에서 클래스를 사용할 때 **Do** 들과 **Don't** 들을 설명한다.

## Doing Work in Constructors

생성자에서 `virtual` 메소드를 호출하지 마라.

생성자에서 에러 신호를 보낼 수 없는 경우 에러가 발생할만한 초기화는 피해라. 프로그램에 적합하다면 생성자에서 에러가 발생했을 때 프로그램을 중단하도록 해라.

## Implicit Conversions

암시적 형변환을 정의하지 마라. `explicit` 키워드로 형변환을 막거나 일변수 생성자의 호출을 차단해라.

!!! note

    암시적 형변환은 가령 `double` 변수를 받는 메소드에 `int` 형 변수를 전달할 때 발생한다.

!!! example

    다음과 같이 `explicit` 키워드로 암시적 형변환을 막아라.

    ```c++ 
    class Foo {
    explicit Foo(int x, double y);
    ...
    };

    void Func(Foo f);
    ...

    Func({42, 3.14});  // Error
    ```

    `operator bool()` 등을 막을 수 있다.

가끔 설계 목적이 값을 교환가능한 객체를 만드는 것이라면 암시적 형변환은 필요하고 적절하다. 이 경우 프로젝트 팀장에게 말해서 이 규칙을 예외적으로 면제해달라고 요청해라. 

## Copyable and Movable Types

클래스의 API 에서 클래스가 copyable 인지 movable 인지, copyable 도 아니고 movable 도 아닌지 명시해야 한다.

movable 은 임시적으로 생성되고 할당할 수 있는 객체이다. 

copyable 은 다른 객체로부터 할당될 수 있는 객체이다.

!!! example

    `std::unique_ptr<int>` 는 movable 인데 copyable 은 아니다.

    `int` 나 `std::string` 은 movable 이고 copyable 이다.

    user-defined 타입에서는 copy constructor 나 copy-assignment operator 가 정의되면 copyable 이고, move constructor 와 move-assignment operator 가 정의되면 movable 이다.

copy-only 클래스에서는 copy operation 을 명확하게 정의하고, move-only 클래스에서는 move operation 을 명확하게 정의해라.

non-copyable/movable 클래스에서는 명확하게 copy operation 을 지워라.

!!! example

    다음과 같이 하라.

    ```c++ 
    class Copyable {
    public:
    Copyable(const Copyable& other) = default;
    Copyable& operator=(const Copyable& other) = default;

    // The implicit move operations are suppressed by the declarations above.
    };

    class MoveOnly {
    public:
    MoveOnly(MoveOnly&& other);
    MoveOnly& operator=(MoveOnly&& other);

    // The copy operations are implicitly deleted, but you can
    // spell that out explicitly if you want:
    MoveOnly(const MoveOnly&) = delete;
    MoveOnly& operator=(const MoveOnly&) = delete;
    };

    class NotCopyableOrMovable {
    public:
    // Not copyable or movable
    NotCopyableOrMovable(const NotCopyableOrMovable&) = delete;
    NotCopyableOrMovable& operator=(const NotCopyableOrMovable&)
        = delete;

    // The move operations are implicitly disabled, but you can
    // spell that out explicitly if you want:
    NotCopyableOrMovable(NotCopyableOrMovable&&) = delete;
    NotCopyableOrMovable& operator=(NotCopyableOrMovable&&)
        = delete;
    };
    ```

상위 클래스가 copyable 이라면 상속받는 하위 클래스도 copyable 이어야 한다. 이 경우 `public virtual clone()` 메소드 등을 상위 클래스에 정의해둬라.

## Structs vs. Classes

상수와 관련된 데이터만을 지니는 수동적인 객체만을 `struct` 로 선언해라.

다른 모든 객체를 `class` 로 선언해라.

## Structs vs. Pairs and Tuples

## Inheritance

## Operator Overloading

## Access Control

## Declaration Order
