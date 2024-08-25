---
layout: post
author: noonddudung2
tags: [MAMBA, ICML 2024]
use_math: true
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
* [Mamba](https://tulip-phalange-a1e.notion.site/05f977226a0e44c6b35ed9bfe0076839)
    * ![Mamba](/images/mamba.png)
    
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
    
[^4]: this is a very very long footnote to test if a very very long footnote brings some problems or not; hope that there are no problems but you know sometimes problems arise from nowhere.
