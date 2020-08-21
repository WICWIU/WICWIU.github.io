
!!! note

    아래의 내용은 [저의 블로그 내용](https://ccss17.github.io/makefile.html) 을 다시 정리한 것입니다.

## vpath

:   `vpath` : `prerequisites` 를 찾는 경로를 현재 디렉토리와 더불어서 추가한다. 

기본적으로 `make` 는 `prerequisites` 를 현재 디렉토리에서 찾는다. 그런데 `vpath` 로 특정 `prerequisites` 의 탐색경로를 현재 디렉토리에 더불어서 추가할 수 있다. 
  
:   형식 : `vpath <pattern> <directories, space/colon seperated>`

`<pattern>` 에서 특수문자 `%` 를 사용할 수 있다. `VPATH` 환경변수에도 경로를 추가하여 사용할 수 있다.

!!! example

    ```make
    vpath %.h ../headers ../other-directory

    some_binary: ../headers blah.h
      touch some_binary

    ../headers:
      mkdir ../headers

    blah.h:
      touch ../headers/blah.h
    ```

    위 예시의 `vpath` 때문에 `.h` 확장자를 가진 파일을 현재 디렉토리 뿐만 아니라 `../headers` 디렉토리와 `../other-directory` 에서도 찾는다. 이에 따라 만약 `vpath` 가 포함된 라인을 지우고 `make` 를 실행해보면 파일 `blah.h` 를 찾지 못하여 규칙 `blah.h:` 를 항상 실행하게 된다.
  
## Static Pattern Rules

:   static pattern : 패턴에 맞는 `targets` 을 자동으로 `prerequisites` 으로 변환해주는 규칙이다.

`targets ...: target-pattern: prereq-patterns ...` 의 형식으로 사용된다. `target-pattern` 에 매칭된 `target` 을 `prereq-pattern` 으로 변환해준다.

!!! example

    ```make
    objects = foo.o bar.o
    $(objects): %.o: %.c
      echo "Call gcc to generate $@ from $<"
    ```

    위 규칙은 다음의 규칙과 동일하다.

    ```make
    foo.o: foo.c
      echo "Call gcc to generate $@ from $<"

    bar.o: bar.c
      echo "Call gcc to generate bar.o from bar.c"
    ```

## Static Pattern Rules and Filter

:   static pattern with Filter : static pattern 의 `targets` 을 패턴으로 필터링해서 매칭되지 않는 `target` 은 규칙에 적용시키지 않는 규칙이다. 

!!! example

    ```make
    objects = foo.o bar.o f1.c
    $(objects): %.o: %.c
      echo "Call gcc to generate $@ from $<"
    ```

    위 규칙에서 `f1.c` 는 분명 static pattern 에 해당되지 않는 예외이다. 이 규칙은 다음의 규칙과 동일한 기능을 하게 된다. 

    ```make
    foo.o: foo.c
      echo "Call gcc to generate $@ from $<"

    bar.o: bar.c
      echo "Call gcc to generate bar.o from bar.c"

    f1.c:
      echo "Call gcc to generate f1.c from"
    ```

    따라서 이러한 예외는 규칙에 적용시키지 않기 위하여 다음과 같이 필터링을 걸 수 있다. 

    ```make
    objects = foo.o bar.o f1.c
    $(filter %.o $(objects)): %.o: %.c
      echo "Call gcc to generate $@ from $<"
    ```

    위 규칙은 `objects` 에서 `.o` 로 끝나는 파일만 규칙에 적용시킨다. 

## Double-Colon

:   Double-Colon : Double-Colon 으로 시작되는 규칙이 여러번 정의되어도 그것들을 모두 실행하게 해준다. 

이 규칙은 흔히 쓰이지는 않습니다. 그래도 다음 예시를 한 번 보겠습니다.

!!! example

    ```make
    all: blah
    blah::
      echo "hello"
    blah::
      echo "hello again"
    ```

    위 규칙에서 `blah` 가 Double-Colon 으로 시작되는데 Single-Colon 으로 바꾸면 에러가 발생하면서 마지막 규칙만 실행된다. 

# 명령 실행 관련 규칙

## Command Echoing/Silencing

:   Command Echoing/Silencing : 명령어 앞에 `@` 를 붙히면 명령어가 출력되지 않는다. 

!!! example

    ```make
    all: 
      @echo "hello"
      echo "hello again"
    ```

    위 예시의 출력은 다음과 같다. 
    
    ```shell
    $ make
    hello
    echo "hello again"
    hello again
    ```

!!! tip

    `make -s` 로 실행하면 모든 명령어에 `@` 를 붙혀서 실행한 효과를 얻을 수 있다. 

## Command Execution

:   Command Execution : 각각의 명령어는 항상 새로운 쉘에서 실행되는 것과 같다. 

따라서 `make` 에서 명령어들을 실행하기 위하여 보통 `; \` 로 명령어를 이어준다. 

!!! example

    ```make
    all: 
      cd ..
      echo `pwd`
      cd ..;echo `pwd`
      cd ..; \
      echo `pwd`
    ```

    위 예시의 출력은 다음과 같다. 

    ```shell
    $ make
    cd ..
    echo `pwd`
    /home/ccsss/repo/csrepo/ccss17.github.io/maketest
    cd ..;echo `pwd`
    /home/ccsss/repo/csrepo/ccss17.github.io
    cd ..; \
    echo `pwd`
    /home/ccsss/repo/csrepo/ccss17.github.io
    ```

## Default Shell

:   Default Shell : `make` 가 명령어를 실행하는 기본 쉘은 `/bin/sh` 인데 `SHELL` 변수를 바꿈으로써 기본 쉘을 변경할 수 있다. 

!!! example

    ```make
    all:
      echo "current shell: $$0"
    ```

    위 예시가 `/bin/sh` 을 출력하는데 비해 다음 예시는 `/bin/bash` 를 출력한다. 

    ```make
    SHELL=/bin/bash
    all:
      echo "current shell: $$0"
    ```

## DELETE_ON_ERROR

:   `DELETE_ON_ERROR` : `make` 는 규칙이 `nonzero` 종료 코드를 반환하면 전체 실행을 중단하는데, `.DELETE_ON_ERROR` 가 설정되면 이때 `nonzero` 반환값을 반환한 규칙의 `target` 파일을 삭제한다. 

대부분의 경우에 `.DELETE_ON_ERROR` 를 설정하는 것이 좋다. 

!!! example

    ```make
    all: one

    one:
      touch one
      false
    ```

    위 예시는 `false` 가 `nonzero` 종료코드를 반환하기에 전체실행이 중단되지만 파일 `one` 이 사라지지 않는다. 하지만 다음 예시는 파일 `one` 이 자동으로 삭제된다. 

    ```make
    .DELETE_ON_ERROR
    all: one

    one:
      touch one
      false
    ```
  
## 에러 처리

:   `-k` 옵션 : `make` 를 이 옵션으로 실행하면 에러가 발생해도 실행을 중단하지 않고 끝까지 실행한다.

  - 모든 에러를 한번에 확인하고 싶을 때 유용하다.

:   `-i` 옵션 : `make` 를 이 옵션으로 실행하면 모든 에러를 무시한다. 

!!! example

    ```make
    .DELETE_ON_ERROR:
    all: one 
    
    one:
        touch one
        false
        touch one-post
    ```

    위 예시를 `make -k` 로 실행하면 파일 `one` 생성되었다가 `false` 에서 전체 실행이 중단되고 `one` 이 삭제된다.

    반면 `make -i` 로 실행하면 `false` 가 반환하는 `nonzero` 리턴값을 무시하고 파일 `one` 과 파일 `one-post` 가 생성된다.

:   `-` : 명령어 앞에 `-` 를 붙히면 `nonzero` 종료코드가 반환되어도 전체 실행이 중단되지 않고 무시된다.

!!! example

    ```make
    one:
        -false
        touch one
    ```

    위 예시에서 에러가 무시되고 파일 `one` 이 잘 실행된다. 

# Recursive make

:   `make -C <directory>` : `<directory>` 를 `make` 한다. 

!!! example

    ```make
    new_contents = "hello:;touch inside_file"                                            
    all:
        mkdir -p subdir
        echo $(new_contents) > subdir/makefile
        make -C subdir
    ```

    위 예시는 `subdir` 디렉토리를 만들고 `subdir/makefile` 을 `make` 한다. 

:   `export <variable>` : 재귀적으로 호출된 하위 `make` 에서 상위 `make` 의 변수를 사용할 수 있게 한다. 

!!! example

    ```make
    new_contents = "hello:;echo \$$(cooly)"

    all:
        mkdir -p subdir
        echo $(new_contents) | sed -e 's/^ //' > subdir/makefile
        make -C subdir
    cooly = "The subdirectory can see me!"
    export cooly
    ```

    위 예시의 `export cooly` 로 생성된 `subdir/makefile` 에서 `cooly` 변수를 사용할 수 있게 된다.

:   `EXPORT_ALL_VARIABLES` : `.EXPORT_ALL_VARIABLES:` 를 선언하면 모든 변수가 `export` 된다. 

!!! example

    ```make
    .EXPORT_ALL_VARIABLES:
    new_contents = "hello:\n\techo \$$(cooly)"

    cooly = "The subdirectory can see me!"
    # This would nullify the line above: unexport cooly

    all:
      mkdir -p subdir
      echo $(new_contents) | sed -e 's/^ //' > subdir/makefile
      @echo "---MAKEFILE CONTENTS---"
      @cd subdir && cat makefile
      @echo "---END MAKEFILE CONTENTS---"
      cd subdir && $(MAKE)
    ```

*References*: 

:   https://makefiletutorial.com/ 

:   http://www.cs.colby.edu/maxwell/courses/tutorials/maketutor/