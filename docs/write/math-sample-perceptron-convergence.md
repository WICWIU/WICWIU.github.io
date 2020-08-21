# 퍼셉트론 수렴정리

!!! note ""

    ^^퍼셉트론 수렴 정리(perceptron convergence theorem)^^ : 퍼셉트론이 선형 분리 가능 문제들, 즉 직선으로 분류가 가능한 데이터를 유한번의 학습으로 데이터를 잘 분류하는 가중치를 수렴시킬 수 있다 는 정리이다.

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