우리는 분석을 통하여 데이터셋을 `Tensor`로 변환하는 방법을 이해했다.

**데이터셋을 `Tensor` 로 변환하는 방법이란 (1) 데이터셋을 적절한 방식으로 읽고 (2) 메모리에 저장한 다음 (3) `Tensor` 객체를 데이터셋 형상에 맞춰서 적절히 만들고 (4) 그냥 `Tensor[Index5D(i1, i2, i3, i4, i5)]` 라는 식으로 복사하는 것이다.**

그러므로 **CasiaWebFace** 의 `.png` 데이터셋을 `Tensor` 로 변환하는 본격적인 코딩을 시작할 수 있다. 하지만 그에 앞서 디지털 이미지가 컴퓨터에 저장되는 방식에 대한 이해, 그리고 `.png` 파일 포맷에 대한 가벼운 이해라도 필요하다.

# Digital Image

디지털 이미지는 [$xy$ 축 좌표평면의 좌표 $x, y$ 를 이변수 벡터함수에 입력하고 얻은 출력으로 구성된 것](https://en.wikipedia.org/wiki/Digital_image)이다. 그 출력을 픽셀이라고 한다. 

<figure>
  <img src="https://t1.daumcdn.net/cfile/tistory/2724594D587F706214" width="50%" />
  <figcaption><i>픽셀로 구성되는 이미지 (https://twlab.tistory.com/23)</i></figcaption>
</figure>

수학에서의 공간좌표는 다음과 같다.

<figure>
  <img src="https://user-images.githubusercontent.com/16812446/89725063-24e86f80-da46-11ea-9e9c-d0d5ab4b6c25.png" width="70%" />
  <figcaption><i>수학에서의 3차원 공간좌표</i></figcaption>
</figure>

하지만 이미지에서의 좌표는 수학에서의 좌표와 달리 왼쪽 상단부에서 좌표 $(0, 0)$ 이 시작하고 $y$ 축이 아래쪽 방향으로 증가한다. 이 $xy$ 축에 따른 위치에 있는 값이 이미지의 픽셀값이 된다. 그리고 $z$ 축은 픽셀 정밀도를 나타낸다. $z$ 축이 깊을수록 픽셀 정밀도(`depth`)가 높아져서 색채가 더 높은 이미지가 나타난다. 픽셀 정밀도에 대해서는 아래에서 자세히 다룬다.

<figure>
  <img src="https://user-images.githubusercontent.com/16812446/89725085-71cc4600-da46-11ea-8664-129f8777b06a.png" width="70%" />
  <figcaption><i>이미지에서의 픽셀값을 나타내기 위한 3차원 공간좌표</i></figcaption>
</figure>

## 채널(Channel)

위에서 살펴본 이미지의 공간좌표를 통하여 이미지가 픽셀 값을 정한다는 것을 알아보았다. 그런데 이렇게 $3$ 차원 공간좌표 하나로 이미지를 나타내면 이미지가 한 가지 색의 강도만 나타낼 수 있다. 

따라서 보통은 픽셀 하나에 여러개의 색을 갖게 하고 그 색의 조합으로 최종적으로 픽셀의 색을 결정한다. 대표적으로 빨강, 초록, 파랑의 색 조합으로 픽셀의 색을 결정하는 방식을 트루컬러 **RGB** 방식이라고 한다. 

^^이러한 하나의 이미지 공간좌표를 채널(Channel)^^ 이라고 한다. 하나의 채널은 이미지의 픽셀의 색상 정보를 가지고 있다. 채널이 $1$ 개 라면 보통 빛의 강도만을 나타내어 그레이 스케일 이미지를 표현하게 된다.

**RGB** 의 경우 각 픽셀 당 빨강, 초록, 파랑의 정도를 나타내기 위하여 위와 같은 공간좌표가 $3$ 개 있다. 그러므로 이미지가 $4$ 차원 형상을 갖는다. 즉, **RGB** 는 채널 $3$ 개(공간좌표 $3$개)를 사용하는 것이다.

요즘에는 투명도를 채널을 하나 더 추가하여 $4$ 개의 채널을 갖는 **RGBA** 이미지를 쓰기도 한다. 이 이미지는 픽셀의 색을 나타내는 공간좌표 $4$ 개를 갖는다.

## 픽셀 정밀도(Bit Depth)

이미지가 픽셀을 정밀하게 나타내는 정도를 정밀도(**depth**)라고 한다. 위에서 살펴본 **이미지의 공간좌표에서 $z$축이 깊은 정도** 를 나타내는 것이 픽셀 정밀도(**depth**) 인 것이다. 픽셀 정밀도를 나타내는 ^^단위는 비트^^ 이다.

### 이진 이미지 (`depth=1`)

초기의 이미지는 흑黑 을 뜻하는 $0$ 과 백白 을 뜻하는 $1$ 만으로 픽셀을 구성했다. 이것을 이진 이미지(binary image)라고 한다. 이진 이미지의 경우 픽셀을 표현하기 위하여 `1` 비트만 있으면 되기 때문에 `depth` 가 `1` 이다. 즉, 이진 이미지의 $z$ 축의 깊이는 $1$ 이다.

이진 이미지는 픽셀의 색깔을 나타내기 위하여 하나의 공간좌표만을 가지기 때문에 채널이 `1` 이다.

<figure>
  <img src="https://kr.mathworks.com/help/images/intro4.gif" width="70%" />
  <figcaption><i>이진 이미지 (https://kr.mathworks.com/help/images/image-types-in-the-toolbox.html)</i></figcaption>
</figure>

### 그레이 스케일 (`depth=8`)

그레이 스케일은 빛의 강도만을 픽셀에 나타내는 이미지이다. 그레이 스케일의 픽셀은 `0` 부터 `255` 의 값을 가진다. `8` 비트가 있으면 $2 ^{8} = 256$ 가지 색을 표현할 수 있다. 그러므로 그레이 스케일은 픽셀 하나당 `1` 바이트(`8` 비트) 의 정밀도(**depth**) 를 갖는다. 즉, 그레이 스케일의 $z$ 축의 깊이는 $8$ 이다.

그레이 스케일도 픽셀의 색을 나타내기 위하여 하나의 공간좌표만을 가지기 때문에 채널이 `1` 이다.

<figure>
  <img src="https://upload.wikimedia.org/wikipedia/commons/f/fa/Grayscale_8bits_palette_sample_image.png" width="30%" />
  <figcaption><i>그레이 스케일 이미지 (https://en.wikipedia.org/wiki/Grayscale)</i></figcaption>
</figure>

### 트루 컬러 (`depth=24`)

트루 컬러 이미지는 픽셀이 `3` 개의 채널을 갖고, 각 채널이 `8` 비트의 픽셀 정밀도(`depth`)를 갖는다. 즉, 픽셀의 색을 나타내기 위하여 $3$ 개의 공간좌표를 가지는 것이다. 트루 컬러는 최종적으로 $3$ 개의 공간좌표의 좌표값(픽셀값)을 조합하여 색을 나타낸다. 트루 컬러 이미지는 채널이 `3` 개 이므로 한 픽셀 당 `24` 비트로 색을 표현한다.

**RGB** 는 트루 컬러 이미지의 대표적인 예이다. **RGB** 는 빨강, 초록, 파랑의 강도를 각각 세 가지 채널(세 가지 공간좌표)로 나타낸다. 첫번째 채널이 빨간색의 정도를 `0~255` 로 나타내고, 두번째 채널이 초록색의 정도를 `0~255` 로 나타내고, 세번째 채널이 파란색의 정도를 `0~255` 로 나타낸다. 그리고 이 세 가지 색깔의 색조합을 통해 최종적으로 한 픽셀의 색깔을 만들어낸다.

다음 그림은 `0~255` 의 정수 채널 값을 `0~1` 의 실수로 스케일링 한 것이다.

<figure>
  <img src="https://kr.mathworks.com/help/images/imagetypergb.png" width="70%" />
  <figcaption><i>트루 컬러 이미지 (https://kr.mathworks.com/help/images/image-types-in-the-toolbox.html)</i></figcaption>
</figure>

*References*: 

:   https://en.wikipedia.org/wiki/Digital_image

:   https://twlab.tistory.com/23

:   https://kr.mathworks.com/help/images/image-types-in-the-toolbox.html

# PNG file format

`.png` 파일의 포맷에 대한 정보는 [이곳](https://github.com/corkami/formats/blob/master/image/png.md)을 통하여 얻을 수 있었다.

## Structure

![](https://raw.githubusercontent.com/corkami/formats/master/image/PNGBasicStruct.svg)
<figcaption><i>PNG file Structure (https://github.com/corkami/formats/blob/master/image/png.md)</i></figcaption>

**PNG** 파일은 위와 같이 **Signature** 와 여러개의 **chunk** 들로 구성된다.

1. **Signature**: **PNG** 파일임을 구별할 수 있게 해주는 매직넘버로써 다음과 같은 `8` 바이트 데이터로 구성된다.

    `\x89PNG\x0d\x0a\x1a\x0a` 

2. **chunk**: [**length-type-data-checksum**] 와 같이 `4` 가지 데이터로 구성되는 데이터구조이다. 

    1. `length`: `>4u` 

        `4` 바이트 빅엔디안 `unsigned` 로 이루어진 데이터 길이 지정자이다. 이 길이는 `type-data-checksum` 의 길이가 아니라 오직 `data` 의 길이를 뜻한다. 

    2. `type`: `4c` 
    
        `4` 바이트 `char` 로 이루어진 타입 지정자이다. 타입은 `IHDR`, `IDAT`, `IEND` 등이 있다. `IHDR` 은 헤더 데이터를 뜻하고, `IDAT` 은 실제 이미지 데이터를 뜻하고, `IEND` 는 파일의 끝을 나타내는 데이터를 뜻한다.

    3. `data`: `[length]b`

        `length` 바이트만큼의 실제 이미지 데이터이다.

    4. `checksum(type+data)`: `4u`

        `4` 바이트 `unsigned` 로 이루어진 체크섬이다. 데이터가 훼손되지 않았는지 검증할 수 있게 해준다.

3. **Terminator**: 마지막으로 **PNG** 파일은 파일의 끝을 알리는 다음의 바이트 배열을 갖는다. 

    `00 00 00 00 .I .E .N .D AE 42 60 82`

## Example

![](https://raw.githubusercontent.com/corkami/formats/master/image/PNGRGB_dissected.png)
<figcaption><i>a dissected RGN png (https://github.com/corkami/formats/blob/master/image/png.md)</i></figcaption>

**PNG** 파일은 위와 같이 Signature 로 시작하여 Header, Data, Terminator 로 구성된다. 이것은 빨강, 초록, 파랑 픽셀만을 갖는 단순힌 **PNG** 파일을 분석한 것이다.

*References*: 

:   https://github.com/corkami/formats/blob/master/image/png.md

---

# CImg

이제 실제로 **PNG** 파일을 읽어서 메모리에 저장한 후 `Tensor` 로 변환해볼텐데 바이너리 파일을 읽고 저장하는 코드를 짜려면 [**PNG** 파일의 전체 스펙을 기술한 표준](http://www.libpng.org/pub/png/spec/1.2/png-1.2-pdg.html) 모두 이해한 다음 바이너리 레벨의 처리를 해주어야 하므로 상당히 시간 소모가 많이 된다. 심지어 바이너리 레벨의 처리는 취약점이 발생하기 매우 쉬워서 [**WICWIU**](https://github.com/WICWIU/WICWIU) 에 취약점이 생길 수도 있다. 

그러므로 **PNG** 파일을 읽을 수 있도록 이미 제작된 라이브러리를 사용하는 것이 낫다. `C++` 에서 **PNG** 파일을 읽을 수 있는 라이브러리는 많이 있지만 그 중에서 [`CImg`](http://cimg.eu) 를 사용하기로 했다. 그 이유는 다음과 같다.

1. `CImg` 는 `C++` 에서 이미지를 처리할 수 있도록 최적화된 라이브러리이다.

2. `CImg` 는 이미지를 처리할 수 있는 클래스 템플릿을 제공하여 다양한 픽셀 타입(`bool`, `char`, `int`, `float` 등)으로 이미지를 저장할 수 있다.

3. `CImg` 는 여러 이미지를 손쉽게 다룰 수 있는 기능을 제공한다.

4. `CImg` 는 **thread-safe** 하다.

5. `CImg` 는 가볍다. `CImg` 는 단지 하나의 헤더파일 `CImg.h` 로 구성된 라이브러리이다. `CImg` 에는 오직 `4` 가지 클래스만 정의되어 있다. `CImg` 는 다른 의존성 없이 순전히 **STL** 과 시스템 라이브러리만을 사용한다.

6. `CImg` 는 무료이고 오픈소스이다.

*References*: 

:   http://cimg.eu

## Installation

`git clone --depth=1 https://github.com/dtschump/CImg.git` 로 `CImg.h` 를 가져온 후 그냥 

```cpp
#include "/path/to/CImg.h"
``` 

헤더를 포함시키면 된다. 단지 헤더 파일을 포함시키는 것이 설치의 끝이다. 그리고 편의를 위하여 이름공간 `cimg_library` 를 사용하면 된다. 그러니까 

```c++ 
using namespace cimg_library;
```

를 포함시키면 된다.

## Getting Started

다음의 예제 코드를 보자. `lena.png` 는 http://optipng.sourceforge.net/pngtech/img/lena.png 에서 얻을 수 있다.

```c++ linenums="1"
#include "CImg.h"
#include <iostream>
using namespace cimg_library;
using namespace std;

int main(int c, char ** v)
{
    CImg<unsigned char> lena("lena.png");
    cout << "width: " << lena.width() << endl;
    cout << "height: " << lena.height() << endl;
    cout << "depth: " << lena.depth() << endl;
    cout << "channels: " << lena.spectrum() << endl;
    cout << "size: " << lena.size() << endl;
    cout << "pixel type: " << lena.pixel_type() << endl;
    lena.print();
    return 0;
}    
``` 

- **8**:

    트루 컬러 **RGB** 이미지인 `lena.png` 를 읽고 `unsigned char` 로 읽는다. 이미지는 보통 이처럼 `unsigned char` 로 읽으면 된다. 왜냐하면 각 채널이 `0~255` 의 값을 갖기 때문이다. 만약 `char` 로 읽으면 채널의 값이 `-128~127` 의 값을 갖게 되버린다.

- **9-15**:

    [`CImg` 의 **API**](http://cimg.eu/reference/structcimg__library_1_1CImg.html) 를 참고하면 이미지의 메타데이터를 얻을 수 있는 함수들이 있다. 그러한 함수들로 위와 같은 코드가 구성되었다.

이 프로그램을 `main.cpp` 파일에 저장한다면 리눅스 시스템에서는 다음과 같이 컴파일하고 실행하면 된다.

```shell
$ g++ main.cpp -O2 -lm -lpthread -lX11
$ ./a.out
width: 512
height: 512
depth: 1
channels: 3
size: 786432
pixel type: unsigned char
CImg<unsigned char>: this = 0x7ffdba95e5a0, size = (512,512,1,3) [768 Kio], data = (unsigned char*)0x7fe294140010..0x7fe29420000f (non-shared) = [ 226 226 223 223 226 226 228 227 ... 88 85 80 89 77 79 81 81 ], min = 3, max = 255, mean = 128.228, std = 58.9838, coords_min = (264,198,0,1), coords_max = (445,51,0,0).
```

`width()` 함수가 `512` 를 반환했다. 이것은 이미지의 가로($x$축) 길이를 뜻한다. `height()` 함수가 `512` 를 반환했다. 이것은 이미지의 세로($y$축) 길이를 뜻한다.

`depth()` 함수가 `1` 를 반환했다. 이것은 이미지의 픽셀 정밀도($z$축)을 뜻한다. 하지만 `1` 비트를 뜻하는 것이 아니라 `1` 바이트를 뜻하는 것이므로 각 채널이 `8` 비트의 픽셀 정밀도를 갖는다는 것을 의미한다. 

그런데 트루 컬러 **RGB** 이미지의 픽셀 정밀도는 `24` 라고 했었다. 하지만 `spectrum()` 함수가 `3` 을 반환하였다. 그렇기 때문에 채널이 `3` 개 이므로 $3 \times 8 = 24$ 로써 픽셀 정밀도가 `24` 가 된다. 

*References*: 

:   http://cimg.eu/reference/group__cimg__tutorial.html 

## 픽셀 저장 방식

`CImg` 는 생성될 때 이미지의 모든 픽셀값을 메모리에 한번에 저장한다. `CImg` 는 기본적으로 `4` 차원(`width`, `height`, `depth`, `dim`) 배열이다. 그리고 이 픽셀값들이 실제로 메모리에 저장될 때는 `width * height * depth * dim` 의 크기의 선형 배열(`1` 차원 배열)로 저장된다.

그러니까 `CImg<T>` 로 이미지 객체가 생성되면 `width * height * depth * dim * sizeof(T)` 바이트가 할당된다. 이렇게 픽셀들이 저장된 메모리의 주소값은 `CImg<T>::data()` 메소드가 반환한다. 이것을 `T*` 에 저장하면 된다. 

### 픽셀 저장 순서

픽셀값은 $x$ 축, $y$ 축, $z$ 축, $v$ 축(각각 `width`, `height`, `depth`, `dim(channels)`) 순서대로 저장된다. 즉 이미지의 왼쪽 위 픽셀부터 오른쪽 아래 픽셀까지 순서대로 저장되어 있다. 

픽셀값은 교차로 배치되어 있지 않다. 그렇기 때문에 $n \times m \times 1 \times 3$ 형상의 **RGB** 이미지는 메모리에 선형 순서로

$$ R _{00} R _{01} \dots R _{0n} R _{10} R _{11} \dots R _{1n}  \dots R _{m0} R _{m1} \dots R _{mn} $$ 

$$G _{00} G _{01} \dots G _{0n} G _{10} G _{11} \dots G _{1n}  \dots G _{m0} G _{m1} \dots G _{mn} $$ 

$$B _{00} B _{01} \dots B _{0n} B _{10} B _{11} \dots B _{1n}  \dots B _{m0} B _{m1} \dots B _{mn} $$

로 저장되어 있고, 

$$ R _{00} G _{00} B _{00} R _{01} G _{01} B _{01} \dots $$

처럼 교차로 저장되어 있지 않다.

### 픽셀 값 가져오기

[`CImg` 의 **API**](http://cimg.eu/reference/structcimg__library_1_1CImg.html) 를 참조하면 [`()` 연산자 오버로딩 메소드](http://cimg.eu/reference/structcimg__library_1_1CImg.html#accb8526e4303186fb6246ac1301fdf66)를 볼 수 있다. 이 메소드는 다음과 같이 정의되어 있다.

```c++ linenums="1"
T& operator() 	(const unsigned int  	x,
                 const unsigned int  	y = 0,
                 const unsigned int  	z = 0,
                 const unsigned int  	c = 0 
                ) 	
```

그리고 설명은 다음과 같다.

!!! quote

    Access to a pixel value.

    Return a reference to a located pixel value of the image instance, being possibly const, whether the image instance is const or not. This is the standard method to get/set pixel values in CImg<T> images.

    Parameter

    :   `x`:	X-coordinate of the pixel value

    :   `y`:	Y-coordinate of the pixel value

    :   `z`:	Z-coordinate of the pixel value

    :   `c`:	C-coordinate of the pixel value

이것을 사용하면 매우 편리하게 좌표로 이미지가 저장되어 있는 곳의 메모리를 참조할 수 있다.

위에서 설명한 $n \times m \times 1 \times 3$ 형상의 **RGB** 이미지를 참조하는 예시를 들어보자.

$R _{00}$ 값을 가져오려면 `img(0, 0, 0, 0)` 로 참조하면 된다.

$R _{01}$ 값을 가져오려면 `img(1, 0, 0, 0)` 로 참조하면 된다.

$G _{00}$ 값을 가져오려면 `img(0, 0, 0, 1)` 로 참조하면 된다.

$G _{01}$ 값을 가져오려면 `img(1, 0, 0, 1)` 로 참조하면 된다.

$B _{00}$ 값을 가져오려면 `img(0, 0, 0, 2)` 로 참조하면 된다.

!!! example

    $1 \times 5 \times 1 \times 1$ 형상의 이미지 $A$ 를 `CImg` 로 저장한다면 메모리에 선형순서로

    $$ A _{00} A _{10} A _{20} A _{30} A _{40} $$

    와 같이 저장된다. 이때 픽셀값 $A _{00}$ 은 `img(0, 0)`, $A _{10}$ 은 `img(0, 1)`, $\dots$, $A _{40}$ 는 `img(0, 4)` 로 참조할 수 있다.

    $5 \times 1 \times 1 \times 1$ 형상의 이미지 $A$ 를 `CImg` 로 저장한다면 메모리에 선형순서로

    $$ A _{00} A _{01} A _{02} A _{03} A _{04} $$
    
    와 같이 저장된다. 이때 픽셀값 $A _{00}$ 은 `img(0, 0)`, $A _{01}$ 은 `img(1, 0)`, $\dots$, $A _{04}$ 는 `img(4, 0)` 로 참조할 수 있다.

또한 `CImg` 는 축이 `1` 일 경우 그 좌표값을 생략할 수 있도록 해준다. 다음 예시를 보면 $(10, 10)$ 의 $B$ 의 값을 `valB = img(10,10,2)` 로 참조하는 것이 가능하다는 것을 알 수 있다.

!!! example

    ```c++ linenums="1"
    // Construct a 100x100x1x3 (color) image with pixels set to '0'
    CImg<float> img(100,100,1,3,0);
    const float
    // Read red value at coordinates (10,10)
    valR = img(10,10,0,0), 
    // Read green value at coordinates (10,10)
    valG = img(10,10,0,1), 
    // Read blue value at coordinates (10,10) (Z-coordinate can be omitted)
    valB = img(10,10,2),   
    // Compute average pixel value
    avg = (valR + valG + valB)/3; 
    // Replace the color pixel (10,10) by the average grey value
    img(10,10,0) = img(10,10,1) = img(10,10,2) = avg; 
    ```

*References*: 

:   http://cimg.eu/reference/group__cimg__storage.html

## Image Loop

`CImg` 는 픽셀 버퍼 루프, 차원 루프 매크로를 제공한다. 가령 `cimg_forX(img,x)` 은 

```cpp
for (int x = 0; x<img.width(); ++x)
``` 

와 똑같다. `CImg` 의 이 기능은 데이터셋을 `Tensor` 로 변환할 때 편하게 사용될 수 있을 것 같다.

!!! example

    ```c++ linenums="1"
    CImg<unsigned char> img(256,256,1,3);       // Define a 256x256 color image
    cimg_forXYC(img,x,y,c) { img(x,y,c) = (x+y)*(c+1)/6; }
    img.display("Color gradient");
    ```

!!! success

    이로써 데이터셋을 `Tensor` 로 변환하는 코드를 대충 다음과 같이 구상해볼 수 있을것 하다.

    ```c++ linenums="1"
    CImg<unsigned char> lena("lena.png");

    Tensor<float> t; 
    cimg_forXYZC(lena, x, y, z, c) { 
        t[Index3D(Shape, x, y, c)] = static_cast<float>(img(x, y, z, c));
    }
    ```

    그런데 축이 `1` 일 경우 좌표를 생략해도 된다고 했으니까 다음과 같이 더욱 간략하게 짤 수도 있을 것 같다.

    ```c++ linenums="1"
    CImg<unsigned char> lena("lena.png");

    Tensor<float> t; 
    cimg_forXYC(lena, x, y, c) { 
        t[Index3D(Shape, x, y, c)] = static_cast<float>(img(x, y, c));
    }
    ```

*References*: 

:   http://cimg.eu/reference/group__cimg__loops.html

## CImg 테스트

`CImg` 가 충분히 괜찮은 라이브러리인 것 같으니 마지막으로 충분히 검증된 파이썬 패키지를 통하여 이미지를 읽는 것을 비교하면서 `CImg` 가 픽셀을 잘 저장하고 있는지 테스트해도록 하자.

먼저 `CImg` 로 다음과 같은 간단한 프로그램을 만들어본다.

```c++ linenums="1"
#include "CImg.h"
#include <iostream>
using namespace cimg_library;
using namespace std;

int main(int c, char ** v)
{
    CImg<float> lena("lena.png");
    cout << "R of (0, 0): " << lena(0, 0, 0) << endl;
    cout << "R of (0, 1): " << lena(1, 0, 0) << endl;
    cout << "R of (27, 199): " << lena(199, 27, 0) << endl;
    cout << "G of (0, 0): " << lena(0, 0, 1) << endl;
    cout << "G of (381, 501): " << lena(501, 381, 1) << endl;
    cout << "B of (0, 0): " << lena(0, 0, 2) << endl;
    cout << "B of (1, 0): " << lena(0, 1, 2) << endl;
    cout << "B of (144, 9): " << lena(9, 144, 2) << endl;
    return 0;
} 
```

- **9-16**:

    $(0, 0), (0, 1), (27, 199)$ 의 **R** 값과 $(0, 0), (381, 501)$ 의 **G** 값과 $(0, 0), (1, 0), (144, 9)$ 의 **B** 값을 출력해본다.

출력 결과는 다음과 같다. 

```shell
$ g++ main.cpp -O2 -lm -lpthread -lX11
$ ./a.out
R of (0, 0): 226
R of (0, 1): 226
R of (27, 199): 211
G of (0, 0): 137
G of (381, 501): 179
B of (0, 0): 125
B of (1, 0): 125
B of (144, 9): 72
```

이제 **Python** 패키지 `imageio` 를 통하여 똑같은 이미지를 읽고 같은 좌표값을 출력해본다. 다음과 같이 간단한 **Python** 프로그램을 만들고 `test_cimg.py` 로 저장하자.

```python
import imageio
lena = imageio.imread("lena.png")

print("R of (0, 0):", lena[0, 0, 0])
print("R of (0, 1):", lena[0, 1, 0])
print("R of (27, 199):", lena[27, 199, 0])
print("G of (0, 0):", lena[0, 0, 1])
print("G of (381, 501):", lena[381, 501, 1])
print("B of (0, 0):", lena[0, 0, 2])
print("B of (1, 0):", lena[1, 0, 2])
print("B of (144, 9):", lena[144, 9, 2])
```

출력 결과는 다음과 같다.

```shell
$ python test_cimg.py
R of (0, 0): 226
R of (0, 1): 226
R of (27, 199): 211
G of (0, 0): 137
G of (381, 501): 179
B of (0, 0): 125
B of (1, 0): 125
B of (144, 9): 72
```

`CImg` 프로그램의 출력 결과와 완전히 똑같다는 것을 알 수 있다.

---

# Coding: Data &rarr; Tensor

데이터셋을 `Tensor` 로 변환하는 방법은 **(1) 데이터셋을 적절한 방식으로 읽고 (2) 메모리에 저장한 다음 (3) `Tensor` 객체를 데이터셋 형상에 맞춰서 적절히 만들고 (4) 그냥 `Tensor[Index5D(i1, i2, i3, i4, i5)]` 라는 식으로 복사하는 것이었다.**

그런데 이제 우리는 디지털 이미지가 어떤 형태로 컴퓨터에 저장되는지, 그리고 `.png` 파일 포맷은 어떻게 되어 있는지, 그리고 `CImg` 로 `.png` 파일을 읽고 어떻게 픽셀값들을 정확한 좌표로 참조할 수 있는지 알았다.

그러므로 **CasiaWebFace** 의 `.png` 데이터셋을 `Tensor` 로 변환하는 본격적인 코딩을 시작할 수 있다. 

## Convert first `.png` &rarr; `Tensor`

먼저 우리는 지금까지의 분석과 학습을 기반으로 **CasiaWebFace** 데이터셋 중 첫번째 디렉토리의 첫번째 `.png` 파일을 읽어서 `Tensor` 로 변환하는 코드를 다음과 같이 쉽게 만들 수 있게 되었다. 

다음 코드에서는 `.png` 를 `Tensor` 로 변환할 뿐만 아니라 제대로 변환되었는지 확인하기 위하여 `CImg` 객체와 `Tensor` 객체의 메타데이터와 실제데이터를 비교해보았다.

```c++ linenums="1"
#include <iostream>
#include <fstream>
#include <iterator>
#include <vector>
#include <dirent.h>
#include <sys/types.h>
#include "CImg.h"
#include "WICWIU/WICWIU_src/Tensor.hpp"
using namespace cimg_library;

DIR* getDirList(const char * dirName)
{
    DIR *dir;
    if ((dir = opendir(dirName)) == NULL)
        throw std::invalid_argument("could not open directory");
    return dir;
}

void test_convertFirstCasia2Tensor();

int main(int argc, char const *argv[])
{
    test_convertFirstCasia2Tensor();
    return EXIT_SUCCESS;
}

void test_convertFirstCasia2Tensor()
{
    DIR * dir;
    std::string casiaTrainDirPath = "/tmp/casia_train";
    std::string casiaTrainFirstDirPath;
    std::string casiaTrainFirstImagePath;

    //
    // Get first directory of casia_train
    //
    try
    {
        dir = getDirList(casiaTrainDirPath.c_str());
        if (dir == nullptr)
            throw;
        struct dirent *ent;

        for (int idx = 0; (ent = readdir(dir)) != NULL; idx++)
        {
            if (idx == 2) // first index (because: 0 -> . 1 -> .. 2 -> first path)
            {
                casiaTrainFirstDirPath = casiaTrainDirPath + "/" + std::string(ent->d_name);
            }
        }
        closedir(dir);
    } 
    catch (const std::invalid_argument& e)
    {
        std::cerr << e.what() << std::endl;
    }

    //
    // Get first image of first directory
    //
    try
    {
        dir = getDirList(casiaTrainFirstDirPath.c_str());
        if (dir == nullptr)
            throw;
        struct dirent *ent;

        for (int idx = 0; (ent = readdir(dir)) != NULL; idx++)
        {
            if (idx == 2) // first index (because: 0 -> . 1 -> .. 2 -> first path)
            {
                casiaTrainFirstImagePath = casiaTrainFirstDirPath + "/" +std::string(ent->d_name);
            }
        }
        closedir(dir);
    } 
    catch (const std::invalid_argument& e)
    {
        std::cerr << e.what() << std::endl;
    }

    //
    // Data to Tensor
    //
    CImg<unsigned char> casiaTrainFirstImage(casiaTrainFirstImagePath.c_str());

    Tensor<unsigned char> * casiaTrainFirstTensor =\ 
        new Tensor<unsigned char>(
                casiaTrainFirstImage.width(),
                casiaTrainFirstImage.height(),
                casiaTrainFirstImage.spectrum()
                );
    Shape * casiaTrainFirstTensorShape = casiaTrainFirstTensor->GetShape();

    cimg_forXYC(casiaTrainFirstImage, x, y, c) { 
        (*casiaTrainFirstTensor)[Index3D(casiaTrainFirstTensorShape, x, y, c)] =\
            casiaTrainFirstImage(x, y, c);
    }

    std::cout << "##########################################" << std::endl;
    std::cout << "######### Meta data comparison ###########" << std::endl;
    std::cout << "##########################################" << std::endl;
    std::cout << std::endl;

    std::cout << "CImg width: " << casiaTrainFirstImage.width() << std::endl;
    std::cout << "CImg height: " << casiaTrainFirstImage.height() << std::endl;
    std::cout << "CImg depth: " << casiaTrainFirstImage.depth() << std::endl;
    std::cout << "CImg channels: " << casiaTrainFirstImage.spectrum() << std::endl;
    std::cout << "CImg size: " << casiaTrainFirstImage.size() << std::endl;
    std::cout << "CImg pixel type: " << casiaTrainFirstImage.pixel_type() << std::endl;
    casiaTrainFirstImage.print();
    std::cout << std::endl;

    std::cout << "Tensor shape: " << casiaTrainFirstTensorShape << std::endl;
    std::cout << "Tensor rank: " << casiaTrainFirstTensor->GetRank() << std::endl;
    std::cout << "Tensor dim(0): " << casiaTrainFirstTensor->GetDim(0) << std::endl;
    std::cout << "Tensor dim(1): " << casiaTrainFirstTensor->GetDim(1) << std::endl;
    std::cout << "Tensor dim(2): " << casiaTrainFirstTensor->GetDim(2) << std::endl;
    std::cout << "Tensor Capacity: " << casiaTrainFirstTensor->GetCapacity() << std::endl;
    std::cout << std::endl;

    std::cout << "##########################################" << std::endl;
    std::cout << "######### Real data comparison ###########" << std::endl;
    std::cout << "##########################################" << std::endl;
    std::cout << std::endl;

    std::cout << "CImg R of (0, 0): " << static_cast<unsigned int>(casiaTrainFirstImage(0, 0, 0)) << std::endl;
    std::cout << "CImg R of (0, 1): " << static_cast<unsigned int>(casiaTrainFirstImage(1, 0, 0)) << std::endl;
    std::cout << "CImg R of (27, 159): " << static_cast<unsigned int>(casiaTrainFirstImage(159, 27, 0)) << std::endl;
    std::cout << "CImg G of (0, 0): " << static_cast<unsigned int>(casiaTrainFirstImage(0, 0, 1)) << std::endl;
    std::cout << "CImg G of (31, 51): " << static_cast<unsigned int>(casiaTrainFirstImage(51, 31, 1)) << std::endl;
    std::cout << "CImg B of (0, 0): " << static_cast<unsigned int>(casiaTrainFirstImage(0, 0, 2)) << std::endl;
    std::cout << "CImg B of (1, 0): " << static_cast<unsigned int>(casiaTrainFirstImage(0, 1, 2)) << std::endl;
    std::cout << "CImg B of (144, 9): " << static_cast<unsigned int>(casiaTrainFirstImage(9, 144, 2)) << std::endl;
    std::cout << std::endl;

    std::cout << "Tensor R of (0, 0): " << static_cast<unsigned int>((*casiaTrainFirstTensor)[Index3D(casiaTrainFirstTensorShape, 0, 0, 0)]) << std::endl;
    std::cout << "Tensor R of (0, 1): " << static_cast<unsigned int>((*casiaTrainFirstTensor)[Index3D(casiaTrainFirstTensorShape, 1, 0, 0)]) << std::endl;
    std::cout << "Tensor R of (27, 159): " << static_cast<unsigned int>((*casiaTrainFirstTensor)[Index3D(casiaTrainFirstTensorShape, 159, 27, 0)]) << std::endl;
    std::cout << "Tensor G of (0, 0): " << static_cast<unsigned int>((*casiaTrainFirstTensor)[Index3D(casiaTrainFirstTensorShape, 0, 0, 1)]) << std::endl;
    std::cout << "Tensor G of (31, 51): " << static_cast<unsigned int>((*casiaTrainFirstTensor)[Index3D(casiaTrainFirstTensorShape, 51, 31, 1)]) << std::endl;
    std::cout << "Tensor B of (0, 0): " << static_cast<unsigned int>((*casiaTrainFirstTensor)[Index3D(casiaTrainFirstTensorShape, 0, 0, 2)]) << std::endl;
    std::cout << "Tensor B of (1, 0): " << static_cast<unsigned int>((*casiaTrainFirstTensor)[Index3D(casiaTrainFirstTensorShape, 0, 1, 2)]) << std::endl;
    std::cout << "Tensor B of (144, 9): " << static_cast<unsigned int>((*casiaTrainFirstTensor)[Index3D(casiaTrainFirstTensorShape, 9, 144, 2)]) << std::endl;
    std::cout << std::endl;
}
```

- **11-17**, **29-80**:

    이 코드들은 [Step 1. Data &rarr; Tensor: Analysis&#58; LFW 1](step1-1.md) 에서 이미 설명하였다.

- **85**:

    **CasiaWebFace** 의 첫번째 디렉토리의 첫번째 `.png` 데이터를 `CImg` 로 읽는다.

- **87-92**:

    `.png` 의 $x$축, $y$축, $c$축 정보를 통하여 `Tensor` 를 생성한다.

- **95-98**:

    데이터를 `Tensor` 에 복사한다.

- **100-120**:

    `CImg` 객체와 `Tensor` 의 메타데이터를 비교한다.

- **122-145**:

    `CImg` 객체와 `Tensor` 의 실제데이터를 비교한다.

출력 결과는 다음과 같다.

![cmd_URpMlwXPfv](https://user-images.githubusercontent.com/16812446/89713770-dea6f800-d9d4-11ea-9c6a-a98689eb340e.png)

!!! success

    이로써 우리는 성공적으로 **CasiaWebFace** 의 첫번째 디렉토리의 첫번째 `.png` 데이터를 `Tensor` 로 변환하였다.