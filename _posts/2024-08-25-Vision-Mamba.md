---
layout: post
author: noonddudung2
tags: [MAMBA, ICML 2024]
use_math: true
published: false

---
# <span style="color: black; background-color: #ffebb4"> [논문리뷰] Vision Mamba: Efficient Visual Representation Learning with Bidirectional State Space Model</span>

* Paper: [Vision Mamba](https://arxiv.org/abs/2401.09417)
* Github: [Vision Mamba Code](https://github.com/hustvl/Vim)

---

## <span style="color: black; background-color: #ffd1df">Abstract</span>

* 문제점
  * visual data는 position-sensitivity 존재 & global context of visiual understanding 필요
  * 그러나 state space models (SSMs)는 이게 어려웠음
* 제시하는 방법: Vision Mamba (Vim)
  * marks the image sequences with **<u>position emebddings</u>**
  * compresses the visiual represntation with **<u>bidrectional SSMs</u>**
* 장점
  * computation & memory efficiency

---

## <span style="color: black; background-color: #ffd1df">Background</span>
* [Intro to State Space Models (SSM)](https://huggingface.co/blog/lbourdois/get-on-the-ssm-train)
    * ![SSM](/images/SSM.png)
    * 상태 벡터 (x, y, distance to exit)
    * 상태 공간 모델 (SSMs): 상태 표현을 설명하고, 입력에 따라 다음 상태가 어떻게 될 지 예측
    * **목적: input에서 output으로 이동할 수 있도록 상태표현 $\mathbf{h_t}$를 찾는 것**
    * ![SSM_calc](/images/ssm_calc.png)

    * $\mathbf{h'(t)}=\mathbf{A}\mathbf{h(t)}+\mathbf{B}\mathbf{x(t)}$
      * $\mathbf{x(t)}$: input sequence (ex. 미로에서 왼쪽, 아래로 움직임)
      * $\mathbf{h(t)}$: distance to exit, x, y
      * $\mathbf{A}\mathbf{h(t)}$ $\mathbf{(h(t): current \ state)}$ : h(t)가 시간에 따라서 어떻게 evolve 되는지
      * $\mathbf{Bx(t) (x(t):input)}$: $\mathbf{x(t)}$ 가 state에 어떻게 영향을 미치는지
    * $\mathbf{y(t)}=\mathbf{C}\mathbf{h(t)}$
      * $\mathbf{y(t)}$: predicted output (ex. 출구로 더 빨리 도달하기 위해 왼쪽으로 이동)
      * $\mathbf{Ch(t)}$: current state가 output에 어떻게 영향을 미치는지

    * Discretization with zero-order hold (ZOH) - 텍스트 같은 데이터는 이산화된 데이터기 때문에 연속적인 데이터를 이산화하는 과정이 필요함
      * $\mathbf{\bar{A} = exp(\Delta{A})} \ (\mathbf{\Delta}: timescale \ parameter)$
      * $\mathbf{\bar{B}={\Delta A}^{-1}(exp(\Delta A)-I)\cdot{\Delta B}}$
    * Discretization System (시간에 따른 중요성을 강조하기 위한 matrix)
      * $\mathbf{h_{t} = \bar{A} h_{t-1}+\bar{B}x_{t}}$
      * $\mathbf{\bar{y}=\mathbf{C}h_{t}}$
    
    * 재귀적 표현
      * ![SSM_recurrent](/images/ssm_recurrent.png)

    * Convolution
      * $\mathbf{\bar{K}=(C\bar{B},C{\bar{AB}},...,C{\bar{A}}^{M-1}{\bar{B}})}$
      * $\mathbf{y=x\times{\bar{K}}}$
      * $\mathbf{\bar{K} \in R^M \ M :input \ sequence의 \ 길이}$



* [Mamba](https://tulip-phalange-a1e.notion.site/05f977226a0e44c6b35ed9bfe0076839)
    * ![Mamba](/images/mamba.png)
    * transformer처럼 훈련을 병렬화 & 추론이 선형적으로 (빠르게) 이루어지는 아키텍처가 Mamba
    * 

---

## <span style="color: black; background-color: #ffd1df">Method</span>
### <span style="color: black; background-color: #ffe4e1">Preliminaries</span>

일단 이해는 안되지만 어쨌든 간에 continuous $\rightarrow$ discrete하게 만들고 그거를 convolution을 이용해서 output을 예측할 수 있다는 거인듯? 당연한건가 암튼...

* Continuous System
  * $\mathbf{h'(t)}=\mathbf{A}\mathbf{h(t)}+\mathbf{B}\mathbf{x(t)}$
  * $\mathbf{y(t)}=\mathbf{C}\mathbf{h(t)}$
* Notation
  * $\mathbf{A} \in \mathbb{R^{N \times N}},\mathbf{B} \in \mathbb{R^{N \times 1}},\mathbf{C} \in \mathbb{R^{1 \times N}}$
  * $\mathbf{h(t)} \in \mathbb{R^{N \times 1}}$
  * $\mathbf{x(t) \in \mathbb{R} \rightarrow{y(t)} \in \mathbb{R}}$
* Discretization with zero-order hold (ZOH)
  * $\mathbf{\bar{A} = exp(\Delta{A})} \ (\mathbf{\Delta}: timescale \ parameter)$
  * $\mathbf{\bar{B}={\Delta A}^{-1}(exp(\Delta A)-I)\cdot{\Delta B}}$
* Discretization System (시간에 따른 중요성을 강조하기 위한 matrix)
  * $\mathbf{h_{t} = \bar{A} h_{t-1}+\bar{B}x_{t}}$
  * $\mathbf{\bar{y}=\mathbf{C}h_{t}}$
* Output **y** w/ global conv: Linear Time Invariance (LTI)
  * $\mathbf{\bar{K}=(C\bar{B},C{\bar{AB}},...,C{\bar{A}}^{M-1}{\bar{B}})}$
  * $\mathbf{y=x\times{\bar{K}}}$
  * $\mathbf{\bar{K} \in R^M \ M :input \ sequence의 \ 길이}$

---

### <span style="color: black; background-color: #ffe4e1">Vision Mamba</span>
![vim](/images/vim_overview.png)
* 2-D image: $ \mathbf{t \in \mathbb{R}^{H \times W \times C} \rightarrow x_p \in \mathbb{R}^{J \times (P^2 \cdot C)}}$
  * $\mathbf{P}$ : patch size
  * $\mathbf{J}$ : 전체 patch 수 아마도 $\mathbf{\frac{H}{P},\frac{W}{P}}$
  * $\mathbf{x_p \in \mathbb{R}^{J \times D}}$ 인데, $\mathbf{t_{cls}}$ 가 추가되어서 아래처럼 +1 된 듯
  * $\mathbf{E_{pos} \in \mathbb{R}^{(J+1)} \times D}$

![vim_calc](/images/vim.jpeg)

* $\mathbf {T_l = Vim(T_{l-1})+T_{l-1}}$  
* $\mathbf{f = Norm(T^0_{L}) \\ (T^0_L: L번째 \\ 레이어의 \\ class \\ token \\ output)}$
* $\mathbf{\hat{p} = MLP(f)} \\ \mathbf{(final \\ prediction \\ \hat p)}$

---

### <span style="color: black; background-color: #ffe4e1">Vim Block</span>


<!-- 
# Sample heading 1

## Sample heading 2

### Sample heading 3

#### Sample heading 4

##### Sample heading 5

###### Sample heading 6

Mauris viverra dictum ultricies. Vestibulum quis ipsum euismod, facilisis metus sed, varius ipsum. Donec scelerisque lacus libero, eu dignissim sem venenatis at. Etiam id nisl ut lorem gravida euismod.

## Lists

Unordered:

- Fusce non velit cursus ligula mattis convallis vel at metus[^2].
- Sed pharetra tellus massa, non elementum eros vulputate non.
- Suspendisse potenti.

Ordered:

1. Quisque arcu felis, laoreet vel accumsan sit amet, fermentum at nunc.
2. Sed massa quam, auctor in eros quis, porttitor tincidunt orci.
3. Nulla convallis id sapien ornare viverra.
4. Nam a est eget ligula pellentesque posuere.

## Blockquote

The following is a blockquote:

> Suspendisse tempus dolor nec risus sodales posuere. Proin dui dui, mollis a consectetur molestie, lobortis vitae tellus.

## Thematic breaks (<hr>)

Mauris viverra dictum ultricies[^3]. Vestibulum quis ipsum euismod, facilisis metus sed, varius ipsum. Donec scelerisque lacus libero, eu dignissim sem venenatis at. Etiam id nisl ut lorem gravida euismod. **You can put some text inside the horizontal rule like so.**

---

{: data-content="hr with text"}

Mauris viverra dictum ultricies. Vestibulum quis ipsum euismod, facilisis metus sed, varius ipsum. Donec scelerisque lacus libero, eu dignissim sem venenatis at. Etiam id nisl ut lorem gravida euismod. **Or you can just have an clean horizontal rule.**

---

Mauris viverra dictum ultricies. Vestibulum quis ipsum euismod, facilisis metus sed, varius ipsum. Donec scelerisque lacus libero, eu dignissim sem venenatis at. Etiam id nisl ut lorem gravida euismod. Or you can just have an clean horizontal rule.

## Code

Now some code:

```
const ultimateTruth = 'follow middlepath';
console.log(ultimateTruth);
```

And here is some `inline code`!

## Tables

Now a table:


| Tables        |      Are      |  Cool |
| --------------- | :-------------: | ------: |
| col 3 is      | right-aligned | $1600 |
| col 2 is      |   centered   |   $12 |
| zebra stripes |   are neat   |    $1 |

## Images

![theme logo](http://www.abhinavsaxena.com/images/abhinav.jpeg)

This is an image[^4]

---

{: data-content="footnotes"}

[^1]: this is a footnote. You should reach here if you click on the corresponding superscript number.
    
[^2]: hey there, don't forget to read all the footnotes!
    
[^3]: this is another footnote.
    
[^4]: this is a very very long footnote to test if a very very long footnote brings some problems or not; hope that there are no problems but you know sometimes problems arise from nowhere. -->
