---
layout: post
title:  "Q-Learning Primer"
date:   2018-06-17 17:16:00 -0500
categories: research
---

Q-learning is one of the most common techniques for learning an optimal policy in reinforcement learning. Since I'm still trying to learn this stuff, I figured I would make a reference sheet for myself that goes over the basics of Q-learning.

Markov Decision Process

To understand anything in RL, you need to know what a Markov Decision Process (MDP) is. As you can see on Wikipedia or a billion academic papers, MDPs are usually defined as a 5-tuple $ (S, A, R, T, \gamma) $.

- $S$ is the (finite) set of states describing your environment. In a gridworld, for example, it would be the set of possible $$(x, y)$$ coordinates. In Atari, it would be the $210 \times 160 \times 3$ image space of the game.
- $A$ is the (finite) set of actions available to the agent, such as up, down, left, right.
- $R$ is the reward function that maps $$(s, a, s')$$ tuples to a real-valued reward, $R: S \times A \times S' \rightarrow \mathbb{R}$. In order words, given that the agent was in a state $s'$, takes action $a$, and ends up in state $s'$, then it receives $R(s, a, s')$ reward.
- $T$ is the transition function $T(s, a, s') = \textrm{Pr}(s_{t+1} = s' \vert s_t = s, a_t = a)$. It can also be described as the *dynamics* of the agent in the environment. $T(s, a)$ describes a probability distribution --- the MDP does not assume deterministic dynamics. So given that the agent is in state $$s$$ and takes action $a$, there is a distribution over the possible next states. For example, if you are in state $(1, 1)$ and take action *North*, then perhaps your transition function will define the "next state" probability distribution $\{(1, 2): 0.8, (0, 1): 0.1, (2, 1): 0.1\}$.
- $\gamma$ is the discount factor. This is just a fiddly parameter that you use when computing expected rewards to make rewards from future states worth less than rewards from more recent states.