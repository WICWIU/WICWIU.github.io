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

*References*: 

:   https://google.github.io/styleguide/cppguide.html

!!! info

    이번 포스트에서는 [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html) 의 목표까지만 다루고 실질적인 코딩 컨벤션 규칙은 다음 포스트에서 다루겠습니다. 
    
    그런데 다음 포스트에서 다룰 [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html) 은 독자가 [Abseil](https://abseil.io/) 을 기본적으로 사용할 수 있다고 가정하고 있기 때문에 기초적인 [Abseil](https://abseil.io/) 사용법을 알고 넘어가야 합니다.
    
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

    모두 [**WICWIU**](https://github.com/WICWIU/WICWIU) 에 유용한 기능들 같네요.

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