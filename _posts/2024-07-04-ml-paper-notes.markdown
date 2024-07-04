---
layout: post
title:  "Collection of notes/summary on random ML papers" 
date:   2024-07-4 3:06:34 -0700
categories: 
---

[Updated July 4, 2024]

### [Decision Transformer: Reinforcement Learning via Sequence Modeling](https://arxiv.org/pdf/2106.01345)
### [Offline Reinforcement Learning as One Big Sequence Modeling Problem](https://arxiv.org/pdf/2106.02039)
- Throwing $$(a_t, s_t, r_{t+1}, ... )$$ sequences at transformers. Seems to work.
- One difference between two paper is mainly how it's tokenized. Decision transformers puts an (action, state, reward) thruple into a learned embedding, while the latter one flattens the feature vectors of actions and states and rewards. 


### [TRANSFORMERS ARE SAMPLE-EFFICIENT WORLD MODELS](https://arxiv.org/pdf/2209.00588)
- Policy is trained with the world model (VQ-VAE + Transformer)
- First step is to gather experience following our policy and save it into a replay buffer (policy model conditioned on output of VQ-VAE)
- Second is to take samples of length context length from the replay buffer and backprop errors through VQ-VAE and Transformer G (Transformer is conditioned on: latent vector, action, ...)
- Third is updating the policy network: Rollout for H steps, using Transformer G autoregressively, picking next action with Policy network. Can use value network if actor-critic method, back prop appropriately.

### [Trust Region Policy Optimization (TRPO)](https://arxiv.org/pdf/1502.05477)
- Expected discounted reward of a new policy can be written as the sum of expected reward of old policy + estimated advantage function. Difficult to optimize due to dependence of state distribution on new policy, but it can be approximated by using the state distribution of the old policy. This approximation matches to first order, so small steps in the policy should be fine, but doesn't prescribe step size. Using mixture update provides a lower bound. TRPO generalizes the previous result to any kind of update, constrained by the KL divergence between new policy and old, which guarantees monotonic improvement.

### [Proximal Policy Optimization (PPO)](https://arxiv.org/pdf/1707.06347)
- Instead of computing that constrained optimization problem as in TRPO, what if we just clip how large the update can be.
- Empirically works better than TRPO, a lot simpler to implement.

### [Direct Preference Optimization(DPO)](https://arxiv.org/pdf/2305.18290)
- Reparametrizing the reward model, and plugging it back in the Bradley-Terry preference model yields a probability of human preference in terms of optimal policy instead of a reward model, from which a MLE can be extracted for optimization. 
- $$\mathscr{L}_{DPO}(\pi_\theta ; \pi_{ref}) = -\mathbb{E}_{(x,y_w,y_l) ~ D}\biggl [log \, \sigma \bigl(\beta \, log \frac{\pi_\theta(y_w | x)}{\pi_{ref}(y_w | x)} - \beta \, log \frac{\pi_\theta(y_l | x)}{\pi_{ref}(y_l | x)} \bigr )\biggr]$$
- Does not require RL; RL is finicky. But requires paired data since no reward model
- Seems like DPO doesn't work that well, at least in chatbot arenas, as of July 2024.

