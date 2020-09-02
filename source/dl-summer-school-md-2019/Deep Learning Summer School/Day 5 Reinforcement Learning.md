---
title: Day 5 Reinforcement Learning
date: 2019-10-07 14:32:46
---

# RL part 1

RL: the problem of finding suitable actions to take in a given situation in order to maximize rewards.

### Difference:

- No supervisor, only reward signal
- Feedback is delayed not instantaneous
- Data is sequential, non i.i.d
- Agent's actions affect the subsequent data

RL setup follows the Markov property: the future is independent of the past given the present.

Markov Decision Processes (MDP) formally describes the environment for RL.

Agent's behavior is determined by its **policy**.

Policies are stationary, since MDP depends on the current state (not history).

→ Dynamic Programming

→ Exploration v.s. Exploitation (acting greedily with respect to the best you know)

DL: sequence of activation leading to loss

RL: sequence of actions leading to reward

Main Algorithm:

![Day%205%20Reinforcement%20Learning/Untitled.png](Day%205%20Reinforcement%20Learning/Untitled.png)