## What?

[Coding Convention 이란 indentation, comments, declarations, statements, white space, naming conventions, programming principles 등등에 대한 코딩 가이드라인입니다.](https://en.wikipedia.org/wiki/Coding_conventions) 개인이 진행하는 프로젝트라면 굳이 컨벤션을 지켜야 할 이유가 없습니다. 하지만 다수가 진행하는 프로젝트라면 가독성과 유지보수를 위하여 코딩 컨벤션을 통일하고 지켜야만 합니다. 코딩 컨벤션은 프로그램의 기능과 성능 자체에는 아무런 영향을 끼치지 않습니다. 즉, 코딩 컨벤션은 오로지 개발자를 위한 것입니다. 특히 내가 아닌 ^^다른 개발자를 위한 것^^ 입니다.

## Why?

코딩 컨벤션이 지켜지지 않은 프로젝트는 가독성이 떨어지고 유지보수를 하기 힘들어지며 확장성이 낮아집니다. 즉, 후임 개발자가 프로젝트를 물려받았을 때 뒤죽박죽, 중구난방인 코드를 해석하면서 쓸모없는 비용이 추가로 발생하게 되는 것입니다. 

코드가 뒤죽박죽인 정도가 높아질수록 프로젝트를 유지보수하고 확장하는 비용도 높아집니다. 그럼에도 불구하고 어떻게든 프로젝트를 확장했다면 컨벤션이 통일되지 않은 상태이기 때문에 이로인하여 또 다시 중구난방인 코드가 추가됩니다. 이런 일이 반복될수록 프로젝트를 개발하는 비용은 점차 늘어나게 됩니다.

프로젝트를 개발하는 비용이 늘어나면 여러가지 비상사태가 초래되는데, 그 중 가장 큰 문제는 ^^기존의 프로젝트를 유지보수하고 확장하는 비용이 프로젝트를 아예 버리고 처음부터 다시 시작하는 비용보다 비싸질 때가 온다는 것^^ 입니다. 즉, 뒤죽박죽 중구난방인 코드가 프로젝트에 계속 쌓인다면 언젠가 그 실타래를 푸는 것보다 그냥 프로젝트에 임종선고를 내리고 처음부터 다시 짜는 비용이 더 싸지는 때가 온다는 것입니다.

그런데 불행히도 [**WICWIU**](https://github.com/WICWIU/WICWIU) 의 코딩 컨벤션은 통일되어 있지 않고, 유지보수와 확장에 대한 명료한 규칙도 정해져 있지 않습니다. 그러므로 우리는 언젠가 [**WICWIU**](https://github.com/WICWIU/WICWIU) 에 임종 선고를 내리지 않기 위하여 [**WICWIU**](https://github.com/WICWIU/WICWIU) 의 Coding Convention 을 통일할 필요가 있습니다.

## How? 

좋은 Coding Convention 은 순전히 숙련된 개발자의 경험으로부터 나옵니다. 각 커뮤니티와 회사들이 자신들만의 경험을 기반으로 코딩 컨벤션을 통일하곤 합니다. 하지만 [**WICWIU**](https://github.com/WICWIU/WICWIU) 개발자는 보통 한동대 학부생이므로 코딩 경험이 극히 부족한 경우가 대부분입니다. 즉, 우리의 코딩 경험만으로 코딩 컨벤션을 정립하기에는 무리가 있습니다. 그러므로 우리는 기존의 정립된 코딩 컨벤션을 차용할 필요가 있습니다.

여러 커뮤니티와 회사들이 자신들의 코딩 컨벤션을 정립해두었습니다. 대표적으로 [Linux kernel](https://www.kernel.org/doc/html/v4.10/process/coding-style.html), [GNU C++ Style](https://gcc.gnu.org/wiki/CppConventions), [Microsoft C++ Style Conventions](https://docs.microsoft.com/en-us/windows/win32/stg/coding-style-conventions), [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html), [LLVM coding standards](http://llvm.org/docs/CodingStandards.html), [Chromium’s style guide](http://www.chromium.org/developers/coding-style), [Mozilla’s style guide](https://developer.mozilla.org/en-US/docs/Developer_Guide/Coding_Style), [WebKit’s style guide](http://www.webkit.org/coding/coding-style.html), [Qt Coding Style](http://wiki.qt.io/Qt_Coding_Style),   [Unreal Engine Coding Standard](https://docs.unrealengine.com/latest/INT/Programming/Development/CodingStandard/), [C++ Best Practices](http://codergears.com/QACenter/index.php?qa=questions), [Blender Coding Style](http://wiki.blender.org/index.php/Dev:Doc/Code_Style), [Blink Coding Style Guidelines](http://www.chromium.org/blink/coding-style), [Inkscape Coding Style](https://inkscape.org/en/develop/coding-style/), [CppCoreGuidelines](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md) 등등이 있습니다. 
그 중에서 일단 Google Style 이 좋지 않겠냐는 의견이 중론이어서 먼저 [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html) 을 정리해 보았습니다.

그래서 정리를 해봤는데, 논란이 될 만한 부분이 꽤 많았습니다. 하지만 **Google** 의 권위를 믿고 꾹 참고 계속 번역 및 정리 작업을 계속 했었죠. 그런데 너무 미심쩍어서 `C++` 커뮤니티들에 **Google** 의 `C++` 코딩 컨벤션에 대하여 검색을 해보니까 `C++` 커뮤니티에서는 **Google** 의 Style Guide 가 **Google** 같은 코드가 1억줄이 넘는 프로젝트와 수많은 legacy 프로젝트를 다뤄야하는 곳에서나 적합하지 새로운 `C++` 프로젝트에는 매우 부적절하다는 목소리가 많았습니다. 심지어 절대로 **Google** 의 Style Guide 를 따르지 말라는 독립된 블로그 포스트도 있었고, `C++` 커뮤니티에서 Google Style Gudie 는 쓰레기다는 등의 부정적인 여론이 상당히 많았습니다. **Google** 이 하는 말이라면 무엇이든 옳다고 생각했던 게 실수였던 것 같네요. **Google** 때문에 시간낭비를 너무 많이했습니다. 앞으로는 심지어 **Google** 이라고 해도 커뮤니티 같은 곳을 통해서 사람들의 의견을 다방면으로 수집해봐야겠습니다. 제가 번역/정리 하던 **Google** Style Guide 의 legacy 문서를 확인하려면 [Google C++ Style Guide 미완성 문서](googlestyleguide.md)를 클릭하세요. 단, 몇몇 기업과 커뮤니티는 [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html) 의 단점을 고쳐서 잘 사용하고 있다고 합니다. [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html) 의 문서 구조 자체와 다루고 있는 주제들 자체는 좋기 때문인 것 같습니다.

`C++` 커뮤니티의 중론은 `clang-format` 으로 `C++` 의 포맷팅을 자동화하고 코딩 컨벤션은 [CppCoreGuidelines](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md) 을 따르는 것이 좋다는 의견에 합의되어 있었습니다. 그래서 지금부터 이것을 번역 및 정리해야겠네요. 그래서 [CppCoreGuidelines](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md) 를 번역하고 정리하고 있었는데, 이번에는 우리나라 `C++` 커뮤니티인 [C++ Korea](https://www.cppkorea.org) 에서 이미 [CppCoreGuidelines 한글 번역 프로젝트](https://www.cppkorea.org/CppCoreGuidelines/) 를 완료했다는 것을 알게되었습니다. 그러니까 [CppCoreGuidelines](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md) 번역 및 정리 작업을 그만 두고 우리나라 `C++` 개발자 형들이 이미 깔끔하게 번역해두신 [CppCoreGuidelines 한글 번역 프로젝트](https://www.cppkorea.org/CppCoreGuidelines/) 을 읽도록 하겠습니다.

그런데 [CppCoreGuidelines 한글 번역 프로젝트](https://www.cppkorea.org/CppCoreGuidelines/) 는 [CppCoreGuidelines](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md) 의 원래 내용 순서를 제목 알파벳 순으로 바꾸어놓았었습니다. 그래서 [CppCoreGuidelines](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md) 의 원본 순서대로 볼 수 있도록 [CppCoreGuidelines 한글 번역 프로젝트](https://www.cppkorea.org/CppCoreGuidelines/) 의 내용을 정리했습니다.

---

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