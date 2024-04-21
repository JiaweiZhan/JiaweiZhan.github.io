---
layout: project
title: 'Multi-Agent Proximal Policy Optimization for Cooperative, Multi-Agent Games'
date: 20 Apr 2024
image: /assets/img/projects/mappo_BGW.png
screenshot: /assets/img/projects/mappo_BGW.png
caption: Design deep reinceforcement learning method for the famous Overcooked game.
description: >
    In a multi-agent Overcooked environment, two chefs in a resturant need to collaborate to cook onion soups. My goal is to come up with a reinforcement learning method to maximaize the number of soups delivered within an episode on a variety of layouts.
accent_color: '#4fb1ba'
accent_image:
  background: 'linear-gradient(to bottom,#193747 0%,#233e4c 30%,#3c929e 50%,#d5d5d4 70%,#cdccc8 100%)'
  overlay:    true
author: false
---

# Project Overview
In a multi-agent Overcooked environment, two chefs in a restaurant need to collaborate to cook onion soups. My goal is to come up with a reinforcement learning method to maximize the number of soups delivered within an episode on a variety of layouts. To solve this problem, I proposed to use multi-agent Proximal Policy Optimization (MAPPO) with centralizing training and decentralizing execution techniques. I also adopted reward shaping and multiprocessing. I showed that such implementation achieve high effectiveness in the Overcooked game.

# Theory
## Core: Proximal Policy Optimization
Proximal Policy Optimization (PPO) is trying to solve a question: how can we take the biggest possible improvement step on a policy without stepping so far from the old, using the data that are currently available. This is achieved by clipping in the objective function to prevent the new policy from getting far from the old policy. Specifically, PPO updates policies via

$$\theta_{k + 1}=\mathrm{argmax}_{\theta}E_{s, a\sim\pi_{\theta_k}}[L(s, a, \theta_k, \theta)],$$

by taking multiple steps of gradient descent. Here $L$ is given by:

$$L(s, a, \theta_k, \theta) = \mathrm{min}\left(\frac{\pi_{\theta}(a|s)}{\pi_{\theta_k}(a|s)}A^{\pi_{\theta_{k}}}(s, a), \mathrm{clip}\left(\frac{\pi_{\theta}(a|s)}{\pi_{\theta_k}(a|s)}, 1-\epsilon, 1+\epsilon\right)A^{\pi_{\theta_{k}}}(s, a) \right),$$

where hyperparameter ε controls how far away the new policy is allowed to go from the old. The advantage function $A^{\pi_{\theta}}(s, a)$ is approximated via Generalized Advantage Estimation:

$$A_t^{\mathrm{GAE}(\gamma, \lambda)} = \sum_{l=0}^{\infty}(\gamma\lambda)^{l}\left(r_{t+l} + \gamma V^{\pi, \gamma}(s_{t + l + 1}) - V^{\pi, \gamma}(s_{t + l})\right).$$

Within the advantage function is the value function $V^{\pi, \gamma}(s_t)$ approximated by another model, which is fitted by regression on mean-squared error:

$$  \psi = \mathrm{argmin}_{\phi}||V_{\psi}^{\pi, \gamma}(s_t) - \sum_{l=0}^{\infty}\gamma^{l}r_{t + l}||^2,$$

through gradient descent algorithm.


Example:

Lorem ipsum $$ f(x) = x^2 $$.

Markdown:
~~~md
Lorem ipsum $$ f(x) = x^2 $$.
~~~

~~~latex
$$
\begin{aligned} %!!15
  \phi(x,y) &= \phi \left(\sum_{i=1}^n x_ie_i, \sum_{j=1}^n y_je_j \right) \\[2em]
            &= \sum_{i=1}^n \sum_{j=1}^n x_i y_j \phi(e_i, e_j)            \\[2em]
            &= (x_1, \ldots, x_n)
               \left(\begin{array}{ccc}
                 \phi(e_1, e_1)  & \cdots & \phi(e_1, e_n) \\
                 \vdots          & \ddots & \vdots         \\
                 \phi(e_n, e_1)  & \cdots & \phi(e_n, e_n)
               \end{array}\right)
               \left(\begin{array}{c}
                 y_1    \\
                 \vdots \\
                 y_n
               \end{array}\right)
\end{aligned}
$$
~~~
