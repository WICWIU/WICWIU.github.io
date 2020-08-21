
[**LaTex**](https://en.wikibooks.org/wiki/LaTeX/Mathematics) 에 익숙하신 분이라면 [**WICWIU**](https://github.com/WICWIU/WICWIU/) 나 수식이 들어가는 내용들을 설명하기 위하여 다음과 같이 수식을 작성할 수 있습니다.

## 인라인 수식

문장에 수식을 포함시키려면 $$ 안에 수식을 쓰면 됩니다.

!!! example 

    === "Result"

        직각 삼각형의 두 직각변 $a , b$ 를 각각 한 변으로 하는 정사각형 면적의 합은 빗변 $c$ 를 한 변으로 하는 정사각형의 면적과 같다. 이를 피타고라스 정리 $a ^{2} + b ^{2} = c ^{2}$ 라고 한다.
    
    === "Example"

        직각 삼각형의 두 직각변 \$a , b\$ 를 각각 한 변으로 하는 정사각형 면적의 합은 빗변 \$c\$ 를 한 변으로 하는 정사각형의 면적과 같다. 이를 피타고라스 정리 \$a ^{2} + b ^{2} = c ^{2}\$ 라고 한다.

## 디스플레이 수식

수식을 강조하기 위하여 디스플레이 스타일로 수식을 쓰려면 $$ $$ 안에 쓰면 됩니다.

!!! example 

    === "Result"

        $m \times n$ 행렬 $\mathbf{A}$, $n \times p$ 행렬 $\mathbf{B}$ 에 대한 행렬곱은 $m \times p$ 행렬 

        $$ \mathbf{AB} = \begin{pmatrix} \sum_{k=1}^{n}A _{1k}B _{k1}& \sum_{k=1}^{n}A _{1k}B _{k2}& \dots& \sum_{k=1}^{n}A _{1k}B _{kp} \\ \sum_{k=1}^{n}A _{2k}B _{k1}& \sum_{k=1}^{n}A _{2k}B _{k2}& \dots& \sum_{k=1}^{n}A _{2k}B _{kp} \\ \vdots& \vdots& \ddots& \vdots \\ \sum_{k=1}^{n}A _{mk}B _{k1}& \sum_{k=1}^{n}A _{mk}B _{k2}& \dots& \sum_{k=1}^{n}A _{mk}B _{kp} \\ \end{pmatrix} $$

        이다.

    === "Example"

        \$m \times n\$ 행렬 \$\mathbf{A}\$, \$n \times p\$ 행렬 \$\mathbf{B}\$ 에 대한 행렬곱은 \$m \times p\$ 행렬 

        \$\$ \mathbf{AB} = \begin{pmatrix} \sum_{k=1}^{n}A _{1k}B _{k1}& \sum_{k=1}^{n}A _{1k}B _{k2}& \dots& \sum_{k=1}^{n}A _{1k}B _{kp} \\ \sum_{k=1}^{n}A _{2k}B _{k1}& \sum_{k=1}^{n}A _{2k}B _{k2}& \dots& \sum_{k=1}^{n}A _{2k}B _{kp} \\ \vdots& \vdots& \ddots& \vdots \\ \sum_{k=1}^{n}A _{mk}B _{k1}& \sum_{k=1}^{n}A _{mk}B _{k2}& \dots& \sum_{k=1}^{n}A _{mk}B _{kp} \\ \end{pmatrix} \$\$

        이다.

---

!!! info

    아래의 퍼셉트론 수렴정리와 증명과정을 [:octicons-file-code-24: Source](https://github.com/WICWIU/WICWIU.github.io/blob/master/docs/write_doc/math-sample-perceptron-convergence.md) 에서와 같이 **Markdown** 과 **LaTex** 로 작성할 수 있습니다.

# 예시 1: 퍼셉트론 수렴정리

!!! note ""

    **^^퍼셉트론 수렴 정리(perceptron convergence theorem)^^ : 퍼셉트론이 선형 분리 가능 문제들, 즉 직선으로 분류가 가능한 데이터를 유한번의 학습으로 데이터를 잘 분류하는 가중치를 수렴시킬 수 있다 는 정리이다.**

<h5> <i> 아래의 내용은 <a href="https://nbviewer.jupyter.org/github/metamath1/ml-simple-works/blob/master/perceptron/perceptron.ipynb"> metamath1/ml-simple-works/perceptron/perceptron.ipynb</a> 의 내용을 다시 정리한 것입니다. </i> </h5>

## 엄밀한 정리

"$\|\mathbf{w} ^{*}\|=1$ 인 계수벡터 $\mathbf{w} ^{*}$ 와 $\forall k \in \{1, \dots, n\}$ 에 대한 $k$ 번째 입력벡터 $\mathbf{x}_{k}$ 와 $k$ 번째 출력값을 나타내는 번째 스칼라 $y_k \in \{-1,1\}$ 에 대하여 

$y_k(\mathbf{x}_{k}\cdot \mathbf{w}^{*}) \geq \gamma > 0$ 을 만족하는 $\gamma \in \mathbb{R}$ 가 존재하고, 

$\|\mathbf{x}_{k}\| \leq R$ 인 $R \in \mathbb{R}$ 이 존재하면,

퍼셉트론 알고리즘의 에러 $y_k \neq \hat{y}_{k} = \mathbf{x}_{k}\cdot \mathbf{w}^{*}$ 에 대한 에러 발생횟수 $\epsilon$ 은 $\epsilon \leq \dfrac{R ^{2}}{\gamma ^{2}}$ 이다."

## 정리의 이해

먼저 여기에서는 활성화함수로 양극성 계단함수 $\displaystyle y = h(x) = \begin{cases} 1 &x > 0\\ -1 &x \leq 0\\ \end{cases}$ 를 사용한다. 퍼셉트론 수렴 정리를 한줄씩 풀어서 이해해보자.

![](https://nbviewer.jupyter.org/github/metamath1/ml-simple-works/blob/master/perceptron/fig1.png)

위 그림에서 $\mathbf{w} ^{*}$ 에 의해 결정되는 직선의 방정식은 붉은선이다. 이 붉은선이 모든 데이터를 올바르게 선형 분리하는 선이라고 하자. 그 직선의 방정식을 편향을 나타내기 위한 $w_0 = b, x_0 = 1$ 와 자유변수 $x_1, x_2$ 에 대한 $\mathbf{x} = \big <1, x_1, x_2\big >$ 와 가중치 벡터 $\mathbf{w} ^{*}=\big <b, w_1, w_2\big >$ 에 대하여 $x_1x_2$ 평면 위의 직선의 방정식

$$ \mathbf{w} ^{*}\cdot \mathbf{x} = b + w_1x_1 + w_2x_2 = 0 $$

으로 나타낼 수 있다. 

퍼셉트론 알고리즘은 이와같은 가중치 벡터 $\mathbf{w} ^{*}$ 를 찾는 것이다. 만약 가중치 벡터 $\mathbf{w} ^{*}$ 를 찾았다면 이것을 사용하여 $k$번째 입력벡터 $\mathbf{x}_{k}$ 에 대하여 $\mathbf{w} ^{*}\cdot \mathbf{x} _{k} > 0$ 이면 빨간 직선의 위쪽, $\mathbf{w} ^{*}\cdot \mathbf{x} _{k} < 0$ 이면 빨간 직선의 아래쪽에 위치한다고 판별할 수 있다.

그렇다면 가중치 벡터 $\mathbf{w}^{*}$ 가 존재하여 $y_k(\mathbf{w}^{*}\cdot \mathbf{x}_{k}) = y_k \hat{y_k} \geq \gamma > 0$ 인 $\gamma$ 가 있다는 것은 $\mathbf{w}^{*}$ 가 데이터를 $2$개의 클래스로 잘 선형분리 하고 있다는 것이다.

왜냐하면 $y_k$ 가 직선의 방정식 위쪽에 위치했다면 $y_k > 0$ 인데 예측값 $\hat{y_k} = \mathbf{w}^{*}\cdot \mathbf{x}_k$ 도 데이터가 위쪽에 있다고 판별했으면 $\hat{y_k} = \mathbf{w}^{*}\cdot \mathbf{x}_k > 0$ 이 되어 $y_k \hat{y_k} > 0$ 이 되고,

$y_k$ 가 직선의 방정식 아래쪽에 위치했다면 $y_k < 0$ 인데 예측값 $\hat{y_k} = \mathbf{w}^{*}\cdot \mathbf{x}_k$ 도 데이터가 아래쪽에 있다고 판별했으면 $\hat{y_k} = \mathbf{w}^{*}\cdot \mathbf{x}_k < 0$ 이 되어 $y_k \hat{y_k} > 0$ 이 되기 때문에

$\mathbf{w}^{*}$ 가 데이터를 잘 선형분리하고 있다면 항상 $y_k \hat{y_k} > 0$ 가 될 것이기 때문이다.

이때 결과값, 즉 $\mathbf{w}^{*}\cdot \mathbf{x}_{k}$ 이 $0$ 에 가까울수록 직선의 방정식과의 거리가 가까워진다. 그러므로 $\gamma$ 는 아무리 커봤자 직선의 방정식과 거리가 가장 가까운 입력 벡터 $\mathbf{x}_{k}$ 와 직선의 방정식과의 거리이다. 

또한 입력 벡터 $\mathbf{x}_{k}$ 의 크기, 즉 $\|\mathbf{x}_{k}\|$ 와 같거나 큰 $R \in \mathbb{R}$ 을 정할 수 있다.

이 조건이 충족되면 퍼셉트론 알고리즘이 최대 $\dfrac{R ^{2}}{\gamma ^{2}}$ 번만에 데이터를 잘 선형분리하는 계수 벡터 $\mathbf{w}^{*}$ 를 찾을 수 있다. 

## 증명

$y_k(\mathbf{x}_{k}\cdot \mathbf{w}^{*}) \geq \gamma > 0$ 을 만족하는 $\gamma \in \mathbb{R}$ 가 존재하므로 데이터를 잘 선형분리시킬 수 있는 $\mathbf{w}^{*}$ 가 존재한다. 퍼셉트론 알고리즘은 가중치 학습을 반복하면서 $\mathbf{w} ^{*}$ 을 찾으려할텐데 이때 $t$번째 학습에서의 가중치 $\mathbf{w} ^{(t)}$ 가 $\mathbf{w}^{*}$ 와 평행하다면 두 벡터의 사이각이 $0$ 가 되어 $\mathbf{w}^{*}\cdot \mathbf{w} ^{(t)}$ 값이 최대가 된다.

그러므로 매 학습마다 두 벡터 $\mathbf{w}^{*}, \mathbf{w} ^{(t)}$ 의 내적값이 증가함을 보이면 가중치가 수렴하고 있음을 보인느 것이 된다. 이때 두 벡터의 각도가 줄어듦으로써 내적값이 증가하는 것이 아니라 $\mathbf{w} ^{(t)}$ 의 값 자체가 커져서 내적값이 증가할 수도 있기 때문에 $\mathbf{w} ^{(t)}$ 의 크기 상한값도 함께 보여야 가중치가 수렴한다고 말할 수 있다.

만약 $t$번째 반복에서 $k$번째 입력 벡터 $\mathbf{x}_{k}$ 와 그것의 클래스 레이블 $y _{k}$ 에 대하여 $y _{k}(\mathbf{w} ^{(t-1)} \cdot \mathbf{x}_{k}) < 0$ 이 되었다면 에러가 발생한 것이므로 가중치를 조정해야 한다. 퍼셉트론 알고리즘에서 가중치 조정은 

$$ \mathbf{w} ^{(t)} := \mathbf{w}^{(t-1)} + \eta (y _{k} - \hat{y} _{k})\mathbf{x}_{k} $$

와 같이 이루어졌다는 것을 기억하자. 이때 $\eta = 1$ 로 두고 퍼셉트론의 예측값 $\hat{y}_{k}$ 는 편의를 위하여 제거하자. 그렇게 해도 양이 어느정도 바뀔 뿐 부호는 바뀌지 않으므로 가중치 조정이 가능하기 때문이다. 그러면 가중치 조정은

$$ \mathbf{w}^{(t)} := \mathbf{w}^{(t-1)} + y _{k}\mathbf{x}_{k} $$

와 같이 이루어진다. 그러면 이때 두 벡터 $\mathbf{w}^{*}, \mathbf{w}^{(t)}$ 의 내적값을 계산해보면

$$ \mathbf{w}^{*}\cdot \mathbf{w}^{(t)} = \mathbf{w}^{*}\cdot (\mathbf{w}^{(t-1)}+y _{k} \mathbf{x}_{k}) $$

$$ = \mathbf{w}^{*}\cdot \mathbf{w}^{(t-1)}+y _{k} \mathbf{w}^{*}\cdot \mathbf{x}_{k} $$

$$ \geq  \mathbf{w}^{*}\cdot \mathbf{w}^{(t-1)} + \gamma \ \ (\because y_k(\mathbf{x}_{k}\cdot \mathbf{w}^{*}) \geq \gamma ) $$

이다. 즉, $\mathbf{w}^{*}\cdot \mathbf{w}^{(t)}- \mathbf{w}^{*}\cdot \mathbf{w}^{(t-1)} \geq  \gamma > 0$ 이므로 $\mathbf{w}^{*}\cdot \mathbf{w}^{(t)}$ 이 $\mathbf{w}^{*}\cdot \mathbf{w}^{(t-1)}$ 보다 $\gamma$ 이상 크다는 것이다. 이로써 매 학습마다 $\mathbf{w}^{*}\cdot \mathbf{w}^{(t)}$ 가 증가한다는 것이, 즉 가중치 벡터가 수렴되고 있다는 것이 증명되었다. ▲ 

매 학습마다 내적 $\mathbf{w}^{*}\cdot \mathbf{w}^{(t)}$ 의 값이 최소 $\gamma$ 이상 증가하기 때문에 초기 가중치 벡터를 $\|\mathbf{w}^{0}\| = 0$ 로 두면 $t$번 학습 후 내적값이 

$$ \mathbf{w}^{*}\cdot \mathbf{w}^{(t)} \geq t \gamma $$

이라는 것 즉, 최소 $t \gamma$ 이상이라는 것을 알 수 있다. 그런데 코시-슈바르츠 부등식에 의해 

$$ \|\mathbf{w}^{*}\|\|\mathbf{w}^{(t)}\| \geq \mathbf{w}^{*}\cdot \mathbf{w}^{(t)} $$ 

인데 $\|\mathbf{w}^{*}\| = 1$ 이므로 $\|\mathbf{w}^{(t)}\| \geq \mathbf{w}^{*}\cdot \mathbf{w}^{(t)} \geq t \gamma$ 에서 

$$\|\mathbf{w}^{(t)}\| \geq t \gamma$$

이다. 그러므로 $t \gamma$ 가 $\|\mathbf{w}^{(t)}\|$ 의 최대하계, 즉 하한

$$ \therefore \inf \| \mathbf{w}^{(t)}\| = t \gamma $$

이 된다. ▲ 

$t$번째 학습 이후 가중치 벡터 $\mathbf{w}^{(t)}$ 의 길이의 제곱은 

$$ \|\mathbf{w}^{(t)}\|^{2} = \|\mathbf{w}^{(t-1)} + y _{k} \mathbf{x} _{k}\|^{2} $$

$$ = \|\mathbf{w}^{(t-1)}\| ^{2} + y _{k} ^{2} \|\mathbf{x} _{k}\| ^{2} + 2y _{k} \mathbf{w}^{(t-1)}\cdot  \mathbf{x} _{k} $$

인데 $y _{k} \in \{-1,1\}$ 이므로 $y _{k}^{2} = 1$ 이고 $\|x _{k}\| \leq R \implies \|x _{k}\|^{2}\leq R ^{2}$ 이고 예측값이 출력값과 틀렸으므로 $y _{k} \mathbf{w}^{(t-1)} \cdot \mathbf{x} = y _{k}\hat{y}_{k} < 0$ 이다. 따라서

$$ \|\mathbf{w}^{(t)}\| ^{2} \leq  \|\mathbf{w}^{(t-1)}\| ^{2} + R ^{2} $$

이다. 즉, $\|\mathbf{w}^{(t)}\| ^{2} - \|\mathbf{w}^{(t-1)}\| ^{2} \leq  R ^{2}$ 이므로 초기 가중치 벡터를 $\| \mathbf{w}^{0}\| = 0$ 로 두면 $t$ 번은 학습은 다음과 같이 이루어진다.

$$ \|\mathbf{w}^{1}\|^{2} - \|\mathbf{w}^{0}\| ^{2} \leq R ^{2} $$

$$ \|\mathbf{w}^{2}\|^{2} - \|\mathbf{w}^{1}\| ^{2} \leq R ^{2} $$

$$ \dots $$

$$ \|\mathbf{w}^{(t)}\|^{2} - \|\mathbf{w}^{(t-1)}\| ^{2} \leq R ^{2} $$

이것들을 모두 더하면

$$ \|\mathbf{w}^{(t)}\|^{2} \leq t R ^{2} $$

을 얻는다. 이것이 $\|\mathbf{w}\|^{2}$ 의 최소상계, 즉 상한

$$ \sup \|\mathbf{w}^{(t)}\| ^{2} = t R ^{2} $$

이다. ▲ 

상한과 하한을 같은 부등식에 쓰면 다음과 같다. 

$$ (\inf \|\mathbf{w}^{(t)}\|) ^{2} \leq \|\mathbf{w}^{(t)}\|^{2} \leq \sup \|\mathbf{w}^{(t)}\|^{2} $$

$$ \iff t ^{2} \gamma ^{2} \leq \|\mathbf{w}^{(t)}\|^{2} \leq t R ^{2} $$

여기에서 

$$ \iff t \leq \dfrac{R ^{2}}{\gamma ^{2}} $$

를 얻는다. 이것은 곧 학습 횟수 $t$ 가 상계를 갖는다는 것이므로 

결과적으로 선형분리 가능 데이터에 조건 $\eta =1, \|\mathbf{w}^{0}\| = 0$ 을 적용하면 퍼셉트론 알고리즘이 유한번의 학습으로, 즉 최대 $\dfrac{R ^{2}}{\gamma ^{2}}$ 번의 학습으로(에러로) 가중치 매개변수 $\mathbf{w}$ 를 최적의 가중치 벡터 $\mathbf{w}^{*}$ 로 수렴시킬 수 있다는 것이다. ■   

!!! note

    이는 분류해야 하는 클래스간 거리가 가까울수록 알고리즘 반복 횟수(학습횟수, 에러횟수)는 늘어나지만 어쨌든 수렴은 한다는 것을 보여준다.

    $\gamma$ 가 늘어날수록 학습횟수가 줄어들고, $R$ 이 늘어날수록 학습횟수가 많아진다.

*References*: 

:   https://nbviewer.jupyter.org/github/metamath1/ml-simple-works/blob/master/perceptron/perceptron.ipynb


: 알고리즘 중심의 머신러닝 가이드Machine Learning: An Algorithmic Perspective, 2nd ed., 스티븐 마슬랜드

: http://www.cs.columbia.edu/~mcollins/courses/6998-2012/notes/perc.converge.pdf

---

!!! info

    아래의 Affine Layer 에서 사용되는 행렬미분 정리와 증명과정을 [:octicons-file-code-24: Source](https://github.com/WICWIU/WICWIU.github.io/blob/master/docs/write_doc/math-sample-matrix-derivatives.md) 에서와 같이 **Markdown** 과 **LaTex** 로 작성할 수 있습니다.

# 예시 2: Affine Layer 에서 사용되는 행렬미분 

!!! note ""

    **^^Affine 변환의 역전파^^ : 입력 행렬 $\mathbf{X,W}$ 과 출력행렬 $\mathbf{Y} = \mathbf{XW}$ 를 갖는 Affine 레이어의 역전파는 손실값 $L$ 에 대한 $\mathbf{Y}$ 의 미분 $\dfrac{\partial L}{\partial \mathbf{Y}}$ 에 대하여 $\dfrac{\partial L}{\partial \mathbf{X}} = \dfrac{\partial L}{\partial \mathbf{Y}}\cdot \mathbf{W} ^{\intercal }, \dfrac{\partial L}{\partial \mathbf{W}} = \mathbf{X}^{\intercal } \dfrac{\partial L}{\partial \mathbf{Y}}$ 이다.**

Affine Layer 는 보통 다음과 같이 구성된다.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/msyksphinz/20170622/20170622015132.png)

## Affine Layer 코드 구현

행렬곱 연산과 행렬덧셈 연산을 하는 Affine 레이어는 다음과 같이 코드로 구현할 수 있다.

```python linenums="1"
class Affine:
    def __init__(self, W, b):
        self.W = W
        self.b = b
        self.x = None
        self.dW = None
        self.db = None

    def forward(self, x):
        self.x = x
        out = np.dot(self.x, self.W) + self.b 
        return out

    def backward(self, dout):
        dx = np.dot(dout, self.W.T)
        self.dW = np.dot(self.x.T, dout)
        self.db = np.sum(dout, axis=0) 
        return dx
```

다음 코드는 입력 데이터가 행렬이 아니라 텐서인 경우, 즉 4차원 데이터인 경우도 처리할 수 있도록 구현한 것이다.

```python linenums="1"
class Affine:
    def __init__(self, W, b):
        self.W = W
        self.b = b
        self.x = None
        self.original_x_shape = None
        self.dW = None
        self.db = None

    def forward(self, x):
        # 텐서 대응
        self.original_x_shape = x.shape
        x = x.reshape(x.shape[0], -1) 
        self.x = x
        out = np.dot(self.x, self.W) + self.b 
        return out

    def backward(self, dout):
        dx = np.dot(dout, self.W.T)
        self.dW = np.dot(self.x.T, dout)
        self.db = np.sum(dout, axis=0)
        dx = dx.reshape(*self.original_x_shape)  # 입력 데이터 모양 변경(텐서 대응)
        return dx
```

## 행렬곱 미분 정리

$n \times d$ 입력 행렬 $\mathbf{X}$, $d \times m$ 가중치 행렬 $\mathbf{W}$ 에 대한 $n \times m$ 행렬 $\mathbf{Y=XW}$ 과 손실함수 $l$ 에 대한 스칼라 $L=l(\mathbf{Y})$ 에 대하여 $\mathbf{X}$ 의 미분은 

(1)

$$ \dfrac{\partial L}{\partial \mathbf{X}} = \dfrac{\partial L}{\partial \mathbf{Y}}\dfrac{\partial \mathbf{Y}}{\partial \mathbf{X}} = \dfrac{\partial L}{\partial \mathbf{Y}}\mathbf{W}^{\intercal } $$ 

이고, $\mathbf{W}$ 의 미분은

(2)

$$ \dfrac{\partial L}{\partial \mathbf{W}} = \dfrac{\partial L}{\partial \mathbf{Y}}\dfrac{\partial \mathbf{Y}}{\partial \mathbf{W}} = \mathbf{X}^{\intercal } \dfrac{\partial L}{\partial \mathbf{Y}}$$ 

이다.

### (1) 증명

$n \times d$ 입력 행렬 $\mathbf{X}$, $d \times m$ 가중치 행렬 $\mathbf{W}$ 에 대한 행렬곱 $\mathbf{Y}$ 는 $n \times m$ 행렬

$$ \mathbf{Y} = \mathbf{XW} = \begin{pmatrix} x _{11}&x _{12}&\dots&x _{1d}\\ x _{21}&x _{22}&\dots&x _{2d}\\ \vdots & \vdots & \ddots & \vdots \\ x _{n1}&x _{n2}&\dots&x _{nd}\\ \end{pmatrix} \begin{pmatrix} w _{11} & w _{12} &\dots & w _{1m} \\ w _{21} & w _{22} &\dots & w _{2m} \\ \vdots & \vdots & \ddots & \vdots \\ w _{d1} & w _{d2} &\dots & w _{dm} \\ \end{pmatrix} $$

$$ = \begin{pmatrix} \displaystyle \sum_{k=1}^{d}x _{1k} w _{k1} & \displaystyle \sum_{k=1}^{d}x _{1k} w _{k2} & \dots & \displaystyle \sum_{k=1}^{d}x _{1k} w _{km} \\ \displaystyle \sum_{k=1}^{d}x _{2k} w _{k1} & \displaystyle \sum_{k=1}^{d}x _{2k} w _{k2} & \dots & \displaystyle \sum_{k=1}^{d}x _{2k} w _{km} \\ \vdots & \vdots & \ddots & \vdots \\ \displaystyle \sum_{k=1}^{d}x _{nk} w _{k1} & \displaystyle \sum_{k=1}^{d}x _{nk} w _{k2} & \dots & \displaystyle \sum_{k=1}^{d}x _{nk} w _{km} \\ \end{pmatrix} $$

이다. 이 순전파 출력 $\mathbf{Y}$ 이 손실함수 $l$ 로 전달되어 최종적으로 스칼라 $L$ 이 되었다고 하면 

$$ L = l(\mathbf{Y}) = l(\mathbf{XW}) $$

이다.

$L$ 이 스칼라고 $\mathbf{Y}$ 는 $n \times m$ 행렬이므로 $L$ 에 대한 $\mathbf{Y}$ 의 미분은 $n \times m$ 행렬 

$$ \dfrac{\partial L}{\partial \mathbf{Y}} = \begin{pmatrix} \dfrac{\partial L}{\partial y _{11}}& \dfrac{\partial L}{\partial y _{12}}& \dots& \dfrac{\partial L}{\partial y _{1m}}\\ \dfrac{\partial L}{\partial y _{21}}& \dfrac{\partial L}{\partial y _{22}}& \dots& \dfrac{\partial L}{\partial y _{2m}}\\ \vdots & \vdots & \ddots & \vdots \\ \dfrac{\partial L}{\partial y _{n1}}& \dfrac{\partial L}{\partial y _{n2}}& \dots& \dfrac{\partial L}{\partial y _{nm}}\\ \end{pmatrix} $$

이다. $\mathbf{Y}$ 는 $\mathbf{X, W}$ 에 대한 함수이므로 $L$ 에 대한 $\mathbf{X, W}$ 의 미분은 이독립변수와 일매개변수의 합성함수의 미분

$$ \dfrac{\partial L}{\partial \mathbf{X}} = \dfrac{\partial L}{\partial \mathbf{Y}}\dfrac{\partial \mathbf{Y}}{\partial \mathbf{X}}, \dfrac{\partial L}{\partial \mathbf{W}} = \dfrac{\partial L}{\partial \mathbf{Y}}\dfrac{\partial \mathbf{Y}}{\partial \mathbf{W}} $$

이다. 이때 $L$ 에 대한 $\mathbf{X}$ 의 미분은 행렬 미분의 정의에 의하여 야코비 행렬

$$ \dfrac{\partial L}{\partial \mathbf{X}} = \begin{pmatrix} \dfrac{\partial L}{\partial x _{11}}& \dfrac{\partial L}{\partial x _{12}}& \dots& \dfrac{\partial L}{\partial x _{1d}}\\ \dfrac{\partial L}{\partial x _{21}}& \dfrac{\partial L}{\partial x _{22}}& \dots& \dfrac{\partial L}{\partial x _{2d}}\\ \vdots & \vdots & \ddots & \vdots \\ \dfrac{\partial L}{\partial x _{n1}}& \dfrac{\partial L}{\partial x _{n2}}& \dots& \dfrac{\partial L}{\partial x _{nd}}\\ \end{pmatrix} $$

이다. 이때 $L$ 에 대한 $\mathbf{X}$ 의 $(1,1)$ 원소 $x_{11}$ 의 미분은

$\mathbf{Y}$ 를 행렬이 아니라 매개변수의 나열로 본다면, $\dfrac{\partial L}{\partial x _{11}}$ 에 대한 매개변수를 갖는 합성함수의 편미분으로 나타낼 수 있으므로 스칼라

$$ \dfrac{\partial L}{\partial x _{11}} = \sum_{i=1}^{n}\sum_{j=1}^{m}\dfrac{\partial L}{\partial y _{ij}}\dfrac{\partial y _{ij}}{\partial x _{11}} $$

이다. 그런데 귀찮은 덧셈 연산을 피하기 위하여 모든 스칼라 $\dfrac{\partial L}{\partial y _{ij}}$ 들을 행렬 $\dfrac{\partial L}{\partial \mathbf{Y}}$ 로 쓰고, 모든 스칼라 $\dfrac{\partial y _{ij}}{\partial x _{11}}$ 들을 행렬 $\dfrac{\partial \mathbf{Y}}{\partial x _{11}}$ 로 쓰면 이것을 Frobenius 내적

$$ = \bigg < \dfrac{\partial L}{\partial \mathbf{Y}} , \dfrac{\partial \mathbf{Y}}{\partial x _{11}} \bigg  > _{\mathbf{F}}$$

으로 나타낼 수 있다. 이때 

$$ \dfrac{\partial \mathbf{Y}}{\partial x _{11}} = \begin{pmatrix} w _{11}& w _{12}& \dots& w _{1m} \\ 0& 0& \dots& 0\\ \vdots & \vdots & \ddots & \vdots \\ 0& 0& \dots& 0\\ \end{pmatrix} $$

이므로 

$$ \bigg < \dfrac{\partial L}{\partial Y} , \dfrac{\partial Y}{\partial x _{11}} \bigg > _{\mathbf{F}} = \Bigg < \begin{pmatrix} \dfrac{\partial L}{\partial y _{11}}& \dfrac{\partial L}{\partial y _{12}}& \dots& \dfrac{\partial L}{\partial y _{1m}}\\ \dfrac{\partial L}{\partial y _{21}}& \dfrac{\partial L}{\partial y _{22}}& \dots& \dfrac{\partial L}{\partial y _{2m}}\\ \vdots & \vdots & \ddots & \vdots \\ \dfrac{\partial L}{\partial y _{n1}}& \dfrac{\partial L}{\partial y _{n2}}& \dots& \dfrac{\partial L}{\partial y _{nm}}\\ \end{pmatrix}, \begin{pmatrix} w _{11}& w _{12}& \dots& w _{1m} \\ 0& 0& \dots& 0\\ \vdots & \vdots & \ddots & \vdots \\ 0& 0& \dots& 0\\ \end{pmatrix}  \Bigg > _{\mathbf{F}} $$

$$ = \dfrac{\partial L}{\partial y _{11}} w _{11} + \dfrac{\partial L}{\partial y _{12}} w _{12} + \dots+ \dfrac{\partial L}{\partial y _{1m}} w _{1m} = \sum_{k=1}^{m}\dfrac{\partial L}{\partial y _{1k}}w _{1k} $$

이다. 즉, $\displaystyle \dfrac{\partial L}{\partial x _{11}} = \bigg < \dfrac{\partial L}{\partial \mathbf{Y}} , \dfrac{\partial \mathbf{Y}}{\partial x _{11}} \bigg  > _{\mathbf{F}}= \sum_{k=1}^{m}\dfrac{\partial L}{\partial y _{1k}}w _{1k}$ 이다. 마찬가지로 

$$\displaystyle \dfrac{\partial L}{\partial x _{12}} = \bigg < \dfrac{\partial L}{\partial \mathbf{Y}} , \dfrac{\partial \mathbf{Y}}{\partial x _{12}} \bigg  > _{\mathbf{F}}= \sum_{k=1}^{m}\dfrac{\partial L}{\partial y _{1k}}w _{2k}$$

$$\displaystyle \dfrac{\partial L}{\partial x _{21}} = \bigg < \dfrac{\partial L}{\partial \mathbf{Y}} , \dfrac{\partial \mathbf{Y}}{\partial x _{21}} \bigg  > _{\mathbf{F}}= \sum_{k=1}^{m}\dfrac{\partial L}{\partial y _{2k}}w _{1k}$$

$$ \vdots $$

$$\displaystyle \dfrac{\partial L}{\partial x _{ij}} = \bigg < \dfrac{\partial L}{\partial \mathbf{Y}} , \dfrac{\partial \mathbf{Y}}{\partial x _{ij}} \bigg  > _{\mathbf{F}}= \sum_{k=1}^{m}\dfrac{\partial L}{\partial y _{ik}}w _{jk}$$

이다. 그렇다면 최종적으로 $L$ 에 대한 $\mathbf{X}$ 의 미분은 $n \times d$ 행렬

$$ \therefore  \dfrac{\partial L}{\partial \mathbf{X}} = \begin{pmatrix} \displaystyle \sum_{k=1}^{m}\dfrac{\partial L}{\partial y _{1k}}w _{1k}& \displaystyle \sum_{k=1}^{m}\dfrac{\partial L}{\partial y _{1k}}w _{2k}& \dots& \displaystyle \sum_{k=1}^{m}\dfrac{\partial L}{\partial y _{1k}}w _{dk} \\ \displaystyle \sum_{k=1}^{m}\dfrac{\partial L}{\partial y _{2k}}w _{1k}& \displaystyle \sum_{k=1}^{m}\dfrac{\partial L}{\partial y _{2k}}w _{2k}& \dots& \displaystyle \sum_{k=1}^{m}\dfrac{\partial L}{\partial y _{2k}}w _{dk} \\ \vdots & \vdots & \ddots & \vdots \\ \displaystyle \sum_{k=1}^{m}\dfrac{\partial L}{\partial y _{nk}}w _{1k}& \displaystyle \sum_{k=1}^{m}\dfrac{\partial L}{\partial y _{nk}}w _{2k}& \dots& \displaystyle \sum_{k=1}^{m}\dfrac{\partial L}{\partial y _{nk}}w _{dk} \\ \end{pmatrix}$$

$$ = \begin{pmatrix} \dfrac{\partial L}{\partial y _{11}}& \dfrac{\partial L}{\partial y _{12}}& \dots& \dfrac{\partial L}{\partial y _{1m}}\\ \dfrac{\partial L}{\partial y _{21}}& \dfrac{\partial L}{\partial y _{22}}& \dots& \dfrac{\partial L}{\partial y _{2m}}\\ \vdots & \vdots & \ddots & \vdots \\ \dfrac{\partial L}{\partial y _{n1}}& \dfrac{\partial L}{\partial y _{n2}}& \dots& \dfrac{\partial L}{\partial y _{nm}}\\ \end{pmatrix}\begin{pmatrix} w_{11}& w_{21}& \dots & w_{d1} \\ w_{12}& w_{22}& \dots & w_{d2} \\ \vdots & \vdots & \ddots & \vdots \\ w_{1m}& w_{2m}& \dots & w_{dm} \\ \end{pmatrix} $$

$$ = \boxed{\dfrac{\partial L}{\partial \mathbf{Y}}\mathbf{W}^{\intercal } } $$

이다. ■ 

### (2) 증명

마찬가지로 $L$ 에 대한 $\mathbf{W}$ 의 미분은 행렬 미분의 정의에 의하여 야코비 행렬

$$ \dfrac{\partial L}{\partial \mathbf{W}} = \begin{pmatrix} \dfrac{\partial L}{\partial w_{11}}& \dfrac{\partial L}{\partial w_{12}}& \dots& \dfrac{\partial L}{\partial w_{1m}}\\ \dfrac{\partial L}{\partial w_{21}}& \dfrac{\partial L}{\partial w_{22}}& \dots& \dfrac{\partial L}{\partial w_{2m}}\\ \vdots & \vdots & \ddots & \vdots \\ \dfrac{\partial L}{\partial w_{d1}}& \dfrac{\partial L}{\partial w_{d2}}& \dots& \dfrac{\partial L}{\partial w_{dm}}\\ \end{pmatrix} $$

인데 이때 $L$ 에 대한 $\mathbf{W}$ 의 $(1,1)$ 원소 $w _{11}$ 의 미분은 

$\mathbf{Y}$ 를 행렬이 아니라 매개변수의 나열로 본다면, $\dfrac{\partial L}{\partial w _{11}}$ 에 대한 매개변수를 갖는 합성함수의 편미분으로 나타낼 수 있으므로 스칼라

$$ \dfrac{\partial L}{\partial w_{11}} = \sum_{i=1}^{n}\sum_{j=1}^{m}\dfrac{\partial L}{\partial y _{ij}}\dfrac{\partial y _{ij}}{\partial w_{11}} $$

이다. 이것도 마찬가지로 Frobenius 내적 

$$ = \bigg < \dfrac{\partial L}{\partial \mathbf{Y}} , \dfrac{\partial \mathbf{Y}}{\partial w _{11}} \bigg  > _{\mathbf{F}}$$

으로 나타낼 수 있다. 그러면 마찬가지로

$$ \dfrac{\partial \mathbf{Y}}{\partial w _{11}} = \begin{pmatrix} x _{11}& 0& \dots& 0 \\ x _{21}& 0& \dots& 0\\ \vdots & \vdots & \ddots & \vdots \\ x _{n1}& 0& \dots& 0\\ \end{pmatrix} $$

이므로

$$ \bigg < \dfrac{\partial L}{\partial \mathbf{Y}} , \dfrac{\partial \mathbf{Y}}{\partial w _{11}} \bigg  > _{\mathbf{F}} = \Bigg < \begin{pmatrix} \dfrac{\partial L}{\partial y _{11}}& \dfrac{\partial L}{\partial y _{12}}& \dots& \dfrac{\partial L}{\partial y _{1m}}\\ \dfrac{\partial L}{\partial y _{21}}& \dfrac{\partial L}{\partial y _{22}}& \dots& \dfrac{\partial L}{\partial y _{2m}}\\ \vdots & \vdots & \ddots & \vdots \\ \dfrac{\partial L}{\partial y _{n1}}& \dfrac{\partial L}{\partial y _{n2}}& \dots& \dfrac{\partial L}{\partial y _{nm}}\\ \end{pmatrix}, \begin{pmatrix} x _{11}& 0& \dots& 0 \\ x _{21}& 0& \dots& 0\\ \vdots & \vdots & \ddots & \vdots \\ x _{n1}& 0& \dots& 0\\ \end{pmatrix} \Bigg > _{\mathbf{F}} $$

$$ = \dfrac{\partial L}{\partial y _{11}}x _{11} + \dfrac{\partial L}{\partial y _{21}}x _{21} + \dots+ \dfrac{\partial L}{\partial y _{n1}}x _{n1}  = \sum_{k=1}^{n} \dfrac{\partial L}{\partial y _{k1}}x _{k1} $$

이다. 즉, $\displaystyle \dfrac{\partial L}{\partial w _{11}} = \bigg < \dfrac{\partial L}{\partial \mathbf{Y}} , \dfrac{\partial \mathbf{Y}}{\partial w _{11}} \bigg  > _{\mathbf{F}}= \sum_{k=1}^{n}\dfrac{\partial L}{\partial y _{k1}}x _{k1}$ 이다. 마찬가지로 

$$\displaystyle \dfrac{\partial L}{\partial w _{12}} = \bigg < \dfrac{\partial L}{\partial \mathbf{Y}} , \dfrac{\partial \mathbf{Y}}{\partial w _{12}} \bigg  > _{\mathbf{F}}= \sum_{k=1}^{n}\dfrac{\partial L}{\partial y _{k2}}x_{k1}$$

$$\displaystyle \dfrac{\partial L}{\partial w _{21}} = \bigg < \dfrac{\partial L}{\partial \mathbf{Y}} , \dfrac{\partial \mathbf{Y}}{\partial w _{21}} \bigg  > _{\mathbf{F}}= \sum_{k=1}^{n}\dfrac{\partial L}{\partial y _{k1}}x_{k2}$$

$$ \vdots $$

$$\displaystyle \dfrac{\partial L}{\partial w _{ij}} = \bigg < \dfrac{\partial L}{\partial \mathbf{Y}} , \dfrac{\partial \mathbf{Y}}{\partial w _{ij}} \bigg  > _{\mathbf{F}}= \sum_{k=1}^{n}\dfrac{\partial L}{\partial y _{kj}}x_{ki}$$

이다. 그렇다면 최종적으로 $L$ 에 대한 $\mathbf{W}$ 의 미분은 $d \times m$ 행렬

$$ \therefore \dfrac{\partial L}{\partial \mathbf{W}} = \begin{pmatrix} \displaystyle \sum_{k=1}^{n}\dfrac{\partial L}{\partial y_{k1}}x_{k1}& \displaystyle \sum_{k=1}^{n}\dfrac{\partial L}{\partial y_{k2}}x_{k1}& \dots& \displaystyle \sum_{k=1}^{n}\dfrac{\partial L}{\partial y_{km}}x_{k1} \\ \displaystyle \sum_{k=1}^{n}\dfrac{\partial L}{\partial y_{k1}}x_{k2}& \displaystyle \sum_{k=1}^{n}\dfrac{\partial L}{\partial y_{k2}}x_{k2}& \dots& \displaystyle \sum_{k=1}^{n}\dfrac{\partial L}{\partial y_{km}}x_{k2} \\ \vdots & \vdots & \ddots & \vdots \\ \displaystyle \sum_{k=1}^{n}\dfrac{\partial L}{\partial y_{k1}}x_{kd}& \displaystyle \sum_{k=1}^{n}\dfrac{\partial L}{\partial y_{k2}}x_{kd}& \dots& \displaystyle \sum_{k=1}^{n}\dfrac{\partial L}{\partial y_{km}}x_{kd} \\ \end{pmatrix}$$

$$ = \begin{pmatrix} x_{11}& x_{21}& \dots & x_{n1} \\ x_{12}& x_{22}& \dots & x_{n2} \\ \vdots & \vdots & \ddots & \vdots \\ x_{1d}& x_{2d}& \dots & x_{nd} \\ \end{pmatrix}\begin{pmatrix} \dfrac{\partial L}{\partial y _{11}}& \dfrac{\partial L}{\partial y _{12}}& \dots& \dfrac{\partial L}{\partial y _{1m}}\\ \dfrac{\partial L}{\partial y _{21}}& \dfrac{\partial L}{\partial y _{22}}& \dots& \dfrac{\partial L}{\partial y _{2m}}\\ \vdots & \vdots & \ddots & \vdots \\ \dfrac{\partial L}{\partial y _{n1}}& \dfrac{\partial L}{\partial y _{n2}}& \dots& \dfrac{\partial L}{\partial y _{nm}}\\ \end{pmatrix} $$

$$ = \boxed{\mathbf{X}^{\intercal }\dfrac{\partial L}{\partial \mathbf{Y}}} $$

이다. ■ 

## 행렬덧셈 미분 정리

$n \times m$ 출력 행렬 $\mathbf{Y}$, $n \times m$ 편향 행렬 $\mathbf{B}$ 에 대한 $n \times m$ 순입력 행렬 $\mathbf{Z=Y + B}$ 와 손실함수 $l$ 에 대한 스칼라 $L=l(\mathbf{Y})$ 에 대하여 $\mathbf{Y}$ 의 미분은 

(1)

$$ \dfrac{\partial L}{\partial \mathbf{Y}} = \dfrac{\partial L}{\partial \mathbf{Z}} $$ 

이고, $\mathbf{B}$ 의 미분은

(2)

$$ \dfrac{\partial L}{\partial \mathbf{B}} = \dfrac{\partial L}{\partial \mathbf{Z}} $$ 

이다.

### (1) 증명

$n \times m$ 출력 행렬 $\mathbf{Y}$, $n \times m$ 편향 행렬 $\mathbf{B}$ 에 대한 행렬 $\mathbf{Z = Y + B}$ 는 $n \times m$ 행렬

$$ \mathbf{Z} = \mathbf{Y + B} = \begin{pmatrix} y_{11}&y_{12}&\dots&y_{1m}\\ y_{21}&y_{22}&\dots&y_{2m}\\ \vdots & \vdots & \ddots & \vdots \\ y_{n1}&y_{n2}&\dots&y_{nm}\\ \end{pmatrix} + \begin{pmatrix} b_{11} & b_{12} &\dots & b_{1m} \\ b_{21} & b_{22} &\dots & b_{2m} \\ \vdots & \vdots & \ddots & \vdots \\ b_{n1} & b_{n2} &\dots & b_{nm} \\ \end{pmatrix} $$

$$ = \begin{pmatrix} \displaystyle y_{11}+b_{11} & \displaystyle y_{12}+b_{12} & \dots & \displaystyle y_{1m}+b_{1m} \\ \displaystyle y_{21}+b_{21} & \displaystyle y_{22}+b_{22} & \dots & \displaystyle y_{2m}+b_{2m} \\ \vdots & \vdots & \ddots & \vdots \\ \displaystyle y_{n1}+b_{n1} & \displaystyle y_{n2}+b_{n2} & \dots & \displaystyle y_{nm}+b_{nm} \\ \end{pmatrix} $$

이다. 이 $\mathbf{Z}$ 이 손실함수 $l$ 로 전달되어 최종적으로 스칼라 $L$ 이 되었다고 하면 

$$ L = l(\mathbf{Z}) = l(\mathbf{Y + B}) $$

이다.

$L$ 이 스칼라고 $\mathbf{Z}$ 는 $n \times m$ 행렬이므로 $L$ 에 대한 $\mathbf{Z}$ 의 미분은 $n \times m$ 행렬 

$$ \dfrac{\partial L}{\partial \mathbf{Z}} = \begin{pmatrix} \dfrac{\partial L}{\partial z_{11}}& \dfrac{\partial L}{\partial z_{12}}& \dots& \dfrac{\partial L}{\partial z_{1m}}\\ \dfrac{\partial L}{\partial z_{21}}& \dfrac{\partial L}{\partial z_{22}}& \dots& \dfrac{\partial L}{\partial z_{2m}}\\ \vdots & \vdots & \ddots & \vdots \\ \dfrac{\partial L}{\partial z_{n1}}& \dfrac{\partial L}{\partial z_{n2}}& \dots& \dfrac{\partial L}{\partial z_{nm}}\\ \end{pmatrix} $$

이다. $\mathbf{Z}$ 는 $\mathbf{Y, B}$ 에 대한 함수이므로 $L$ 에 대한 $\mathbf{Y, B}$ 의 미분은 이독립변수와 일매개변수의 합성함수의 미분

$$ \dfrac{\partial L}{\partial \mathbf{Y}} = \dfrac{\partial L}{\partial \mathbf{Z}}\dfrac{\partial \mathbf{Z}}{\partial \mathbf{Y}}, \dfrac{\partial L}{\partial \mathbf{B}} = \dfrac{\partial L}{\partial \mathbf{Z}}\dfrac{\partial \mathbf{Z}}{\partial \mathbf{B}} $$

이다. 이때 $L$ 에 대한 $\mathbf{Y}$ 의 미분은 행렬 미분의 정의에 의하여 야코비 행렬

$$ \dfrac{\partial L}{\partial \mathbf{Y}} = \begin{pmatrix} \dfrac{\partial L}{\partial y_{11}}& \dfrac{\partial L}{\partial y_{12}}& \dots& \dfrac{\partial L}{\partial y_{1m}}\\ \dfrac{\partial L}{\partial y_{21}}& \dfrac{\partial L}{\partial y_{22}}& \dots& \dfrac{\partial L}{\partial y_{2m}}\\ \vdots & \vdots & \ddots & \vdots \\ \dfrac{\partial L}{\partial y_{n1}}& \dfrac{\partial L}{\partial y_{n2}}& \dots& \dfrac{\partial L}{\partial y_{nm}}\\ \end{pmatrix} $$

이다. 이때 $L$ 에 대한 $\mathbf{Y}$ 의 $(1,1)$ 원소 $y_{11}$ 의 미분은

$\mathbf{Z}$ 를 행렬이 아니라 매개변수의 나열로 본다면, $\dfrac{\partial L}{\partial y_{11}}$ 에 대한 매개변수를 갖는 합성함수의 편미분으로 나타낼 수 있으므로 스칼라

$$ \dfrac{\partial L}{\partial y_{11}} = \sum_{i=1}^{n}\sum_{j=1}^{m}\dfrac{\partial L}{\partial z _{ij}}\dfrac{\partial z _{ij}}{\partial y_{11}} $$

이다. 그런데 귀찮은 덧셈 연산을 피하기 위하여 모든 스칼라 $\dfrac{\partial L}{\partial z _{ij}}$ 들을 행렬 $\dfrac{\partial L}{\partial \mathbf{Z}}$ 로 쓰고, 모든 스칼라 $\dfrac{\partial z _{ij}}{\partial y_{11}}$ 들을 행렬 $\dfrac{\partial \mathbf{Z}}{\partial y_{11}}$ 로 쓰면 이것을 Frobenius 내적

$$ = \bigg < \dfrac{\partial L}{\partial \mathbf{Z}} , \dfrac{\partial \mathbf{Z}}{\partial y_{11}} \bigg  > _{\mathbf{F}}$$

으로 나타낼 수 있다. 이때 

$$ \dfrac{\partial \mathbf{Z}}{\partial y_{11}} = \begin{pmatrix} 1 & 0& \dots& 0 \\ 0& 0& \dots& 0\\ \vdots & \vdots & \ddots & \vdots \\ 0& 0& \dots& 0\\ \end{pmatrix} $$

이므로 

$$ \bigg < \dfrac{\partial L}{\partial \mathbf{Z}} , \dfrac{\partial \mathbf{Z}}{\partial y _{11}} \bigg  > _{\mathbf{F}} = \Bigg < \begin{pmatrix} \dfrac{\partial L}{\partial z_{11}}& \dfrac{\partial L}{\partial z_{12}}& \dots& \dfrac{\partial L}{\partial z_{1m}}\\ \dfrac{\partial L}{\partial z_{21}}& \dfrac{\partial L}{\partial z_{22}}& \dots& \dfrac{\partial L}{\partial z_{2m}}\\ \vdots & \vdots & \ddots & \vdots \\ \dfrac{\partial L}{\partial z_{n1}}& \dfrac{\partial L}{\partial z_{n2}}& \dots& \dfrac{\partial L}{\partial z_{nm}}\\ \end{pmatrix}, \begin{pmatrix} 1& 0& \dots& 0 \\ 0& 0& \dots& 0\\ \vdots & \vdots & \ddots & \vdots \\ 0& 0& \dots& 0\\ \end{pmatrix} \Bigg > _{\mathbf{F}} $$

$$ = \dfrac{\partial L}{\partial z_{11}} $$

이다. 즉, $\displaystyle \dfrac{\partial L}{\partial y _{11}} = \bigg < \dfrac{\partial L}{\partial \mathbf{Z}} , \dfrac{\partial \mathbf{Z}}{\partial y _{11}} \bigg  > _{\mathbf{F}}= \dfrac{\partial L}{\partial z _{11}}$ 이다. 마찬가지로 

$$\displaystyle \dfrac{\partial L}{\partial y _{ij}} = \bigg < \dfrac{\partial L}{\partial \mathbf{Z}} , \dfrac{\partial \mathbf{Z}}{\partial y _{ij}} \bigg  > _{\mathbf{F}}= \dfrac{\partial L}{\partial z _{ij}}$$

이다. 그러므로 $L$ 에 대한 $\mathbf{Y}$ 의 미분은 $n \times m$ 행렬 

$$ \therefore \dfrac{\partial L}{\partial \mathbf{Y}} = \begin{pmatrix} \dfrac{\partial L}{\partial z _{11}}& \dfrac{\partial L}{\partial z _{12}}& \dots& \dfrac{\partial L}{\partial z _{1m}}\\ \dfrac{\partial L}{\partial z _{21}}& \dfrac{\partial L}{\partial z _{22}}& \dots& \dfrac{\partial L}{\partial z _{2m}}\\ \vdots & \vdots & \ddots & \vdots \\ \dfrac{\partial L}{\partial z _{n1}}& \dfrac{\partial L}{\partial z _{n2}}& \dots& \dfrac{\partial L}{\partial z _{nm}}\\ \end{pmatrix} = \dfrac{\partial L}{\partial \mathbf{Z}} $$ 

이다. ■ 

*References*: 

:   http://cs231n.stanford.edu/handouts/derivatives.pdf

:   http://cs231n.stanford.edu/handouts/linear-backprop.pdf
