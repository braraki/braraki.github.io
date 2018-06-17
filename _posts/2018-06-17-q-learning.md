---
layout: post
title:  "Q-Learning Primer"
date:   2018-06-17 17:16:00 -0500
categories: research
---

Q-learning is one of the most common techniques for learning an optimal policy in reinforcement learning. Since I'm still trying to learn this stuff, I figured I would make a reference sheet for myself that goes over the basics of Q-learning.

Markov Decision Process

To understand anything in RL, you need to know what a Markov Decision Process (MDP) is. MDPs are usually defined as a 5-tuple

$ (S, A, R, T, \gamma) $