---
layout: post
title:  Parameter Initialization
date:   2021-03-07 00:00:00 +0800
image:  neural-net.jpg
tags:   Resources
author: yeeun-lee
---
# Parameters Initialization

## Initializing neural networks

신경망 생성시 파라미터들을 어떻게 초기화 하는 것이 좋을까?

### Importance of Effective Initialization

일반적으로 신경망을 학습하는 프로세스는 다음과 같다

1. 파라미터의 초기화(Initialize the parameters)
2. 최적화 알고리즘의 선택(Optimization algorithm)
3. 일련의 과정을 반복
    1. input의 Forward propagation
    2. cost function의 계산(loss)
    3. 파라미터 하에서 back propagation을 활용한 gradient의 계산
    4. optimization 알고리즘에 따라, gradients를 활용한 각 파라미터들의 업데이트

초기화 단계는 궁극적인 모델의 성능에 매우 중요한 역할을 수행하며, 때문에 적합한 방식으로 초기화를 해야 한다.

0으로 초기화하게 되면 학습동안 뉴런은 동일한 특징을 학습하게 된다.(상수로 초기화 할 경우)

- 두개의 히든 유닛을 가진 신경망을 가정하고, 모든 biases는 0으로, weights는 하나의 상수 $\alpha$ 로 초기화 했다고 해보자. 이 네트워크에 input $(x_1, x_2)$ 를 forward propagate하게 되면 두 히든 유닛의 결과물은 모두 $relu(\alpha x_1+\alpha x_2)$ 가 될 것이다.
- 따라서 두 히든 유닛은 cost에 동일한 영향을 주게 되고, 동일한 gradients를 얻게 된다.
- 결과적으로 두 뉴런은 학습을 통해 동일한 모습으로 진화해가고, 뉴런들은 다른 것들을 배울 수 없을 것이다.

1) 너무 작은 값들 혹은 2) 너무 큰 값들로 초기화 하는 것은 위의 symmetry는 깨뜨릴지 몰라도 다음과 같은 문제점을 초래한다.
1) : slow learning
2) : divergence

첨부한 링크의 시뮬레이터를 통해 적합한 initializing method의 선택의 중요성을 확인할 수 있다.

### The problem of Exploding or Vanishing Gradients

**case 1** : 너무 큰 initializing → Exploding Gradients

$W^{[1]} = W^{[2]} = \dots = W^{[L-1]}=\begin{bmatrix}1.5 & 0 \\ 0 & 1.5\end{bmatrix}$

모든 가중치가 단위행렬보다 조금 큰 동일한 행렬이라고 가정해보자.

→ $\hat{y} = W^{[L]}1.5^{L-1}x$, 이 값을 activation function에 넣어 얻은 $a^{[l]}$ 은 l이 커짐에 따라 기하급수적으로 커지게 되고, 이는 exploding gradient 문제를 초래하게 된다.

→ 즉, parameters에 대한 cost의 gradients가 너무 커진다는 뜻이다. 따라서 cost는 minimum value(optimul value) 주변에서 계속 진동하여 수렴하지 못하게 된다.

**case 2** : 너무 작은 initializing → Vanishing Gradients

$W^{[1]} = W^{[2]} = \dots = W^{[L-1]}=\begin{bmatrix}0.5 & 0 \\ 0 & 0.5\end{bmatrix}$

이번에는 단위행렬보다 작은 값으로 초기화된 행렬을 가정하자.

→ $\hat{y} = W^{[L]}0.5^{L-1}x$, 이 값을 activation function에 넣어 얻은 $a^{[l]}$ 은 l과 함께 기하급수적으로 감소하게 된다.(0에 가까워진다) 이른 vanishing gradient 문제를 초래한다.

→ 이는 minimum value(global)에 도달하기 이전에 너무 작은 gradient로 cost가 수렴하게 된다.

### How to find appropriate Initialization values

rules of thumb(prevent gradients from vanishing or exploding)

1. activations의 평균은 0이 되어야 한다.
2. activations의 분산은 모든 레이어들에 걸쳐 동일해야 한다.

이와 같은 두개의 가정 하에, backpropagated gradient 신호는 어떠한 레이어에서도 너무 작거나 큰 값에 곱해지면 안된다. exploding/vanishing 없이 input layer까지 돌아가야 한다.

→ 위의 두 가정은 exploding/vanishing signal을 막는것을 보장해준다

### Xavier Initialization

Xavier Initialization(혹은 Glorot Initialization)은 이전 노드와 다음 노드의 개수에 의존하는 방법으로 Uniform 분포를 따르는 방법과 Normal 분포를 따른 두가지 방법이 사용된다.

- Xavier Normal Initialization

    $W\sim N(0, Var(W)), Var(W) = \sqrt{\frac{2}{n_{in}+n_{out}}}$

    n_in : 이전 layer(input)의 노드 수, n_out : 다음 layer의 노드 수

(이번 주차에는 normal distribution만 고려한다.)

- Xavier함수는 비선형 함수(ex. sigmoid, tanh)에서 효과적인 결과를 보여주지만, ReLU와 함께 사용 시 출력 값이 0으로 수렴하게 되는 현상이 나타나면서 비효율적인 결과를 보이게 된다.

이 경우 초기화 방법을 He Initialization을 사용한다

### He Initialization

He Initialization의 경우에도 정규분포와 균등분포 두가지 방법이 사용되지만, 마찬가지로 이번 주차에서는 정규분포만 고려한다.

- He Normal Distribution
$W\sim N(0, Var(W)), Var(W)=\sqrt\frac{2}{n_{in}}$

### Conclusion

- sigmoid, tanh : Xavier가 효과적
- ReLU 계열 : He가 효율적
- 최근 대부분의 모델에서는 He 초기화를 주로 선택함.
- 분포를 선택하는 기준은 명확한 것이 없으나, He의 논문에서는 "최근의 Deep CNN 모델들은 주로 Gaussian Distribution을 따르는 가중치 초기화 방법을 사용한다"라고 말하고 있다.

### References
[AI Notes: Initializing neural networks - deeplearning.ai](https://www.deeplearning.ai/ai-notes/initialization/)

[가중치 초기화 (Weight Initialization)](https://reniew.github.io/13/)
