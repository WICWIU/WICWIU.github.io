
[makefile](https://en.wikipedia.org/wiki/Makefile) 은 프로젝트를 자동으로 빌드하기 위한 규칙들을 명시해주어 `make` 명령어로 긴 컴파일 명령어들을 자동으로 실행할 수 있도록 해주는 파일입니다.

!!! note

    아래의 내용은 [저의 블로그 내용](https://ccss17.github.io/makefile.html) 을 다시 정리한 것입니다.

지금부터 학습하면서 소개해드리는 예제 코드들을 `makefile` 에 저장하고 `make` 명령어를 실행해보세요!

## 기본 규칙

`Makefile` 은 프로젝트를 자동으로 빌드하기 위한 여러개의 규칙으로 구성되는데 규칙의 일반적 문법은 다음과 같습니다.

```make
targets : prerequisites 
command
command
command
...
```

- `targets` : 규칙 이름으로써 공백으로 구분되는 파일 이름들이다. 만들어질 파일 이름을 뜻한다. 보통 규칙 당 하나의 파일만 있다. 

- `commands` : `targets` 을 만드는 일련의 명령어들이다. 이 부분은 공백이 아닌 탭으로 시작되어야만 한다. 

- `prerequisites` : 공백으로 구분되는 파일 이름들로써 `commands` 가 실행되기 위하여 필요한 의존성 파일 리스트를 뜻한다. 

!!! info

    **만약 `prerequisites` 에서 명시된 파일들 중 하나가 존재하지 않는다면 먼저 그 파일 이름의 규칙을 찾아서 그 규칙을 실행하여 파일을 만듭니다.**

## 규칙 실행 순서

`make` 명령을 인자 없이 실행하면 `Makefile` 의 첫번째 규칙이 실행된다. 일반적으로 첫번째 규칙을 `all` 로 정하는 국룰이 있다. 

!!! example

    ```make
    blah: blah.o
    cc blah.o -o blah
    
    blah.o: blah.c
    cc -c blah.c -o blah.o
    
    blah.c:
    echo "int main() { return 0; }" > blah.c
    
    clean:
    rm -f blah.o blah.c blah
    ```

    이러한 `Makefile` 의 경우 `blah` 가 첫번째 규칙이므로 디폴트 규칙이 된다. 

## 관례

통상적으로 `CC` 에 컴파일러를, `CFLAGS` 에 컴파일 옵션을 저장하여 규칙에 삽입한다. 

## 규칙의 `targets` 파일이 이미 존재할 경우

`make` 는 규칙의 파일이 이미 존재할 경우 `"up to date"` 메시지를 출력하고 규칙을 실행하지 않는다. 

!!! example

    ```make
    some_file:
      touch some_file
    ```

    처음 실행할 때는 규칙이 실행 되지만 한번 더 실행하면 규칙이 실행되지 않는다. 

## 축약규칙

위 예제의 `Makefile` 은 다음과 같이 축약할 수 있다. 

```make
some_file:; touch some_file
```

## PHONY

:   `.PHONY` : `targets` 이 실제 파일 이름이 아니라는 것을 명시한다.

`make` 는 효율성을 위하여 `targets` 파일이 이미 존재하면 규칙을 실행하지 않는다. 그런데 `clean` 이나 `all` 같은 규칙은 실제 파일 이름을 지칭하지 않는다. 만약 실수로 `all` 파일이나 `clean` 파일을 생성하였을 경우 `all` 이나 `clean` 규칙이 실행되지 않을 수도 있다. 이것을 방지하기 위하여 `.PHONY` 에 `targets` 을 명시하면 `make` 는 파일이 존재하는지 검사하지 않고 규칙을 무조건 실행한다. 

!!! tip

    또한 파일 존재 검사를 하지 않기 때문에 `.PHONY` 를 사용하면 `make` 의 성능이 향상된다. 

다음의 예시를 살펴보면서 `.PHONY` 를 이해해봅시다.

!!! example

    ```make
    one:; touch one
    clean:; rm -f one
    ```

    위 예제의 `clean` 규칙은 파일 `one` 을 삭제하기로 되어있다. 그런데 만약 `clean` 이라는 이름을 가진 파일이 존재하면 `make clean` 으로 규칙을 실행한다 해도 `make: 'clean' is up to date.` 라는 메세지가 출력되고 규칙이 실행되지 않는다. 

    ```make
    one:; touch one
    clean:; rm -f one
    .PHONY: clean
    ```

    따라서 `.PHONY` 에 `clean` 을 명시하여 실제 파일 이름이 아니라는 것을 명시해준다. 그러면 `clean` 파일이 있어도 규칙이 항상 실행된다. 

## 변수

문자열을 변수에 저장하고 `${}` 나 `$()` 로 사용할 수 있다.

!!! example

    ```make
    files = file1 file2
    some_file: $(files)
      echo "Look at this variable: " $(files)
      touch some_file
    
    file1:
      touch file1
    file2:
      touch file2
    
    clean:
      rm -f file1 file2 some_file
    ```

### 변수 할당

:   `var = value` : 지연된 할당으로써 변수가 사용될 때 그 값들이 재귀적으로 할당된다. 

!!! example
    
    ```make
    foo = $(bar)
    bar = $(ugh)
    ugh = Huh?

    all:;echo $(foo)
    ```

    이때 `foo` 에 할당되는 `bar` 는 `foo` 가 선언되는 시점에서 정의되지 않았기 때문에 컴파일 에러가 날 것 같다. 하지만 `=` 는 지연된 할당으로서 `foo` 가 실질적으로 사용될 때 참조된 모든 변수들을 재귀적으로 찾아가며 할당한다. 

:   `var := value` : 즉시 할당으로써 변수가 선언될 때 그 값들이 재귀적으로 할당된다. 

!!! example

    ```make
    foo := $(bar)
    bar := $(ugh)
    ugh := Huh?

    all:;echo $(foo)
    ```

    이전 예제와는 달리 이 코드는 아무것도 출력하지 않는다. `foo` 가 선언되는 시점에서 `bar` 는 정의되지 않았기 때문이다. 

    ```make
    ugh := Huh?
    bar := $(ugh)
    foo := $(bar)

    all:;echo $(foo)
    ```

    즉시 할당된 변수를 제대로 출력되게 하기 위해서는 위와 같이 선언 순서를 바꾸어 주어야 한다. 즉시 할당과 지연 할당을 섞어서 쓰면 다음과 같이 순서를 배치하는 것 또한 가능하다. 

    ```make
    bar = $(ugh)
    ugh := Huh?
    foo := $(bar)

    all:;echo $(foo)
    ```

    이렇게 해도 된다. 

    ```make
    foo = $(bar)
    ugh := Huh?
    bar := $(ugh)

    all:;echo $(foo)
    ```

다음 예시로 지연 할당과 즉시 할당을 비교해보세요.
  
!!! example

    ```make
    one = one ${later_variable}
    two := two ${later_variable}

    later_variable = later

    .PHONY: all
    all: 
      echo $(one)
      echo $(two)
    ```

    위 예시에서 `one` 에는 `later_variable` 이 할당되지만 `two` 는 `:=` 으로 즉시할당을 사용했기 때문에 `later_variable` 이 할당되지 않는다. 

:   `var ?= value` : 만약 `var` 이 할당되어 있지 않으면 `value` 로 할당한다. 

!!! example

    ```make
    one = hello
    one ?= will not be set
    two ?= will be set

    .PHONY: all
    all: 
      echo $(one)
      echo $(two)
    ```

    위 예시에서 `?=` 로 `one` 과 `two` 를 할당하는데 `two` 만 할당된다. 

:   변수 확장 : 단순히 변수를 이어붙히는 것으로 변수 확장을 할 수 있다. 

!!! example

    ```make
    one = hello
    one := ${one} there
    all: 
      echo $(one)
    ```

    위 예시에서 즉시할당 `:=` 를 지연할당 `=` 으로 바꾼다면 무한루프 에러가 발생한다. 

:   `+=` 변수 확장 : `+=` 연산자로도 변수 확장을 할 수 있다. 

!!!	example

    ```make
    one = hello
    one += there
    all: 
      echo $(one)
    ```

    위 예시는 `hello there` 를 출력한다. 

:   할당되지 않은 변수 : 할당되지 않은 변수를 다른 변수에 할당하면 스페이스 하나가 할당된다. 

!!! example

    ```make
    with_spaces = hello   # with_spaces has many spaces after "hello"                    
    after = $(with_spaces)there

    nullstring =                              
    space = $(nullstring) # Make a variable with a single space.

    .PHONY: all 
    all: 
        @echo "$(after)"
        @echo start"$(nullstring)"end
        @echo start"$(space)"end
    ```

    위 예시의 출력은 다음과 같다. 

    ```shell
    $ make
    hello   there
    startend
    start end
    ```

:   변수 치환 : `$(var:a=b)` 로 변수 `var` 의 `a` 가 `b` 로 치환된다.

!!! example

    ```make
    foo := a.o b.o c.o
    bar := $(foo:.o=.c)
    all: ; echo $(bar)
    ```

    위 예시는 `a.c b.c c.c` 를 출력한다. 

:   패턴 변수 치환 : `%` 를 사용한 패턴을 포함하여 변수 치환을 할 수도 있다. 

!!! example

    ```make
    foo := a.o b.o c.c
    bar := $(foo:%.o=%)
    all: ; echo $(bar)
    ```

    위 예시는 `a b c.c` 를 출력한다. 


## 함축 규칙

`Makefile` 은 다음과 같이 함축 규칙을 지원한다. 

```make
# blah.o 규칙 안에 주석을 달면 함축 규칙이 실행되지 않는다. 
# Implicit command of: "cc blah.o -o blah"
blah:

# Implicit command of: "cc -c blah.c -o blah.o"
blah.o:

blah.c:
echo "int main() { return 0; }" > blah.c

clean:
rm -f blah.o blah blah.c
```

## 특수 문자

:   와일드카드 `*` : 와일드카드 `*` 는 `targets`, `prerequisites`, `commands` 에서 사용 가능하다.

!!! example

    ```make 
    some_file: *.c
        # create the binary
    
    *.c:
        touch f1.c
        touch f2.c
    
    clean:
        rm -f *.c
    ```

    이 `Makefile` 은 `.c` 파일이 단 하나라도 있으면 `*.c` 규칙이 실행되지 않는다. 와일드카드 `*` 는 어떤 문자열이라도 해당되기에 `make` 가 `*.c` 라는 의존성이 해결 된 것으로 판단하기 때문이다. 

:   와일드카드 `*` 함수 : 와일드카드 `*` 를 `targets`, `prerequisites`, `commands` 에서밖에 사용하지 못하기 때문에 변수할당이나 함수인자 전달 시에는 와일드카드 함수를 사용해야 한다. 

!!! example

    ```make
    wrong = *.o # Wrong
    objects := $(wildcard *.c) # Right

    some_binary: 
        touch f1.c
        touch f2.c
        echo $(wrong)
        echo $(objects)

    clean:
        rm -f *.c
    ```

:   `%` 는 모든 파일 이름을 뜻한다.

!!! example

    `%.c` 라고 하면 모든 `.c` 파일을 뜻한다. 

:   `$@` 는 규칙의 `:` 왼쪽 부분이다. 

:   `$<` 는 규칙의 `:` 오른쪽 부분의 첫번째 아이템이다. 

:   `$^` 는 규칙의 `:` 오른쪽 부분이다. 

## 다중 타겟

`targets` 에 여러 파일이 선언되어 사용할 수도 있다. 

!!! example

    ```make
    all: f1.o f2.o
    f1.o f2.o:
      echo $@
    ```

    위 예시에서 규칙 `all` 에서 `f1.o` 와 `f2.o` 를 지정하였는데 각각 `f1.o f2.o` 규칙을 실행하게 된다. 이때 실행 명령 `echo $@` 의 `$@` 에는 각각 맥락에 따라 `f1.o` 와 `f2.o` 가 대입된다. 

`targets` 에 와일드카드 `%` 로 일반적인 파일을 선언하여 사용할 수 있다. 

!!! example

    ```make
    all: f1.o f2.o
    %.o:
      echo $@
    ```

    이 경우 `%.o` 규칙으로 인해 `.o` 확장자를 가진 모든 파일이 이 규칙으로 생성된다. 

    ```make
    all: f1.o f2.o
    %.o:
      echo $@
    f1.o:
      echo TEST
    ```

    하지만 위와 같이 `f1.o` 의 규칙을 따로 지정해줄 경우 `f1.o` 를 생성할 때 `make` 는 `%.o` 규칙이 아니라 `f1.o` 규칙을 실행한다. 

*References*: 

:   https://makefiletutorial.com/ 

:   http://www.cs.colby.edu/maxwell/courses/tutorials/maketutor/