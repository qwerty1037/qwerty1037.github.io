---
layout: single
title: "Multivariable Calculus"
use_math : true
categories: machine_learning
tags: machine_learning
toc: true
toc_sticky: true
---

이 포스팅은 ML을 공부하기 전 알아두면 매우 유용한 다변수 미적분에 대해 다룬다.

[해당 책](https://d2l.ai/chapter_appendix-mathematics-for-deep-learning/multivariable-calculus.html)의 내용을 공부하고, 이를 한글로 번역/정리하였다.   


## 1. 고차원 미분(Higher-Dimensional Differentiation)

$N$개의 $w$중 하나의 작은 변화에 대하여, $L$의 변화는 다음과 같다.

$$
L(w_1+\epsilon_1,w_2,\cdots,w_N) 
\approx 
L(w_1,\cdots,w_n) + \epsilon \frac{\partial}{\partial w_1} L(w_1,\cdots,w_n)
\tag{1.1}
$$

간단한 편미분이다. 다른 변수 $w_2$에도 약간의 변화를 주면,

$$
\begin{align*}\tag{1.2}
L(w_1+\epsilon_1,w_2+\epsilon_2,\cdots,w_N) 
& \approx 
L(w_1,w_2+\epsilon_2,\cdots,w_n) + \epsilon \frac{\partial}{\partial w_1} L(w_1,w_2+\epsilon_2,\cdots,w_n) \\
& \approx
L(w_1,w_2,\cdots,w_n) + \epsilon_2 \frac{\partial}{\partial w_2} L(w_1,w_2,\cdots,w_n) \\
&\quad + \epsilon_1 \frac{\partial}{\partial w_1} \bigl\{ L(w_1,w_2,\cdots,w_n) + \epsilon_2 \frac{\partial}{\partial w_2} L(w_1,w_2,\cdots,w_n) \bigr\} \\
& \approx
L(w_1,w_2,\cdots,w_n) \\
&\quad + \epsilon_2 \frac{\partial}{\partial w_2} L(w_1,w_2,\cdots,w_n) \\
&\quad + \epsilon_1 \frac{\partial}{\partial w_1}  L(w_1,w_2,\cdots,w_n) \\
&\quad + \cancel{\epsilon_1 \epsilon_2 \frac{\partial}{\partial w_1} \frac{\partial}{\partial w_2} L(w_1,w_2,\cdots,w_n)}{} \\
\end{align*}
$$

<strong>higher_order_term(고차항)</strong>은 생략 가능하다. 위를 $N$개의 모든 $w$에 대해 반복하면 다음과 같다.

<a id="equation1.3"></a>
$$
L(w_1+\epsilon_1,w_2+\epsilon_2,\cdots,w_N+\epsilon_N)
\approx
L(w_1+\cdots,w_N) + \sum_i^N \epsilon_i \frac{\partial}{\partial w_i} L(w_1,\cdots,w_N)
\tag{1.3}
$$

이를 벡터로 나타내면, 익숙해 보이는 형태가 된다.

$$
\boldsymbol{\epsilon} = 
\begin{bmatrix}
\epsilon_1 \\ \vdots \\ \epsilon_N
\end{bmatrix}
\text{and } 
\nabla_{\mathbf{x}}L = 
\begin{bmatrix}
\frac{\partial L}{\partial x_1} \\ \vdots \\ \frac{\partial L}{\partial x_N}
\end{bmatrix}
\text{,  }
L(\mathbf{w}+\boldsymbol{\epsilon})
\approx
L(\mathbf{w}) + \boldsymbol{\epsilon} \cdot \nabla_{\mathbf{w}}L(\mathbf{w})
\tag{1.4}
$$

※ 이는 다변수 함수의 1차 테일러 근사식이다. $\mathbf{w}$는 고정점으로 간주된다.   
아마 알고 있겠지만, $\nabla_{\mathbf{w}}L$ 을 L의 *gradient* 라고 한다.

예시를 들어보자. 아래의 함수를 가정하자.

$$
\tag{1.5}
f(x,y)=\log(e^x+e^y) \text{ with gradient } \nabla f(x,y) = \left[\frac{e^x}{e^x+e^y},\frac{e^y}{e^x+e^y}\right]
$$

점 $(0,\log(2))$ 에서, 

$$
\tag{1.6}
f(x,y)=\log(3) \text{ with gradient } \nabla f(x,y) = \left[\frac{1}{3},\frac{2}{3}\right]
$$

따라서, $f$를 점 $(\epsilon_1,\log(2)+\epsilon_2)$ 에서 근사하면 아래와 같다.

$$
f(\epsilon_1,\log(2)+\epsilon_2) \approx \log(3) + \frac{1}{3}\epsilon_1 + \frac{2}{3}\epsilon_2
$$

## 2. 다변수 사슬 법칙(Multivariate Chain Rule)

아래 4변수($w,x,y,z$) 함수를 가정하자.

<a id="equation2.1"></a>
$$
\begin{align*}
f(u,v) &= (u+v)^2\\
u(a,b) &= (a+b)^2, && v(a,b) = (a-b)^2, \\
a(w,x,y,z) &= (w+x+y+z)^2, && b(w,x,y,z) = (w+x-y-z)^2,
\end{align*}
\tag{2.1}
$$

이를 Neural Network로 시각화하면 아래와 같다.

![image1](/assets/images/2024-08-22-MultiVariable_Calculus_files/image1.png){: width="30%" height="30%" .align-center}

$\frac{\partial f}{\partial x}$를 직접 미분하여 구하려 하면, 매우 끔찍한 결과가 나올 것이다.   
대신, $a$의 변동에 대한 $f$의 변화를 구해 보자.

$$
\begin{align*}
& f(u(a+\epsilon , b),\ v(a+\epsilon,b)) \\
\approx & f\left(u(a,b)+\epsilon \frac{\partial u}{\partial a}(a,b),\ v(a,b)+\epsilon \frac{\partial v}{\partial a}(a,b)\right) \\
\approx & f(u(a,b),\ v(a,b)) 
+ \epsilon \frac{\partial u}{\partial a}(a,b) \frac{\partial f}{\partial u}(u(a,b),v(a,b))
+ \epsilon \frac{\partial v}{\partial a}(a,b) \frac{\partial f}{\partial v}(u(a,b),v(a,b)) \\
& \ \text{(Equation 1.3 참조)} \\
\approx & f(u(a,b),\ v(a,b)) 
+ \epsilon \left[\frac{\partial f}{\partial u}(u(a,b),v(a,b)) \frac{\partial u}{\partial a}(a,b) 
+ \frac{\partial f}{\partial v}(u(a,b),v(a,b)) \frac{\partial v}{\partial a}(a,b) \right] \\
\end{align*}
\tag{2.2}
$$

이를 아래와 같이 축약하여 표현할 수 있다.

$$
\frac{\partial f}{\partial a} = \frac{\partial f}{\partial u} \frac{\partial u}{\partial a} + \frac{\partial f}{\partial v}\frac{\partial v}{\partial a}
\tag{2.3}
$$

예시를 들어, 아래와 같은 네트워크를 가정하자.

[TODO: 이미지]

$$
\frac{\partial f}{\partial y} = 
\frac{\partial f}{\partial a} \cdot \frac{\partial a}{\partial u} \cdot \frac{\partial u}{\partial y} +
\frac{\partial f}{\partial u} \cdot \frac{\partial u}{\partial y} + 
\frac{\partial f}{\partial b} \cdot \frac{\partial b}{\partial v} \cdot \frac{\partial v}{\partial y} 
\tag{2.4}
$$

와 같이 chain rule을 이용해 표현할 수 있다.

## 3. BackPropagation Algorithm(역전파 알고리즘)

다시 아래 네트워크[(**2.1**)](#equation2.1)로 돌아오자.

$$
\begin{align*}
f(u,v) &= (u+v)^2\\
u(a,b) &= (a+b)^2, && v(a,b) = (a-b)^2, \\
a(w,x,y,z) &= (w+x+y+z)^2, && b(w,x,y,z) = (w+x-y-z)^2,
\end{align*}
\tag{3.1}
$$

$\frac{\partial f}{\partial w}$를 계산하고자 한다면, 설명한 chain-rule을 이용하면 된다.

$$
\begin{align*}\tag{3.2}
& \frac{\partial f}{\partial w} = 
\frac{\partial f}{\partial u} \cdot \frac{\partial u}{\partial w} + 
\frac{\partial f}{\partial v} \cdot \frac{\partial v}{\partial w} ,\\[0.7em]

& \frac{\partial u}{\partial w} = 
\frac{\partial u}{\partial a} \cdot \frac{\partial a}{\partial w} + 
\frac{\partial u}{\partial b} \cdot \frac{\partial b}{\partial w} ,\\[0.7em]

& \frac{\partial f}{\partial w} = 
\frac{\partial v}{\partial a} \cdot \frac{\partial a}{\partial w} + 
\frac{\partial v}{\partial b} \cdot \frac{\partial b}{\partial w} .\\
\end{align*}
$$

※ 방향에 주목하자. $\frac{\partial f}{\partial w}$를 구하기 위해, $\frac{\partial a}{\partial w}$ 부터 $f$로 올라가며 구해야 한다<Strong>(Input -> Output)</Strong>. 이를 <Strong>정방향</Strong>이라 정의한다.

그러나, 이는 여전히 $\frac{\partial f}{\partial x}$와 같은 것을 계산하는 것을 쉽게 하지 않는다. 그 이유는 chain-rule을 적용하는 <Strong>방향</Strong>에 있다.   
위의 방식은 분모(denominator)에 항상 $\partial w$가 있다. 이 방법으로 $w$가 다른 변수들을 어떻게 변화시키는지 관찰할 수 있다.   
그러나, deep learning에서 우리가 진짜 알고 싶은 것은 <Strong>어떻게 모든 변수가 *loss*를 변화</Strong>시키는지이다!   
따라서, $\partial f$를 분자(numerator)에 계속 유지하며 chain rule을 적용하자.

$$
\begin{align*}\tag{3.3}
\frac{\partial f}{\partial w} &= 
\frac{\partial f}{\partial a} \cdot \frac{\partial a}{\partial w} + 
\frac{\partial f}{\partial b} \cdot \frac{\partial b}{\partial w} ,\\[0.7em]

\frac{\partial f}{\partial a} &= 
\frac{\partial f}{\partial u} \cdot \frac{\partial u}{\partial a} + 
\frac{\partial f}{\partial v} \cdot \frac{\partial v}{\partial a} ,\\[0.7em]

\frac{\partial f}{\partial b} &= 
\frac{\partial f}{\partial u} \cdot \frac{\partial u}{\partial b} + 
\frac{\partial f}{\partial v} \cdot \frac{\partial v}{\partial b} .\\
\end{align*}
$$

※ 다시 방향에 주목해보면, $\frac{\partial f}{\partial u}$ 부터 $\frac{\partial f}{\partial w}$로 내려가며 계산한다<Strong>(Output -> Input)</Strong>. 이를 <Strong>역방향</Strong>이라 정의한다.

도함수를 입력->출력 순으로 계산하는 대신, $f$로부터 입력 쪽으로 역방향으로 계산한다. 이것이 이 알고리즘의 이름이 <Strong>역전파(BackPropagation)</Strong>인 이유다!  

역전파는 2가지 단계를 따른다.   
Step 1) $f$의 값을 구하고, 앞에서 뒤로 단일 단계 편미분(ex: $\frac{\partial f}{\partial u}$)을 구한다. (이들을 합치면 하나의 *forward pass*가 된다.)   
Step 2) $f$의 gradient를 뒤에서 앞으로(*backward pass*) 구한다.

