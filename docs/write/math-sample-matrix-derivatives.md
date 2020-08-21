# Affine Layer 에서 사용되는 행렬미분 

!!! note ""

    ^^Affine 변환의 역전파^^ : 입력 행렬 $\mathbf{X,W}$ 과 출력행렬 $\mathbf{Y} = \mathbf{XW}$ 를 갖는 Affine 레이어의 역전파는 손실값 $L$ 에 대한 $\mathbf{Y}$ 의 미분 $\dfrac{\partial L}{\partial \mathbf{Y}}$ 에 대하여 $\dfrac{\partial L}{\partial \mathbf{X}} = \dfrac{\partial L}{\partial \mathbf{Y}}\cdot \mathbf{W} ^{\intercal }, \dfrac{\partial L}{\partial \mathbf{W}} = \mathbf{X}^{\intercal } \dfrac{\partial L}{\partial \mathbf{Y}}$ 이다.

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

이고, $\mathbf{W}$ 의 미분은

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
