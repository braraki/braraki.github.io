---
layout: post
title:  "Q-Learning Primer"
date:   2018-06-17 17:16:00 -0500
categories: research
---

Q-learning is one of the most common techniques for learning an optimal policy in reinforcement learning. Since I'm still trying to learn this stuff, I figured I would make a reference sheet for myself that goes over the basics of Q-learning.

Markov Decision Process

To understand anything in RL, you need to know what a Markov Decision Process (MDP) is. As you can see on Wikipedia or a billion academic papers, MDPs are usually defined as a 5-tuple $$ (S, A, R, T, \gamma) $$.

- $$S$$ is the (finite) set of states describing your environment. In a gridworld, for example, it would be the set of possible $$(x, y)$$ coordinates. In Atari, it would be the $$210 \times 160 \times 3$$ image space of the game.
- $$A$$ is the (finite) set of actions available to the agent, such as up, down, left, right.
- $$R$$ is the reward function that maps $$(S, A)$$ tuples to a real-valued reward. Or in math-speak, $$R: S \times A \rightarrow \mathbb{R}$$