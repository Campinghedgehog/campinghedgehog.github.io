---
layout: post
title:  "Collection of notes/summary on random ML papers [Updated July 11, 2024]" 
date:   2024-07-4 3:06:34 -0700
categories: 
---

[Updated July 11, 2024]
#### [DotaMath: Decomposition of Thought with Code Assistance and Self-correction for Mathematical Reasoning](https://arxiv.org/pdf/2407.04078)
- trained model to break down tasks into subtasks, generate and run code for each subtask, etc.
- looks like also trajectory sampling + GPT-4
- synthetic data with more step basically

#### [MuMath-Code: Combining Tool-Use Large Language Models with Multi-perspective Data Augmentation for Mathematical Reasoning](https://arxiv.org/pdf/2405.07551)
- generates code data with GPT-4, plugging errors back into GPT-4 if they appear
- chain of thought pretraining in stage 1
- in stage 2, train on cot + code data, masking out interpreter output tokens for the loss 
- during inference, it gets to use an interpreter

#### [Lean Workbook: A large-scale Lean problem set formalized from natural language math problems](https://arxiv.org/pdf/2406.03847v2)
- Kind of like DeepSeek-Prover, but with extra steps
- autoformalization model is trained to formalize, and also informalize (formal back to natural language)
- very interesting, they use a larger model for data filtering
    - after autoformalizing
        - samples that don't compile are rejected
        - then they translate the formalized samples back to natural language, and query the larger model to check if the natural language reconstruction is the same. Marked for human eval if not
- marked samples usually have some pattern. They are modified and added back to training data

#### [Towards Large Language Models as Copilots for Theorem Proving in Lean](https://arxiv.org/pdf/2404.12534)
- tooling around hooking up LLMs to lean
- good stuff

[Updated July 10, 2024]
#### [LeanDojo: Theorem Proving with Retrieval-Augmented Language Models](https://arxiv.org/pdf/2306.15626)
- introduces LeanDojo, a gym-like environment for lean
- ReProver, a RAG premise selection, trained with contrastive loss
- small model

[Updated July 9, 2024]
#### [TORA: A TOOL-INTEGRATED REASONING AGENT FOR MATHEMATICAL PROBLEM SOLVING](https://arxiv.org/pdf/2309.17452)
- used tools to sample trajectories from datasets using GPT-4
- then imitation learning
- eh

#### [Program of Thoughts Prompting: Disentangling Computa- tion from Reasoning for Numerical Reasoning Tasks](https://arxiv.org/pdf/2211.12588)
- clever way to try and separate reasoning and computation
- chain of thought does both
- python is pretty much english so they use a python, LLM generates python, runs it to get answer, and so on

#### [Chain-of-Thought Prompting Elicits Reasoning in Large Language Models](https://arxiv.org/pdf/2201.11903)
- "chain-of-thought prompting does not positively impact performance for small models, and only yields performance gains when used with models of ∼100B parameters"
    - interesting, the deepseek-prover paper used chain of thought for a model with only 7B though

