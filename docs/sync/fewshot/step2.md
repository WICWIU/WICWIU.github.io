
!!! info

    이곳은 **fewshot-learning** 을 구현하기 위한 [**WICWIU**](https://github.com/WICWIU/WICWIU) 개발팀의 [**WICWIU**](https://github.com/WICWIU/WICWIU) 분석/코딩 과정을 기록해두는 곳입니다.

[**WICWIU** 의 학습 과정은](../../dev/wicwiu/learn.md) 다음과 같이 이루어졌다는 것을 이미 알아보았습니다.

- [Step 1. 학습 데이터 &rarr; `Tensor`](step1-1.md)

- Step 2. 순전파

- [Step 3. 역전파](step3.md)

- [Step 4. Weight 파라미터 갱신](step4.md)

여기는 fewshot 팀이 "**Step 2. 순전파**" 을 구현하기 위한 과정을 기록해두는 곳입니다.

---

순전파를 구현하기 위해서는 **FaceNet** 에 대한 이해가 필요하므로 **FaceNet** 논문을 읽어야 한다. 하지만 실제 논문을 읽기 전에 얼굴 인식에 대한 배경지식이 부족하여 [**Keras** 로 **FaceNet** 을 구현하여 얼굴 인식 시스템을 개발하는 블로그 글](https://machinelearningmastery.com/how-to-develop-a-face-recognition-system-using-facenet-in-keras-and-an-svm-classifier/) 을 참고하면서 **FaceNet** 을 이해하기 위한 사전작업을 해보았다.

# FaceNet 사전지식

**FaceNet** 은 2015년 구글에서 개발한 얼굴 인식 시스템이다. **FaceNet** 은 얼굴 인식 시스템의 학습에 사용되는 얼굴에서 특징을 고퀼리티로 추출(face enbedding) 할 수 있다.

## 얼굴 인식

얼굴 인식의 일반적인 기능은 사진 또는 영상으로부터 자동으로 얼굴을 확인(인식)하는 것과 얼굴을 검증하는 것이다.

- 얼굴 검증 : 알려진 신원에 얼굴을 one-to-one 매핑하는 것 (*이 사람이 이 사람인가?*)

- 얼굴 확인(인식) : 알려진 얼굴들로부터 얼굴을 one-to-many 매핑하는 것 (*이 사람이 누구인가?*)

## **FaceNet** 모델

**FaceNet** 은 2015년 구글의 연구원 [Florian Schroff](http://www.florian-schroff.de/) 의 논문 [FaceNet: A Unified Embedding for Face Recognition and Clustering](https://arxiv.org/abs/1503.03832) 에서 발표된 얼굴 인식 시스템이다.

**FaceNet** 은 얼굴 사진이 주어지면 얼굴로부터 고퀼리티의 특징들(features)을 추출하여 이 특징을 표현하는 128 개 원소를 갖는 벡터(face embedding)를 만들어낸다. 기술적으로는 각 원소가 `1` 바이트를 가지므로 각 벡터가 단지 `128` 바이트만으로 얼굴을 표현하는 것이다. **FaceNet** 은 이 face embedding 을 통하여 얼굴 사진을 거리가 얼굴 유사도를 뜻하는 유클리드 공간에 매핑시킨다.

**FaceNet** 은 triplet loss 를 통한 deep CNN 이다. 이 모델로 비슷한 신원의 벡터(얼굴)는 가깝게, 다른 신원의 벡터(얼굴)는 멀게 만든다.

이 모델의 학습은 모델의 중간층으로부터 그것을 추출하는 것이 아니라 embedding 을 직접적을 만드는 것에 초점을 두었는데 이것은 중요한 혁명이다. **FaceNet** 은 이전의 접근에서 했던 것과 달리 중간의 좁은 층이 아니라 embedding 그 자체를 최적화시키는 것을 직접적으로 학습한다.

이 얼굴 embedding 은 표준 얼굴 인식 벤치마킹에서 최고의 성과를 내게 하는 분류 시스템을 학습시키는 기반이 된다.

**FaceNet** 의 논문은 얼굴에서 추출한 같은 얼굴의 특징을 군집화하는 등 embedding 을 다른 용도로 사용하는 방식도 보여주었다.

## **FaceNet** 구현 오픈소스

**FaceNet** 모델을 제공하는 프로젝트는 많이 있다. 

- 가장 유명한 프로젝트는 [OpenFace](https://cmusatyalab.github.io/openface/) 인데 이 프로젝트는 **PyTorch** 를 사용하여 **FaceNet** 모델을 제공한다. 

- 또 유명한 프로젝트는 [FaceNet by David Sandberg](https://github.com/davidsandberg/facenet) 인데 David 는 **TensorFlow** 를 사용하여 **FaceNet** 모델을 제공한다.

- 주목할만한 **Keras** **FaceNet** 예제는 [FaceNet by Hiroki Taniai](https://github.com/nyoki-mtl/keras-facenet) 이다. 

    그는 Inception ResNet v1 을 **TensorFlow** 에서 **Keras** 로 변환해주는 스크립트를 제공했다. 또 이미 학습된 **Keras** 모델을 제공했다.

    !!! info
    
        이 모델은 [MS-Celeb-1M 데이터셋](https://www.microsoft.com/en-us/research/project/ms-celeb-1m-challenge-recognizing-one-million-celebrities-real-world/) 에서 사전학습되었다. 그리고 입력된 이미지를 컬러가 되게 하고 3개의 채널에 대한 표준화된 화이트닝 된 픽셀 값을 갖게 하고 $160 \times 160$ 형상을 갖게 한다.

        [여기](https://drive.google.com/drive/folders/1pwQ3H4aJ8a6yyJHZkTwtjcL4wYWQb7bn) 에서 미리 학습된 **Keras** **FaceNet** 모델을 다운로드할 수 있다. 

## 얼굴 인식을 위한 얼굴 탐색

실질적으로 얼굴 인식을 하기 전에 먼저 얼굴을 탐색해야만 한다. 얼굴 탐색이란 사진에서 얼굴을 자동으로 찾고 얼굴에 네모 박스를 그려서 나머지 영역들은 제외하는 작업이다.

얼굴 탐색을 위하여 MTCNN(Multi-Task Cascaded Convolutional Neural Network) 을 사용하면 좋다. 이것으로 사진에서 얼굴만 추출해낼 수 있다. MTCNN 은 2016년 논문 [Joint Face Detection and Alignment Using Multitask Cascaded Convolutional Networks](https://arxiv.org/abs/1604.02878) 에서 설명된 얼굴 탐색에 사용되는 최적의 기법이다.

### MTCNN 구현 오픈소스

- Iván de Paz Centeno 이 구현한 [ipazc/mtcnn](https://github.com/ipazc/mtcnn) 이 있는데 이것은 `pip` 로도 설치할 수 있다. 

    ```shell
    $ sudo pip install mtcnn
    ```

*참고 및 출처*: 

:   [FaceNet: A Unified Embedding for Face Recognition and Clustering](https://arxiv.org/abs/1503.03832)

---

# **FaceNet** 논문을 위한 사전 수학지식

!!! info 

    이 글은 [저의 블로그 글](https://ccss17.github.io)을 재작성 한 것입니다.

## 노름거리, 벡터 사이의 거리

!!! tldr ""

    $L_1$ 노름(norm) 거리, 맨해튼 거리 : 벡터 $a$ 에 대한 $L_1$ 노름은 

    $$ ||a|| _{1} = |a_1| + |a_2| + |a_3| + \dots + |a_n| = \sum_{i=1}^{n}|a_i| $$

    이다. 

*Example:*

벡터 $a = (4, 3)$ 에 대한 $L_1$ 노름은 오른쪽으로 $4$, 위로 $3$ 움직인 것과 같이 $7$ 이다.

이처럼 $L_1$ 노름은 

![](https://upload.wikimedia.org/wikipedia/commons/thumb/0/08/Manhattan_distance.svg/300px-Manhattan_distance.svg.png){: class=center }

와 같이 바둑판 모양으로 움직인 최단거리를 뜻한다. 

!!! tldr ""

    $L_2$ 노름(norm) 거리, 유클리드 거리, 벡터의 크기, **Squared Distance** : 벡터 $a$ 에 대한 $L_2$ 노름은

    $$ ||a|| _{2} = \sqrt[]{\sum_{i=1}^{n}a _ i ^{2}} = \sqrt[]{a_1 ^{2}+a_2 ^{2}+\dots+a_n ^{2}} $$

    이다.

- $L_2$ 노름은 벡터의 크기의 정의와 동일하다고 볼 수 있다.

- $L_2$ 노름은 유클리드 기하학에서 사용하던 거리와 똑같고 우리가 일상생활에서 직관적으로 생각하던 거리와 동일하다.

- $L_1, L_2$ 노름은 선형회귀 모델의 정규화 항에서 사용된다.

- **논문에서 나오는 수식**

    $\|f(x)\|_{2} = 1$ 은 벡터 $f(x)$ 의 크기 또는 $L_2$ 노름 거리가 $1$ 이라는 것이다.

    $\|f(x_i ^{a}) - f(x_i ^{p})\| ^{2} _{2}$ 는 벡터 $f(x_i ^{a})$ 와 벡터 $f(x_i ^{p})$ 의 거리($L_2$ 거리) 의 제곱이다.

## 폐구간

!!! tldr ""

    구간(interval) : 두 실수 $a, b(a<b)$ 에 대하여 실수의 집합 

    $$\{x \in \mathbb{R} |a \leq x \leq b\}, \{x \in \mathbb{R} |a < x < b\}, \{x \in \mathbb{R}  | a \leq x < b\}, \{x \in \mathbb{R} |a<x \leq b\}$$

    를 구간이라 한다. 

기호로 각각 $[a,b], (a,b), [a,b), (a,b]$ 라 한다.

구간은 수로 그 특성 혹은 존재자체를 수로 추상화할 수 있는 자연의 모든 대상의 집합을 마찬가지로 수로 격하될 수 있는 자연의 어떤 대상을 기준으로 하여 나눈 것에 대응된다.

!!! tldr ""

    폐구간(닫힌구간, closed interval) : $[a,b]$ 를 닫힌구간이라 한다. 

즉, $[a,b] = \{x \in \mathbb{R} | a \leq x \leq b\}$ 이다. 

!!! tldr ""

    개구간(열린구간, open interval) : $(a,b)$ 를 열린구간이라 한다. 

즉, $(a,b) = \{x \in \mathbb{R} | a < x < b\}$ 이다. 

특히 실수 전체의 집합도 하나의 구간으로 보고 개구간 $(-\infty, \infty)$ 로 나타낸다. 

!!! tip

    $\infty$ 는 특정한 값이 아니고 "한없이 커지고 있는 상태" 를 나타내기 때문에 $[-\infty, \infty]$ 나 $(a, \infty]$ 와 같이 $\infty$ 를 폐구간과 엮지 않는다. 실수 $x$ 의 값이 한없이 커질 수는 있어도 $x = \infty$ 라는 값을 가질 수는 없기 때문이다. 

*참고 및 출처*: 

:   [저의 블로그 ㅎㅎ - 벡터](https://ccss17.github.io/vector.html)

:   [저의 블로그 ㅎㅎ - 해석](https://ccss17.github.io/anal.html)

---

# 《FaceNet: A Unified Embedding for Face Recognition and Clustering》 

## Abstract

우리는 이 논문에서 얼굴 유사도가 거리인 유클리드 공간으로 얼굴 사진을 사상(mapping)시키는 것을 학습하는 **FaceNet** 이라는 시스템을 소개한다. 얼굴 유사도가 거리인 유클리드 공간이 생성되면 얼굴 검증(*같은 사람인가?*), 얼굴 확인(*누구인가?*), 얼굴 군집화(*비슷한 얼굴을 모으는 것*)가 매우 쉬워진다.

얼굴 유사도가 거리인 유클리드 공간을 만들기 위하여 우리는 deep convolutional network 를 사용하여 중간층을 최적화(학습)하는 것이 아닌 embedding(얼굴에서 추출한 벡터) 자체를 직접적으로 최적화(학습)시켰다. 학습을 위하여 triplets 을 사용하였다. 이로써 우리는 얼굴 하나당 단지 `128` 바이트를 사용하여 현존하는 얼굴 인식 시스템 중 최고의 성능을 이끌어낼 수 있었다.

우리는 또한 harmonic embeddings 과 harmonic triplet loss 라는 또 다른 얼굴 embedding 방법을 소개한다. 이 두 embedding 방식을 서로 비교할 수 있을 것이다.

## 1. Introduction

우리는 deep convolutional network 를 사용하여 이미지의 유클리드 embedding 을 학습한다. 이 network 는 얼굴 유사도가 곧 $L_2$ 거리인 embedding 공간을 학습하게 된다. 

![image](https://user-images.githubusercontent.com/16812446/89868084-2f913900-dbed-11ea-87f1-1d5a9e2db615.png){: class=center }

:   *Figure 1. Illumination and Pose invariance ([FaceNet: A Unified Embedding for Face Recognition and Clustering](https://arxiv.org/abs/1503.03832))*

위 그림을 보자. 수평으로는 같은 인물의 사진이고 수직으로는 다른 인물의 사진이다. 또한 수평으로는 다른 포즈를 취하고 있다. 이때 두 사진간의 유사도, 즉 거리가 $1.04, 1.22, 1.33, \dots$ 등으로 계산된 것이다. 이 값은 폐구간 $[0, 4]$ 에 존재한다. 값이 $0.0$ 이면 얼굴이 똑같음을 의미하고 값이 $4.0$ 이면 얼굴이 다르다는 것을 의미한다.

이 공간이 생성되면 얼굴 인식 시스템의 고전적인 문제들의 해결이 매우 쉬워진다. 얼굴 검증은 단지 두 embedding 간의 thresholding 을 정하는 것으로 귀결되고, 얼굴 확인은 k-NN 분류기의 문제가 되며, 얼굴 군집화는 k-mean 나 agglomerative clustering 같은 off-the-shelf 기술로 해결할 수 있다. 

!!! example

    *Figure 1.* 에서 thresholding 을 $1.1$ 로 두면 모든 얼굴 사진을 잘 분류할 수 있다는 것을 알 수 있다.

### FaceNet 과 지금까지의 얼굴 인식 접근법과의 차이

지금까지의 얼굴 인식 접근법은 신경망의 중간층을 학습시키는 것이었다. 이 방식의 단점은 간접적이고, 비효율적이라는 것이다. 이 방식에서는 중간층이 새로운 얼굴도 잘 인식하길 바라는 것이었다. 그리고 얼굴을 나타내는 사이즈가 거의 `1000` 차원 정도로 매우 컸다. 

반면 **FaceNet** 은 [LMNN](http://john.blitzer.com/papers/nips05.pdf) 을 기반으로 한 triplet-based 손실함수를 통하여 얼굴을 `128` 차원으로 embedding 시킨다. triplet 은 매칭되는 얼굴 썸네일과 매칭되지 않는 얼굴 썸네일, 그리고 positive pair 를 distance margin 으로 negative 로부터 분리시키기 위한 손실값으로 구성된다. 썸네일이란 얼굴 영역만 잘려진 사진을 뜻한다. 이 썸네일은 스케일 조정과 이미지 변환이 이루어질 수도 있다.

### triplets 선정

어떤 triplets 을 사용할지 선정하는 것은 좋은 성능을 위하여 매우 중요하다.

1. [Curriculum learning [1]](https://www.icml.cc/Conferences/2009/papers/119.pdf) 에서 착안하여 우리는 triplets 의 어려움을 지속적으로 증가시키는 online negative exemplar mining 전략을 소개한다. 

2. 또한 군집화의 정확도를 향상시키기 위하여 한 사람의 embeddings 을 위한 구형 군집화를 이루는 hard-positive mining 기술을 소개한다.

이로써 *Figure 1.* 같은 사진들도 잘 분류할 수 있다. 솔직히 이걸 분류하는 건 겁나 어려운 문제인데 우리는 해냈다.

### 논문 구조 설명

먼저 **2.** 에서 이 연구분야와 관련된 논문들을 리뷰해본다. **3.1** 에서는 triplet loss 를 정의해본다. **3.2** 에서는 우리의 triplet 선정 방식과 모델 학습 절차를 설명한다. **3.3** 에서 모델 구조 사용에 대하여 설명한다. **4.** 과 **5.** 에서 우리의 방식으로 embedding 을 한 것을 정량적 방식으로 결과를 내보고, 정성적 방식으로 군집화 결과를 확인해본다. 

## 2. Related Work

[Deeply  learned  facerepresentations  are  sparse,  selective,  and  robust [15]](https://arxiv.org/pdf/1412.1265) 와 [Deepface:Closing the gap to human-level performance in face verifica-tion [17]](https://www.cs.toronto.edu/~ranzato/publications/taigman_cvpr14.pdf) 와 비슷하게 우리도 순수하게 data driven method 를 사용하여 얼굴 사진의 픽셀로부터 그것을 특정할 수 있는 특징을 학습했다. 우리는 라벨링 된 얼굴 데이터셋에서 가변하는 포즈, 명도, 다양한 조건에서 불변하는 특성을 얻었다.

본 논문에서는 컴퓨터 비전에서 좋은 성과를 내고 있는 두 가지 신경망 구조를 설명한다. 두 개 다 deep convolutional network 이다. 하나는 [Backpropagation Applied to Handwritten Zip Code Recognition [8]](http://yann.lecun.com/exdb/publis/pdf/lecun-89e.pdf) 이고 하나는 [Learningrepresentations by back-propagating errors [11]](https://www.iro.umontreal.ca/~vincentp/ift3395/lectures/backprop_old.pdf) 이다.

첫번째 모델은 여러개의 interleaved convolutional layers 와 non-linear activations 과 local response normalizations 와 max pooling layers 로 구성된 [Visualizing and understandingconvolutional networks [22]](https://cs.nyu.edu/~fergus/papers/zeilerECCV2014.pdf) 의 모델을 기반으로 한다. 우리는 [Network in network [9]](https://arxiv.org/abs/1312.4400) 에서 착안하여 몇몇의 $1 \times 1 \times d$ convolutional layers 를 추가했다.

두번째 모델은 ImageNet 2014 에서 사용된 Szegedy 의 Inception model [Going deeper with convolutions [16]](https://arxiv.org/pdf/1409.4842) 을 기반으로 한다. 이 모델은 병행해서 실행되는 몇 개의 다른 convolutional 과 pooling layers 가 섞인 layers 를 사용하고 그것들의 출력을 합친다. 

## 3. Method

![image](https://user-images.githubusercontent.com/16812446/89889612-e224c400-dc0c-11ea-8770-bb603c6a7436.png){: class=center }

:   *Figure 2. 모델 구조. FaceNet 은 배치 입력층, deep CNN 층, embedding 을 출력하는 $L_2$ normalization 층, triplet loss 층으로 구성된다. ([FaceNet: A Unified Embedding for Face Recognition and Clustering](https://arxiv.org/abs/1503.03832))*

![image](https://user-images.githubusercontent.com/16812446/89889512-af7acb80-dc0c-11ea-8604-6b274ebe621e.png){: class=center }

:   *Figure 3. Triplet Loss 는 같은 신원을 가진 anchor 와 positive 의 거리를 최소화 시키고 다른 신원을 가진 anchor 와 negative 의 거리를 최대화 시킨다. ([FaceNet: A Unified Embedding for Face Recognition and Clustering](https://arxiv.org/abs/1503.03832))*

**FaceNet** 은 deep convolutional network 를 사용한다. 우리는 두 가지 핵심 구조를 논의할 것이다. 첫째는 [Visualizing and understandingconvolutional networks [22]](https://cs.nyu.edu/~fergus/papers/zeilerECCV2014.pdf) 스타일의 networks 이고 둘째는 [Going deeper with convolutions [16]](https://arxiv.org/pdf/1409.4842) 의 Inception 타입의 networks 이다. 이 networks 의 자세한 설명은 **3.3** 에서 한다. 이 모델을 *Figure 2.* 에서 회색박스로 표현했다. 

*Figure 2.* 에서 볼 수 있듯 가장 중요한 것은 어떻게 얼굴 사진을 그 특징을 대표할 수 있는 벡터로 변환하느냐이다. 즉, 우리는 ^^조건(포즈, 명도 등)에 관계없이 모든 얼굴 사진에 대하여 같은 신원의 $L_2$ 거리는 작고 다른 신원의 $L_2$ 거리는 먼 feature space $\mathbb{R}^{d}$ 에 얼굴 사진 $x$ 을 사상시키는 함수 $f(x)$^^ 을 찾아볼 것이다.

여기에서 다른 손실 함수와 triplet 을 비교해보지는 않겠지만, 그래도 triplet 이 얼굴 검증에는 가장 좋을 것이다. [Deep  learning  facerepresentation  by  joint  identification-verification [14]](https://arxiv.org/pdf/1406.4773) 에서의 손실함수는 한 신원의 모든 얼굴을 embedding 공간의 한 점에 사영시킨다. 이것은 억지로 다른 신원과의 거리과 차별성을 강화하면서도 한 신원의 얼굴들이 manifold 에 있게 한다.

### 3.1. Triplet Loss

embedding 은 이미지 $x$ 와 유클리드 공간 $\mathbb{R}$ 에 대하여 $f: x \to \mathbb{R}^{d}$ 으로 표현된다. 또한 우리는 이 embedding 을 $\|f(x)\| _{2} = 1$ 같은 $d$ 차원 초구(hypersphere) 로 제한시킨다.

이 loss 는 [Distance metric learning for large margin nearest neighbor classification [19]](https://jmlr.csail.mit.edu/papers/volume10/weinberger09a/weinberger09a.pdf) 에서 nearest-neighbor classification 에서 착안했다.

이제 우리가 원하는 것은 어떤 사람 사진 $x _{i}^{a}$(*anchor*) 와 같은 사람 사진 $x _{i}^{p}$(*positive*) 의 거리가 다른 사람 사진 $x _{i}^{n}$(*negative*) 과의 거리보다 가까운 것이다. 이것을 *Figure 3.* 이 보여준다. 다시말해 우리가 원하는 것은 positive 와 negative 사이의 여백 $\alpha \in \R$ 와 집합의 크기 $N$ 을 갖는 모든 positive 와 negative 의 triplets 집합 $\mathcal{T}$ 에 대하여 

$$ \forall (f(x_i ^{a}), f(x_i ^{p}), f(x_i ^{n})) \in \mathcal{T} \tag{1}$$ 

$$ \|f(x _{i} ^{a}) - f(x _{i}^{p})\| ^{2} _{2} + \alpha < \| f(x _ i ^{a}) - f(x _i ^{n})\| ^{2}_{2} \tag{2}$$

이다.

!!! note

    모든 triplets ($\forall (f(x_i ^{a}), f(x_i ^{p}), f(x_i ^{n})) \in \mathcal{T}$) 에 대하여 anchor 와 positive 의 거리($\|f(x _{i} ^{a}) - f(x _{i}^{p})\| ^{2} _{2}$)가 anchor 와 negative 의 거리($\| f(x _ i ^{a}) - f(x _i ^{n})\| ^{2}_{2}$)보다 작아야 한다는 것이다.

그렇다면 우리는 anchor 와 positive 와의 거리가 멀면 loss 가 상승하도록, 그리고 anchor 와 negative 와의 거리가 멀면 loss 가 감소하도록 loss 함수를 다음과 같이 정의할 수 있다.

$$ \sum_{i}^{N} \bigg [\|f(x _{i} ^{a}) - f(x _{i}^{p})\| ^{2} _{2} - \| f(x _ i ^{a}) - f(x _i ^{n})\| ^{2}_{2} + \alpha \bigg ] \tag{3}  $$

가능한 모든 triplets 을 생성하는 것은 (2) 같은 조건을 쉽게 충족시킨다. 그런데 이런 triplets 은 느리게 수렴한다. 그러므로 hard triplets 을 선택하는 것은 모델의 성능을 높이는 것에 있어 중요하다. triplet 선택하는 것에 관하여 다음 섹션에서 논의해보자.

### 3.2. Triplet Selection

빠르게 수렴시키기 위하여 $\text{argmax} _{x_i ^{p}}\| f(x_i ^{a} ) - f(x _i ^{p})\| ^{2}_{2}$ 즉, hard positive $x_i ^{p}$ 와 $\text{argmin} _{x_i ^{n}}\| f(x_i ^{a}) - f(x _i ^{n})\| ^{2}_{2}$ 즉, hard negative $x_i ^{n}$ 를 선택하는 것이 중요하다.

그런데 모든 학습 데이터에 대하여 $\text{argmin} , \text{argmax}$ 를 계산하는 것은 어렵다. 또한 라벨링이 잘못된 데이터와 형편없는 이미지가 hard positive 와 hard negative 가 될 수도 있다. 이 문제를 해결하기 위하여 다음 두 선택을 할 수 있다. 

- 가장 최신 network checkpoint 를 사용하며 데이터의 부분집합에 대하여 $\text{argmin} , \text{argmax}$ 를 계산하며 매 $n$ 단계마다 triplets offline 을 생성한다.

- mini-batch 에서 hard positive/negative 를 택함으로써 triplets online 을 생성한다. 

여기에서는 triplets online 생성을 해보고 몇 천개의 exemplars 순서의 mini-batch 속에서만 $\text{argmin} ,\text{argmax}$ 를 계산해본다.

anchor 와 positive 의 거리가 유의미하게 계산되기 위하여 각 mini-batch 마다 최소한의 exemplars 가 존재해야 한다. 우리는 실험하면서 mini-batch 당 신원 당 약 40 개 정도의 얼굴이 선택되도록 훈련 데이터를 추출했다. 또 여기에 무작위로 추출된 negative 얼굴들이 각 mini-batch 에 들어가도록 했다.

hardest positive 를 뽑는 것 대신 우리는 mini-batch 속에서 hard negative 를 선택하면서 all anchor-positive 짝을 사용했다. hard anchor-positive 짝과 all anchor-positive 를 나란히 비교해보지는 않았지만 우리는 실험하면서 all anchor-positive 가 좀 더 안정적이고 학습 시 약간 더 빠르게 수렴하는 것을 발견했다. 

또 우리는 triplets 의 offline 생성을 online 생성과 연결시키는 실험도 했는데 이렇게 하면 batch 크기를 좀 더 줄일 수 있는 것 같았다. 하지만 이것은 아직 확실하지 않다.

hardest negatives 를 택한다면 훈련 초기에 로컬 최소값들이 잘못될 수 있고, 특히 $f(x) = 0$ 이 되는 등 모델이 붕괴될 수도 있다. 이것을 완화하기 위하여 

$$ \|f(x_i ^{a}) - f(x_i ^{p})\|^{2}_{2}<\|f(x_i ^{a})- f(x_i ^{n})\| ^{2}_2 \tag{4} $$

인 $x_i ^{n}$ 을 택할 수 있다. 우리는 이런 negative exemplars 를 semi-hard 라고 불렀다. 이 negatives 들은 여백 $\alpha$ 내부에 있다.

다시 말하지만 빠른 수렴이 중요하기 때문에 SGD 의 성능을 높이기 위하여 우리는 한편으로는 mini-batch 사이즈를 작게 했다.(The general inefficiencyof batch training for gradient descent learning. [20]) 다른 한편으로 구현 details 는 수십에서 수백의 exemplars 의 배치를 더욱 효율적으로 만들었다. 그러나 배치 사이즈에 대한 주된 제약은 mini-batch 에서 어떻게 hard triplets 을 택하느냐 이다. 대부분의 실험에서 우리는 1800 여개 exemplar 의 배치 사이즈를 사용했다.

### 3.3. Deep Convolutional Networks

우리는 모든 실험에서 SGD ([Backpropagation Applied to Handwritten Zip Code Recognition [8]](http://yann.lecun.com/exdb/publis/pdf/lecun-89e.pdf), [Learningrepresentations by back-propagating errors [11]](https://www.iro.umontreal.ca/~vincentp/ift3395/lectures/backprop_old.pdf)) 와 AdaGrad ([Adaptive  subgradientmethods for online learning and stochastic optimization [5]](http://jmlr.org/papers/volume12/duchi11a/duchi11a.pdf)) 를 사용하는 역전파와 CNN 으로 학습을 진행했다.

대부분의 실험에서 우리는 여백을 $\alpha = 0.2$ 로 설정하고, 학습률을 $0.05$ 로 설정하여 학습을 시작했고 모델을 완성하기 위하여 학습률을 낮췄다. 처음에는 [Going deeper with convolutions [16]](https://arxiv.org/pdf/1409.4842) 과 비슷하게 모델을 무작위로 초기화시켰고 CPU cluster 에서 1000~2000 시간동안 학습시켰다. 500시간이 지나자 손실 감소와 정확도 상승이 상당히 느려졌다. 하지만 학습을 더 진행시키자 성능이 눈에 띄게 상승했다.

우리는 두 종류의 구조를 사용하며 그 둘의 trade-off 를 조사했다. 이들의 실질적인 차이는 파라미터와 FLOPS 의 차이에 있다. 최적의 모델은 용도에 따라 달라질 것이다. 가령 데이터센터에서는 많은 파라미터를 둘 수 있고 많은 수의 FLOPS 가 필요할 것이지만, 핸드폰에서 돌릴라면 적은 파라미터가 있어야 메모리 소모를 줄일 수 있다.

!!! note

    trade-off 는 두 상반된 대상에 대하여 하나를 취하면 다른 하나를 잃고, 다른 하나를 취하면 또 다른 것을 잃게 되는 상황을 표현하는 말이다.

우리의 모든 모델은 rectified linear units(ReLU) 를 비선형 활성화함수로 사용한다.

![image](https://user-images.githubusercontent.com/16812446/90309438-9e3f0100-df23-11ea-93f5-01c3b54299c4.png){: class=center }

:   *Table 1. NN1. 이 표는 [Network in network [9]](https://arxiv.org/abs/1312.4400) 에서 착안한 $1 \times 1$ convolutions 을 기반의 [Visualizing and understandingconvolutional networks [22]](https://cs.nyu.edu/~fergus/papers/zeilerECCV2014.pdf) 의 구조를 보여준다. kernel 은 $\text{rows} \times \text{cols}, \text{stride}$ 이고 maxout [Maxout networks [6]](https://arxiv.org/pdf/1302.4389) pooling size 는  $p=2$ 이다. ([FaceNet: A Unified Embedding for Face Recognition and Clustering](https://arxiv.org/abs/1503.03832))*

첫번째 모델은 Table 1. 에서 볼 수 있듯 [Visualizing and understandingconvolutional networks [22]](https://cs.nyu.edu/~fergus/papers/zeilerECCV2014.pdf) 구조 사이에 [Network in network [9]](https://arxiv.org/abs/1312.4400) 에서 제안된 것처럼 $1 \times 1 \times d$ convolutional 층을 더한다. 그러면 $22$ 층 네트워크가 된다. 이 네트워크는 $140M$ 파라미터를 갖고 이미지 당 $1.6B$ FLOPS 을 필요로 한다.

![image](https://user-images.githubusercontent.com/16812446/90309534-5a003080-df24-11ea-8214-e442d35f2acf.png)

:   *Table 2. NN2. NN2 Inception 구현의 세부사항이다. 이 모델은 [Going deeper with convolutions [16]](https://arxiv.org/pdf/1409.4842) 에서 설명한 것과 거의 똑같다. 두 가지 주요 차이점은 max pooling 대신 $L_2$ pooling 을 사용한다는 것이다. pooling 은 최종 평균 pooling 을 제외하고 항상 $3 \times 3$ 이고 각 Inception 모듈의 convolutional 모듈과 parallel 이다. ([FaceNet: A Unified Embedding for Face Recognition and Clustering](https://arxiv.org/abs/1503.03832))*

두번째 모델은 [Going deeper with convolutions [16]](https://arxiv.org/pdf/1409.4842) 스타일의 Inception 모델을 사용한다. 이 모델은 $20$ 배 적은 파라미터(6.6M-7.5M)를 사용하고 $5$배 적은 FLOPS(500M-1.6B) 를 갖는다. 이 모델은 필터 depth 와 개수를 대폭 줄여서 핸드폰에서도 구동될 수 있다. NNS1 은 26M 개의 파라미터를 갖고 이미지 당 220M FLOPS 만 필요하다. NNS2 는 4.3M 개의 파라미터와 20M FLOPS 를 갖는다. Table2. 의 NN2 는 우리의 가장 큰 네트워크의 세부사항이다. 

![image](https://user-images.githubusercontent.com/16812446/90309670-705abc00-df25-11ea-84d5-b2bef2e1f672.png){: class=center }

:   *Table 3. Network Architectures. 이 표는 hold out test set 에서 우리의 모델 구조의 성능을 비교한다. Reported is the mean validation rate VAL at 10E-3 false accept rate. Also shown is the standard error of the mean across the five test splits. ([FaceNet: A Unified Embedding for Face Recognition and Clustering](https://arxiv.org/abs/1503.03832))*

!!! note

    Training Datasets과 Test Datasets를 분리하여 분석하는 절차를 holdout 기법이라고 알려져 있다. (출처: https://mlearn.tistory.com/entry/Holdout [Machine Learning])

NN3 는 구조상 똑같지만 입력 사이즈를 $160 \times 160$ 으로 줄였다. NN4 는 단지 $96 \times 96$ 의 입력 사이즈를 가지며 이로써 CPU 부하를 확 줄일 수 있다. NN2 는 1.6B FLOPS 인데 NN4 는 단지 285M FLOPS 가 필요하다. 또한 입력 사이즈를 줄이면 higher layers 에서 $5 \times 5$ convolutional 를 사용하지 않는다. 우리는 일반적으로 $5 \times 5$ convolutional 을 약간의 정확도 손실을 감수하면서 전체적으로 없앨 수 있다는 것을 발견했다.

![image](https://user-images.githubusercontent.com/16812446/90310498-d0089580-df2c-11ea-8eb4-95f7b7e31fd7.png){: class=center }

:   *Figure 4. FLOPS 와 정확도의 trade-off 를 다양한 사이즈와 구조로 구성된 다른 모델들을 광범위하게 비교해본 것이다. 빨갛게 표시한 것이 본 논문에서 주로 다루고 있는 모델들이다. ([FaceNet: A Unified Embedding for Face Recognition and Clustering](https://arxiv.org/abs/1503.03832))*

Figure 4. 에서 우리의 모든 모델의 성능을 비교한 결과를 볼 수 있다.

## 4. Datasets and Evaluation

우리는 네가지 데이터셋에 대하여 우리의 방법을 평가해보았다. 주어진 두 얼굴 사진의 embedding 벡터 $x_i,x_j$ 의 거리 $D(x_i, x_j)$ 를 구하여 서로 같은지 다른지 분류해봤다. 이때 모든 같은 신원의 얼굴 순서쌍 $(i, j)$ 의 모음을 $\mathcal{P} _{\text{same}}$ 라고 하고 모든 다른 신원의 얼굴 순서쌍의 모음을 $\mathcal{P} _{\text{diff}}$ 라고 하자.

그러면 threshold $d$ 에 대하여 같다고 분류되었고 올바르게 분류된 얼굴 순서쌍 모음 *true accepts* 를

$$ \text{TA}(d) = \{(i, j) \in \mathcal{P} _{\text{same}} | D(x_i, x_j) \leq d\} \tag{5} $$

라고 할 수 있고, 같다고 분류되었지만 올바르게 분류되지 않은 얼굴 순서쌍 모음 *false accept* 를 

$$ \text{FA}(d) = \{(i, j) \in \mathcal{P} _{\text{diff}} | D(x_i, x_j) \leq d\} \tag{6} $$

라고 할 수 있다. 그렇다면 검증성공률 *validation rate* $\text{VAL}(d)$ 과 *false accept rate* $\text{FAR}(d)$ 를 주어진 얼굴 거리 $d$ 에 대하여 

$$ \text{VAL}(d) = \dfrac{|\text{TA}(d)|}{|\mathcal{P} _{\text{same}}|}, \text{FAR}(d) = \dfrac{|\text{FA(d)|}}{|\mathcal{P} _{\text{diff}}|} \tag{7} $$

로 정의할 수 있다.

!!! note

    집합 $A$ 에 대한 집합의 크기, 즉 원소 개수(cardinality) 를 $|A|$ 로 표기한다.

### 4.1. Hold-out Test Set

우리는 우리의 훈련 데이터셋과 같은 분포를 가지는 백만개 정도의 사진으로 구성된 hold out 데이터셋을 사용했다. 평가를 위해 이미지를 20만개 정도의 서로소 집합으로 분할하여 5개의 작은 집합을 만들었다. VAL 와 FAR 는 $100k \times 100k$ 의 이미지 짝에 대하여 계산된다.

### 4.2. Personal Photos

12k 개의 사람 사진으로 구성된 테스트 셋이다. FAR, VAR rate 를 $12k \times 12k$ 개의 이미지 짝에 대하여 전부 계산할 거다.

### 4.3. Academic Datasets

Labled Faces in the Wild(LFW) 와 Youtube Faces DB 도 테스트 데이터셋으로 사용할거야.

## 5. Experiments

우리는 8백만개의 서로 다른 신원의 1억에서 2억개의 훈련 데이터셋을 사용했어. 먼저 face detector 로 각 얼굴 사진은 얼굴에 맞게 crop 시켜서 썸네일을 생성했어. 이 썸네일을 $96 \times 96$ 에서 $224 \times 224$ 의 크기로 각 모델의 입력에 맞게 크기 조정을 해서 실험했지.

### 5.1. Computation Accuracy Trade-off

실험에 대하여 자세하게 설명하기 전에 정확도와 FLOPS 의 trade-off 를 좀 설명해줄께. Figure 4 은 4.2 섹션에서 말한 우리의 테스트 데이터 셋에 대한 x축이 FLOPS 를 나타내고 y축이 정확도 $0.001$ 에 대한 FAR 를 나타내지. Figure 4 에 나타나는 정확도와 각 모델이 요구하는 FLOPS 의 상관관계가 되게 흥미롭지 않아? 우리는 빨갛게 표시한 NN1, NN2, NN3, NNS1, NNS2 만 집중적으로 다뤄볼거야.

우리는 모델의 파라미터 개수에 대한 trade-off 도 조사해봤어. 근데 이건 좀 모호한 부분이 있지. 가령 Inception 모델을 기반으로 한 NN2 는 NN1 와 비교할만한 성능을 내지만 단지 20 분의 1 의 파라미터를 갖고 있어. 물론 파라미터 개수를 줄이면 성능이 떨어지지. 

### 5.2. Effect of CNN Model

![image](https://user-images.githubusercontent.com/16812446/90312984-968f5480-df43-11ea-96a8-56dcebb91da0.png){: class=center }

:   *Figure 5. 네트워크 구조들 성능 비교. 이 그래프는 4.2 섹션에서 말한 테스트셋에 대한 4 가지 모델의 ROC 커브를 보여주고 있어. 커브가 FAR 의 $10E-4$ 지점에서 떨어지지. 이건 groundtruth 라벨에 있는 노이즈로 설명될 수 있어. 모델 성능은 가장 높은 순대로 다음과 같단다. NN2($224 \times 224$ 입력의 Inception 모델), NN1($1 \times 1$ convolutional), NNS1(단지 220M FLOPS 를 갖는 small Inception 모델), NNS2(20M FLOPS 만을 갖는 tiny Inception 모델) ([FaceNet: A Unified Embedding for Face Recognition and Clustering](https://arxiv.org/abs/1503.03832))*

!!! note

    Ground-truth는 기상학에서 유래된 용어로 어느한 장소에서 수집된 정보를 의미합니다. Ground-truth는 보통 '지상 실측 정보'로 해석되며 인공위성과 같이 지구에서 멀리 떨어져서 지구를 관찰하였을 때 지구의 전체적인 관점을 보는 것에는 넓은 시야를 가질 수 있지만 실제 지면의 구조를 세밀하게 보는 것은 빛이 구름이나 대기를 통과하게 되면서 실제 모습이 왜곡되어 제대로 파악하는 것은 어렵습니다. 이러한 상황에세 지상 정보를 직접 측정한다면 보다 정확한 정보를 얻을 수 있는 것입니다. 이러한 정보에 인공위성에서 관측된 데이터를 참조하여 사용한다면 좀 더 정확한 데이터를 얻을 수 있습니다.

    기계학습의 관점에서 보았을때 Ground-truth는 학습하고자 하는 데이터의 원본 혹은 실제 값을 표현할때 사용됩니다. 이미지에서 객체 감지를 목표로 하는 알고리즘의 예를 봅시다.(출처 : https://eair.tistory.com/16)

이제 우리가 Figure 5. 에서 보여준 4 가지 모델들의 성능에 대하여 자세히 말해줄께. 한 종류는 [Visualizing and understandingconvolutional networks [22]](https://cs.nyu.edu/~fergus/papers/zeilerECCV2014.pdf) 와 [Network in network [9]](https://arxiv.org/abs/1312.4400) 를 기반으로 만든 $1 \times 1$ convolutional 모델이고, 다른 한 종류는 모델 사이즈를 획기적으로 줄인 [Going deeper with convolutions [16]](https://arxiv.org/pdf/1409.4842) 의 Inception 모델이었지. 솔직히 전반적으로 우리 모델의 성능은 다 좋아. 근데 NN3 같은 경우 FLOPS 과 모델 사이즈를 획기적으로 줄이면서도 성능은 여전히 괜찮았어.

4.2 섹션에서 말한 테스트셋에 대한 자세한 평가를 Figure 5 가 보여주고 있어. 가장 큰 모델이 tiny NNS2 에 비하여 좋은 성능을 내고 있지만, 솔직히 NNS2 는 이미지 하나를 30ms 로 처리하면서 꽤 나쁘지 않은 성능을 내고 있어. 이미지당 30ms 로 처리하는 건 핸드폰에서도 돌릴 수 있는 성능이야.

### 5.3. Sensitivity to Image Quality

![image](https://user-images.githubusercontent.com/16812446/90313262-1ae2d700-df46-11ea-95f6-16ad4c204bc6.png){: class=center }
:   *Table 4. 왼쪽 표는 검증성공률 VAR 의 10E-3 정확도가 JPEG 의 다양한 퀼리티에 따라 어떻게 달라지는지 보여주고 있어. 오른쪽 표도 VAR 의 10E-3 정확도가 이미지 사이즈에 대하여 어떻게 달라지는지 보여주지. 이 실험은 test hold-out 데이터셋의 첫번째 분할 데이터에 대하여 NN1 에서 수행됐어.  ([FaceNet: A Unified Embedding for Face Recognition and Clustering](https://arxiv.org/abs/1503.03832))*

Table 4 는 다양한 이미지 사이즈에 대해서도 우리의 모델이 겁나 견고하다는 것을 보여주지. 심지어 JPEG 압축률이 20 까지 떨어질 때까지 정확도가 나쁘지 않다는 결론이 나왔어. $120 \times 120$ 썸네일 뿐만 아니라 $80 \times 80$ 사이즈 썸네일까지도 괜찮은 성능이 나오더라. 이 결과는 솔직히 주목할만해. 왜냐면 모델이 $220 \times 220$ 이미지에서 훈련되었으니까. 더 낮은 해상도에 대한 얼굴 사진에 대하여 학습하는 것은 나중에라도 모델을 향상시킬 수 있지.

### 5.4. Embedding Dimensionality

![image](https://user-images.githubusercontent.com/16812446/90313454-bc1e5d00-df47-11ea-9bfa-51244c66f317.png){: class=centersmall }
:   *Table 5. Embedding Dimensionality 에 따른 VAL 비교. 이 표는 4.1 섹션에서 말한 hold-out 데이터셋을 NN1 으로 학습시키면서 다양한 embedding 차원에 대한 성능을 비교한거야. VAL 의 정확도는 10E-3 이지. 우리는 5개 분할 데이터에 대한 평균 standard error 를 계산해봤어. ([FaceNet: A Unified Embedding for Face Recognition and Clustering](https://arxiv.org/abs/1503.03832))*

우리는 다양한 embedding 차원을 조사하고 128 을 선택했어. 더 큰 embedding 을 선택하면 작은 것보다 좋지 않을까 라고 생각하는 친구들이 있을 수도 있지만, 더 큰 걸 사용하면 똑같은 정확도를 내기 위하여 더 큰 학습 비용이 발생해. 다시 말해서 Table 5 가 보여주는 성능 차이는 통계적으로 무의미하다는 거야.

내가 강조하고 싶은 건 훈련 중에는 128 차원의 float 벡터가 사용되지만, 정확도의 손실 없이 이걸 128 바이트로 양자화할 수 있어.

!!! note

    양자화(quantization)라는 것은 샘플링(sampling)한 아날로그 형태로 되어 있는 신호나 정보를 디지털화(digitizing) 하는 작업을 말한다. 좀 더 쉽게 말하자면 다음과 같다. 0 ~ 1사이의 숫자를 생각해보자. 아날로그 방식으로 생각해보면 0과 1사이엔 실제로 무수히 많은 숫자가 존재한다. 0.1, 0.2479, 0.78001, 0.99999 등 우리가 셀 수 없이 많은 수가 무한대로 존재하는 것이다. 현실적으로 이 무수한 숫자들을 다루기란 불가능하기 때문에 우리는 일정한 범위와 기준값을 정해놓고 어떤 임의의 숫자가 나오면 그와 가장 근접한 기준값으로 만들어 사용하는 것이다. 

    예를 들어 우리가 어떤 전압 값을 측정한다고 해보자. 이때 우리는 0.1V 단위로만 값을 측정한다고 가정해보자. 실제의 전압 값이 3.4122319800200922212935482...V 라고 했을 때 과연 이 값을 전부 표현하는것이 옳은가? 저렇게까지 정밀하게 측정하기도 어려울 뿐더러 그럴 만한 정밀한 센서도 없고 그럴 필요성도 없다. 이럴때 우리는 뒤에 필요 없는 정보는 생각하지 않고 측정된 값을 가장 근접한 값으로 만들어 사용하는 것이다. 실제로 센서에 의해 측정된 아날로그 전압값이 3.41223V라고 했을 때 0.1V 단위로 측정한다고 했으니 이때의 양자화 시킨 Voltage값은 3.4V일 것이다. 

    결국 양자화라는 것은 무한대로 이루어진 셀 수 없는 아날로그 정보를 셀 수 있을 만큼의 간격으로 만들어서 유의미한 정보만을 사용하는 것이다. 이 말은 샘플링에도 유효할 것이다. (출처 : https://twlab.tistory.com/19)

그러므로 모든 얼굴이 큰 스케일의 군집화와 얼굴 인식에 아주 이상적인 128 차원 바이트 벡터로 표현될 수 있는 거지. 이것보다 더 작은 embedding 을 사용하면 무시할 수 있는 정확도 손실과 함께 핸드폰 같은 임베디드 기기에서 사용할 수 있는거지.

### 5.5. Amount of Training Data

![image](https://user-images.githubusercontent.com/16812446/90313721-ac077d00-df49-11ea-9155-ad104fb03c53.png)
:   *Table 6. 훈련 데이터 사이즈에 따른 검증성공률 VAL. 이 표는 $96 \times 96$ 이미지를 작은 모델에 입력하면서 700 시간 동안 학습한 결과를 비교해주고 있어. 이 모델은 NN2 와 비슷하지만 Inception 모델의 $5 \times 5$ convolutional 은 없어. ([FaceNet: A Unified Embedding for Face Recognition and Clustering](https://arxiv.org/abs/1503.03832))*

Table 6 은 훈련 데이터 양에 따른 정확도를 비교해주지. 솔직히 우리에게 시간이 무한한게 아니니까 작은 모델에서 테스트해봤어. 결국 천만개 이상의 데이터셋을 사용해야 정확도를 확 끌어낼 수 있다는 걸 알 수 있지.

### 5.6. Performance on LFW

LFW 데이터셋에서도 우리의 모델을 평가해봤는데 훈련 데이터셋을 9개로 분할하여 $L_2$ 거리의 threshold 를 정해봤어. 분류(*같은지 틀린지*) 에서는 테스트 데이터셋을 10개로 분할하여 시행해봤지. 결국 최적의 threshold 는 $1.242$ 였어. 다만 8번째 분할 데이터만 threshold 가 $1.256$ 이었어.

우리는 다음 두 가지 모두로 실험을 진행했지.

1. Center crop 된 LFW 썸네일.

2. LFW 썸네일에 face detector 를 사용했어.

![image](https://user-images.githubusercontent.com/16812446/90313877-de65aa00-df4a-11ea-9fcd-12efac6f7a4c.png)
:   *Figure 6. LFW 에러들. 특히 False reject 에서는 단지 8 개만 잘못 분류된 거였고 5 개는 라벨링이 잘못된 사진이었어. ([FaceNet: A Unified Embedding for Face Recognition and Clustering](https://arxiv.org/abs/1503.03832))*

Figure 6 은 ^^모든^^ 실패 케이스를 보여주지. 우리는 center crop 된 썸네일에 대하여 $98.87\% \pm 0.15$ 의 분류 정확도를 얻었어. 이건 DeepFace 나 예전에 state-of-the-art 였던 DeepId2+ 보다 더 좋은 성능이지. 이건 NN1 의 성능이야. 하지만 NN3 도 통계적으로 비교가 무의미한 성능을 내더라.

### 5.7. Performance on Youtube Faces DB

우리는 유튜브 비디오의 첫 100 프레임에 대한 얼굴 유사도 평균을 사용했는데, 분류 정확도가 $95.12\% \pm 0.39$ 가 나왔어. 처음 100 프레임을 사용했을 때 $95.18\%$ 가 나왔지. [Deepface:Closing the gap to human-level performance in face verifica-tion [17]](https://www.cs.toronto.edu/~ranzato/publications/taigman_cvpr14.pdf) 에서는 정확도가 $91.4\%$ 였고 DeepId2+ 의 정확도는 $93.2\%$ 였어.

### 5.8. Face Clustering

![image](https://user-images.githubusercontent.com/16812446/90314061-de19de80-df4b-11ea-9061-119a5ebc277b.png)
:   *Figure 7. Face Clustering ([FaceNet: A Unified Embedding for Face Recognition and Clustering](https://arxiv.org/abs/1503.03832))*

우리가 만든 모델이 낳은 embedding 을 같은 신원의 얼굴 사진을 묶는데에도 쓸 수 있어. 진짜 굉장하지? 나이, 포즈, 명암, 폐색이 달라져도 응집 군집화를 잘 해내는 걸 알 수 있어.

## 6. Summary

우리는 얼굴 사진을 거리가 유사도인 유클리드 공간에 embedding 하는 방법을 소개했어. 이건 지금까지와의 방법과 다르게 중간 layers 를 학습하는 것에 집중하는 것이 아니라 embedding 자체를 직접적으로 학습시키는 거라 혁신적이지. 

*참고 및 출처*: 

:   [FaceNet: A Unified Embedding for Face Recognition and Clustering](https://arxiv.org/abs/1503.03832)