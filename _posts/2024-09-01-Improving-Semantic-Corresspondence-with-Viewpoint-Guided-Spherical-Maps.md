---
layout: post
author: noonddudung2
tags: [CVPR 2024 oral, semantic correspondence]
use_math: true
published: false

---
<!-- # <span style="color: black; background-color: #ffebb4"> [논문리뷰] Improving Semantic Correspondence with Veiwpoint-Guided Spherical Maps</span> -->
## <span style="color: black; background-color: #ffd1df">Paper</span>
* Paper: [SC paper](https://arxiv.org/pdf/2312.13216)
* Github: [SC Code](https://github.com/VICO-UoE/SphericalMaps)

---

## <span style="color: black; background-color: #ffd1df">Background</span>
* Self-**di**stillation with **no** labels (DINO)
* SSL
* Semantic Corresspondence (SC)


---

## <span style="color: black; background-color: #ffd1df">Abstract</span>

---

## <span style="color: black; background-color: #ffd1df">Introduction</span>
* 문제점: 3D understanding이 없는 simple part detecting SSL SC 방식
  1. **object symmetries** : 대칭의 object를 보여주었을 때 -> reflected version of feature map 만듦
  2. **repeated structures** : 비슷하게 보이는 부분들에 대해 비슷한 특징으로 인식
  
* 해결책
  1. seperate visually similar parts (**repeated structures**) by mapping them to different locationon a sphere
  2. simple genomtric constraints during the training
  3. new evaluation protocol: PCK to KAP

---

## <span style="color: black; background-color: #ffd1df">Related Work</span>
* Image Matching: 다른 view에서 같은 부분 찾기
* Semantic Correspondence (SC)
  * 다른 view에서 같은 object instance 찾기만 하는 것이 아닌, 같은 object category의 instances 찾기
* 3D priors for correspondence
  * 보통 annotaed meshes 사용해서 SC
  * mesh가 없는 경우 precise camera pose를 통해서 SC
  * 이 논문에서는 mesh 없이 coarse viewpoint supervision으로도 SC 가능

---

## <span style="color: black; background-color: #ffd1df">Method</span>
### <span style="color: black; background-color: #ffe4e1">Problem statement</span>
* General Formulation
  * 목적: learn a mapping $f(I,x)=z \ or \ f(I)=z$로 표시
    * $I: \Lambda \rightarrow \mathbb{R}^3, \Lambda \subset \mathbb{R}^2$
    * a pixel location: $ x \in \Lambda$
    * n-dimensional embedding space $ z \in \mathcal{Z} \subset \mathbb{R}^n$
  
  * $f(I,x) = f(I',x')$ when $x \leftrightarrow x'$ same semantic part and instance
    * $argmin_{x'}d_{\mathcal{Z}(f(I,x),f(I',x'))}$

* Spherical mapping
  * simplified the 3D structure to a sphere $S^2 \subset \mathbb{R}^3$
    * $S$는 geodesic distance 제공하여 계산을 단순하게 만들어 줌
    * 따라서 이 논문에서는 $f_{S}(I,x)$ *spherical mapping*이 목적

### <span style="color: black; background-color: #ffe4e1">Learning formulation</span>
![sc_pipeline](images/sc/sc_pipeline.png)
![sc_eq1](images/sc/sc_eq1.png)
* $\phi: \Lambda \rightarrow \mathcal{Z}_{\phi} $: pretrained SSl model
* spherical prototype $S_{\mathcal{Z}}: S \rightarrow \mathcal{Z}$
* $M(I,x)$ : object's sufrace에 만 적용되어서 loss computation 제한
* $\Gamma (\phi(I,x),S_{\mathcal{Z}(f_S(I,x))})$: SSL pretrained model의 z location sphere z location 간의 차이를 줄이기 위한 목적
  * $\Gamma$: cosine distance
  * $S_{\mathcal{Z}}$: ViT self attention 없이 cross attention 으로 cateogry condition으로 넣어서 사용

---

### <span style="color: black; background-color: #ffe4e1">Summary & Review/span>
* 원래 SC task에서 어떤 방식으로 했는지를 정확히 모르니까 차이점을 알기가 어려운 듯


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
