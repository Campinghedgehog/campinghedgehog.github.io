---
layout: post
title:  "[Book Review] RL Book" 
date:   2024-06-28 3:06:34 -0700
categories: 
---

Book review for [Reinforcement Learning: An Introduction](http://incompleteideas.net/book/the-book.html).

It's pretty good.

It starts by introducing [bandit problems](https://en.wikipedia.org/wiki/Multi-armed_bandit), highlighting the [exploitation vs exploration](https://en.wikipedia.org/wiki/Exploration-exploitation_dilemma) problem. 

The reinforcement learning problem is then concretized as (finite) [Markov decision process](https://en.wikipedia.org/wiki/Markov_decision_process).
The Bellman equations for value and action-value functions are also introduced:

$$v_\pi = \sum_a \pi(a\mid s) \sum_{s',r} p(s',r \mid s,a) [r + \gamma v_\pi(s')], \, \text{ for all } s \in S$$

$$q_\pi$$ is defined similarly, replacing the value function $$v_\pi$$ with the state-action function $$q_\pi$$ instead.

The Bellman equation is really just the expected reward defined recursively, and is well-defined because the summation converges if the discount factor is less than 1.
Optimal value and state-action functions are then just the maximum over all policies. 

The rest of the book is about how to compute the optimal value, action-state functions, and policy both computationally tractably and sample efficiently. 

## Part 1
The first part goes over solutions that keeps track and tabulates the values of the (state,action) pairs.

### Yes Model
The most simple case is where a perfect model of the environment is available, i.e. the transition probabilities are known, as well as the rewards. 
The a naive search for an optimal policy has a runtime of $$O(n^k)$$, where $$n$$ is the number of states and $$k$$ is the number of actions per state. This gets intractable very quickly.
The book introduces the [Policy Improvement Theorem](http://incompleteideas.net/book/ebook/node42.html), which allows one to pick just the action that returns the greatest reward at a specific state (and effectively ignore or prune all the other branches). The key to the theorem is that the current representation of the policy spans all state-action pairs, so changing the output of one state-action pair does not affect the others, allowing for a monotic improvement in the policy (part two is where this is no longer the case).

### No Model
The rest of Part 1 goes over methods for addressing the case where we don't have a perfect model of the environment (but still the states and actions).

Methods include: Monte Carlo, TD(0), n-step TD, etc.

All the methods are different sides of the same thing, varying in how far and how wide one looks in order to update the value or state-action function at a given state or state-action pair.
Monte Carlo follows the policy until some terminal state, and then updates the state or state-action with the return observed; Monte Carlo methods go deepest with no width (it goes down only 1 path and looks at no other branching states on the way down). TD(0) on the other hand only goes 1-step deep; it updates the value estimates of states by the difference between the actual reward and estimated reward. Everything in between TD(0) and Monte Carlo are the n-step TD methods; $$n$$ seems to be a way to trade off bias for variance.

The figure below summarises it well.

![]({{ site.url }}/assets//images/2024-06-28-book-review-RL-book/spaceOfMethods.png)
*Image: "Figure 8.11" by Richard S. Sutton and Andrew G. Barto, used under CC BY-NC-SA 2.0. Source: <http://incompleteideas.net/book/RLbook2020.pdf>*

The book does a good job introducing the different methods, and then synthesizing them into a coherent framework.

## Part 2
Part 2 is where the condition of keeping track of all state and state-value pairs is dropped. As such, the Policy Improvement Theorem no longer applies. This is because in many problems, there are simply too many states and state-actions to keep track of.

Instead, the book assumes that the value and state-action functions are differentiable functions with fewer parameters than the number of states and state-action pairs. 

The following few chapters up to chapter 12 go over somewhat outdated things (still good to know though). To simplify the analysis the function approximation method is assumed to be a linear function approximation. The key method is to do gradient descent/ascent on the weights. The previous RL methods are also adapted to use gradient descent. Linearity also makes GD and SGD have convergence guarentees. 

The book also goes over feature engineering stuff (kernel methods, tiling, etc.). 

One interesting chapter is the off-policy methods with function approximation. Notably that it doesn't work very well.

It seems like a lot of RL methods with function approximation doesn't work too well due to bad representation (i.e. hand-crafted features suck). Representation learning has come a long way since, so that's nice. 

Chapter 13 is more interesting. Instead of using the value-function to choose an optimal policy (i.e. choosing the action with greatest value), one could simply approximate the policy directly. *Prima facie* this seems hard to do since the returns depends on both the actions taken but also the distribution of those states at which the actions are taken. The key theorem that makes this possible is the [Policy Gradient Theorem](https://lilianweng.github.io/posts/2018-04-08-policy-gradient/#proof-of-policy-gradient-theorem), which informally states that the gradient of the performance (with respect to some set of paramters) is only proportional only to the gradient of the policy (and not the gradient of the distribution of states). If the value function is also approximated during the learning loop, this naturally gives rise to the actor-critic family of methods ([lowers variance at the cost of bias](https://en.wikipedia.org/wiki/Bias–variance_tradeoff)).

## Part 3
The rest of the book goes over the connections between RL and neuroscience, and recent (at the time) RL breakthroughs.

A more recently published book also goes over the relationship between RL and biology (and life I guess): [A Brief History of Intelligence](https://www.amazon.com/Brief-History-Intelligence-Humans-Breakthroughs/dp/0063286343). 
> ### A book Review inside of a book Review
> It's also pretty good.

The at-the-time recent breakthroughs include [AlphaGo](https://www.nature.com/articles/nature16961) and [AlphaGoZero](https://www.nature.com/articles/nature24270).

I thought AlphaGoZero was more interesting since it outperformed AlphaGo with zero (hence the name) expert knowledge. It generated its own training data by playing with itself[^1]. The neural network outputs both the probability of winning and a vector of move probabilities. During self-play, it does a monte carlo tree search to get a stronger estimate of the probability of winning and the vector of move probabilities, which are then used to compute an error for back propogation. Generated data is also stored for future training. Concurrently, other networks (same architecture different parameters) are trained on the data generated. Every N number of iterations, a the different paramters play against each other to find a winner to use for self-play and more data generation. How neat.

And that's about it. Would recommend. 

[^1]: if only ._.