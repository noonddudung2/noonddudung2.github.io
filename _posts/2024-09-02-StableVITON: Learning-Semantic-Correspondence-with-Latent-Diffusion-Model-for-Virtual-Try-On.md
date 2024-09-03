---
layout: post
author: noonddudung2
tags: [CVPR 2024, T2I, geo-aware]
use_math: true
published: true

---
<!-- # <span style="color: black; background-color: #ffebb4"> [논문리뷰] Improving Semantic Correspondence with Veiwpoint-Guided Spherical Maps</span> -->
## <span style="color: black; background-color: #ffd1df">Paper</span>
* Paper: [StableVITON]()
* Github: [StableVITON code]()

---

## <span style="color: black; background-color: #ffd1df">Abstract</span>
* 문제점
  1. preserve the clothing details
  2. utilizing the robust generative capability
* 해결책 & contribution: SC between the colthing and the human body
  1. first **end-to-end** virtual try-on method without independent warping process
  1. **zero cross-attention blocks**
  2. attention total variation loss

---

## <span style="color: black; background-color: #ffd1df">Introduction</span>
* preserving the clothing details <-> using the pretrained diffusion model

---

## <span style="color: black; background-color: #ffd1df">Method</span>
![pipeline](images/stableviton/pipeline.png)
### <span style="color: black; background-color: #ffe4e1">Model Overview</span>
* clothing-agnostic map: 의류 정보를 제거한 사람의 image
  * a person image: $x \in \mathbb{R}^{H \times W \times 3}$
  * clothing-agnoostic map: $x_a \in \mathbb{R}^{H \times W \times 3}$
* notation
  * $z_t$: the noisy iamge
  * $\mathcal{E}(x_a)$: latent agnostic map
  * $x_{m_a}$: resized clothing-agnositc mask
  * $\mathcal{E}(x_p)$: latent dense pose condition
  * $\mathcal{E}(x_c)$: spatial encoder (SD encoder), fine details 

### <span style="color: black; background-color: #ffe4e1">StableVITON</span>
* Zero Cross-Attention Block
  * Q: previous self-attention layer (human body) <-> K,V: spatial encoder's feature map (clothing)
  * fails to learn the exact semantic correspondence between query and key tokens
* Augmentation: random shifts to input condiiton
  ![stableviton_eq1](images/stableviton/stableviton_eq1.png)
* Attetion Total Variation (ATV) Loss
  * 문제:cross attention ma에서 scores가 분산되어서 나타남
  * coordinate map $F \in \mathbb{R}^{H_1 \times W_1 \times 2}$:
    * $F_{ijn} = \frac{1}{h_k w_k}\sum_{k=1}^{h_k}\sum_{l=1}^{w_k}(A_{ijkl}\odot G_{kkln})$
  * $\mathcal{L}_{ATV}=\ \lvert\lvert \nabla (F \odot M)\rvert\rvert_1$
  ![stablevition_eq2](images/stableviton/stableviton_eq2.png)  



---

## <span style="color: black; background-color: #ffe4e1">Summary & Review</span>
* diffusion model의 문제점을 해결하고자 semantic correspondence를 활용
* diffusion try on이 아니더라도 어떤 task가 있는데 문제점이 spatial information이 사라지는 것이고 -> semantic correspondence를 사용하면 문제가 개선된다면?

<!-- 
# Sample heading 1

## Sample heading 2
å
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
