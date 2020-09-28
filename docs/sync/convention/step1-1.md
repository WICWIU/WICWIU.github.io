## What?

[Coding Convention 이란 indentation, comments, declarations, statements, white space, naming conventions, programming principles 등등에 대한 코딩 가이드라인입니다.](https://en.wikipedia.org/wiki/Coding_conventions) 개인이 진행하는 프로젝트라면 굳이 컨벤션을 지켜야 할 이유가 없습니다. 하지만 다수가 진행하는 프로젝트라면 가독성과 유지보수를 위하여 코딩 컨벤션을 통일하고 지켜야만 합니다. 코딩 컨벤션은 프로그램의 기능과 성능 자체에는 아무런 영향을 끼치지 않습니다. 즉, 코딩 컨벤션은 오로지 개발자를 위한 것입니다. 특히 내가 아닌 ^^다른 개발자를 위한 것^^ 입니다.

## Why?

코딩 컨벤션이 지켜지지 않은 프로젝트는 가독성이 떨어지고 유지보수를 하기 힘들어지며 확장성이 낮아집니다. 즉, 후임 개발자가 프로젝트를 물려받았을 때 뒤죽박죽, 중구난방인 코드를 해석하면서 쓸모없는 비용이 추가로 발생하게 되는 것입니다. 

코드가 뒤죽박죽인 정도가 높아질수록 프로젝트를 유지보수하고 확장하는 비용도 높아집니다. 그럼에도 불구하고 어떻게든 프로젝트를 확장했다면 컨벤션이 통일되지 않은 상태이기 때문에 이로인하여 또 다시 중구난방인 코드가 추가됩니다. 이런 일이 반복될수록 프로젝트를 개발하는 비용은 점차 늘어나게 됩니다.

프로젝트를 개발하는 비용이 늘어나면 여러가지 비상사태가 초래되는데, 그 중 가장 큰 문제는 ^^기존의 프로젝트를 유지보수하고 확장하는 비용이 프로젝트를 아예 버리고 처음부터 다시 시작하는 비용보다 비싸질 때가 온다는 것^^ 입니다. 즉, 뒤죽박죽 중구난방인 코드가 프로젝트에 계속 쌓인다면 언젠가 그 실타래를 푸는 것보다 그냥 프로젝트에 임종선고를 내리고 처음부터 다시 짜는 비용이 더 싸지는 때가 온다는 것입니다.

그런데 불행히도 [**WICWIU**](https://github.com/WICWIU/WICWIU) 의 코딩 컨벤션은 통일되어 있지 않고, 유지보수와 확장에 대한 명료한 규칙도 정해져 있지 않습니다. 그러므로 우리는 언젠가 [**WICWIU**](https://github.com/WICWIU/WICWIU) 에 임종 선고를 내리지 않기 위하여 [**WICWIU**](https://github.com/WICWIU/WICWIU) 의 Coding Convention 을 통일할 필요가 있습니다.

## How? 

좋은 Coding Convention 은 순전히 숙련된 개발자의 경험으로부터 나옵니다. 각 커뮤니티와 회사들이 자신들만의 경험을 기반으로 코딩 컨벤션을 통일하곤 합니다. 하지만 [**WICWIU**](https://github.com/WICWIU/WICWIU) 개발자는 보통 한동대 학부생이므로 코딩 경험이 극히 부족한 경우가 대부분입니다. 즉, 우리의 코딩 경험만으로 코딩 컨벤션을 정립하기에는 무리가 있습니다. 그러므로 우리는 기존의 정립된 코딩 컨벤션을 차용할 필요가 있습니다.

여러 커뮤니티와 회사들이 자신들의 코딩 컨벤션을 정립해두었습니다. 대표적으로 [Linux kernel](https://www.kernel.org/doc/html/v4.10/process/coding-style.html), [GNU C++ Style](https://gcc.gnu.org/wiki/CppConventions), [Microsoft C++ Style Conventions](https://docs.microsoft.com/en-us/windows/win32/stg/coding-style-conventions), [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html), [LLVM coding standards](http://llvm.org/docs/CodingStandards.html), [Chromium’s style guide](http://www.chromium.org/developers/coding-style), [Mozilla’s style guide](https://developer.mozilla.org/en-US/docs/Developer_Guide/Coding_Style), [WebKit’s style guide](http://www.webkit.org/coding/coding-style.html), [Qt Coding Style](http://wiki.qt.io/Qt_Coding_Style),   [Unreal Engine Coding Standard](https://docs.unrealengine.com/latest/INT/Programming/Development/CodingStandard/), [C++ Best Practices](http://codergears.com/QACenter/index.php?qa=questions), [Blender Coding Style](http://wiki.blender.org/index.php/Dev:Doc/Code_Style), [Blink Coding Style Guidelines](http://www.chromium.org/blink/coding-style), [Inkscape Coding Style](https://inkscape.org/en/develop/coding-style/), [CppCoreGuidelines](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md) 등등이 있습니다. 그 중에서 일단 Google Style 이 좋지 않겠냐는 의견이 중론이어서 먼저 [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html) 을 정리해 보았습니다.

## Google C++ Style Guide 를 정리하다가 발견한 여러 논란

그래서 정리를 해봤는데, 논란이 될 만한 부분이 꽤 많았습니다. 하지만 **Google** 의 권위를 믿고 꾹 참고 계속 번역 및 정리 작업을 계속 했었죠. 그런데 너무 미심쩍어서 `C++` 커뮤니티들에 **Google** 의 `C++` 코딩 컨벤션에 대하여 검색을 해보니까 `C++` 커뮤니티에서는 **Google** 의 Style Guide 가 **Google** 같은 코드가 1억줄이 넘는 프로젝트와 수많은 legacy 프로젝트를 다뤄야하는 곳에서나 적합하지 새로운 `C++` 프로젝트에는 매우 부적절하다는 목소리가 많았습니다. 심지어 절대로 **Google** 의 Style Guide 를 따르지 말라는 독립된 블로그 포스트도 있었고, `C++` 커뮤니티에서 Google Style Gudie 는 쓰레기다는 등의 부정적인 여론이 상당히 많았습니다. 물론 그 블로그 포스트를 반박하는 의견도 상당히 많았습니다. 

- [Google C++ Style Guide is No Good](https://eyakubovich.github.io/2018-11-27-google-cpp-style-guide-is-no-good/)

- [Hacker News Discussion: Google C++ Style Guide is No Good](https://news.ycombinator.com/item?id=18555771)

    ![image](https://user-images.githubusercontent.com/16812446/90983456-4f3f3e80-e5a9-11ea-8975-a06283e664af.png)

- [Reddit Discussion: Google C++ Style Guide is No Good](https://www.reddit.com/r/cpp/comments/a1b7km/google_c_style_guide_is_no_good/)

- [Why isn’t it good to follow Google's C++ style guide?](https://www.quora.com/Why-isn%E2%80%99t-it-good-to-follow-Googles-C++-style-guide)

    ![image](https://user-images.githubusercontent.com/16812446/90983465-6f6efd80-e5a9-11ea-94b6-36fb590dd62f.png)

- [Which C++ style guide do you follow?](https://www.reddit.com/r/cpp/comments/8oja7y/which_c_style_guide_do_you_follow/)

    ![image](https://user-images.githubusercontent.com/16812446/90983506-b230d580-e5a9-11ea-86ab-59d40c257fde.png)

- [Why Google Style Guide for C++ is a deal-breaker](https://www.linkedin.com/pulse/20140503193653-3046051-why-google-style-guide-for-c-is-a-deal-breaker)

- [Reddit Discussion: Why Google Style Guide for C++ is a deal-breaker](https://www.reddit.com/r/programming/comments/28alvi/why_google_style_guide_for_c_is_a_dealbreaker/)

- [Reddit Discussion2: Why Google Style Guide for C++ is a deal-breaker](https://www.reddit.com/r/cpp/comments/289n27/this_blog_post_matches_much_of_my_thinking_on/)

- [What programming style do you use?](https://www.reddit.com/r/cpp/comments/5zth1c/what_programming_style_do_you_use/)

- [favorite C++ coding standards?](https://www.reddit.com/r/cpp/comments/80m4vx/favorite_c_coding_standards/)

- [Reddit Discussion: Do you adopt abseil?](https://www.reddit.com/r/cpp/comments/9hlf8o/do_you_adopt_abseil/)

    ![image](https://user-images.githubusercontent.com/16812446/90983444-2fa81600-e5a9-11ea-964a-55a37848f8d0.png)

- [Choose C++ style guideline #5](https://github.com/open-telemetry/opentelemetry-cpp/issues/5)

    ![image](https://user-images.githubusercontent.com/16812446/90983779-70089380-e5ab-11ea-8f62-b6c31ecd9d54.png)


솔직히 모르겠습니다. 저는 전문 `C++` 개발자가 아니고 아직 대학교 학부생입니다. 그래서 Google Style 이 옳은지 다른 Style 이 옳은지 판단할 수 없습니다. 그런데 사실 이렇게 논란이 많은 것 자체로 Google C++ Style Guide 를 저평가할 수밖에 없습니다. 왜냐하면 Google C++ Style Guide 로 더 이상 시간낭비를 하고 싶지 않기 때문입니다. 위에 올려둔 커뮤니티들 링크에서 수많은 토론이 진행되었었는데 그 와중에 [CppCoreGuidelines](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md) 은 논란 없이 좋다는 의견에 합의되어 있었죠. 그렇다면 공프기/캡스톤 하느라 시간이 없는 우리 입장에서는 Google C++ Style Guide 가 좋은지 나쁜지 판단하는 것보다 논란이 없는 CppCoreGuidelines 을 따르는 것이 더 나을 것입니다.

어쨌든 **Google** 이 하는 말이라면 무엇이든 옳다고 생각했던 게 실수였던 것 같네요. **Google** 때문에 시간낭비를 너무 많이했습니다. 앞으로는 심지어 **Google** 이라고 해도 커뮤니티 같은 곳을 통해서 사람들의 의견을 다방면으로 수집해봐야겠습니다. 제가 번역/정리 하던 **Google** Style Guide 의 legacy 문서를 확인하려면 [Google C++ Style Guide 미완성 문서](googlestyleguide.md)를 클릭하세요. 단, 몇몇 기업과 커뮤니티는 [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html) 의 단점을 고쳐서 잘 사용하고 있다고 합니다. [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html) 의 철학과 문서 구조 자체와 다루고 있는 주제들 자체는 좋기 때문인 것 같습니다. 

`C++` 커뮤니티의 중론은 `clang-format` 으로 `C++` 의 포맷팅을 자동화하고 코딩 컨벤션은 [CppCoreGuidelines](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md) 을 따르는 것이 좋다는 의견에 합의되어 있었습니다. 그래서 지금부터 이것을 번역 및 정리해야겠네요. Google C++ Style 이 좋은지 나쁜지 저는 더 이상 관심없습니다. 그것을 판단하고 있는 것 자체가 무의미하고, 시간낭비일 뿐입니다. CppCoreGuidelines 은 `C++` 커뮤니티에서도 논란이 없었기 때문에 의심하지 않고 쓸 수 있을 것 같네요.

그래서 [CppCoreGuidelines](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md) 를 번역하고 정리하고 있었는데, 이번에는 우리나라 `C++` 커뮤니티인 [C++ Korea](https://www.cppkorea.org) 에서 이미 [CppCoreGuidelines 한글 번역 프로젝트](https://www.cppkorea.org/CppCoreGuidelines/) 를 완료했다는 것을 알게되었습니다. 그러니까 [CppCoreGuidelines](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md) 번역 및 정리 작업을 그만 두고 우리나라 `C++` 개발자 형들이 이미 깔끔하게 번역해두신 [CppCoreGuidelines 한글 번역 프로젝트](https://www.cppkorea.org/CppCoreGuidelines/) 을 읽도록 하겠습니다.

!!! note

    사실 이로써 CppCoreGuidelines 에 대한 안심을 한 번 더 하게되었습니다. 왜냐하면 우리나라 `C++` 커뮤니티에서도 CppCoreGuidelines 을 인정하고 그것을 번역 및 정리하는 프로젝트를 진행한 것이기 때문이죠. CppCoreGuidelines 은 저에게 더 이상 시간낭비를 하지 않게 해줄 것 같네요. 후...

그런데 [CppCoreGuidelines 한글 번역 프로젝트](https://www.cppkorea.org/CppCoreGuidelines/) 는 [CppCoreGuidelines](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md) 의 원래 내용 순서를 제목 알파벳 순으로 바꾸어놓았었습니다. 그래서 [CppCoreGuidelines](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md) 의 원본 순서대로 볼 수 있도록 [CppCoreGuidelines 한글 번역 프로젝트](https://www.cppkorea.org/CppCoreGuidelines/) 의 내용을 정리했습니다.

---

# C++ Core Guidelines 사전지식

하지만 C++ Core Guidelines 은 C++ 문법서가 아니기 때문에 여러가지 고급 문법을 설명 없이 다루고 있습니다. 따라서 그러한 기능들을 사전에 알아두고 읽어야 합니다.

- span

- gsl::index

- owner

- fopen, malloc, strdup 이 반환하는 포인터는 할당 해제를 해주어야 하므로 fopen 이 아닌 ifstream 을 사용해야 한다는 것

- https://www.cppkorea.org/CppCoreGuidelines/Philosophy/#example-bad_6 에서 예제가 무엇을 낭비하는지 독자 스스로 찾아라 했는데 `strlen` 이 매 루프마다 계산되고 있다는 것이다.

- std::chrono

- 사전조건 gsl::Expects() : if 나 assert 로 할 수 있지만 이게 더 좋다고하네. 왜? 

- 사후조건 gsl::Ensures() : if 나 assert 로 할 수 있지만 이게 더 좋다고하네. 왜? 

- lock_guard, RAII

- constexpr

- template 에 적용되는 requires, Concept

- zstring 

- std::strlen 

- not_null 

- czstring 

- string_span

- extern

- ㅁ든걸 const 로

- C스타일로코딘 ㄴㄴㄴ

- 스마트 포인터

# C++ Core Guidelines

- [Abstraciton](https://www.cppkorea.org/CppCoreGuidelines/home/)
- [In: 소개](https://www.cppkorea.org/CppCoreGuidelines/Introduction/)
- [P: 철학](https://www.cppkorea.org/CppCoreGuidelines/Philosophy/)
- [I: 인터페이스](https://www.cppkorea.org/CppCoreGuidelines/Interfaces/)
- [F: 함수](https://www.cppkorea.org/CppCoreGuidelines/Functions/)
- [C: 클래스와 클래스 계층 구조](https://www.cppkorea.org/CppCoreGuidelines/Class/)
- [Enum: 열거형](https://www.cppkorea.org/CppCoreGuidelines/Enum/)
- [R: 리소스 관리](https://www.cppkorea.org/CppCoreGuidelines/Resource/)
- [ES: 표현식과 문장](https://www.cppkorea.org/CppCoreGuidelines/Expr/)
- [Per: 성능](https://www.cppkorea.org/CppCoreGuidelines/Performance/)
- [CP: 동시성과 병렬처리](https://www.cppkorea.org/CppCoreGuidelines/Concurrency/)
- [E: 오류 처리](https://www.cppkorea.org/CppCoreGuidelines/Errors/)
- [Con: 상수와 불변성](https://www.cppkorea.org/CppCoreGuidelines/Const/)
- [T: 템플릿과 제너릭 프로그래밍](https://www.cppkorea.org/CppCoreGuidelines/Templates/)
- [CPL: C 스타일 프로그래밍](https://www.cppkorea.org/CppCoreGuidelines/CPL/)
- [SF: 소스 파일](https://www.cppkorea.org/CppCoreGuidelines/Source/)
- [SL: 표준 라이브러리](https://www.cppkorea.org/CppCoreGuidelines/SL/)

참고할 만한 내용:

- [A: 설계 아이디어](https://www.cppkorea.org/CppCoreGuidelines/Architecture/)
- [NR: 규칙이 아닌 것들과 근거없는 이야기들](https://www.cppkorea.org/CppCoreGuidelines/Not/)
- [RF: 참고 자료들](https://www.cppkorea.org/CppCoreGuidelines/References/)
- [Pro: 프로파일](https://www.cppkorea.org/CppCoreGuidelines/Profile/)
- [GSL: 가이드라인 지원 라이브러리](https://www.cppkorea.org/CppCoreGuidelines/GSL/)
- [NL: 이름과 만듦새 규칙들](https://www.cppkorea.org/CppCoreGuidelines/Naming/)
- [FAQ: 자주 묻는 질문에 대한 대답](https://www.cppkorea.org/CppCoreGuidelines/FAQ/)
- [Appendix A: 라이브러리](https://www.cppkorea.org/CppCoreGuidelines/Libraries/)
- [Appendix B: 모던 C++ 코드](https://www.cppkorea.org/CppCoreGuidelines/appendix/Modernizing/)
- [Appendix C: 토론](https://www.cppkorea.org/CppCoreGuidelines/home/appendix/Discussion.md)
- [Appendix D: 유용한 도구들](https://www.cppkorea.org/CppCoreGuidelines/appendix/Tools/)
- [용어 해설](https://www.cppkorea.org/CppCoreGuidelines/Glossary/)
- [To-do: 미분류 규칙](https://www.cppkorea.org/CppCoreGuidelines/Unclassified/)

---

# clang-format 

`clang-format` 은 `C`, `C++`, `Objective-C` 코드를 자동으로 포맷팅해주는 툴입니다. `clang-format` 은 **LLVM**, **Google**, **Chromium**, **Mozilla**, **WebKit** 의 `C++` 코딩 컨벤션에서 말하는 코드 포맷팅 기준을 자동으로 맞춰주기 때문에 일일히 코딩 컨벤션을 맞추느라 수고하지 않아도 되게 해줍니다. 또한 코드 스타일을 `.clang-format` 파일에 명시하면 코드 스타일을 커스터마이징 할 수도 있습니다. 

[**WICWIU**](https://github.com/WICWIU/WICWIU) 를 만들고 **commit** 하기 전에 `clang-format` 을 한번 실행하기만 하면 코드 포맷이 자동으로 통일되는 것입니다.

## clang-format 설치

=== "시스템 설치"

    일반 유저는 `sudo` 명령어를 사용할 수 없기 때문에 시스템을 해킹하지 않는 이상 시스템 관리자에게 문의하여 다음 명령어로 `clang-format` 을 설치해주라고 부탁하세요.

    ```shell
    $ sudo apt install clang-format
    ```

=== "유저 설치"

    다음 명령어로 저의 [`standalone` 레포지토리](https://github.com/ccss17/standalone)를 **clone** 해주세요. 이 레포지토리는 필요한 패키지를 설치없이 standalone 하게 실행할 수 있도록 필요 라이브러리 의존성을 자동으로 해결해주면서 바이너리에 `alias` 를 지정해주는 스크립트를 담고 있습니다. 

    ```shell
    $ git clone --depth 1 https://github.com/ccss17/standalone 
    ```

    그리고 다음과 같이 `setup.sh` 을 실행하면 됩니다.

    ```shell
    $ cd standalone
    $ ./setup.sh
    ```

    그러면 `clang-format` 뿐만 아니라 `zsh`, `bat`, `curl`, `gotop`, `nvtop` 등의 딥러닝 개발 시 매우 유용한 툴들이 자동으로 설치됩니다. 하지만 `clang-format` 만을 설치하고 싶다면 다음 명령어를 입력하면 됩니다.

    ```shell
    $ cd standalone/clang-format
    $ ./setup.sh
    ```

    !!! note
    
        하지만 이 방식은 실행 파일들을 standalone 하게 설치하는 것이기 때문에 불필요한 추가 라이브러리가 계속 다운로드 되어 서버의 저장용량을 낭비 할 수도 있습니다. 따라서 서버 관리자가 `clang-format` 을 정식으로 설치해줄 때까지 임시적으로 사용할 수 있는 방식입니다. 서버 관리자가 서버에 `clang-format` 을 정식으로 설치해주었다면 불필요해진 이 standalone 레포지토리를 삭제하면 좋을 것입니다.

## clang-format 사용법

### 미리 정의된 스타일

만약 `test.cpp` 을 **Google** 스타일로 자동으로 포맷팅하고 싶다면 다음의 명령어를 실행하면 됩니다.

```shell
$ clang-format --style=google -i test.cpp
```

### 커스텀 스타일

만약 커스텀 스타일로 포맷팅을 하고 싶다면 `.clang-format` 파일에 [Clang documentations](https://clang.llvm.org/docs/ClangFormatStyleOptions.html#) 의 내용을 참고하여 스타일을 만든 후 `--style` 옵션 명시 없이 `clang-format` 을 실행하면 됩니다.

### 모든 파일 포맷팅

모든 파일을 한꺼번에 포맷팅하고 싶다면 다음의 명령어를 실행하면 됩니다.

```shell
$ find . -regex '.*\.\(cpp\|hpp\|cu\|c\|h\)' -exec clang-format -style=file -i {} \;
```

### VSCode 연동 자동 포맷팅 (추천)

하지만 이렇게 `clang-format` 을 사용하는 것보다 훨씬 편하고 힘이 덜들어가는 방법이 있습니다. 바로 **VSCode** 와 연동하여 `clang-format` 을 사용하는 것인데요. 파일을 수정하고 저장하는 것만으로 자동으로 포맷팅을 해줍니다.

!!! note

    아직 딥러닝 서버에 **VSCode** 를 연동하지 않았다면 [VSCode 딥러닝 서버 연동하기](../../dev/VSCode/VSCode.md) 를 확인해주세요.

1. 먼저 [**VSCode** 의 `clang-format` 확장](https://marketplace.visualstudio.com/items?itemName=xaver.clang-format)을 설치하세요.

2. 명령팔레트의 **Preferences: Open Settings (JSON)** 을 실행하여 `settings.json` 을 여세요.

    ![2020-08-28_15-52](https://user-images.githubusercontent.com/16812446/91530985-fffd6300-e946-11ea-81a0-988bdd297cea.png)

3. 파일에 다음과 같이 `"editor.formatOnSave": true` 을 추가하세요.

    ```shell
    {
        ...
        "editor.formatOnSave": true
        ...
    }
    ```

4. 만약 `clang-format` 을 [`standalone` 레포지토리](https://github.com/ccss17/standalone) 을 통하여 설치했다면 파일에 다음과 같이 `"clang-format.executable": "/home/USER/.standalone/clang-format/standalone-clang-format.sh"` 을 추가해주세요. `/home/USER` 를 꼭 자신의 홈 디렉토리 경로로 바꿔주어야 합니다!

    ```shell
    {
        ...
        `"clang-format.executable": "/home/USER/.standalone/clang-format/standalone-clang-format.sh"`
        ...
    }
    ```

## `clang-format` 포맷팅 통일

우선 [**WICWIU**](https://github.com/WICWIU/WICWIU) 에서의 `clang-format` 포맷팅을 널리 알려진 `C++` 포맷팅으로 통일하도록 하겠습니다. 이후에 포맷팅을 바꾸고 싶다면 [**WICWIU**](https://github.com/WICWIU/WICWIU) 팀원들 간의 협의를 통하여 바꿀 수 있습니다.

이를 위하여 다음 명령어로 [.clang-format](https://gist.github.com/ccss17/48b6c29ba8cb72b02d5166e5bf6bfddd) 을 받아서 `.clang-format` 파일에 저장해주세요.

```shell
$ git clone https://gist.github.com/ccss17/48b6c29ba8cb72b02d5166e5bf6bfddd
$ cp 48b6c29ba8cb72b02d5166e5bf6bfddd/.clang-format ~
```

이제 [**WICWIU**](https://github.com/WICWIU/WICWIU) 를 자동으로 포맷을 통일할 수 있게 되었습니다. 