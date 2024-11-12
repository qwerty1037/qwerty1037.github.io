---
layout: single
title: "Multivariable Culculus"
use_math : true
categories: machine_learning
tags: machine_learning
toc: true
toc_sticky: true
---

# MultiVariable Calculus

이 포스팅은 ML을 공부하기 전 알아두면 매우 유용한 다변수 미적분에 대해 다룬다.

아래 책의 내용을 공부하고, 이를 한글로 번역/정리하였다.   
https://d2l.ai/chapter_appendix-mathematics-for-deep-learning/multivariable-calculus.html

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
&\quad + \epsilon_2 \frac{\partial}{\partial w_2} L(w_1,w_2+\epsilon_2,\cdots,w_n) \\
&\quad + \epsilon_1 \frac{\partial}{\partial w_1}  L(w_1,w_2,\cdots,w_n) \\
&\quad + \cancel{\epsilon_1 \epsilon_2 \frac{\partial}{\partial w_1} \frac{\partial}{\partial w_2} L(w_1,w_2,\cdots,w_n)}{} \\
\end{align*}
$$

<strong>higher_order_term(고차항)</strong>은 생략 가능하다. 위를 $N$개의 모든 $w$에 대해 반복하면 다음과 같다.

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

$$
\begin{align*}
f(u,v) &= (u+v)^2\\
u(a,b) &= (a+b)^2, && v(a,b) = (a-b)^2, \\
a(w,x,y,z) &= (w+x+y+z)^2, && b(w,x,y,z) = (w+x-y-z)^2,
\end{align*}
\tag{1.6}
$$

이를 Neural Network로 시각화하면 아래와 같다.

[TODO: 이미지]

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
\tag{1.7}
$$

이를 아래와 같이 축약하여 표현할 수 있다.

$$
\frac{\partial f}{\partial a} = \frac{\partial f}{\partial u} \frac{\partial u}{\partial a} + \frac{\partial f}{\partial v}\frac{\partial v}{\partial a}
\tag{1.8}
$$

