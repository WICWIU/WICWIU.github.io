!!! info

    이번 포스트에서는 [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html) 의 목표까지만 다루고 실질적인 코딩 컨벤션 규칙은 다음 포스트에서 다루겠습니다. 
    
    다음 포스트에서 다룰 [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html) 은 독자가 [Abseil](https://abseil.io/) 을 기본적으로 사용할 수 있다고 가정하고 있기 때문에 기초적인 [Abseil](https://abseil.io/) 사용법을 알고 넘어가야 합니다.
    
    그런데 [Abseil](https://abseil.io/) 의 공식적인 빌드 툴은 [bazel](https://bazel.build/) 이기 때문에 [bazel](https://bazel.build/) 에 대하여 먼저 간단히 알아보겠습니다.

---

# Bazel

[bazel](https://bazel.build/) 은 `C++`, `Java`, `Android`, `iOS`, `Go` 등의 소프트웨어의 빌드 및 테스트를 자동화시켜주는 툴이다.

## Why Bazel?

[bazel](https://bazel.build/) 은 **Make**, **Ant**, **Gradle** 등의 툴과 비슷하다. 하지만 [bazel](https://bazel.build/) 은 다음의 이유 때문에 특별하다.

- [bazel](https://bazel.build/) 은 **Google** 의 빌드 시스템이다.

- [bazel](https://bazel.build/) 의 빌드 명령어는 매우 쉽고 간단하고 고수준이다. 가령 **Make** 로 빌드하기 위하여 **Makefile** 을 만들면 모든 파일을 일일이 컴파일러에게 명시해주어야만 해서 너무 어렵다.

- 똑같은 빌드 파일로 여러 플랫폼과 아키텍처로 컴파일 할 수 있다. **Google** 은 실제로 데이터센터 서버에서 실행될 거대한 프로그램부터 핸드폰에서 실행될 어플리케이션까지 모두 다 [bazel](https://bazel.build/) 을 사용하여 컴파일한다.

- [bazel](https://bazel.build/) 은 빌드 파일을 분석해서 다시 컴파일해야하는 부분만 컴파일한다.

    !!! note
    
        **Flutter** 로 개발 해보신 분들은 수정된 코드만 탐지해서 빠르게 리빌딩하는 **Hot reload** 기능이 떠오르실 겁니다. 그것을 `C++` 프로젝트에서도 할 수 있다는 것이지요.

- [bazel](https://bazel.build/) 은 매우 큰 프로그램도 매우 빠르게 빌드한다. 가령 우리 **Google** 에서는 서버에서 실행될 소스파일만 `10` 만개 정도인 프로젝트가 있는데 파일이 변경되지 않으면 빌드 시간이 단지 `0.2` 초밖에 안걸린다.

- [bazel](https://bazel.build/) 은 Linux, macOS, Windows 에서 실행가능하고 손쉽게 cross-compile 을 할 수 있다. 

- [bazel](https://bazel.build/) 이 crash 가 거의 나지 않는다는 것을 우리 **Google** 이 보증한다. 우리도 [bazel](https://bazel.build/) 로 **Google** 의 프로젝트를 관리하고 있다.

그렇다면 우리 **Google** 은 왜 다른 빌드 시스템을 사용하지 않는가?

- `Make`, `Ninja`: 이 툴들은 타겟에 대한 매우 정확한 명령을 제공한다. 하지만 그것을 올바르게 사용하는 것은 전적으로 개발자의 몫이다. 반면 [bazel](https://bazel.build/) 의 유저 인터페이스는 매우 고수준이다.

- `Ant`, `Maven`: 이들은 **Java** 만 지원하는 반면 [bazel](https://bazel.build/) 은 다양한 언어를 모두 다 지원한다.

- `Gradle`: [bazel](https://bazel.build/) 의 빌드 파일은 `Gradle` 보다 구조화되어 있어서 더 이해하기 쉽고 재사용하기 쉽다.

우리 **Google** 도 원래 **Makefile** 을 사용해왔다. 그런데 소프트웨어의 규모가 커지면 커질수록 **Makefile** 은 빌드 속도를 느리게했다. 그리고 **Makefile** 의 빌드를 신뢰할 수도 없었다. 결과적으로 **Makefile** 은 **Google** 개발자들의 생산성에 직격타를 날렸고 우리 **Google** 의 기업적인 민첩성에도 영향을 미쳤다. [bazel](https://bazel.build/) 은 이 문제를 해결했다. 

[bazel](https://bazel.build/) 은 처음에는 **Google** 내부의 서버 프로그램 개발 도구였다. 하지만 그 유용성이 입증되어 계속 확장되었다. 그 결과 지금은 심지어 핸드폰 어플을 위한 `iOS` 와 `Android` 빌드에도 쓸 수 있다.

[bazel](https://bazel.build/) 은 다음과 같은 프로젝트에서 빛을 발한다.

- 매우 큰 프로젝트.

- 여러 언어로 작성된 프로젝트.

- 여러 플랫폼에 배포되어야 하는 프로젝트.

- 수많은 테스트를 해야 하는 프로젝트.

!!! note

    모두 [**WICWIU**](https://github.com/WICWIU/WICWIU) 에 유용한 기능들 같네요. 모든 **Google** 프로젝트가 [bazel](https://bazel.build/) 로 빌드되기 때문에 TensorFlow 도 [bazel](https://bazel.build/) 로 빌드됩니다.

    심지어 우리 연구실이 좋아하는 **PyTorch** 도 [bazel](https://bazel.build/) 로 빌드되더군요. https://github.com/pytorch/pytorch/blob/master/BUILD.bazel 를 보세요.

## Installation

그러면 [bazel](https://bazel.build/) 을 쉽게 배울 수 있도록 [bazel](https://bazel.build/)  튜토리얼을 딥러닝 서버에서 가볍게 배워보겠습니다.

다음의 명령어로 [bazel](https://bazel.build/) 의 실행파일을 다운로드 받고 [bazel](https://bazel.build/) 을 명령어 `bazel` 로 실행할 수 있도록 환경변수에 추가합니다.

```shell
$ wget https://github.com/bazelbuild/bazel/releases/download/3.4.1/bazel-3.4.1-linux-x86_64
$ mkdir ~/bin
$ mv bazel-3.4.1-linux-x86_64 ~/bin/bazel
$ chmod +x ~/bin/bazel
```

=== "bash 쉘 사용자"

    그리고 `export PATH="$PATH:$HOME/bin"` 를 `.bashrc` 에 추가해주세요. 다음의 명령어로 간단하게 추가할 수 있습니다.

    ```shell
    $ echo export PATH="\$PATH:\$HOME/bin" >> ~/.bashrc
    ```

    그리고 다음 명령어를 통해 `rc` 파일을 쉘에 적용해주거나 쉘을 다시 시작하세요.

    ```shell
    $ source ~/.bashrc
    ```

=== "zsh 쉘 사용자"

    그리고 `export PATH="$PATH:$HOME/bin"` 를 `.zshrc` 에 추가해주세요. 다음의 명령어로 간단하게 추가할 수 있습니다.

    ```shell
    $ echo export PATH="\$PATH:\$HOME/bin" >> ~/.zshrc
    ```

    그리고 다음 명령어를 통해 `rc` 파일을 쉘에 적용해주거나 쉘을 다시 시작하세요.

    ```shell
    $ source ~/.zshrc 
    ```

이제 [bazel](https://bazel.build/) 을 시험삼아 실행해보세요.

```shell
$ bazel
```

## Tutorial

다음의 튜토리얼을 통하여 [bazel](https://bazel.build/) 의 기본적인 사용법을 알 수 있습니다.

1. [Bazel C++ Simple Tutorial](https://docs.bazel.build/versions/3.4.0/tutorial/cpp.html) 

2. [Bazel C++ Common Use Cases](https://docs.bazel.build/versions/3.4.0/cpp-use-cases.html)

이 튜토리얼을 마쳤다면 다음의 [bazel](https://bazel.build/) 사용법을 알 수 있습니다.

- [bazel](https://bazel.build/) 로 프로젝트를 빌드하려면 workspace 를 설정해야 한다. workspace 란 프로젝트의 소스파일과 [bazel](https://bazel.build/) 의 빌드를 저장하는 디렉토리이다. workspace 는 `WORKSPACE` 파일을 갖고 있어야 하고, 하나 이상의 `BUILD` 파일을 갖고 있어야 한다.

- `C++` 빌드 규칙을 선언하기 위하여 `BUILD` 파일에 먼저 다음의 선언을 해야 한다.

    ```c++ 
    load("@rules_cc//cc:defs.bzl", "cc_binary", "cc_library")
    ```

- `C++` 파일의 컴파일을 위하여 `cc_binary` 빌드 규칙을 만들어야 한다. 다음과 같이 `name` 에 실행파일의 이름, `srcs` 에 소스파일을 명시한다.

    ```c++ 
    cc_binary(
        name = "hello-world",
        srcs = ["hello-world.cc"],
    )
    ```

- [bazel](https://bazel.build/) 로 프로젝트를 빌드하기 위하여 다음 명령어를 사용한다. 이때 `WORKSPACE` 파일이 있는 곳에서 이 명령어를 실행해야 한다.

    ```shell
    $ bazel build //path/to/package:target-name
    ```

- 다음 명령어의 출력 결과를 [Graphviz](http://www.webgraphviz.com/) 에 입력하면 프로젝트 의존성을 그래프 형태로 비쥬얼하게 볼 수 있다.

    ```shell
    $ bazel query --notool_deps --noimplicit_deps "deps(//main:hello-world)" --output graph
    ```

- 다음과 같이 빌드 규칙의 `deps` 에 의존성을 추가하면 된다. 그러면 [bazel](https://bazel.build/) 이 의존성을 먼저 빌드해준다.

    ```c++ 
    cc_library(
        name = "hello-greet",
        srcs = ["hello-greet.cc"],
        hdrs = ["hello-greet.h"],
    )

    cc_binary(
        name = "hello-world",
        srcs = ["hello-world.cc"],
        deps = [
            ":hello-greet",
        ],
    )
    ```

- 의존성이 다른 디렉토리에 있다면 다음과 같이 `//path/to/library:target-name` 을 `dets` 에 추가하면 된다.

    ```c++ 
    cc_library(
        name = "hello-greet",
        srcs = ["hello-greet.cc"],
        hdrs = ["hello-greet.h"],
    )

    cc_binary(
        name = "hello-world",
        srcs = ["hello-world.cc"],
        deps = [
            ":hello-greet",
            "//lib:hello-time",
        ],
    )
    ```

    이때 `hello-time` 빌드 규칙에서 다음과 같이 `visibility` 옵션에 `//path/to/target:__pkg__` 를 추가하여 가시성 설정을 해주어야 한다.

    ```c++ 
    cc_library(
        name = "hello-time",
        srcs = ["hello-time.cc"],
        hdrs = ["hello-time.h"],
        visibility = ["//main:__pkg__"],
    )
    ```

- 여러 파일을 한꺼번에 컴파일하고 싶다면 다음과 같이 `glob` 을 사용한다.

    ```c++ 
    cc_library(
        name = "build-all-the-files",
        srcs = glob(["*.cc"]),
        hdrs = glob(["*.h"]),
    )
    ```

- 다음은 의존성에 대한 예시이다. `sandwich.h` 가 `bread.h` 에 의존하고, `bread.h` 가 `flour.h` 에 의존한다면 `BUILD` 파일을 다음과 같이 설정하면 된다.

    ```c++ 
    cc_library(
        name = "sandwich",
        srcs = ["sandwich.cc"],
        hdrs = ["sandwich.h"],
        deps = [":bread"],
    )

    cc_library(
        name = "bread",
        srcs = ["bread.cc"],
        hdrs = ["bread.h"],
        deps = [":flour"],
    )

    cc_library(
        name = "flour",
        srcs = ["flour.cc"],
        hdrs = ["flour.h"],
    )
    ```

- 다른 디렉토리에 있는 의존성에 대한 예시이다. `C++` 프로젝트가 다음과 같은 파일 구조를 갖고 있다고 하자.

    ```c++ 
    └── my-project
        ├── legacy
        │   └── some_lib
        │       ├── BUILD
        │       ├── include
        │       │   └── some_lib.h
        │       └── some_lib.cc
        └── WORKSPACE
    ```

    이때 `some_lib.cc` 가 `some_lib.h` 에 의존하고 있다. 그런데 `#include "legacy/some_lib/include/some_lib.h` 로 헤더를 선언한 것이 아니라. `#include "some_lib.h` 로 헤더를 선언했다고 하자. 그러면 [bazel](https://bazel.build/) 이 `some_lib.cc` 를 빌드하기 위하여 헤더 파일을 탐색할 경로 `legacy/some_lib/include` 를 추가해주어야 한다.

    ```c++ 
    cc_library(
        name = "some_lib",
        srcs = ["some_lib.cc"],
        hdrs = ["include/some_lib.h"],
        copts = ["-Ilegacy/some_lib/include"],
    )
    ```

- `C++` 프로젝트가 다른 디렉토리에 있는 `mylib.so` 라는 라이브러리와 `mylib.h` 라는 헤더에 의존성이 있다면 `cc_library` 를 다음과 같이 선언하면 된다.

    ```c++ 
    cc_library(
        name = "mylib",
        srcs = ["mylib.so"],
        hdrs = ["mylib.h"],
    )
    ```

*References*: 

:   https://bazel.build/ 

---

# Abseil

[Abseil](https://abseil.io/) 은 **Google** 에서 제작한 `C++` 표준 STL 라이브러리의 확장이다. **Google** 은 [Abseil](https://abseil.io/) 을 통하여 STL 의 부족한 부분을 보완했다. [Abseil](https://abseil.io/) 은 **Google** 의 `C++` 프로젝트의 기반으로 광범위하게 사용되고 있다.

## Why Abseil?

**Google** 은 `C++` 프로젝트에 [Abseil](https://abseil.io/) 을 사용하는 것을 추천한다.

*Why*:

- [Abseil](https://abseil.io/) 은 항상 최신 `C++` 표준과 호환된다.

- **Google** 의 오픈소스 프로젝트와 호환된다. 가령 **TensorFlow**, **gRPC** 등등.

    !!! note
    
        TensorFlow 소스코드 분석을 해야 할 때도 [Abseil](https://abseil.io/) 을 알아야 하겠네요.

- [Abseil](https://abseil.io/) 은 **Google** 이 `C++` 제품을 만드는 인터페이스이기 때문에 **Google** 이 어떻게 `C++` 프로젝트를 개발하는지 배울 수 있다.

- **Google** 은 [Abseil](https://abseil.io/) 을 매일 발전시키고 있고 `C++` 개발자들의 요구를 끊임없이 [Abseil](https://abseil.io/) 에 구현하고 있다.

## Codemap

[Abseil](https://abseil.io/) 은 다음의 `C++` 라이브러리들로 이루어져 있다.

- `base` : 다른 라이브러리의 초기화 코드를 담고 있다. 다른 라이브러리가 이 라이브러리에 의존한다.

- `algorithm` : `C++` STL 의 `algorithm`의 확장이다.

- `container` : `C++` STL 의 `container`의 확장이다.

- `debugging` : 메모리 누수 검증, stacktrace 등등 을 지원하는 디버깅 라이브러리이다.

- `hash` : hash 타입과 관련 기능을 제공하는 프레임워크이다.

- `memory` : `std::make_unique()` 와 호환되는 메모리 관리 관련 라이브러리이다.

- `meta` : 타입 체크 관련 기능을 제공한다.

- `numeric` : `128` 비트 정수형 관련 기능을 제공한다.

- `strings` : STL 과 호환되는 `string` 관련 기능을 제공한다.

- `synchronization` : `std::mutex` 를 대체하는 `absl:Mutex` 등 병렬 실행에 관한 기능을 제공한다.

- `time` : 시간과 관련된 다양한 기능을 제공한다.

- `types` : non-container 타입 관련 기능을 제공한다.

- `utility` : 여러 유틸리티와 도움이 될만한 코드들을 제공한다.

*References*: 

:   https://abseil.io

---

# C++ Version

C++17 을 써라. 아직 C++2x 는 쓰지 마라. 비표준 C++ 확장은 쓰지 마라. 

비표준 C++ 확장에 대해서는 아래에서 더 자세히 설명한다.

---

!!! note

    아래의 내용은 [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html) 을 정리한 것입니다.

    아래의 내용에서 말하는 1인칭 "나" 또는 "우리" 등등은 제가 아니라 **Google** 이며, 타겟 독자들은 **Google** 의 개발자입니다.

# Google C++ Style Guide

## Background

구글의 많은 프로젝트가 C++ 로 진행된다. 이는 C++ 의 강력함 때문이다. 그러나 그 강력함이 종종 복잡함을 낳는다. 복잡함은 곧바로 버그를 낳고 가독성과 유지보수성과 확장성을 떨어뜨린다.

이 가이드는 그러한 C++ 의 복잡함을 통제하기 위하여 **Do** 들과 **Don't** 들을 알려줄 것이다. 구글의 대부분의 C++ 프로젝트가 이 컨벤션을 따른다.

## Goals of the Style Guide

앞으로 소개할 컨벤션들은 다음과 같은 목표를 달성하기 위하여 정립된 규칙들이다. 왜 이런 스타일로 코딩해야 하는지 의문이 드는 사람에게 이러한 목표를 달성하기 위하여 라고 말할 수 있을 것이다.

1. Style rules should pull their weight

    (코딩 컨벤션은 가치가 있어야 한다.)

    코딩 컨벤션은 해당 공동체의 모든 개발자에게 설명되고 기억되어야 할만한 가치가 있어야 한다. 그러므로 매우 안좋은 코딩 컨벤션은 어차피 사람들이 그렇게 코딩하지 않을 것이므로 굳이 정립해둘 필요가 없다. 가령 `goto` 문이 매우 안좋은 코드라는 것은 이미 널리 알려졌기 때문에 이것들은 굳이 코딩 컨벤션에 언급하지 않았다.

2. Optimize for the reader, not the writer

    (writer 가 아닌 reader 를 위하여 최적화하라.)

    구글의 대부분의 프로젝트은 단기성 프로젝트가 아니라 장기 프로젝트이다. 그러므로 프로젝트는 계속해서 후임 개발자에게 읽힌다. 이는 프로젝트에 **writing** 시간보다 **reading** 시간이 더 많이 소모된다는 것이다. 그러므로 우리는 코드를 **writing** 에 최적화시킨 것이 아니라 가독성, 유지보수, 디버깅에 좋도록 최적화시켰다. 그러므로 이 목표를 기반으로 "Leave a trace for the reader"(독자에게 흔적을 남겨라) 라는 세부 목표 등이 세워질 수 있다. 가령 포인터의 ownership 을 옮기는 등의 매우 흔치 않은 일이 발생하면 왜 이렇게 했는지 후임 개발자가 알 수 있도록 힌트를 남겨야 한다.

3. Be consistent with existing code

    (코딩 컨벤션을 지키면서 코딩해라.)

    일관된 코딩 컨벤션을 유지하는 것은 우리를 다른 더 중요한 문제에 집중할 수 있게 해준다. 대부분 Style 의 통일은 자동화 될 수 있다. "Be Consistent" 라는 목표는 항상 "just pick one and stop worrying about it."(하나를 택하고 더 이상 걱정마세요) 로 요약된다. 코딩 컨벤션에 자유를 주면 오히려 이 스타일이 더 낫냐 저 스타일이 더 낫냐고 토론하면서 더 많은 비용이 발생한다.

4. Be consistent with the broader C++ community when appropriate

    (가능하면 널리 알려진 C++ 코딩 컨벤션도 지켜라.)

    다른 C++ 커뮤니티가 지키는 코딩 컨벤션에 대한 적절한 일관성을 유지하는 것도 **목표 3** 과 같은 맥락에서 이해될 수 있다. 하지만 널리 통용되는 C++ 코딩 컨벤션에도 장점이 있고 단점이 있다. 장점은 받아들여야 하지만, 단점은 널리 통용되는 C++ 코딩 컨벤션이라도 과감히 받아들이지 않는다.

5. Avoid surprising or dangerous constructs

    (위험한 C++ 문법은 피해라.)

    C++ 은 한 사람이 잠깐 생각하고 설계한 것과 달리 생각보다 놀랍고 위험한 기능들을 갖고 있다. 어떤 Style 가이드는 이런 함정에 빠지지 않기 위하여 정립되었다.

6. Avoid constructs that our average C++ programmer would find tricky or hard to maintain

    (대부분의 개발자들이 어려워하는 C++ 문법도 피해라.)

    C++ 은 그 복잡함 때문에 프로젝트에 추가되어서는 안되는 기능들도 갖고 있다. 까다로운 문법을 사용하는 것을 현 시점에서 당신의 팀원이 다 이해하였고 그 유용성도 잘 사용하게 되었다고 해도, 그 다음 팀, 또는 그 다음 팀의 팀원들이 그 까다로운 문법을 이해할 수 있을지 알 수 없다. 

7. Be mindful of our scale

    (당신의 코드 한 줄이 얼마나 큰 파급력을 미칠지 경각심을 가져라.)

    코드가 1억 줄이 넘어가는 프로젝트에는 수천명의 개발자가 함께 작업하고 있다. 이런 프로젝트에서는 사소한 실수가 막대하게 큰 비용을 발생시킬 수 있다. 가령 개인 프로젝트에서는 `namespace` 의 충돌이 아주 사소한 실수이지만, 큰 규모의 프로젝트에서는 매우 큰 비용을 발생시키는 문제가 된다.

8. Concede to optimization when necessary

    (가끔 성능 최적화가 필요하다면 코딩 컨벤션을 어겨도 좋다.)

    성능 최적화는 종종 필요하고 적절하다. 심지어 코딩 컨벤션을 어겨서라도 성능 최적화가 이루어질 수 있다면 성능 최적화된 코드를 과감히 작성하라. 

이 문서는 모든 합리적 제약을 기술하도록 작성된 것이다. 하지만 비판적인 자세를 가져라. 그리고 당신에게 확신이 안선다면 프로젝트 팀장에게 이 규칙에 대하여 당신의 비판을 말하라.

# Header Files

모든 `.cc` 파일은 `.h` 헤더 파일과 연관되어 있다. 아주 예외적으로 유닛 테스트에 사용되는 작은 `.cc` 파일들은 단지 `main()` 함수만 갖고 있다.

!!! note

    `C++` 프로그램을 작성하기 위한 파일의 확장자는 `.cc`, `.cpp`, `.cxx`, `.c++` 등이 있는데 [**WICWIU**](https://github.com/WICWIU/WICWIU) 는 `.cpp` 확장자를 사용하고 있습니다.

헤더 파일의 올바른 사용은 가독성, 프로젝트 크기, 프로젝트의 성능에 큰 영향을 미친다. 다음의 규칙들은 헤더 파일을 어떻게 올바르게 사용할 수 있는지 가이드해준다.

### Self-contained Headers

헤더 파일은 반드시 독립적이어야 한다. 즉, 그 자체로 컴파일 되어야 한다. 그리고 반드시 `.h` 확장자를 가져야 한다. 포함되어야 하는 비헤더 파일은 `.inc` 확장자를 가져야 하며 아주 예외적으로 사용되어야 한다. 헤더 파일이 반드시 독립적이어야 한다는 것은 그것이 필요한 모든 것을 지니고 있어야 한다는 것이다. 

헤더 파일은 아래에서 설명할 헤더 가드를 가져야만 한다.

`template` 과 `inline` 함수는 선언과 동시에 정의해라. 그렇지 않으면 link 단계에서 에러가 발생한다. 예외적으로 template 구현을 특수화시켜야 할 경우 오직 해당 `.cc` 파일에서 구현하는 것이 허락된다.

### The `#define` Guard

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

### Forward Declarations

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

### Inline Functions

`inline` 함수는 오직 코드가 작을 때, 즉 코드가 10 줄 이하일 때 사용하라.

*Why*:

:	inline 함수는 그 코드가 작으면 작을수록 효율적이다. 하지만 `inline` 함수를 남용하면 프로그램이 오히려 느려진다. 함수의 크기가 커지면 inlining 은 코드의 크기를 매우 크게 한다.

`virtual` 이나 리커시브 함수 같은 함수들은 보통 `inline` 되지 않는다. 특히 리커시브 함수는 절대 `inline` 화 시키면 안 된다.

### Order of Includes

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
    
### Names of Includes

모든 헤더 파일은 `.` 이나 `..` 이 없이 포함되어야 한다. 

!!! example

    `google-awesome-project/src/base/logging.h` 헤더 파일은 다음과 같이 포함되어야 한다.

    ```c++ 
    #include "base/logging.h"
    ```

### 헤더 포함 규칙

필요한 모든 헤더를 포함시켜라.

!!! example

    만약 당신이 `bar.h` 와 `foo.h` 헤더를 포함해야 하는데 `bar.h` 가 `foo.h` 를 포함하고 있다면 당신의 파일에서 실제로 `bar.h` 만 포함해도 된다. 하지만 `bar.h` 와 `foo.h` 를 포함시켜라.

### 조건부 헤더 

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

### Namespaces 

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
    
### Unnamed Namespaces and Static Variables

만약 어떤 `.cc` 파일이 외부에서 참조되지 않는다면 코드를 이름없는 `namespace` 로 묶거나 `static` 으로 선언해라. `main()` 함수를 정의하는 `main.cpp` 이 보통 이 경우에 해당한다.

!!! note

    이름없는 `namespace` 로 묶거나 함수를 `static` 으로 선언하면 외부에서 접근할 수 없게 된다.

!!! example

    외부에서 참조되지 않는 `.cc` 파일이라면 모든 코드를 다음의 이름없는 `namespace` 로 묶어라. 

    *Do*:

    ```c++ 
    namespace {
    ...
    }   // namespace
    ```

### Nonmember, Static Member, and Global Functions

함수를 `namespace` 에 두는 것을 선호해라. 완전한 전역함수를 사용하지 마라. 

!!! note

    **"메소드"** 는 클래스의 멤버 함수이고, **"함수"** 는 클래스에 속하지 않은 일반적인 함수입니다.

그렇다고 모든 함수를 어떤 클래스의 `static` 메소드로 선언하지는 마라. `static` 메소드는 반드시 그 클래스와 밀접하게 연관되어 있어야 한다.

### Local Variables

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

### Static and Global Variables

`static` 변수를 갖는 객체를 만들지 마라.

!!! info

    자세한 설명은 https://google.github.io/styleguide/cppguide.html#Static_and_Global_Variables 참고

(일단 Pass)

#### Decision on destruction

(일단 Pass)

#### Decision on initialization

(일단 Pass)

#### Common patterns

(일단 Pass)

### thread_local Variables

(일단 Pass)

---

# Classes

우리는 클래스를 광범위하게 사용한다. 이 섹션에서 클래스를 사용할 때 **Do** 들과 **Don't** 들을 설명한다.

### Doing Work in Constructors

생성자에서 `virtual` 메소드를 호출하지 마라.

생성자에서 에러 신호를 보낼 수 없는 경우 에러가 발생할만한 초기화는 피해라. 프로그램에 적합하다면 생성자에서 에러가 발생했을 때 프로그램을 중단하도록 해라.

### Implicit Conversions

암시적 형변환을 정의하지 마라. `explicit` 키워드로 형변환을 막거나 일변수 생성자의 호출을 차단해라.

!!! note

    암시적 형변환은 가령 `double` 변수를 받는 메소드에 `int` 형 변수를 전달할 때 발생한다.

!!! example

    다음과 같이 `explicit` 키워드로 암시적 형변환을 막아라.

    *Do*:

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

### Copyable and Movable Types

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

    *Do*:

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

### Structs vs. Classes

상수와 관련된 데이터만을 지니는 수동적인 객체만을 `struct` 로 선언해라.

다른 모든 객체를 `class` 로 선언해라.

### Structs vs. Pairs and Tuples

변수가 의미있는 이름을 가질 수 있도록 `std::tuple` 이나 `std::pair` 보다 `struct` 를 사용해라.

*Why?*:

:   `std::tuple` 이나 `std::pair` 를 사용하면 커스텀 데이터 구조를 정의하지 안하도 되지만, `.first`, `.second`, `std::get<X>` 를 사용해야 하기 때문에 각 변수의 의미를 알아차리기가 쉽지 않다. 이때 `struct` 를 사용하면 코드를 읽을 때 각 변수들이 어떤 의미인지 쉽게 알 수 있다.

    `pair` 나 `tuple` 은 그 의미가 특정되지 않은 일반적인 코드에 적합하다.

상속은 반드시 **is-a** 관계에서만 사용해라. 상속을 너무 많이 사용하지 마라.

*Why*:

:	과도한 상속은 오버헤드를 발생시켜 성능 저하를 일으킨다.

!!! example

    `Foo` 가 `Bar` 클래스를 상속받는다면 반드시 **"`Foo` 는 `Bar` 의 한 종류이다."** 라는 관계가 성립해야만 한다.

`virtual` 함수나 `virtual` 소멸자를 오버라이딩할 때 명시적으로 주석을 달아라.

### Inheritance

상속을 사용하려면 `public` 상속으로 해라.

오버라이딩을 했다면 오버라이딩 했다는 주석을 달거나 `override` 키워드 또는 `final` 키워드를 꼭 붙여라.

오버라이딩 할 때에는 `virtual` 키워드를 달지 말아라.

!!! example

    메소드를 오버라이딩 했다면 다음과 같이 `virtual` 키워드는 달지 말고, `override` 키워드를 반드시 달고, 추상 클래스의 메소드는 `= 0` 또는 `= delete` 또는 `= defualt` 로 선언하여 구현 불가능하게 만들어라.

    *Do*:

    ```c++ 
    class Shape {
    public:
        virtual void Rotate(double radians) = 0;
    };

    class Circle : public Shape {
    public:
        void Rotate(double radians) override;
    };
    ```

*Why:*

:	상위 클래스의 `virtual` 메소드를 오버라이딩 하는 메소드가 아닌데도 `override` 키워드나 `final` 키워드가 존재하면 컴파일 에러가 발생하여 에러를 손쉽게 잡아낼 수 있다.

    또한 이 키워드들 자체가 하나의 Documentation 처럼 기능한다. 이로써 코드를 읽는 사람이 모든 상위 클래스를 확인하면서 문제의 메소드가 `virtual` 인지 아닌지 확인하지 않아도 된다.

오버라이딩 메소드에는 `virtual` 키워드를 붙이지 말아라.

### Operator Overloading

연산자 오버로딩은 신중하게 선택해라. 연산자의 의미가 명백하고, 뭔가 과격하거나 놀라운 것이 아니고, 기존의 연산자의 의미와 일관성이 있을 때에만 연산자 오버로딩을 해라.

!!! example

    `|` 은 `C++` 에서 OR 연산에 사용된다. `|` 은 쉘에서 파이프로 쓰이는데, 갑자기 `|` 연산자를 파이프의 의미를 갖는 연산자로 오버로딩 하면 안된다.

user-defined literals 은 사용하지 말아라.

!!! example

    `C+` 은 `operator""` 로써 user-defined literals 을 사용할 수 있게 한다. 하지만 사용하지 마라.

연산자를 오버로딩했으면 그와 관련된 연산자도 오버로딩해라.

!!! example

    `>` 연산자를 오버로딩 했으면 `<` 연산자도 오버로딩 해라.

연산자 오버로딩을 피하기 위하여 당신만의 함수나 메소드를 구현하지는 말아라.

!!! example

    `==`, `=`, `<<` 연산자 오버로딩을 하지 않고 싶어서 `Equals()`, `CopyFrom()`, `PrintTo()` 같은 함수나 메소드를 구현하지 말아라. 그냥 연산자 오버로딩을 해라.

`&&`, `||`, `,`(comma), `&` 연산자와 `operator""` 는 오버로딩 하지 마라.

### Access Control

클래스 데이터 멤버는 그것이 상수가 아닌 이상 `private` 으로 선언해라.

*Why*:

:	이것은 데이터의 불변성을 보장해준다.

### Declaration Order

`public:` 으로 선언을 시작하고, 그 다음은 `protected:`, 마지막으로 `private:` 을 선언해라.

각 섹션의 선언의 순서는 이렇게 해라. `type` 들, 상수들, factory 함수들, 생성자들, 할당 연산자들, 소멸자, 다른 메소드들, 데이터 멤버들.

!!! example

    (실제 구글 C++ 프로젝트의 클래스 선언의 예시를 가져와야 할 듯)

각 섹션의 비슷한 선언들을 함께 묶어라.

---

# Functions

### Inputs and Outputs

`C++` 에서 함수의 출력은 `return` 값 또는 output parameter 로 전달된다. 이때 output parameter 보다 `return` 값으로 출력을 전달해라. 

*Why*:

:	그게 가독성이 더 좋고 성능이 output parameter 와 같거나 더 낫다.

!!! example

    *Don't*:

    ```c++ 
    #define OUT
    void calculate(int a, int b, OUT int& c) {
        c = a + b;
    }
    ```
    
    위의 함수는 output parameter 로 출력을 전달한다. 이렇게 하지 말고 다음과 같이 `return` 을 사용해라.

    *Do*:

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

### Write Short Functions

함수를 작게 만들어라.

함수가 하나의 기능만 하도록 만들어라.

우리는 가끔 큰 함수가 필요하다는 것을 알고 있으므로 함수 크기에 대한 엄격한 길이 제한을 두지 않을 것이다. 그러나 함수의 길이가 `40` 줄이 넘어간다면 함수를 세부 기능으로 쪼개서 더 작은 함수로 만들 수 있는지 검토해봐라.

함수를 짧고 단순하게 유지시키고, 다른 사람이 읽었을 때 쉽게 이해하고 바꿀 수 있도록 만들어라.

*Why*:

:	당신이 만든 함수가 엄청 큰데도 잘 기능한다고 하자. 그래도 후임 개발자가 나중에 와서 당신이 만든 함수에 새로운 기능을 추가해야할 때 시간이 오래걸리고 버그를 발생시킬 가능성이 크다.

    또 작은 함수를 만들면 테스트 하기에도 편하다.

### Function Overloading

오버로딩은 코드를 읽는 사람이 호출문만 보고 왜 이렇게 오버로딩했는지 이해할 수 있을 때에만 사용해라. 즉, 의미적으로 별 차이가 없는 오버로딩만 사용해라.

!!! example

    `const std::string&` 을 파라미터로 받는 함수와 `const char*` 를 파라미터로 받는 함수를 오버로딩 할 수 있다.

    하지만 `std::string_view` 로 통합하는 것이 더 낫다.

### Default Arguments

`virtual` 이 아닌 메소드에 디폴트 값이 항상 같은 값을 가질 때에만 디폴트 파라미터를 사용해라.

`virtual` 메소드에서는 디폴트 값을 사용하지 마라.

물론 디폴트 파라미터도 가끔 적절하고 가독성을 높혀준다. 만약 디폴트 파라미터를 써야할지 말지 혼란스럽다면 그냥 오버로딩해라.

### Trailing Return Type Syntax

람다 표현식을 제외하면 대부분의 경우에서 trailing return type 을 사용하지 마라.

!!! example

    *Do*:

    ```c++
    int foo(int x);
    ```

    위와 같은 함수 선언이 일반적인 함수 선언이다. 하지만 trailing return type 을 사용하면 다음과 같이 함수를 선언할 수 있다.

    *Don't*:

    ```c++ 
    auto foo(int x) -> int;
    ```

    이 둘은 완전히 똑같은 선언이다. 하지만 람다 표현식 같은 예외적인 경우를 제외하고 기존의 함수 선언을 사용해라.

---

# Google-Specific Magic

우리는 우리의 `C++` 프로젝트를 더욱 견고하게 만들기 위하여 많은 트릭과 유틸리티를 사용한다. 

### Ownership and Smart Pointers 

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

    *Do*:

    ```c++ 
    std::unique_ptr<Foo> FooFactory();
    void FooConsumer(std::unique_ptr<Foo> ptr);
    ```

합리적인 이유가 없다면 Ownership 을 공유할 수 없도록 코딩해라.

*Why*:

:	Ownership 을 공유하면서 복사하는 비용이 많이 든다. 만약 성능 향상이 기대되고 `std::shared_ptr<const Foo>` 처럼 객체의 불변성이 보장된다면 Ownership 공유를 해도 좋다.

Ownership 을 공유해야 한다면 `std::shared_ptr` 을 사용해라.

절대로 `std::auto_ptr` 을 사용하지 말아라. 그 대신 `std::unique_ptr` 를 사용해라.

### cpplint

스타일을 점검하기 위하여 [cpplint.py](https://raw.githubusercontent.com/google/styleguide/gh-pages/cpplint/cpplint.py) 를 사용해라.

*Why*:

:	[cpplint.py](https://raw.githubusercontent.com/google/styleguide/gh-pages/cpplint/cpplint.py) 는 파일을 읽고 스타일 에러를 점검해주는 툴이다.

!!! note

    구글의 프로젝트는 저마다 `cpplint.py` 를 어떻게 사용해야 하는지 메뉴얼을 갖고 있다. 만약 당신의 프로젝트에 `cpplint.py` 가 없다면 당신이 따로 [cpplint.py](https://raw.githubusercontent.com/google/styleguide/gh-pages/cpplint/cpplint.py) 를 다운로드해라.

---

# Other C++ Features

### Rvalue References

Rvalue 참조자를 아래에서 정의하는 특별한 경우에서만 사용해라. Rvalue 참조자는 오직 임시 객체에만 사용되는 참조자이다.

(https://google.github.io/styleguide/cppguide.html#Rvalue_references)

### Friends

`friend` 키워드는 반드시 같은 파일 내에서 사용해라. 

*Why:*

:	이로써 코드를 읽는 사람이 다른 파일을 찾아볼 필요가 없도록 해라.

### Exceptions

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

### noexcept

(일단 Pass)

### Run-Time Type Information (RTTI)

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

    또한 무분별한 RTTI 의 사용은 프로젝트의 유지보수성을 떨어뜨린다. 왜냐하면 코드를 읽는 사람이 프로젝트를 분석하면서 `typeid` 나 `dynamic_cast` 가 나올 때마다 포인터가 실제로 갖고 있는 객체에 따라 분기를 나눠가면서 분석해야 하기 때문이다. 가령 다음과 같은 코드를 보자.

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

### Casting

`C` 스타일 형변환을 사용하지 말고 `C++` 스타일의 형변환을 사용해라.

*Why:*

:	`C` 스타일 형변환은 매우 모호하다. 이로써 여러가지 문제가 발생한다. 데이터가 손실되는 것은 물론이고 어떤 의도로 작성된 코드인지 이해하기 힘들 수도 있다.

!!! example

    *Do*:

    가령 `C++` 스타일 형변환은 `static_cast<float>(double_value)` 이다. 
    
    또 `int64 y = int64{1} << 42` 와 같은 brace initialization 도 `C++` 형변환이다.

    이러한 `C++` 스타일 형변환을 사용해라.

    *Don't*:

    하지만 `(int)x` 같은 `C` 스타일 형변환은 사용하지 마라.

산술 타입의 데이터를 형변환 할 때 `int64{x}` 와 같은 brace initialization 을 사용해라.

*Why:*

:	brace initialization 은 데이터 손실이 일어나면 컴파일이 되지 않도록 해주기 때문에 가장 안전하다.

`static_cast` 는 상위 클래스의 포인터를 하위 클래스로 형변환할 때, 또는 하위 클래스의 포인터를 상위 클래스로 형변환할 때 사용해라. 단, 이때 상위 클래스 포인터를 하위 클래스 포인터로 형변환 한다면 객체가 반드시 하위 클래스이어야 한다.

`const` 의 의미를 제거해야 하는 상황에서 `const_case` 형변환을 사용해라.

위험한 형변환이나 `int` 를 포인터로 형변환 하는 등 가끔 말도 안 되는 형변환이 필요할 때 `  reinterpret_cast` 를 사용해라. 단, `reinterpret_cast` 는 당신이 지금 하고 있는 행동이 무엇인지 완벽하게 이해하고 있을 때 사용해라. 또한 `reinterpret_cast` 를 사용하기 전에 `absl::bit_cast` 를 사용할 수 있는 상황인지 점검해봐라.

비트수준의 데이터를 다른 타입으로 바꾸어야 할 때 `absl::bit_cast` 를 사용해라.

!!! example

    *Do*:

    `double` 타입의 데이터를 `int64` 로 바꾸고 싶을 때 `absl::bit_cast` 를 사용해라.

`dynamic_cast` 에 대한 가이드는 RTTI 섹션을 보아라.

### Streams

상황에 따라 스트림을 적절히 사용해라. 스트림이란 `<iostream>` 헤더로 추가되는 `C++`의 표준 I/O 기능이다. 구글의 많은 프로젝트가 디버깅 로그와 테스트 코드 진단을 위하여 스트림을 사용한다.

`printf` 를 사용하지말고 `<<` 또는 `>>` 를 사용해라.

*Why:*

:	`printf` 는 사용하기 어렵고 이식성이 매우 떨어진다. 심지어 `std::string` 을 사용할 수도 없고 user-defined 타입은 당연히 사용 불가능하다. 스트림은 콘솔 I/O 를 위하여 `std::cin`, `std::cout`, `std::cerr`, `std::clog` 를 제공하는데 이것을 사용해라.

`<<` 를 오버로딩 한다면 스트림을 위하여서만 오버로딩해라.

하지만 `std::cerr` 나 `std:clog` 를 사용하는 것보다 로그 라이브러리를 사용해라.

또한 `std::stringstream` 을 사용하지 말고 `absl/strings` 나 그와 비슷한 라이브러리를 사용해라.

end-user 를 위하여 I/O 스트림을 사용하지 말고 개발자를 위하여, 진단을 위하여, 테스트를 위하여 사용해라.

### Preincrement and Predecrement

후위 증감 연산자(`i++`, `i--`)가 꼭 필요하지 않은 이상 전위 증감 연산자(`++i`, `--i`) 를 사용해라.

*Why*:

:	전위 증감 연산자가 일반적으로 가독성이 더 좋고, 좀 더 효율적이다.

### Use of const

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

#### Where to put the const

(일단 Pass)

### Use of constexpr

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

### Integer Types

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

`int` 가 최소한 `4` 바이트일 것이라고 생각하는 것은 좋다. 하지만 `int` 가 `4` 바이트 이상일 것이라고 가정하지 마라. `4` 바이트 이상의 변수가 필요하다면 `int64_t` 나 `uint64_t` 를 써라.

합리적인 이유가 없는 이상 `uint32_t` 같은 unsigned 정수타입을 사용하지 마라. 특히, 숫자가 음수가 되지 않는다는 이유로 unsigned 타입을 사용하지 마라. 그 대신 `assert` 를 사용해라.

*Why*:

:	역사적인 사고로 인하여 `C++` 도 unsigned 타입을 사용하긴 하지만 표준 기구의 여러 구성원들이 이것을 실수라고 보고 있다. 하지만 지금 이 시점에서 `C++` 의 unsigned 를 고치는 것은 불가능하다.

당신의 코드가 사이즈를 반환한다면 차라리 큰 사이즈를 써서 모든 사이즈를 잘 표현할 수 있도록 해라.

정수형 변수를 변환할 때 매우 신중하도록 해라.

*Why*:

:	정수형 변수의 형변환을 할 때 잘못하면 취약점과 다른 여타 문제가 쉽게 발생한다.

### 64-bit Portability

코드는 항상 `64` 비트와 `32` 비트에 친화적이어야 한다.

`printf` 계열 함수를 사용하지 말고 `StrCat` 이나 `Substitute` 나 `std::ostream` 에서 제공하는 `std::cout` 등을 사용해라.

*Why*:

:	`printf()` 함수는 일부 정수 타입에 대하여 사용하기 어렵고 시용적이지 않은 매크로를 요구한다. 합리적인 이유가 없다면 `printf` 계열 함수를 사용하지 마라. 그 대신 typesafe 한 `StrCat` 이나 `Substitute` 나 `std::ostream` 에서 제공하는 `std::cout` 등을 사용해라.

`sizeof(void *) != sizeof(int)` 라는 것을 기억해라. 포인터 사이즈 정수형 변수를 원한다면 `intptr_t` 를 사용해라.

You may need to be careful with structure alignments, particularly for structures being stored on disk. Any class/structure with a int64_t/uint64_t member will by default end up being 8-byte aligned on a 64-bit system. If you have such structures being shared on disk between 32-bit and 64-bit code, you will need to ensure that they are packed the same on both architectures. Most compilers offer a way to alter structure alignment. For gcc, you can use __attribute__((packed)). MSVC offers #pragma pack() and __declspec(align()).

!!! help

    (내용이 이해 안됨)

`64` 비트 상수를 만들려면 braced-initialization 을 사용해라.

!!! example

    *Do*:

    ```c++ 
    int64_t my_value{0x123456789};
    uint64_t my_mask{3ULL << 48};
    ```

### Preprocessor Macros

매크로를 사용하지 마라. 특히 `.h` 파일에서 매크로를 절대로 사용하지 마라.

`C++` API 를 만들라고 절대로 매크로를 사용하지 마라.

*Why*:

:	당신이 보는 매크로 코드는 컴파일러가 보는 코드와 전혀 다르다. 이것은 예측하지 못하는 실행결과를 낳을 수도 있다. 특히 매크로가 전역 scope 에서 선언되었을 때 예측 못하는 결과를 낳는다.

    `C++` API 를 만들기 위하여 매크로를 사용하면 문제는 더 심각해진다. 개발자가 API 인터페이스를 잘못 사용함으로써 컴파일러로부터 나오는 모든 에러메시지로 매크로가 인터페이스를 어떻게 구성하고 있는지 설명해야 한다.

!!! example

    다음과 같은 패턴으로 코딩하지 마라.

    *Don't*:

    ```c++ 
    class WOMBAT_TYPE(Foo) {
        ...
        public:
        EXPAND_PUBLIC_WOMBAT_API(Foo)

        EXPAND_WOMBAT_COMPARISONS(Foo, ==, <)
    };
    ```

매크로 대신 `inline` 함수, enums, `const` 변수를 사용해라.

매크로로 성능을 향상 시키려 하지 말고 `inline` 함수를 써라.

매크로로 상수를 저장하려고 하지 말고 `const` 변수를 선언해라.

매크로로 약어를 만들려고 하지 말고 참조자를 사용해라.

매크로를 사용하기 전에 매크로를 사용하지 않고 원하는 기능을 구현할 수 있는지 검토해라. 그래도 굳이 매크로를 사용해야 한다고 판단이 선다면 당신의 프로젝트 팀장과 논의해봐라.

매크로를 사용했다면 사용한 직후 바로 `#undef` 를 해라.

### 0 and nullptr/NULL 

빈 포인터를 표현하기 위하여 `NULL` 이 아니라 `nullptr` 을 사용해라.

빈 `char` 를 표현하기 위하여 `0` 이 아니라 `'\0'` 을 사용해라.

### sizeof

`sizeof(type)` 이 아닌 `sizeof(varname)` 을 사용해라.

*Why*:

:	`sizeof(varname)` 은 특정 변수에 대한 사이즈를 반환한다. 만약 이후에 누군가 `varname` 을 다른 타입으로 바꾼다면 `sizeof(varname)` 의 사이즈는 안전하게 업데이트된다.

!!! example

    *Do*:

    ```c++ 
    MyStruct data;
    memset(&data, 0, sizeof(data));
    ```

    위와 같이 코딩해라. 그리고 다음과 같이 코딩하지 마라.

    *Don't*:

    ```c++ 
    memset(&data, 0, sizeof(MyStruct));
    ```

### Type deduction

가독성이 좋아지거나 성능 향상이 기대될 때에만 type deduction 을 사용해라.

단지 코딩할 때 더 편하다는 이유로 type deduction 을 사용하지 마라.

!!! example

    다음과 같은 함수 템플릿에서 명확한 타입 지정없이 `0` 만 전달하면 컴파일러가 type deduction 을 한다.

    ```cpp 
    template <typename T>
    void f(T t);

    f(0);  // Invokes f<int>(0)
    ```

    함수 템플릿의 type deduction 은 거의 대부분의 상황에서 해도 좋다.

!!! example

    다음과 같이 `auto` 로 변수를 선언하면 컴파일러가 알아서 type deduction 을 해준다.

    ```cpp 
    auto a = 42;  // a is an int
    auto& b = a;  // b is an int&
    auto c = b;   // c is an int
    auto d{42};   // d is an int, not a std::initializer_list<int>
    ```

    다음의 코드를 보자.

    ```cpp 
    std::unique_ptr<WidgetWithBellsAndWhistles> widget_ptr =
    absl::make_unique<WidgetWithBellsAndWhistles>(arg1, arg2);
    absl::flat_hash_map<std::string,
                        std::unique_ptr<WidgetWithBellsAndWhistles>>::const_iterator
    it = my_map_.find(key);
    std::array<int, 6> numbers = {4, 8, 15, 16, 23, 42};
    ```

    위 코드를 다음과 같이 type deduction 하면 코드가 더 분명해지고 가독성이 좋아진다. 그렇기 때문에 이런 식의 type deduction 은 해도 좋다. 

    ```cpp 
    auto widget_ptr = absl::make_unique<WidgetWithBellsAndWhistles>(arg1, arg2);
    auto it = my_map_.find(key);
    std::array numbers = {4, 8, 15, 16, 23, 42};
    ```

!!! example

    다음과 같이 함수 반환형에도 type deduction 을 사용할 수 있다. 

    ```cpp 
    auto f() { return 0; }  // The return type of f is int
    ```

    함수 반환형의 type deduction 은 함수가 매우 작을 때에만 사용해라.

!!! example

    다음과 같이 람다 표현식에도 `auto` 를 사용할 수 있다.

    ```cpp 
    // Sort `vec` in decreasing order
    std::sort(vec.begin(), vec.end(), [](auto lhs, auto rhs) { return lhs > rhs; });
    ```

    람다 표현식의 파라미터에 type deduction 을 사용하는 것은 타입 에러가 나지 않도록 주의해라. 이 겨웅 대부분의 상황에서 type deduction 을 하지 않고 그냥 자료형을 명시적으로 선언하는 것이 좋다.

!!! example

    다음은 Lambda capture 의 type deduction 예시이다.

    ```cpp 
    [x = 42, y = "foo"] { ... }  // x is an int, and y is a const char*
    ```

!!! example

    `tuple`, `struct`, `array` 나 `map` 같은 것에서도 `auto` 를 사용할 수 있다. 

    ```cpp 
    auto [iter, success] = my_map.insert({key, value});
    if (!success) {
        iter->second = value;
    }
    ```

    이 경우의 type deduction 은 가독성을 높혀준다. `struct` 에서도 type deduction 을 할 수도 있지만 다음과 같이 어떤 필드인지 주석을 달아줘라. 

    ```cpp 
    auto [/*field_name1=*/ bound_name1, /*field_name2=*/ bound_name2] = ...
    ```

하지만 type deduction 은 종종 코드를 모호하게 만든다. 코드를 읽는 사람은 type 을 결정하는 코드와 실행 흐름을 일일이 찾아내야만 한다.

그러므로 type deduction 을 사용하면 코드가 더 명확해지고 안전해질 때에만 사용해라. 단지 더 편하다는 이유로 type deduction 을 사용하지 마라.

### Designated initializers

Designated initializer 는 오직 `C++20` 프로젝트에서만 써라.

(참조 : https://google.github.io/styleguide/cppguide.html#Designated_initializers)

### Lambda expressions

적절한 상황에서만 lambda 표현식을 써라. 람다 표현식의 파라미터가 scope 를 넘어선다면 적절한 capture 를 사용해라. 꼭 필요한 변수만 capture 해라.

*Why*:

:   람다 표현식은 STL 의 알고리즘 라이브러리를 사용할 때 유용하고, 가독성을 높혀준다. capture 를 적절히 사용하면 코드 중복을 줄일 수 있다. 

    그러나 capture 의 무분별한 사용은 dangling-pointer 취약점을 발생시킨다. 포인터를 value 로 capture 하는 것은 deep copy 를 해주지 않는다. 또한 매우 긴 함수를 anonymous 함수로 람다 표현식에 전달하는 것은 가독성을 떨어뜨린다.

!!! example

    다음과 같은 코드로 scope 바깥의 모든 것을 참조할 수 있도록 capture 를 하지마라.

    *Don't*:

    ```c++ 
    Foo foo;
    ...
    executor->Schedule([&] { Frobnicate(foo); })
    ```

    다음과 같이 필요한 것만 capture 해라.

    *Do*:

    ```c++ 
    Foo foo;
    ...
    executor->Schedule([&foo] { Frobnicate(foo); })
    // BETTER - The compile will fail if `Frobnicate` is a member
    // function, and it's clearer that `foo` is dangerously captured by
    // reference.
    ```

!!! info

    dangling-pointer 란 이미 메모리 할당 해제가 된 객체를 포인팅하고 있는 포인터입니다.

!!! example

 	람다 표현식은 다음과 같이 anonymous 함수를 사용해야할 때 편하다.

    ```c++ 
    std::sort(v.begin(), v.end(), [](int x, int y) {
        return Weight(x) < Weight(y);
    });
    ```

    람다 표현식은 다음과 같이 capture `[]` 를 통하여 현재 scope 를 넘어선 변수의 접근도 허용해준다. `weight` 는 value 롤 capture 했고, `sum` 은 참조자로 capture 했다.

    ```c++ 
    int weight = 3;
    int sum = 0;
    std::for_each(v.begin(), v.end(), [weight, &sum](int x) {
        sum += weight * x;
    });
    ```

    캡처를 `[&]` 로 하면 다음과 같이 scope 바깥의 모든 것을 참조할 수 있게 된다.

    ```c++ 
    const std::vector<int> lookup_table = ...;
    std::vector<int> indices = ...;
    std::sort(indices.begin(), indices.end(), [&](int a, int b) {
        return lookup_table[a] < lookup_table[b];
    });
    ```

### Template metaprogramming

템플릿을 너무 복잡하게 사용하지 마라. 템플릿을 low level 에 존재하는 소수의 인터페이스를 구현하기 위하여 사용해라. 

템플릿을 사용하기 전에 당신의 팀원과 후임 개발자들이 코드를 잘 이해할 수 있고 유지보수하기에 편할지 두 번 생각해라.

*Why*:

:	템플릿은 매우 유연한 인터페이스를 제공해준다. [Google C++ Test](https://github.com/google/googletest), `std::tuple`, `std::function`, Boost.Spirit 같은 것들은 템플릿 없이 존재할 수 없었을 것이다.

    하지만 템플릿을 과도하게 쓰면 코드의 의미가 모호해진다. 또 전문가가 아닌 이상 코드를 쉽게 이해할 수 없게 만든다. 또 템플릿은 그 객체가 매우 단순하더라도 컴파일을 매우 느리게 만든다.

    템플릿을 

### Boost

반드시 다음 리스트에 있는 [Boost library](https://www.boost.org/) 만 사용해라.

!!! info

    [Boost library](https://www.boost.org/) 는 `C++` 의 STL 을 보완하기 위한 라이브러리입니다. [Boost library](https://www.boost.org/) 는 `C++` 의 표준 라이브러리에서 제공하지 않는 파일시스템 기능, 선형대수학 기능, 무작위 숫자 생성 기능, 멀티 스레딩, 정규표현식, 유닛 테스트, `64` 비트를 넘어서는 데이터타입(`128` 비트 변수, `256` 비트 변수, `512` 비트 변수, `1024` 비트 변수), 스마트 포인터 등등을 제공합니다.

- [Call Traits](https://www.boost.org/doc/libs/1_74_0/libs/utility/call_traits.htm) from `boost/call_traits.hpp`

- [Compressed Pair](https://www.boost.org/libs/utility/compressed_pair.htm) from `boost/compressed_pair.hpp`

- [The Boost Graph Library (BGL)](https://www.boost.org/libs/graph/) from `boost/graph`, except serialization (`adj_list_serialize.hpp`) and parallel/distributed algorithms and data structures (`boost/graph/parallel/*` and `boost/graph/distributed/*`).

- [Property Map](https://www.boost.org/libs/property_map/) from `boost/property_map`, except parallel/distributed property maps (`boost/property_map/parallel/*`).

- [Iterator](https://www.boost.org/libs/iterator/) from `boost/iterator`  

- The part of [Polygon](https://www.boost.org/libs/polygon/) that deals with Voronoi diagram construction and doesn't depend on the rest of Polygon: `boost/polygon/voronoi_builder.hpp`, `boost/polygon/voronoi_diagram.hpp`, and `boost/polygon/voronoi_geometry_type.hpp`

- [Bimap](https://www.boost.org/libs/bimap/) from `boost/bimap`

- [Statistical Distributions and Functions](https://www.boost.org/libs/math/doc/html/dist.html) from `boost/math/distributions`

- [Special Functions](https://www.boost.org/libs/math/doc/html/special.html) from `boost/math/special_functions`

- [Root Finding Functions](https://www.boost.org/libs/math/doc/html/root_finding.html) from `boost/math/tools`

- [Multi-index](https://www.boost.org/libs/multi_index/) from `boost/multi_index`

- [Heap](https://www.boost.org/libs/heap/) from `boost/heap`

- The flat containers from [Container](https://www.boost.org/libs/container/): `boost/container/flat_map`, and `boost/container/flat_set`

- [Intrusive](https://www.boost.org/libs/intrusive/) from `boost/intrusive`.

- [The boost/sort library](https://www.boost.org/libs/sort/).

- [Preprocessor](https://www.boost.org/libs/preprocessor/) from `boost/preprocessor`.

*Why*:

:	[Boost library](https://www.boost.org/) 는 매우 고퀼리티의 일반화된 이식가능한 여러 기능을 제공하면서 `C++` 의 STL 이 제공해주지 못하는 갭을 채워준다. 하지만 몇몇 [Boost library](https://www.boost.org/) 라이브러리는 높은 코딩 스킬을 요구하기도 하고 가독성을 떨어뜨린다. 그러나 [Boost library](https://www.boost.org/) 를 적절히 사용하면 코딩 생산성이 매우 높아지고, 개발자가 `C++` 의 STL 이 제공하지 않는 유틸리티를 만드느라 시간낭비 하는 것을 방지해주고 자신의 프로젝트의 기능을 구현하는 것에 집중할 수 있게 해준다.

!!! note

    우리는 위의 허가된 [Boost library](https://www.boost.org/) 리스트를 추가하기 위하여 계속 검토중이다. 그러므로 이후에 이 리스트는 계속 확장될 수 있다.

### std::hash

`std::hash` 의 특수화를 정의하지 마라.

(참고 : https://google.github.io/styleguide/cppguide.html#Designated_initializers)

### Other C++ Features

[Boost library](https://www.boost.org/) 같은 다른 `C++` 의 최신 확장들을 무분별하게 사용하는 것은 가독성을 떨어뜨린다. 다른 `C++` 확장은 기존의 메커니즘과 중복되는 것들이 있기 때문에 혼란을 초래한다. Nonstandard Extensions 섹션에서 더 자세하게 논해본다.

한편 다음의 `C++` 기능은 사용하지 마라.

- `<ratio>` 가 제공하는 컴파일 타임 rational number. 

- `<cfenv>` 와 `<fenv.h>`. 왜냐면 많은 컴파일러가 이 기능을 지원하지 않는다. 

- `<filesystem>`. 테스트가 충분히 되지 않았기에 취약점이 존재한다.

### Nonstandard Extensions

위에서 언급한 [Boost library](https://www.boost.org/) 의 허가 리스트를 제외하고는 비표준 확장을 사용하지 마라.

### Aliases

alias 는 API 를 사용하는 사용자를 편하게 한다. 하지만 그 의도와 의미를 분명하게 문서화하라.

이때 `typedef` 보다 `using` 을 사용해라.

!!! example

    다음의 코드를 봐라. `typedef` 보다 `using` 을 써라.

    ```c++ 
    typedef Foo Bar;
    using Bar = Foo;
    using other_namespace::Foo;
    ```

그러나 단지 타이핑을 덜 하기 위하여 alias 를 공용 API 에서 사용하지 마라. 만약 alias 를 정의했다면 그 의도와 의미를 분명하게 문서화를 해라.

!!! example

    *Do*:

    ```c++ 
    namespace mynamespace {
        // Used to store field measurements. DataPoint may change from Bar* to some internal type.
        // Client code should treat it as an opaque pointer.
        using DataPoint = foo::Bar*;

        // A set of measurements. Just an alias for user convenience.
        using TimeSeries = std::unordered_set<DataPoint, std::hash<DataPoint>, DataPointComparator>;
    }  // namespace mynamespace
    ```

    위와 같은 코드는 alias 의 의도와 의미를 분명하게 문서화해뒀다. 하지만 다음의 코드는 그 의미와 의도가 문서화되지 않아서 코드가 모호해졌다.

    *Don't*:

    ```c++ 
    namespace mynamespace {
        // Bad: none of these say how they should be used.
        using DataPoint = ::foo::Bar*;
        using ::std::unordered_set;  // Bad: just for local convenience
        using ::std::hash;           // Bad: just for local convenience
        typedef unordered_set<DataPoint, hash<DataPoint>, DataPointComparator> TimeSeries;
    }  // namespace mynamespace
    ```

---

# Naming

가장 중요한 컨벤션이 바로 네이밍 컨벤션이다. 네이밍 스타일로써 코드를 읽는 사람은 곧바로 이것이 타입인지 변수인지 함수인지 상수인지 매크로인지 이해할 수 있기 때문이다. 따라서 개개인의 네이밍 선호에 자유를 주는 것보다 네이밍을 통일하는 것이 압도적으로 더 좋다.

!!! info

    **Pascal case** 는 단어가 합쳐진 부분마다 첫글자를 대문자로 나머지 부분을 소문자로 네이밍하는 것입니다. 가령 `PascalCaseNamingStyle`.

    **camel case** 는 **Pascal case** 와 똑같은데 예외적으로 맨 앞 문자를 소문자로 네이밍합니다. 가령 `camelCaseNamingStyle`.

    **snake case** 는 단어가 합쳐진 부분을 `_` 로 붙이고 모두 소문자로 네이밍합니다. 가령 `snake_case_naming_style`.

### General Naming Rules

^^일반적인 네이밍 규칙은 그 변수의 의미를 영어로 작성한 후 공백을 지우는 것^^ 이다. 그리고 아래에서 설명해줄 네이밍의 타입에 따라서 camel case 또는 Pascal case 또는 snake case 를 사용해라.

후임 개발자가 읽었을 때 이해하기 어려운 약칭과 줄임말로 네이밍을 하지 마라. 만약 Wikipedia 에 존재하는 약칭이나 줄임말이라면 써도 좋다.

일반적으로 네이밍의 정확성은 그 네이밍이 사용되는 스코프에 비례하여 지어져야 한다. 

!!! example

    `3` 줄의 코드에서 쓰이고 말 변수라면 `n` 이라는 이름을 붙혀도 좋다.

    하지만 전반적인 프로젝트에서 계속 사용되는 변수라면 `n` 이라는 이름은 너무 모호하기에 사용해서는 안된다.

물론 널리 알려인 약칭으로 네이밍을 해도 된다.

!!! example

    `i` 는 iteration 변수로 널리 알려져 있으니 써도 된다.

    `T` 는 템플릿 변수로 널리 알려져 있기 때문에 써도 좋다.

### File Names

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

### Type Names

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

### Variable Names

변수 이름은 snake case 로 지어라. 변수 이름에는 함수의 변수, 함수의 파라미터, 클래스 멤버 변수 등이 포함된다. 

#### Common Variable names

!!! example

    *Do*:

    ```cpp 
    std::string table_name;  // OK - lowercase with underscore.`
    ```

    변수 이름을 위와 같이 짓고 다음과 같이 짓지 마라.

    *Don't*:

    ```cpp 
    std::string tableName;   // Bad - mixed case.
    ```

#### Class Data Members

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
    
#### Struct Data Members

!!! example

    `struct` 의 멤버 변수의 이름을 다음과 같이 지어라.

    ```cpp 
    struct UrlTableProperties {
        std::string name;
        int num_entries;
        static Pool<UrlTableProperties>* pool;
    };
    ```

### Constant Names

상수로 선언된 변수 즉, `constexpr` 이나 `const` 로 선언된 변수의 이름에는 `k` 를 맨 앞에 붙이고 Pascal case 로 지어라.

모든 [Static storage duration](https://en.cppreference.com/w/cpp/language/storage_duration#Storage_duration) 을 갖는 변수, 즉 `static` 변수나 전역 변수를 이렇게 네이밍 해라.

단, 대문자로 단어를 구분할 수 없는 문자라면 `_` 를 사용해도 좋다.

!!! example

    ```cpp
    const int kDaysInAWeek = 7;
    const int kAndroid8_0_0 = 24;  // Android 8.0.0
    ```

### Function, Method Names

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

### Namespace Names

`namespace` 의 이름은 snake case 로 지어라. 하지만 `namespace` 의 이름은 `_` 가 필요없도록 짧고 간단할수록 좋다.

가장 최상위 `namespace` 는 프로젝트의 이름으로 지어라.

하위 `namespace` 는 세부 프로젝트의 이름이나 팀의 이름으로 지어라.

!!! example

    `websearch::index` 이나 `websearch::index_util` 와 같이 네이밍해라.

### Enumerator Names

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

### Macro Names

일반적으로 매크로는 사용되어서는 안된다. 하지만 정말로 필요한 경우 매크로를 모두 대문자로 네이밍하고 단어 중간을 `_` 로 구분지어라. 

!!! example

    정말로 매크로가 필요하다면 다음과 같이 네이밍해라.

    ```cpp 
    #define ROUND(x) ...
    #define PI_ROUNDED 3.0
    ```

### Exceptions to Naming Rules

당신이 기존의 `C` 와 `C++` 에 이미 존재하는 비슷한 무언가를 네이밍한다면 기존의 `C` 와 `C++` 에서 따르는 네이밍 컨벤션을 따라도 좋다.

!!! example
    
    `bigopen()` : function name, follows form of `open()`

    `uint` : typedef

    `bigpos` : struct or class, follows form of pos

    `sparse_hash_map` : STL-like entity; follows STL naming conventions

    `LONGLONG_MAX` : a constant, as in INT_MAX 

---

# Comments

주석은 코드 가독성에 있어서 매우 중요한 역할을 한다. 반드시 아래의 규칙을 기반으로 주석을 달아라.

그러나 기억하라. 가장 좋은 주석이란 주석 없이 코드 그 자체로 이해가 되는 것이다.

주석을 달 때 타겟을 반드시 당신의 후임 개발자를 대상으로 하라. 그러니 주석을 친절하게 달아라. 그 후임 개발자가 언젠가 당신이 될 것이다.

### Comment Style

`//` 나 `/* */` 스타일을 모두 사용해도 좋다. 그러나 `//` 가 좀 더 일반적이다.

당신의 프로젝트에서 주석을 다는 스타일의 일관성을 지키면 된다.

### File Comments

파일의 시작을 라이센스를 명시하는 주석으로 시작해라.

!!! example

    **Apache 2.0** 이나 **BSD** 나 **LGPL** 이나 **GPL** 이나 **MIT** 등의 라이센스를 선택하여 주석을 달아라.

그리고 어떤 파일인지, 어떤 것을 구현하는지, 혹은 어떤 것을 테스트하는지 주석을 달아라.

모든 파일에 이 주석이 반드시 있어야 한다.

만약 `.h` 파일이 다중 추상화를 선언한다면, 그 추상 클래스와 구현 클래스들의 전체 구조에 대한 주석을 반드시 달아라. 각각의 구현에 대한 세세한 설명은 그 구현 코드에서 주석을 달아라.

이때 `.h` 와 `.cc` 파일에 주석을 중복으로 달지 마라.

### Class Comments

모든 `class` 와 `struct` 에 코드를 읽는 사람이 이것이 무엇이고, 언제 사용해야 하고, 어떻게 사용하는지 충분히 이해할 수 있도록 반드시 주석을 달아라.

!!! example

    다음과 같이 코드를 읽는 사람에게 이것을 언제 사용해야 하고, 어떻게 사용해야 하는지 반드시 주석을 달아라.

    *Do*:

    ```c++ 
    // Iterates over the contents of a GargantuanTable.
    // Example:
    //    GargantuanTableIterator* iter = table->NewIterator();
    //    for (iter->Seek("foo"); !iter->done(); iter->Next()) {
    //      process(iter->key(), iter->value());
    //    }
    //    delete iter;
    class GargantuanTableIterator {
    ...
    };
    ```

만약 `class` 가 멀티스레드에 의하여 다뤄질 수 있다면, 주석을 더더욱 상세하게 달아라. 이로써 thread-safe, blocking, race-condition 등등에 관한 이 객체에서 주의해야하는 멀티스레딩 정보를 충분히 제공해라.

`class` 선언을 `.h` 에서 했고 구현을 `.cc` 에서 했다면 `class` 에 대한 주석을 `.h` 에 달아라. 그리고 `class` 의 세부 작동과 그 구현에 관한 주석을 `.cc` 에 달아라.

### Function Comments

함수와 클래스의 메소드에 다음과 같이 주석을 달아라.

#### Function Declarations

모든 함수의 선언부에는 함수를 어떻게 사용하는지 충분히 이해할 수 있는 주석이 있어야 한다.

함수가 매우 단순고 매우 명백한 경우에만 주석을 달지 않아도 된다.

함수의 주석에는 다음의 내용이 있어야만 한다.

- 입력과 출력이 무엇인지.

- 함수가 동적 메모리를 할당할 경우 함수를 호출한 코드에서 메모리를 해제해주어야 하는지.

- 함수를 어떻게 사용해야 성능이 향상되는지. 

!!! example

    다음과 같이 주석을 달아라.

    *Do*:

    ```c++ 
    // Returns an iterator for this table.  It is the client's
    // responsibility to delete the iterator when it is done with it,
    // and it must not use the iterator once the GargantuanTable object
    // on which the iterator was created has been deleted.
    //
    // The iterator is initially positioned at the beginning of the table.
    //
    // This method is equivalent to:
    //    Iterator* iter = table->NewIterator();
    //    iter->Seek("");
    //    return iter;
    // If you are going to immediately seek to another place in the
    // returned iterator, it will be faster to use NewIterator()
    // and avoid the extra seek.
    Iterator* GetIterator() const;
    ```

그러나 불필요하고 장황한 설명은 하지 마라.

메소드를 오버라이딩 했다면 상위 클래스 메소드에 이미 주석이 있을 것이므로, 설명을 반복하지 말고 오버라이딩에 초점을 맞춰서 주석을 달아라. 

생성자와 소멸자에 주석을 달 때는, 생성자와 소멸자가 무엇을 위한 것인지 주석을 달아라. 단지 `// destorys this object` 라고 주석을 다는 것은 무의미하다. 생성자가 그 파라미터로 무슨 일을 하는지, 소멸자가 무엇을 소멸하는지 설명해라. 만약 어떤 것이 너무 trivial 하고 명백하다면 주석을 달지 않아도 된다.

#### Function Definitions

함수가 tricky 한 코드를 갖고 있다면 당신이 왜 이런 식으로 코드를 구현했는지 반드시 주석으로 설명해라.

`.h` 에 이미 있는 주석을 반복하지 마라. 그러나 간단하게 요약하는 주석은 허용된다.

### Variable Comments

일반적으로 변수의 이름만 봐도 변수에 대한 아이디어가 충분히 이해될 수 있도록 이름을 지어야 한다. 그러나 분명 주석이 필요한 경우가 있는데, 이 경우 다음과 같이 주석을 달아라.

#### Class Data Members

`class` 의 멤버 변수의 목적은 반드시 명확해야 한다. 그런데도 어떤 변수가 그 타입과 이름으로 그 목적이 명확하게 전달되지 않을 것 같으면 반드시 주석을 달아라. 하지만 `int num_events_;` 처럼 타입과 이름으로 그 목적이 충분히 설명된다면 주석을 달지 마라.

!!! example

    다음의 변수는 타입과 이름만으로 변수의 목적이 충분히 설명되지 않기에 주석을 달아야 한다.

    *Do*:

    ```c++ 
    private:
    // Used to bounds-check table accesses. -1 means
    // that we don't yet know how many entries the table has.
    int num_total_entries_;
    ```

#### Global Variables

모든 전역 변수에 그 목적에 대한 주석을 반드시 달아라.

!!! example

    *Do*:

    ```c++ 
    // The total number of tests cases that we run through in this regression test.
    const int kNumTestCases = 6;
    ```

### Implementation Comments

실제 코드 부분에서 tricky 하고 그 의미가 명백하지 않고 중요한 부분에 반드시 주석을 달아라.

!!! example

    다음의 코드는 매우 tricky 하다. 그러니 주석을 달아라.

    *Do*:

    ```c++ 
    // Divide result by two, taking into account that x
    // contains the carry from the add.
    for (int i = 0; i < result->size(); ++i) {
        x = (x << 8) + (*result)[i];
        (*result)[i] = x >> 1;
        x &= 1;
    }
    ```

어떤 코드 한 줄이 tricky 하고 그 의미가 명백하지 않다면 라인의 끝에 공백 `2` 개 이후에 주석을 달아라.

!!! example

    다음 코드에서 `return;` 은 그 의미가 명백하지 않다. 그렇기 때문에 공백 `2` 개를 띄우고 주석을 달아라.

    *Do*:

    ```c++ 
    // If we have enough memory, mmap the data portion too.
    mmap_budget = max<int64>(0, mmap_budget - index_->length());
    if (mmap_budget >= data_size_ && !MmapData(mmap_chunk_bytes, mlock))
        return;  // Error already logged.
    ```

### Function Argument Comments

함수 파라미터의 의미가 명백하지 않게 되었다면 다음과 같이 하라.

- 함수 파라미터가 상수 literal 이고 그것이 여러군데에서 쓰였다면 그것을 `constant` 변수로 선언하여 이름을 부여하라. 그리고 그 변수 이름으로써 그 literal 의 의미를 설명해라.

- 함수 파라미터에 `bool` 타입 변수가 있다면 그것을 `enum` 으로 바꿔서 그 이름으로써 그 의미를 설명해라.

- 함수의 파라미터들이 함수 기능에 대한 옵션을 설정한다면, 그 파라미터들을 한 군데로 모아서 `class` 나 `struct` 로 만드는 것을 고려해봐라. 그러고 나서 파라미터로 객체 하나만 전달해라.

    *Why*:
    
    :	객체의 이름 자체로 그 의미가 분명해진다. 그리고 함수의 파라미터가 줄어들어서 함수를 읽거나 쓸 때 좋다.

!!! example

    다음의 코드는 여러 파라미터가 함수의 기능을 설정한다.

    *Don't*:

    ```c++ 
    // What are these arguments?
    const DecimalNumber product = CalculateProduct(values, 7, false, nullptr);
    ```

    그러나 다음과 같이 이 변수들을 하나로 묶은 `class` 또는 `struct` 를 정의해서 그 객체 하나만을 파라미터로 전달할 수 있도록 해라.

    *Do*:

    ```c++ 
    ProductOptions options;
    options.set_precision_decimals(7);
    options.set_use_cache(ProductOptions::kDontUseCache);
    const DecimalNumber product =
        CalculateProduct(values, options, /*completion_callback=*/nullptr);
    ```

### Don'ts

명백한 것에 대한 주석을 달지 마라. 코드가 무엇을 하는지 문자 그대로 설명하지 마라.

`C++` 을 잘 아는 사람이 봤을 때에도 명백하지 않은 코드에 대한 higher level 인 설명을 해라. 즉, 이 코드가 왜 필요하고 이 코드가 무엇을 하는지 주석으로 설명해라.

!!! example

    다음의 코드처럼 코드가 무엇을 하는 일을 문자 그대로 설명하지 마라.

    *Don't*:

    ```c++ 
    // Find the element in the vector.  <-- Bad: obvious!
    auto iter = std::find(v.begin(), v.end(), element);
    if (iter != v.end()) {
        Process(element);
    }
    ```

    다음 코드처럼 higher level 의 설명을 해라.

    *Do*:

    ```c++ 
    // Process "element" unless it was already processed.
    auto iter = std::find(v.begin(), v.end(), element);
    if (iter != v.end()) {
        Process(element);
    }
    ```

그러나 다시 말하지만, 가장 좋은 주석은 코드 그 자체로 설명이 되는 코드이다. 위의 예시의 코드는 다음과 같이 코드 그 자체로 설명이 되는 코드로 바꿀 수 있다. 이것이 가장 좋은 코드이고 가장 좋은 주석이다.

*Do*:

```c++ 
if (!IsAlreadyProcessed(element)) {
  Process(element);
}
```

### Punctuation, Spelling, and Grammar

문법과 스펠링을 틀리지 않도록 해라.

!!! tip

    [**VSCode** 의 스펠링 체크 확장](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker) 을 쓰면 됩니다.

### TODO Comments

(일단 pass)

---

# Formatting

개발자에게 코드 형식의 자유를 주는 것이 아니라 프로젝트의 코드 형식을 일관되게 통일하는 것은 매우 중요하다. 이로써 코드를 읽는 사람이 코드를 쉽게 이해할 수 있기 때문이다.

코드 포맷팅을 자동으로 할 수 있게 하기 위하여 [emacs setting file](https://raw.githubusercontent.com/google/styleguide/gh-pages/google-c-style.el) 을 만들어 뒀다.

### Line Length

모든 코드의 라인은 `80` 문자를 넘으면 안된다.

하지만 다음의 예외 상황에서는 지키지 않아도 된다.

- 주석을 다음 라인으로 넘겼을 때 가독성이 떨어진다.

- raw-string 이 `80` 문자를 넘겼다.

- `include` 헤더 선언.

- 헤더 가드.

- `using` 문을 사용할 때.

### Non-ASCII Characters

Non-ASCII 문자를 사용할 때 반드시 **UTF-8** 을 사용해라.

### Spaces vs. Tabs

탭을 사용하지 말고 공백을 사용하여 **indentation** 을 해라. 

공백 `2` 개를 사용하여 **indent** 해라.

!!! example

    다음과 같이 공백 `2` 개를 사용하여 **indent** 해라.

    *Do*:

    ```c++ 
    ReturnType ClassName::FunctionName(Type par_name1, Type par_name2) {
      DoSomething();
      ...
    }
    ```

    다음의 코드는 <kbd>Tab</kbd> 또는 공백 `4` 개를 사용하여 **indent** 했다. 이렇게 하지 마라.

    *Don't*:

    ```c++ 
    ReturnType ClassName::FunctionName(Type par_name1, Type par_name2) {
        DoSomething();
        ...
    }
    ```

!!! info

    **VSCode** 에서 <kbd>Tab</kbd> 을 눌렀을 때 공백 `2` 개가 생기도록 설정할 수 있습니다.

### Function Declarations and Definitions

함수의 선언에 반환형, 함수 이름, 파라미터를 같은 라인에 두어라.

!!! example

    *Do*:

    ```c++ 
    ReturnType ClassName::FunctionName(Type par_name1, Type par_name2) {
      DoSomething();
      ...
    }
    ```

한 줄로 함수 선언을 다 할 수 없다면 파라미터를 다음 라인으로 내리고 첫번째 파라미터의 **indentation** 에 맞춰라.

!!! example

    *Do*:

    ```c++ 
    ReturnType ClassName::ReallyLongFunctionName(Type par_name1, Type par_name2,
                                                 Type par_name3) {
      DoSomething();
      ...
    }
    ```

첫번째 파라미터조차 함수 선언에 둘 수 없다면 다음과 같이 공백 `4` 개로 파라미터들을 **indent** 해라.

!!! example

    *Do*:

    ```c++ 
    ReturnType LongClassName::ReallyReallyReallyLongFunctionName(
        Type par_name1,  // 4 space indent
        Type par_name2,
        Type par_name3) {
      DoSomething();  // 2 space indent
      ...
    }
    ```

### Lambda Expressions

람다 표현식은 함수의 포맷팅과 같이 포맷팅해라. 이때 capture 를 표현하는 `[]` 에는 공백을 남기지 마라.

!!! example

    *Do*:
    ```c++ 
    int x = 0;
    auto x_plus_n = [&x](int n) -> int { return x + n; }
    ```

함수의 파라미터로 들어간 람다 표현식은 함수 파라미터 포맷팅과 같이 포맷팅해라.

!!! example

    *Do*:

    ```c++ 
    std::set<int> blacklist = {7, 8, 9};
    std::vector<int> digits = {3, 9, 1, 8, 4, 7, 1};
    digits.erase(std::remove_if(digits.begin(), digits.end(), [&blacklist](int i) {
                    return blacklist.find(i) != blacklist.end();
                }),
                digits.end());
    ```

### Floating-point Literals

부동소수점 변수는 반드시 명확한 소수점을 부여해라.

!!! example

    `C++` 에서는 다음과 같이 소수점을 생략해도 된다. 하지만 이렇게 하지 마라.

    *Don't*:

    ```c++ 
    float f = 1.f;
    long double ld = -.5L;
    double d = 1248e6;
    ```

    다음과 같이 소수점을 명확하게 표시하라.

    *Do*:

    ```c++ 
    float f = 1.0f;
    float f2 = 1;   // Also OK
    long double ld = -0.5L;
    double d = 1248.0e6;
    ```

### Function Calls

함수 호출은 하나의 라인에 하라.

!!! example

    *Do*:

    ```c++ 
    bool result = DoSomething(argument1, argument2, argument3);
    ```

하지만 `80` 문자를 넘어간다면 파라미터를 다음 라인으로 내리고 첫번째 파라미터의 **indentation** 에 맞춰라.

!!! example

    *Do*:

    ```c++ 
    bool result = DoSomething(averyveryveryverylongargument1,
                              argument2, argument3);
    ```

첫번째 파라미터도 한 줄에 표현할 수 없다면 다음과 같이 공백 `4` 개로 파라미터들을 **indent** 해라.

!!! example

    *Do*:

    ```c++ 
    bool result = DoSomething(
        argument1, argument2,  // 4 space indent
        argument3, argument4);
    ```

어떤 변수의 의미가 명백하지 않다면 다음과 같이 새로운 변수에 저장하여 그 의미를 변수 이름으로 보여줘라.

!!! example

    *Do*:

    ```c++ 
    int my_heuristic = scores[x] * y + bases[x];
    bool result = DoSomething(my_heuristic, x, y, z);
    ```

변수의 구조가 의미에 영향을 준다면 다음과 같이 구조를 표현할 수 있도록 자유롭게 포맷팅을 해라.

!!! example

    *Do*:

    ```c++ 
    // Transform the widget by a 3x3 matrix.
    my_widget.Transform(x1, x2, x3,
                        y1, y2, y3,
                        z1, z2, z3);
    ```

### Braced Initializer List Format

(일단 pass)

### Conditionals

`if-else` 문을 사용할 때 `if` 문 다음에 공백을 하나 둬라.


`else` 문은 `{` 와 같은 라인에 써라.

!!! example

    *Do*:

    ```c++ 
    if (condition) {  // no spaces inside parentheses
      ...  // 2 space indent.
    } else if (...) {  // The else goes on the same line as the closing brace.
      ...
    } else {
      ...
    }
    ```

조건을 표현하는 `(...)` 에 공백을 두지 마라.

!!! example

    *Do*:

    ```c++ 
    if (condition) {  // Good - proper space after IF and before {.
    ```

    *Don't*:

    ```c++ 
    if(condition) {   // Bad - space missing after IF.
    if ( condition ) { // Bad - space between the parentheses and the condition
    if (condition){   // Bad - space missing before {.
    if(condition){    // Doubly bad.
    ```

조건문이 매우 간단하고 `else` 를 쓰지 않을 때에만 조건문을 한줄에 써라. 

!!! example

    *Do*:

    ```c++ 
    if (x == kFoo) return new Foo();
    if (x == kBar) return new Bar();
    ```

    다음의 조건문은 `else` 가 있으므로 한줄로 쓰면 안된다.

    *Don't*:
    
    ```c++ 
    // Not allowed - IF statement on one line when there is an ELSE clause
    if (x) DoThis();
    else DoThat();
    ```

조건문의 코드가 `1` 줄이라면 중괄호 `{ ... }` 를 쓰지 않아도 된다. 하지만 어떤 프로젝트에서 중괄호를 쓰기로 약속했다면 그것을 지켜라.

!!! example

    다음 두 형태 모두 가능하다.

    *Do*:

    ```c++ 
    if (condition)
      DoSomething();  // 2 space indent.

    if (condition) {
      DoSomething();  // 2 space indent.
    }
    ```

하지만 `if-else` 중 어느 하나라도 중괄호를 쓰면 모두 중괄호를 붙혀라.

!!! example

    *Don't*:
    
    ```c++ 
    // Not allowed - curly on IF but not ELSE
    if (condition) {
      foo;
    } else
      bar;

    // Not allowed - curly on ELSE but not IF
    if (condition)
      foo;
    else {
      bar;
    }
    ```

    *Do*:
    
    ```c++ 
    // Curly braces around both IF and ELSE required because
    // one of the clauses used braces.
    if (condition) {
      foo;
    } else {
      bar;
    }
    ```

### Loops and Switch Statements

`switch` 문의 `case` 들은 공백 `2` 개로 **indent** 하고 모두 중괄호로 묶어라.

`case` 의 코드는 마찬가지로 공백 `2` 개로 **indent** 하면 된다.

`switch-case` 에는 반드시 `default` 를 둬라. 만약 `default` 가 절대로 존재해서는 안된다면 `assert(false)` 등으로 에러를 발생시켜라.

!!! example

    *Do*:

    ```c++ 
    switch (var) {
      case 0: {  // 2 space indent
        ...      // 4 space indent
        break;
    }
      case 1: {
        ...
        break;
    }
      default: {
        assert(false);
      }
    }
    ```

### Pointer and Reference Expressions

### Boolean Expressions

### Return Values

### Variable and Array Initialization

### Preprocessor Directives

### Class Format

### Constructor Initializer Lists

### Namespace Formatting

### Horizontal Whitespace

#### General

#### Loops and Conditionals

#### Operators

#### Templates and Casts

### Vertical Whitespace

---

# Exceptions to the Rules

### Existing Non-conformant Code

### Windows Code

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

- 추상 클래스의 추상 메소드라면 `= 0;` 또는 `= delete;` 또는 `= default;` 로써 클래스를 생성 불가능하게 만들어라.

- 하나의 함수는 하나의 기능만 해야 한다.

- 매직 넘버를 사용하면 안된다. `0` 이나 `1` 까지는 허용한다. 물론 테스트 코드에서는 매직 넘버를 사용해도 좋다.