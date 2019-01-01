---
title: Collaborative Filtering for Implicit Feedback on Browsing Hits Data
tags: [data science, recommendation system]
categories: [Notes]
date: 2018-03-17 11:32:30
---

Recently, I worked on recommendation system in SSENSE. I have employed the **collaborative filtering** algorithm based on **implicit feedback**, which is the number of hits per item.

The methodology part is based on:

> Hu, Yifan, Yehuda Koren, and Chris Volinsky. ["Collaborative filtering for implicit feedback datasets."](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.167.5120&rep=rep1&type=pdf) Data Mining, 2008. ICDM'08. Eighth IEEE International Conference on. Ieee, 2008.

While the implicit feedback is the number of hits on the product/item page.

<!--more-->

## Methodology

### Setting

`u` - user, `v` another user
`i` - item, `j` another item

$r_{ui}$ - observations, e.g. hits, time spent, $r_{ui}=0$ means missing observation

Preference:

$$
P_{ui} =
\begin{cases}
1, & r_{ui} > 0 \\\
0, & r_{ui} = 0
\end{cases}
$$

Confidence:

$$
C_{ui} =
\begin{cases}
1 + \alpha \times r_{ui}, & r_{ui} > 0 \\\
1, & r_{ui} = 0
\end{cases}
$$

### Goal

Find a vector $x_u \in \mathbb{R}^f$ for each user u, and a vector $y_i \in \mathbb{R}^f$ for each item i, thus $p_{ui} = x_u^T y_i$

### Math to English

The vectors strive to map users and items into a common latent factor space where they can be directly compared.

### Cost Function

$$\min_{x_\*, y_\*} \sum_{u,i} c_{ui}(p_{ui}-x^T_uy_i)^2 + \lambda(\sum_{u} \| x_u \| ^ 2 + \sum_{i} \| y_i \| ^2)$$

### Algorithm: alternating-least-squares (als)

**alternate between recomputing user-factors and item-factors.**

**Step 1.** recomputing all user factors $X$

$$
 x_u = (Y^T C^u Y + \lambda I)^{-1} Y^T C^u p(u) \\\\
 where\ Y^T C^u Y = Y^T Y + Y^T (C^u - I)Y
$$

**Step 2.** recomputing all item factors $Y$

$$ y_i = (X^T C^i X + \lambda I)^{-1} X^T C^i p(i) $$

**Step 3.** Iterate over `step 1` and `step 2`, till stablize

### Explaining Recommendation

As the preference for item `i` given user `u` is

$$\hat{p_{ui}} = y^T_i x_u = y_i^T (Y^T C^u Y + \lambda I)^{-1} Y^T C^u p(u).$$

Now, we denote $W^u = (Y^T C^u Y + \lambda I)^{-1}$ as weighting matrix associated with user `u`. Accordingly, the weighted similarity between items `i` and `j` from `u`'s viewpoint is denoted by $s^u_{ij} = y_i^T W^u y_j$.

Hence, the predicted user `i`'s preference on item `u` is:

$$\hat{p_{ui}} = \sum_{j:r_{uj}>0} s^u_{ij}c_{uj}$$

This means each past actions ( $r_{uj}>0$ ) receives a separate term in forming the predicted $\hat{p_{ui}}$, and thus we can isolate its unique contribution.

## Recommendation in Action

{% iframe http://jinyi.me/portfolio/assets/recommender-system-pdp/index.html '100%' '562.5px' %}
