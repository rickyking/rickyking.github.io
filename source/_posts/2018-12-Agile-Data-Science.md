---
layout: "post"
title: "Agile Data Science"
date: "2018-12-31 09:49"
updated: "2018-01-16 22:49"
tags:
  - data science
  - agile
categories:
  - Notes
---

Nowadays, the Agile became very famous among software development. Data science is another trending discipline, Many and many more companies try to build and benefit from.

Scrum as methodologies tries to help build a good software in small increments. There are lots of attempts to apply agile software methods to data science[^1][^2], but the results are mostly unsatisfactory.

Based on my experiences, reading, and recent participating a SCRUM training with McGill University, I try to discuss:

- why agile **scrum** framework is not a good fit for data science project
- the agile **concept** following data science discipline
- the **adaption** from the framework
- the minimum viable product in the context of data product

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

Hereby, I try to do an analysis on Agile Manifesto[^3] over Data Science discipline:

- **Individuals and interactions** over processes and tools: **sharing** is mandatory, as the team is built from different disciplines (DS, DE, Business), we need to communicate efficiently inside the team, as well as towards the customer.
- **Working software** over comprehensive documentation: full-stack data scientist should be able to create and deliver a **proof-of-concept application** in the meantime to enable the insights publish, and gathering the feedback.
- **Customer collaboration** over contract negotiation: as Data Science team, we need to **periodically and frequently** communicate, discuss the discovery with customer all along the project, while compared to delivering once having good results. Hence the question is `How can we make the intermediate discovery understandable by customers?`.
- **Responding to change** over following a plan: already happens in Data Science, as we further uncover the data itself, the plan of further analysis will be altered accordingly upon trial-error.

After analyzing the Agile Manifesto, actually the data science can benefit the concept of Agile:

> The ability to create and respond to change in order to succeed in an uncertain and turbulent environment.

## Break rules and adapt

Data Science project is possible to inhere the Agile software development. We need to bravely break the rules to solve the issues in the first section.

In general, we should keep in mind the concept of Agile (iterative, incremental and continuous), as due to uncertainty of research, we are unable to convert these directly into **time-constraint** sprint and **point-based** task.

But, the data preparation is the only exception due to uncertainty. Thus we should separate the data preparation part from the SCRUM sprint, especially we know that we are very dependant on other resources or the data is very dirty, and the preprocessing might involve the interaction outside of the team.

Then, Scrum methodology should serve as project management framework that manages the data science project. In practice: 

- Sprint: a time-box of flexible 2-4 weeks, when we should reorder, re-prioritize the future according to the newest discovery.
- Sprint Planning: we assess the known discovery of data and make appropriate change on the sprint-level planning if needed (such as switching the methodology, adjusting the ETA of completion, changing scope, ...).
- Daily Scrum (Stand-up): we should share knowledge of the team's newest discovery based on data and use the time to make quick adjustment on the short term task (such as switching data source, asking for domain knowledge expert, changing algorithms, ...).
- Sprint Review: we should abandon the review during the project with team, except when we achieve a certain milestone. We use the review to present and synchronize with business/product but only at leadership level.
- Sprint Retrospective: we should abandon the retrospective during the project, except when we finish the project.

## Minimum Viable Product (MVP)

Agile also has a concept of MVP. So what is MVP within data science project context?

It depends. Inside team, MVP could be a **baseline** model as first attempt, or the insights displaying in `jupyter notebook` or `rmarkdown notebook`. But towards business or engineering, it should be a **moderate performant** model that works and delivers an acceptable results (e.g. recommendation system should deliver recommendation that won't hurt end-user experience), or the insights displayed in a dashboard that need to be aligned with business.


[^1]: Quora Discussion [https://www.quora.com/Is-the-agile-approach-applicable-in-data-science-projects/]()
[^2]: Linkedin Post on Agile Data Science [https://www.linkedin.com/pulse/agile-data-science-waclaw-kusnierczyk/]()
[^3]: Agile Manifesto [https://agilemanifesto.org/]()