## More DeepSeek papers
- [DeepSeekMath: Pushing the Limits of Mathematical Reasoning in Open Language Models](https://arxiv.org/pdf/2402.03300)
    - much larger corpus of math/code data, steeper learning curve during fine tuning
    - introduces Group Relative Policy Optimization (GRPO)
        - normal PPO requires a value model (critic) for estimating the baseline for the advantage function (for reducing variance without introducing bias, introduced in chapter 13 policy gradient chapter in the RL book)
        - GRPO instead samples N different outputs from the old policy, and optmizes for the average PPO loss
        - the advantage function is just the rewards normalized (subtract avg reward, divide by std dev of rewards)
        - saves memory as there is no need for a value network anymore
- [DeepSeek-Prover: Advancing Theorem Proving in LLMs through Large-Scale Synthetic Data](https://arxiv.org/pdf/2405.14333v1)
    - data generation loop for formal proofs in lean
        - model first fine-tuned on lean -> natural language pairs (MMA dataset)
        - they gathered a large corpus of math problems in english
        - use the model to automatically formalize into lean
            - filter out those that proved False (had a contradiction)
        - performed a model guided proof search for both the statement and its negation, until timeout
        - finuned model on newly generated data, repeat
    - pretty clever way of using human priors to guide its way through the very large space of possible proofs
    - feels like more search + RAG could help

[Updated July 8, 2024]
#### [Metric Flow Matching for Smooth Interpolations on the Data Manifold](https://arxiv.org/pdf/2405.14780)
- Like Conditional Flow Matching but with a metric dependent on the data

#### [Deep Learning as Ricci Flow](https://arxiv.org/pdf/2404.14265)
- they took a test set, forward pass, measured each sub layer, noticed curvature follows a ricci flow-ish schedule

#### [Bayesian Flow Networks](https://arxiv.org/pdf/2308.07037)
- not sure I quite understood this paper, but it seems like it's deforming some simple prior distribution, into one that maximizes compression, through bayesian updates. 
- Seems like with a suitable prior, the bayesian updates are like taking a step with a natural gradient.
- almost like a metric flow kind of thing, where a simple (spherical) distribution is deformed into a more complicated one

#### [Deconstructing Denoising Diffusion Models for Self-Supervised Learning](https://arxiv.org/pdf/2401.14404v1)
- Denoising auto-encoders aren't that different from diffusion, etc.

[Updated July 7, 2024]
#### [IMPROVING GENERALIZATION IN META REINFORCE- MENT LEARNING USING LEARNED OBJECTIVES](https://arxiv.org/pdf/1910.04098)
- parameterizes the objective function, instead of assuming a fixed one
- samples into a replay buffer and trains off-policy
- training loop now trains the critic, policy, then objective function, etc...
    - the policy is backproped by the error between old policy and new one
    - objective network needs to be able to be differentiated wrt to policy parameters
    - for a sampled trajectory, the policy parameters are (temporarily) updated wrt the objective function until the end of the trajectory, where the critic is used to score the new policy, which is used as the objective function loss to back prop through the objective function network

## DeepSeek Papers
- [DeepSeek LLM Scaling Open-Source Language Models with Longtermism](https://arxiv.org/pdf/2401.02954)
    - Chinese open-source LLM
    - investigates scaling laws, optimal batch sizes and learning rates given compute budget
    - observes that data quality may affect scaling behavior
- [DeepSeek-Coder: When the Large Language Model Meets Programming - The Rise of Code Intelligence](https://arxiv.org/pdf/2401.14196)
    - pretrained with fill-in-the-middle, on top of next token prediction
    - a lot of data cleaning and filtering (dedeplication, filtering out shit code that doesn't compile, bad formatting eg lines too long, data heavy files)
    - DeepSeek model from previous paper trained on code data also showed improvement (DeepSeekCoder was only a little bit better at programming benchmarks, not as good as all others)
        - seems like _ceteris paribus_ more compute is just better, which I guess isn't suprising
        - original model was also trained on the 2T tokens, so I'm guessing something something conditional kolmogorov complexity something something..
- [DeepSeek-VL: Towards Real-World Vision-Language Understanding](https://arxiv.org/pdf/2403.05525)
    - "Our research reveals that maintaining a significant proportion of language data—specifically, at least 70%—is essential to preserve the integrity of language knowledge within the mode"
    - "Nevertheless, compared to Large Language Models (LLMs), vision-language adaptors (e.g., a 2-layer MLP) have a significantly smaller parameter capacity. This limitation in model capacity restricts the capabilities that can be learned during this stage. A natural question arises: Can the law of data scaling be effective at this stage? To address this question, we conducted a simple experiment in Table 8. The results demonstrate that expanding the data scale at this stage does not provide benefits and may even lead to inferior performance."
    - training with large proportion on just multimodal data lead to catastrophic forgetting, and degradation on language bench marks
        - a lot of multimodal data have very simple language structure etc., inherent competition between multimodal and linguistic capabilities conjectured
        - they interleave with language training
    - seems like some variant of curriculum learning is still relevant to effective training of models
- [DeepSeek-V2: A Strong, Economical, and Efficient Mixture-of-Experts Language Model](https://arxiv.org/pdf/2405.04434)
    - Multihead latent attention (reduces KV cache, good performance), uses DeepSeekMOE
    - online RL better than offline

[Updated July 4, 2024]
### [Decision Transformer: Reinforcement Learning via Sequence Modeling](https://arxiv.org/pdf/2106.01345)
### [Offline Reinforcement Learning as One Big Sequence Modeling Problem](https://arxiv.org/pdf/2106.02039)
- Throwing $$(a_t, s_t, r_{t+1}, ... )$$ sequences at transformers. Seems to work.
- One difference between two paper is mainly how it's tokenized. Decision transformers puts an (action, state, reward) thruple into a learned embedding, while the latter one flattens the feature vectors of actions and states and rewards. 


### [TRANSFORMERS ARE SAMPLE-EFFICIENT WORLD MODELS](https://arxiv.org/pdf/2209.00588)
- really just this [World Models](https://arxiv.org/pdf/1803.10122) paper but with VQ-VAE and transformers instead of RNN
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

## RLHF papers
- LLMs
    - [Fine-Tuning Language Models from Human Preferences](https://arxiv.org/pdf/1909.08593)
    - [Training language models to follow instructions with human feedback](https://arxiv.org/pdf/2203.02155) 
    - [Training a Helpful and Harmless Assistant with Reinforcement Learning from Human Feedback](https://arxiv.org/pdf/2204.05862) (Anthropic)

- [Deep Reinforcement Learning from Human Preferences](https://arxiv.org/pdf/1706.03741)
    - Instead of reward coming from environment, a reward model is fitted from human feed back.
    - Works in Atari and open AI gym. For some games, the fitted reward model converged faster than direct reward (better shape of reward model)
    - "Moreover, we show that collecting feed- back online improves the system’s performance and prevents it from exploiting weaknesses of the learned reward function."

- [QUANTIFYING DIFFERENCES IN REWARD FUNCTIONS](https://arxiv.org/pdf/2006.13900)
    - defines a pseudo-metric for reward functions

- Early papers
    - [Preference-Based Policy Learning](https://inria.hal.science/inria-00625001/PDF/Preference-based_Policy_Learning.pdf)
    - [APRIL: Active Preference-learning based Reinforcement Learning](https://arxiv.org/pdf/1208.0984)
    - [Programming by Feedback](https://inria.hal.science/hal-00980839/PDF/programming_by_feedback.pdf)
    - [A Bayesian Approach for Policy Learning from Trajectory Preference Queries](https://papers.nips.cc/paper_files/paper/2012/file/16c222aa19898e5058938167c8ab6c57-Paper.pdf)
    - [Generative Adversarial Imitation Learning](https://arxiv.org/pdf/1606.03476)
        - Like GANs but for RL
    - [Deep Q-learning from Demonstrations](https://arxiv.org/pdf/1704.03732)
        - Supervised pretraining on expert demonstration, then RL on environment collecting data in some replay buffer (policy is epsilon-greedy wrt Q), then sample from replay buffer and demonstrations again etc.
    - [Guided Cost Learning: Deep Inverse Optimal Control via Policy Optimization](https://arxiv.org/pdf/1603.00448)
    - [Cooperative Inverse Reinforcement Learning](https://arxiv.org/pdf/1606.03137)

