---
title: "NYC CitiBike [4] - Model Framework"
date: 2015-04-09 20:53:14
tags: [data science, open data, r, citibike]
categories: [R]
---

Our objective of the task is to use operational data to predict the financial flow.

## Planning

Here is my analysis plan.

{% asset_img Steps.png %}

<!-- more -->
## Profit & Loss analysis

In order to achieve the goal, we have in the first place done a P&L analysis crossed with the operational data in our possession. With this analysis, we are now capable to create a finanical model with operational data.

P.S.: we are not able to integrate in the model the impact of the price change over the number of subscription/pass or the number of trip. But a simple coefficient can be applied to the result.

{% asset_img Citibike.png %}

### Profit

The profit is decomposed by three revenues: annual subscription, 24h/7days pass, overtime charge.

The actual operational data provided via Citibike allows us to modelize at the daily level:

- The new annual subscription
- The new 24H/7days pass

Models are constructed with General Additive Model & Random Forest with calendar information (including public holiday), weather of the day.

With the result/prediction of new annual subscription and new pass, we can calculate the "active" annual subscription and new pass of each day. The "active" is defined as the number of user which can use the citibike on that day. The "active" user can help to explain the number of trips made.

Overtime charge is not quite easy to modeling. Several approaches are possible:

- with the prediction of number of trips, we can simulate the trips from the collection (on same characteristics) of trips recorded. And then calculate the overtime from the trips simulated.
- by using observed data, we can calculate the number of trips which are longer than 45 mins (for annual subscriber) or 30 mins (for pass user) and cut their overtime duration (deducted by threshold) per 30 minutes in order to create ratio per overtime category (0-30 mins, 30-60 mins, 60-90 mins, 90-120 mins, and >120 mins). And then calculate the variation of the ratio by weekdays and season.

We have opted for the second approach in this case. Because the simulation of the first approach is long and memory consuming, furthermore not easy to play within a shiny-framework. The second approach is more adapted for a shiny-framework, light and user playable.

### Loss

The cost is decomposed by four essential elements: subscription processing cost, maintenance cost (bike & station), fixed cost.

In this part, the modeling or scenario is already done by the "profit" part. We can reuse the result and allow user to play with the input and generate the cost.

- subscription processing cost: processing fee per new subscription * number of new pass/subscription
- bike repairing cost: cost per bike * ratio(0.33%) * total trip per day
- station maintenance: ??
- fixed cost: ??

## Shiny application

In order to provide the simulation and visualization tool, the final product will be a shiny application.

{% asset_img shiny_app.png %}