이쯤에서 하나의 의문이 들 수 있다. 
역전파와 정방향 전파나 계산하는 것은 유사한데, 왜 역전파를 굳이 사용하는가? 이 포스팅은 수학적 이론에 대해서 다루고 있지만, 이 글을 쓰면서도 궁금했다!
이유가 궁금하다면 [부록1](#appendix1)을 참조하자.

## 4. Hessians(헤시안 행렬)

더 나은 근사를 위해서, gradient만을 사용하기보다 고차 미적분을 사용하는 것을 고려할 수 있다.

그러나 "개수가 너무 많다" 는 큰 문제가 있다. 만약 $f(x_1,...,x_n)$의 $n$변수 함수가 있으면, $n^2$의 이차도함수가 존재한다.

$$
\tag{4.1}
\frac{d^2f}{dx_idx_j} = \frac{d}{dx_i}\left(\frac{d}{dx_j}f\right)
$$

이를 *Hessian* 행렬로 묶을 수 있다.

$$
\tag{4.2}
H_f = 
\begin{bmatrix}
\frac{d^2f}{dx_1dx_1} & \cdots & \frac{d^2f}{dx_1dx_n} \\
\vdots & \ddots & \vdots \\
\frac{d^2f}{dx_ndx_1} & \cdots & \frac{d^2f}{dx_ndx_n}
\end{bmatrix}
$$

이 행렬의 모든 항이 독립인 것은 아니다. 실제로 mixed partials(혼합 편도함수, 2개 이상의 변수에 대한 편미분)이 존재하고 연속적이라면, 모든 $i$와 $j$에 대해 아래와 같이 쓸 수 있다.

$$
\tag{4.3}
\forall i,j,\ \frac{d^2f}{dx_idx_j} = \frac{d^2f}{dx_jdx_i} 
$$

※ 선형대수학의 *independent*와는 다르게, 여기에서 독립은 헤시안 행렬의 각 항목이 서로 종속되지 않고 고유한 정보(unique information)를 제공하는가에 대한 의미로 사용된다.   
※ 이유가 궁금하면, [부록2](#appendix2)의 클레로의 정리(Clairaut's theorem)을 참조하라.

이를 이용해, 특정 점 $\mathbf{x}_0$ 근처에서 가장 잘 맞는(근사하는) 이차함수(quadratic)을 찾을 수 있다.

아래 예시를 보자.

$$
\tag{4.4}
f(x_1,x_2) = a + b_1x_1 + b_2x_2 + c_{11}x_1^2 + c_{12}x_1x_2 + c_{22}x_2^2
$$

이는 2변수 이차함수의 일반적 표현이다. 영점에서의 함수값, gradient, hessian을 살펴보면,

$$
\begin{align*}\tag{4.5}
f(0,0) &= a \\
\nabla f(0,0) &= 
\begin{bmatrix}
b_1 \\ b_2
\end{bmatrix}
\\[0.8em]
\mathbf{H}f(0,0) &=
\begin{bmatrix}
2c_{11} & c_{12} \\
c_{12} & 2c_{22}
\end{bmatrix}
\end{align*}
$$

원래의 다항식을 벡터꼴로 되돌리자.

$$\tag{4.6}
f(\mathbf{x})=f(0)+\nabla f(0)\cdot \mathbf{x}\ + \frac{1}{2} \mathbf{x}^{\top}\mathbf{H}f(0)\mathbf{x}
$$

이를 일반화하면, 다변수함수의 2차 테일러 근사식이 도출된다.

$$\tag{4.7}
f(\mathbf{x})=f(\mathbf{x}_0)+\nabla f(\mathbf{x}_0)\cdot (\mathbf{x}-\mathbf{x}_0)\ + \frac{1}{2} (\mathbf{x}-\mathbf{x}_0)^{\top}\mathbf{H}f(\mathbf{x}_0)(\mathbf{x}-\mathbf{x}_0)
$$

## 5. A Little Matrix Calculus(행렬 미적분)

이 섹션에서는 행렬 미적분에 대해 다룬다. 개인적으로 이 부분을 모르고 머신러닝을 공부했을때 큰 어려움을 겪어서, 이 섹션의 중요도가 아주 높다고 생각한다. 이 섹션을 완벽하게 이해한다면, 앞으로 머신러닝을 배울 때 나오는 수식들에 대해 막연함만을 느끼는 일은 줄어들 것이라 생각한다.

* $f(\mathbf{x})=\boldsymbol{\beta}^{\top}\mathbf{x}$

행렬의 도함수를 다룰 때 유용한 표기법인 *"denominator layout matrix derivative"*(분모 배열 행렬 도함수)를 알아보자. 이는 편미분을 차례로 나열하여, 미분의 <Strong>분모</Strong>에 위치한 벡터/행렬/텐서 형태로 나타낸다. 쉽게 말해, 미분하는 변수(denominator)의 차원과 결과물의 차원을 맞추는 것이다.

고정된 열벡터 $\boldsymbol{\beta}$와 내적함수 $f(\mathbf{x})=\boldsymbol{\beta}^{\top}\mathbf{x}$ 에 대해,

$$\tag{5.1}
\frac{df}{d\mathbf{x}} = 
\begin{bmatrix}
\frac{df}{dx_1} \\
\vdots \\
\frac{df}{dx_n}
\end{bmatrix}
$$

$\mathbf{x}$의 차원과 $\frac{df}{d\mathbf{x}}$의 차원이 동일함에 주목하자.

함수를 구성 요소로 풀어서 정리하면,

$$\tag{5.2}
f(\mathbf{x}) = \sum_{i=1}^n \beta_ix_i = \beta_1x_1 + \cdots + \beta_nx_n
$$

각각의 변수에 대해 편미분을 취하자.

$$\tag{5.3}
\frac{df}{dx_1} = \beta_1 \quad \cdots \quad \frac{df}{dx_n} = \beta_n
$$

이를 다시 행렬로 표현하면 최종적으로 아래와 같다.

$$\tag{5.4}
\frac{df}{d\mathbf{x}} = 
\begin{bmatrix}
\frac{df}{dx_1} \\
\vdots \\
\frac{df}{dx_n}
\end{bmatrix} =
\begin{bmatrix}
\beta_1 \\
\vdots \\
\beta_n
\end{bmatrix} =
\boldsymbol{\beta}
$$

이 섹션에서 자주 볼 수 있는 행렬 미적분의 성질:

  1. 계산 과정은 복잡하다.
  2. 최종 결과는 깔끔하고, 1변수 미적분과 유사한 결과가 보일 것이다. $\left(\frac{d}{dx}(bx)=b, \frac{d}{dx}(\boldsymbol{\beta}^{\top}\mathbf{x}) = \boldsymbol{\beta}\right)$
  3. 뜬금없는 전치(transpose)가 나타날 수 있다. 이는 분모의 형상(차원)에 맞추기 위함이다.

직관을 가지고, 더 나아가보자.

* $\frac{d}{d\mathbf{x}}\left( \mathbf{x}^{\top} \boldsymbol{A} \mathbf{x} \right)$

<a id="note-5-1"></a>

계산의 중복을 줄이기 위해, Einstein notation(아인슈타인 표기법)을 사용하자.[<sup>[5-1]</sup>](#footnote-5-1)   
※ 아인슈타인 표기법이 뭐에요? [**부록3**](#appendix3)으로

$$\tag{5.5}
\mathbf{x}^{\top} \boldsymbol{A} \mathbf{x} = x_ia_{ij}x_j
$$

$x_k$에 대해 미분하면,

$$\tag{5.8}
\frac{d}{dx_k}\left(\mathbf{x}^{\top} \boldsymbol{A} \mathbf{x}\right) = \frac{d}{dx_k} x_ia_{ij}x_j
= \frac{dx_i}{dx_k}a_{ij}x_j + x_ia_{ij}\frac{dx_j}{dx_k} = a_{kj}x_j+x_ia_{ik}
$$

아인슈타인 표기법에서 인덱스의 이름은 임의적이다. $i$와 $j$가 다른 인덱스라는 것은 중요하지 않으므로, 둘 다 $i$로 인덱싱하자.

$$\tag{5.9}
\frac{d}{dx_k}x_ia_{ij}x_j = a_{kj}x_j+x_ia_{ik} = a_{ki}x_i+x_ia_{ik} = (a_{ki}+a_{ik})x_i
$$

※ $j$를 $i$로 바꿔도 되는 이유를 한동안 이해하지 못했다. 자세한 내용은 아인슈타인 표기법 챕터(부록 3)에서 다루겠지만, 아인슈타인 표기법의 아래첨자의 경우 가능한 모든 경우에 대한 합을 의미한다. 즉, $a_{ik}+a_{jk}$에 대해 $i$와 $j$가 모두 1,2,3 3가지 경우라고 한다면, $j$를 $i$로 바꾸더라도 결국 9가지의 다른 경우의 합을 의미하기 때문에, 변환해도 된다고 이해했다.

$a_{ki}+a_{ik}$는 $\boldsymbol{A} + \boldsymbol{A}^{\top}$의 $k$행 $i$열이다. 따라서

$$\tag{5.10}
\frac{d}{dx_k}x_ia_{ij}x_j = [\boldsymbol{A} + \boldsymbol{A}^{\top}]_{ki}x_i
$$

아인슈타인 표기법의 정의를 생각해 본다면, 위 식은 $\boldsymbol{A} + \boldsymbol{A}^{\top}$ 과 $\mathbf{x}$의 행렬곱의 $k$번째 행을 뜻한다는 것을 알 수 있다.

$$\tag{5.11}
\left[ \frac{d}{d\mathbf{x}}(\mathbf{x}^{\top}\boldsymbol{A}\mathbf{x}) \right]_k = 
\frac{d}{dx_k}x_ia_{ij}x_j
= [(\boldsymbol{A} + \boldsymbol{A}^{\top})\mathbf{x}]_k
$$

좌항과 우항의 $k$번째 행이 모두 같으므로, 두 값이 같다. 최종적으로 다음을 얻을 수 있다.

$$\tag{5.12}
\frac{d}{d\mathbf{x}}(\mathbf{x}^{\top}\boldsymbol{A}\mathbf{x}) = (\boldsymbol{A} + \boldsymbol{A}^{\top})\mathbf{x}
$$

계산 과정이 정말 복잡했지만, 최종 결과는 매우 간단하다. 더 나아가서, 1변수 미분의 케이스를 보자.

$$\tag{5.13}
\frac{d}{dx}(xax) = \frac{dx}{dx}ax + xa\frac{dx}{dx} = (a+a)x
$$

다시, 단변수일때의 결과와 유사하지만 전치가 포함된 결과를 얻었다.

그런데 왜 이러한 패턴이 보일까? 왜 그런지 알아보자. 행렬 미분을 할 때, 결과값이 또 다른 행렬 표현이라고 가정해보자. 즉, 행렬과 그 전치 행렬의 곱과 합으로 표현할 수 있는 형태가 될 것이다. 그러한 표현이 존재한다면, 모든 행렬에 대해 성립해야 한다. 
특히 1x1행렬(scalar)에 대해서도 성립해야 하는데, 이 경우 행렬곱은 숫자곱에 불과하고, 행렬 합은 단순 덧셈이며, 전치 연산은 아무 영향을 미치지 않는다. 
즉, 우리가 얻는 어떤 표현이든 단일 변수 표현과 일치해야 한다! 이는 어느 정도 연습을 하면, 관련 단일 변수 표현을 알고 있는 것만으로도 종종 행렬 미분을 추측할 수 있다는 뜻이다.

* $\frac{\partial}{\partial \boldsymbol{V}}\|\boldsymbol{X}-\boldsymbol{U}\boldsymbol{V}\|_2^2$ 

$\boldsymbol{X}$는 $n \times m$행렬, $\boldsymbol{U}$는 $n \times r$행렬, $\boldsymbol{V}$는 $r \times m$행렬이다. 

※ 이 계산은 *Matrix Factorization(행렬 분해)* 영역에서 중요한 역할을 한다. 그러나, 지금은 그냥 미분 계산에 불과하다.

1 $\times$ 1 행렬(scalar)에서는 어떠할지 생각해 보자. 

$$\tag{5.14}
\frac{d}{d v}(x-uv)^2 = -2(x-uv)u
$$

행렬 표현으로 전환하면 아래와 같다.

$$\tag{5.15}
\frac{\partial}{\partial \boldsymbol{V}}\|\boldsymbol{X}-\boldsymbol{U}\boldsymbol{V}\|_2^2
=
-2(\boldsymbol{X}-\boldsymbol{U}\boldsymbol{V})\boldsymbol{U}
$$

그러나, 이는 정확하지 않다. $(\boldsymbol{X}-\boldsymbol{U}\boldsymbol{V})$는 $n\times m$ 행렬이고, $\boldsymbol{U}$는 $n\times r$ 행렬이다.
행렬곱을 하기에 차원이 맞지 않는다.

구하고자 하는 대상은 $\frac{d}{d\boldsymbol{V}}$이다. *Denominator layout matrix derivative* 표기법에 따라 결과값의 차원은 $\boldsymbol{V}$와 같은 $r \times m$이여야 한다. 
따라서 전치를 포함해 $n\times m$행렬과 $n\times r$행렬을 곱해 $r\times m$행렬을 얻어야 한다. 
방법은,  $\boldsymbol{U}^{\top}$를 $(\boldsymbol{X}-\boldsymbol{U}\boldsymbol{V})$에 곱하는 것이다.
따라서 추정한 답은 아래와 같다.

$$\tag{5.16}
\frac{d}{d \boldsymbol{V}}\|\boldsymbol{X}-\boldsymbol{U}\boldsymbol{V}\|_2^2
=
-2\boldsymbol{U}^{\top}(\boldsymbol{X}-\boldsymbol{U}\boldsymbol{V})
$$

과연 이 답이 맞을까? 직접 계산해보자.

모든 $a$와 $b$에 대해, 아래 값을 찾아야 한다.

$$\tag{5.17}
\frac{d}{d v_{ab}}\|\boldsymbol{X}-\boldsymbol{U}\boldsymbol{V}\|_2^2
=
\frac{d}{d v_{ab}}\sum_{i,j}\left(x_{ij}-\sum_k u_{ik}v_{kj}\right)^2
$$

$\boldsymbol{V}_{ab}$에 대한 미분이므로, $\boldsymbol{X}$와 $\boldsymbol{U}$의 모든 항은 상수 취급할 수 있다. 따라서,

$$\tag{5.18}
\frac{d}{d v_{ab}}\|\boldsymbol{X}-\boldsymbol{U}\boldsymbol{V}\|_2^2
=
\sum_{i,j}2\left(x_{ij}-\sum_k u_{ik}v_{kj}\right)\left(-\sum_k u_{ik} \frac{d v_{kj}}{d v_{ab}}\right)
$$

$\frac{d v_{kj}}{d v_{ab}}$는 $k=a$, $j=b$일 때만 0이 아니므로, 아래와 같이 정리할 수 있다.

$$\tag{5.19}
\frac{d}{d v_{ab}}\|\boldsymbol{X}-\boldsymbol{U}\boldsymbol{V}\|_2^2
=
-2\sum_{i}\left(x_{ib}-\sum_k u_{ik}v_{kb}\right)u_{ia}
$$

<a id="note-5-2"></a>

중요한 미묘한 점은, $k=a$라는 조건이 내부 합$\left(\sum_k u_{ik}v_{kj}\right)$에서는 발생하지 않는다는 것이다!
이는 $k$가 내부 항에서 합산되는 더미 변수이기 떄문이다. 보다 자세한 이해를 위해, 아래를 참조하라.[<sup>[5-2]</sup>](#footnote-5-2)

$$\tag{5.20}
\frac{d}{dx_1}\left(\sum_i x_i\right)^2 = 2\left(\sum_i x_i\right)
$$

행렬 표현으로 나타내보자.

$$\tag{5.21}
\sum_k u_{ik}v_{kb} = [\boldsymbol{U}\boldsymbol{V}]_{ib}
$$

따라서 $\sum_i$안의 전체 표현은

$$\tag{5.22}
x_{ib}-\sum_k u_{ik}v_{kb} = [\boldsymbol{X}-\boldsymbol{U}\boldsymbol{V}]_{ib}
$$

전체 미분을 나타내면,

$$\tag{5.23}
\frac{d}{d v_{ab}}\|\boldsymbol{X}-\boldsymbol{U}\boldsymbol{V}\|_2^2
=
-2 \sum_i [\boldsymbol{X}-\boldsymbol{U}\boldsymbol{V}]_{ib} u_{ia}
$$

이전 예제처럼 각 행과 열의 요소가 같으므로 좌변과 우변은 같은 행렬이라는 결론을 내기 위해, 우변이 행렬의 $a$, $b$ 요소로 표현되면 좋을 것 같다.
$u_{ia}=[\boldsymbol{U}^{\top}]_{ai}$ 이므로, 아래와 같이 인덱스를 바꿔 표현할 수 있다.

$$\tag{5.24}
\frac{d}{d v_{ab}}\|\boldsymbol{X}-\boldsymbol{U}\boldsymbol{V}\|_2^2
=
-2 \sum_i [\boldsymbol{U}^{\top}]_{ai} [\boldsymbol{X}-\boldsymbol{U}\boldsymbol{V}]_{ib}
$$

잘 보면, 위는 행렬곱을 풀어서 쓴 식이다. 따라서

$$\tag{5.25}
\frac{d}{d v_{ab}}\|\boldsymbol{X}-\boldsymbol{U}\boldsymbol{V}\|_2^2
=
-2 [\boldsymbol{U}^{\top}(\boldsymbol{X}-\boldsymbol{U}\boldsymbol{V})]_{ab}
$$

따라서, 최종 결과는 아래와 같다.

$$\tag{5.26}
\frac{d}{d \boldsymbol{V}}\|\boldsymbol{X}-\boldsymbol{U}\boldsymbol{V}\|_2^2
=
-2 \boldsymbol{U}^{\top}(\boldsymbol{X}-\boldsymbol{U}\boldsymbol{V})
$$

추정한 값과 실제 계산 결과가 일치하는 것을 확인할 수 있다!

---

<a id="footnote-5-1"></a> [[5-1]](#note-5-1)
여기서부터 혼돈이 올 수 있으므로, 간단한 예시를 들어 직접 계산해보자.

$$\tag{5.6}
\mathbf{x} = 
\begin{bmatrix}
x_1 \\ x_2
\end{bmatrix}
,\ 
\boldsymbol{A} = 
\begin{bmatrix}
a_{11} & a_{12} \\
a_{21} & a_{22}
\end{bmatrix},
$$

일 때,

$$\tag{5.7}
\begin{align*}
\mathbf{x}^{\top} \boldsymbol{A} \mathbf{x} 
&=
\begin{bmatrix}
x_1 & x_2
\end{bmatrix}
\begin{bmatrix}
a_{11} & a_{12} \\
a_{21} & a_{22}
\end{bmatrix}
\begin{bmatrix}
x_1 \\ x_2
\end{bmatrix} \\
&=
\begin{bmatrix}
x_1 & x_2
\end{bmatrix}
\begin{bmatrix}
a_{11}x_1 + a_{12}x_2 \\ a_{21}x_1 + a_{22}x_2
\end{bmatrix} \\
&= x_1a_{11}x_1 + x_1a_{12}x_2 + x_2a_{21}x_1 + x_2a_{22}x_2 \\
&= \sum_{i=1}^2 \sum_{j=1}^2 x_ia_{ij}x_j
\end{align*}
$$

따라서 아인슈타인 표기법으로 나타내면 (수식 5.5)와 같다.

<a id="footnote-5-2"></a> [[5-2]](#note-5-2)
근데, 더 명확한 이해를 위해 애초에 아래와 같이 표시하면 안되나?

$$
\frac{d}{d v_{ab}}\|\boldsymbol{X}-\boldsymbol{U}\boldsymbol{V}\|_2^2
=
\sum_{i,j}2\left(x_{ij}-\sum_k u_{ik}v_{kj}\right)\left(-\sum_p u_{ip} \frac{d v_{pj}}{d v_{ab}}\right)
$$

이부분에 대해서 잘 아시는 분은 댓글 달아주시면 감사하겠습니다.

<a id="appendix1"></a>
## Appendix 1. 그래서 역전파가 왜 효율적인가?

<a id="appendix2"></a>
## Appendix 2. Clairaut's theorem(클레로의 정리)

<a id="appendix3"></a>
## Appendix 3. Einstein notation(아인슈타인 표기법) 