---
layout: post
author: noonddudung2
tags: [CVPR 2024]
use_math: true
published: true

---
<!-- # <span style="color: black; background-color: #ffebb4"> [논문리뷰] Improving Semantic Correspondence with Veiwpoint-Guided Spherical Maps</span> -->
## <span style="color: black; background-color: #ffd1df">Paper</span>
* Paper: [Telling Left from Right](https://telling-left-from-right.github.io/files/Telling_Left_from_Right_cr.pdf)
* Github: [Telling Left from Right code](https://github.com/Junyi42/geoaware-sc)

---

## <span style="color: black; background-color: #ffd1df">Abstract</span>
* 문제점
  1. struggle to grasp the gemoetry and orientation of instances (오른쪽 vs. 왼쪽 같이 방향을 잘 모름)
  2. limitation of the current models under simple post-processing
* 해결책 & contributions
  1. improve geometric awareness of the features
  2. benchmark=
  3. SOTA


---

## <span style="color: black; background-color: #ffd1df">Introduction</span>
* Semantic Correspondence
  * 정의: the establishment of pixel-level matches between two imnages with semantically similar objects
  * 문제: underperform on "geometry-aware" semantic coresspondences (ex. 오른쪽 왼쪽 구분 못함)
* "geometry-**ambiguous** matching" **innate** failure or can be **alleviated through post processing**?
  * 해결책
    1. test-time viewpoint alignmnt startegy (viewpoint 똑같게 맞추기)
    2. train a lightweight post-processing module by using soft-argmax based dense training objective
    3. pose-vairant augmentation startegy, window soft-argmax module

---

## <span style="color: black; background-color: #ffd1df">Related Work</span>
* Semantic Correspondence
  1. feature extraction
  2. cost volume computation
  3. mathicng field regression

---

## <span style="color: black; background-color: #ffd1df">Geometric Awareness of Deep Features (분석 느낌)</span>
* Geometry-Aware Semantic Correspondence
  * 정의: challenging case of semantic correspondence, requires an understanding of instances' orientations or geometry
    * **semantic + orientation understanding이 복합적으로 필요한 cases**
      1. cluster keypoints into subgroups by semantic parts: $\mathcal{G}_{parts}$
        * parts = {ears, paws,...}
      2. subgroup consists of $p_{(parts,index)}$
        * $p_{(paws,front \ left)}$
      3. $<p_i^s,p^t_i>$ correspondence two conditions
        * same subgroup
        * there are other visible keypoints belong to the same subgroup
* Evaluation on the Geometry-aware Subset
  ![evaluation](/images/zeroshotsc/evaluation.png)
  * SPair-71k dataset의 60% 정도가 위의 조건을 만족하는 geometry-aware subset dataset임에도 불구하고, **Geo** set이 standard set보다 PCK 결과가 모든 method에 대해서 현저히 낮음 -> 그만큼 이런 set에 대해서 잘 못한다는 뜻
* Sensitivity to Pose Variation: standard set보다 geo가 더 민감
* Global Pose Awareness of Deep Features
  * Instance matching distance (IMD) : pose prediction accuracy metric (포즈 별로 instance와의 distance 차이를 계산해서 가장 distance가 적은 pose로 예측)
    $IMD(I^s,I^t,M^s)=\sum_{p \in M^s}{\lvert\lvert F^s(p)-NN(F^s(p).F^t)\rvert \rvert_2}$
      * p: a pixel within the source instance mask
      * $F^s(p)$: feature vector at p
      * $NN(F^s(p),F^t)$: nearest-neighboring feature vector 
  * Pose prediction via IMD
    ![IMD](images/zeroshotsc/IMD.png)

---

## <span style="color: black; background-color: #ffd1df">Improving Geo-Aware Correspondence (방법 제시) </span>
* Test-time **Adaptive Pose Alignment**
  * augmentation하면서 IMD가 minimum 되는 지점을 찾아서 align viewpoint 
  ![adaptive](images/zeroshotsc/adaptive.png)

* Dense Training Objective
![pipeline](images/zeroshotsc/pipeline.png)
  * $\mathcal{L}= \mathcal{L_{dense}} + \mathcal{L_{sparse}}$
    * $\mathcal{L_{sparse}}=CL(\tilde{F}^s(P^s),\tilde{F}^t(P^t))$ : contrastive loss, potentially neglects additional informative features
    * $\mathcal{L_{dense} = \sum_i{\lvert\lvert \hat p^t_i - (p^t_i + \epsilon)\rvert \rvert _2}}$
      * $\hat p^t_i$: predicted point position
      * $p^t_i + \epsilon$: perturbed G.T. position

* Pose-variant Augmentation: self flip - source img and flppied src img
* Window Soft Argmax: sub-pixel reasoningm prevents it from nosiy response

---

## <span style="color: black; background-color: #ffd1df">Limitations </span>
* small instances
* extreme pose variations

---

## <span style="color: black; background-color: #ffd1df">Summary & Review </span>
* 문제 정의: T2I SD의 "geometry-aware" 부족
* geometry-aware set을 정의해서 standard set과 비교를 통해 이 문제가 잘 해결되고 있지 않음을 증명/뷴석 (3) 후 방법 제시 (4)
* 방법 제시: pose prediction, loss 식 추가

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
