---
title: Recommendation System Miscellus
date: 2018-06-05 09:16:55
tags: [Machine Learning, Recommendation System, State-of-art]
category: Notes
---

{% note %}
I gathered my side notes on recommendation project while doing SSENSE's product display pages (PDP) where my developped model serves as an output on PDP pages' widget.
{% endnote %}

## Recommendation Motivation

{% asset_img motivation.png %}

Why we have recommendation nowadays? The internet goes from web directory (a list) to search engine (passive), now emerging with recommendation system (pro-active). All serve the need to help internet surfer discovers/finds relevant information with the overload of information.

<!-- more -->

## Recommendation Algorithm Summary

State-of-art algorithm & Classical Approach:

- Popularity: not just hot
- Collaborative Filtering
    - Memory Based / Neighbourhood Based: User-based x Item-based (kNN)
    - Model Based:
        - Association Rule: Apriori, FP-Growth
        - Matrix Decomposition: SVD/SVD++, Factorization Machine (ALS)
    - Clustering: User-based x Item-based
    - Classification/Regression Model: purchase/visit as Y
    - LSA / pLSA / LDA: vectorize items
    - Restricted Boltzmann Machine (RBM): 2-layer neural network
    - Graph-based algorithm
    - Session-based RNN
- Content-based / Knowledge-based
    - Item Attribut Vector x User Profile Vector
- Context-aware: when, where, mood

## Recommendation System Properties

{% asset_img properties.png %}

Detailed explaination in properties can be found on [
Recommender Systems Handbook](https://www.springer.com/gp/book/9780387858203): chapter 8.3

## Implicit & Explicit Feedback

{% asset_img feedback.png %}

- Explicit: user explicitly expresses like/dislike, e.g. rating, like
- Implicit: Not as obvious in terms of preference, it’s only the observation, e.g. purchase, hits

Implicit feedback algorithm starts from:

    Hu, Yifan, Yehuda Koren, and Chris Volinsky. "Collaborative filtering for implicit feedback datasets." Data Mining, 2008. ICDM'08. Eighth IEEE International Conference on. Ieee, 2008.

Link to the paper: [http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.167.5120&rep=rep1&type=pdf]()

## Challenges

{% asset_img challenges.png %}

We have two main challenges: cold start on users & items, long tail on items.

## Model Pipeline

{% asset_img pipeline.png %}

Existing pipeline on ssense.com

## Evaluation

Three stages of evaluation on recommendation:

{% asset_img eval.png %}
