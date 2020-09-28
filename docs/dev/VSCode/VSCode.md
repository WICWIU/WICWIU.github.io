먼저 **VSCode** 에 익숙하지 않다면 다음의 링크를 확인해주세요. 

- [**VSCode** 공식 튜토리얼](https://code.visualstudio.com/docs/introvideos/basics)

- [ProgrammerBase 의 **VSCode** 튜토리얼 (추천)](https://ccss17.github.io/ProgrammerBase/vscode/)

명령 팔레트가 뭔지 모르고 어떻게 여는지 모르겠다면 위 링크를 통하여 **VSCode** 의 튜토리얼을 완수해주세요. 제가 만든 **VSCode** 튜토리얼을 본다면 빠르게 시간절약을 하면서 튜토리얼을 완수할 수 있습니다.

# VSCode 를 사용해야 하는 이유

자신의 컴퓨터에 GPU 가 엄청 좋은 게 달려있지 않은 이상 딥러닝 서버에서 [**WICWIU**](https://github.com/WICWIU/WICWIU) 개발을 할 수밖에 없습니다. 하지만 딥러닝 서버에서 원격으로 개발하는 것은 쉽지 않습니다. 왜냐하면 `ssh` 와 `vim` 등을 터미널 환경에서 사용해야 하기 때문입니다. 물론 익숙한 사람은 관계없습니다. 그러나 아무리 터미널 환경에 익숙하다고 해도 [**WICWIU**](https://github.com/WICWIU/WICWIU) 의 소스코드를 분석할 때에는 한계에 봉착합니다. 그래서 대안책으로 Github 소스보기를 통하여 소스분석을 하기도 하지만 **VSCode** 의 소스코드 분석 툴을 사용하는 것이 훨씬 낫습니다. 

**VSCode** 를 사용하면 원격 서버에 연결하여 편하게 개발을 할 수 있을 뿐더러 **VSCode** 가 제공하는 소스코드 분석 기능까지 제공받을 수 있습니다.

## VSCode 연동 방법

먼저 [**VSCode** 의 SSH 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh) 을 설치합니다. 그리고 명령 팔레트를 열어서 다음의 기능을 실행합니다.

![](https://microsoft.github.io/vscode-remote-release/images/remote-ssh-command-palette.png)

*이미지 출처 : https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh* 

그러면 서버 IP 와 아이디, 비밀번호를 입력할 수 있는데 적절히 잘 입력해줍니다. 그리고 나서 폴더 열기를 통해 딥러닝 서버에 있는 [**WICWIU**](https://github.com/WICWIU/WICWIU) 레포지토리를 열기만 하면 됩니다.

![2020-08-28_16-20](https://user-images.githubusercontent.com/16812446/91533177-689a0f00-e94a-11ea-91de-81fb9e92f13f.png)

그러면 위 사진과 같이 딥러닝 서버의 [**WICWIU**](https://github.com/WICWIU/WICWIU) 레포지토리와 연동이 되서 코드를 편집하면 딥러닝 서버에서도 편집됩니다. 그리고 **VSCode** 의 터미널을 열면 자동으로 딥러닝 서버의 터미널로 열려서 컴파일과 실행도 딥러닝 서버에서 할 수 있게 됩니다.

# VSCode C++ 코드 분석

[**WICWIU**](https://github.com/WICWIU/WICWIU) 는 `C++` 프로젝트이기 때문에 **VSCode** 에서 제공하는 `C++` 분석 헬퍼를 사용하면 소스코드 분석을 매우 쉽게 할 수 있게 됩니다.

먼저 [`C++` 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) 을 설치하세요. 이때 중요한 점은 딥러닝 서버에서 연동되는 **VSCode** 확장은 따로 설치해주어야 한다는 것입니다.

확장을 설치하고 딥러닝 서버에 **VSCode** 를 연동해보면 다음과 같이 **Install on SSH: 서버 아이피** 버튼이 생깁니다.

<img src="https://user-images.githubusercontent.com/16812446/91533586-16a5b900-e94b-11ea-9783-7b0c9db8fbde.png" width="50%" height="auto">

이것을 클릭하여 딥러닝 서버에서도 `C++` 확장을 설치해주세요. 그러면 `C++` 확장이 제공하는 많은 기능을 사용할 수 있습니다. 대표적으로 다음과 같은 기능들을 사용할 수 있습니다.

- 변수 추천하기

    ![](https://code.visualstudio.com/assets/docs/cpp/cpp/list-members-cpp.png)

    *이미지 출처 : https://code.visualstudio.com/docs/cpp/cpp-ide* 

- 정의 추척하기 : <kbd>F12</kbd> 를 누르면 정의로 바로 건너뛸 수 있고, <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>F10</kbd> 을 누르면 정의를 간단하게 볼 수 있습니다.

    ![](https://code.visualstudio.com/assets/docs/cpp/cpp/peekdefn.png)

    *이미지 출처 : https://code.visualstudio.com/docs/cpp/cpp-ide* 

    !!! note
    
        사실상 분석할 때 이 기능을 가장 많이 사용하게 됩니다. 터미널에서 Vim 으로 이렇게 정의를 추적하려면 정말 힘들어요. **VSCode** 로 [**WICWIU**](https://github.com/WICWIU/WICWIU) 분석을 하는 것이 쉽고 편합니다.

# VSCode Git 연동 

**VSCode** 를 딥러닝 서버에 연동했으니 여러가지 Git 확장들을 설치하면 프로젝트 개발이 훨씬 편해집니다.

## Git Graph

[Git Graph](https://marketplace.visualstudio.com/items?itemName=mhutchie.git-graph) 확장을 설치하면 다음과 같이 [**WICWIU**](https://github.com/WICWIU/WICWIU) 의 브랜치 상황을 자세히 볼 수 있습니다.

![2020-08-28_16-35](https://user-images.githubusercontent.com/16812446/91534399-6fc21c80-e94c-11ea-996a-2e3d09b67076.png)

이 확장의 장점은 누가 어떤 브랜치를 만들었는지 시각적으로 볼 수 있고 클릭을 하면 각각의 **commit** 마다 어떤 내용이 수정되었는지 볼 수 있다는 것입니다.

## Git Lens

[Git Lens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) 확장을 설치하면 다음과 같이 [**WICWIU**](https://github.com/WICWIU/WICWIU) 의 소스코드 한줄 한줄을 누가 수정했고 언제 수정했고 왜 수정했는지 볼 수 있습니다.

![2020-08-28_16-38](https://user-images.githubusercontent.com/16812446/91534724-fb3bad80-e94c-11ea-9150-bffb93dcdca6.png)

위 사진을 보면 커서가 있는 곳의 오른쪽에 희미한 글씨로 

```
chuniee0126, a year ago, Update MNIST...
```

라는 메시지가 떠오릅니다. 이로써 이 코드를 누가 고쳤고, 언제 고쳤고, 왜 고쳤고(**commit** 메시지) 를 빠르게 알 수 있습니다.

Git 의 핵심 기능인 책임 추적이 쉽게 가능해지는 것이지요.

# 기타 확장 

이뿐 아니라 여러분들도 다른 편리한 확장을 설치하셔서 사용하세요. 하지만 확장이 너무 많으면 렉이 걸릴 수도 있으니 주의하세요. 정말 편리한 확장을 찾았다면 이 문서를 수정하셔서 다른 [**WICWIU**](https://github.com/WICWIU/WICWIU) 개발자들이 사용할 수 있도록 추천해주세요!