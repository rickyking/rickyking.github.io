---
layout: "post"
title: "Agile Data Science"
date: "2018-12-31 09:49"
updated: "2018-12-31 14:49"
tags:
  - data science
  - agile
categories:
  - Notes
---

Nowadays, the Agile became very famous among software development. Data science is another trending discipline, Many and many more companies try to build and benefit from.

Scrum as methodologies tries to help build a good software in small increments. There are lots of attempts to apply agile software methods to data science, but the results are mostly unsatisfactory.

Based on my experiences and reading, I try to:

- explain why agile **scrum** framework is not a good fit for data science project
- analyze and map the agile **concept** to data science discipline
- as the concept is good, how can we adapt

<!-- more -->

## Why agile process (specifically Scrum) is not applicable in data science?

Agile software is aiming to deliver a *production software*, while data science is to provide **actionable insights**.

In order to offer the insights that is actionable, we as data scientist need to make **iterative experiments** based on assumptions, results from last experiments which creates uncertainty, while engineering uses **known science and engineering techniques** to build on a linear schedule.

<figure>
<a href="Scientific Method"><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/5c/The_Scientific_Method_as_an_Ongoing_Process.svg/1024px-The_Scientific_Method_as_an_Ongoing_Process.svg.png" width="500" alt="Scientific Method" title="Scientific Method"></a>
<figcaption align='center'>The scientific method flowchart.</figcaption>
</figure>

{% note %}
#### Engineering is precise and science is uncertain.
-- [A manifesto for agile data science](https://www.oreilly.com/ideas/a-manifesto-for-agile-data-science)
{% endnote %}

So in data science project, we usually spent more time on discovery than the engineering, and the time spent is always uncertain due to the nature of science/data.

## The agile concept is still applicable in Data Science Project

Agile software development benefits: iterative/incremental, *shippable* product, continuously re-evaluation (customer/development).

Russell Jurney, the author of [Agile Data Science 2.0](http://shop.oreilly.com/product/0636920051619.do) made it short:

> The goal for the agile data science process is to document, facilitate, and guide exploratory data analysis to discover and follow the critical path to a compelling analytics product.

Hereby, I try to do an analysis on Agile Manifesto over Data Science discipline:

- **Individuals and interactions** over processes and tools: **sharing** is mandatory, as the team is built from different disciplines (DS, DE, Business), we need to communicate efficiently inside the team, as well as towards the customer.
- **Working software** over comprehensive documentation: full-stack data scientist should be able to create and deliver a **proof-of-concept application** in the meantime to enable the insights publish, and gathering the feedback.
- **Customer collaboration** over contract negotiation: as Data Science team, we need to **periodically and frequently** communicate, discuss the discovery with customer all along the project, while compared to delivering once having good results. Hence the question is `How can we make the intermediate discovery understandable by customers?`.
- **Responding to change** over following a plan: already happens in Data Science, as we further uncover the data itself, the plan of further analysis will be altered accordingly upon trial-error.

After analyzing the Agile Manifesto, actually the data science can benefit the concept of [Agile](https://www.agilealliance.org/agile101/):

> The ability to create and respond to change in order to succeed in an uncertain and turbulent environment.

Source:

- [Agile 101](https://www.agilealliance.org/agile101/)
- [Linkedin Post on Agile Data Science](https://www.linkedin.com/pulse/agile-data-science-waclaw-kusnierczyk/)
- [Quora Discussion](https://www.quora.com/Is-the-agile-approach-applicable-in-data-science-projects)

## Break rules and adapt

```python
class DerivedClassName(BaseClassName):
    pass
```

Data Science project is possible to inhere the Agile software development. We need to bravely break the rules to solve the issues in the first section.
