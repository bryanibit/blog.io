---
layout: post
mathjax: true
title: Kalman Filter
date: 2017-8-29
categories: blog
tags: [理论梳理]
description: KF KS
---

## Problem Statement

[Kalman Filter Equation Explanation](http://bilgin.esme.org/BitsAndBytes/KalmanFilterforDummies)

All in all, the KF is shown like that:

![KF Equation](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/insight_formula.gif)

The above subscript $k$ is state. Our purpose is to find $\hat{X}_k$, the estimate of the signal $X_k$. The only unkonwn componet in this equation is the $K_k$, Kalman Gain, which you should calculate for each consequent state.

If $K_k$ is constant like 0.5, then $\hat{X}_k$ is like half of measured value plus half of previous history value. From the above equation, we can conclude that **Kalman Filter finds the most optimum averaging factor for each consequent state. Also somehow remembers a little bit about the past states**. Unfortunately, it is more sophisticated in fact.


## Build a Model

$$x_k = Ax_{k-1} + B\mu_k + w_{k-1}\\z_k = Hx_k + v_k$$

$x_k$ is signal value. $u_k$ is control signal and $w_{k-1}$ is process noise(which may be hard to conceptualize). Most of time, these is no control signal $u_k$. The second equation tells that any measurement value (which we are not sure its accuracy) is a linear combination of the signal value and the measurement noise. They are both considered to be Gaussian. The entities A, B and H are in general form matrices. But in most of our signal processing problems, we use models such that these entities are just numeric values and constant. Even most probably, they'll be equal to 1.

If we are pretty sure that our system *fits into this model* (most of the systems do by the way), the only thing left is to estimate the **mean and standard deviation** of the noise functions $w_{k-1}$ and $v_k$. The better you estimate the noise parameters, the better estimates you get.

## Process iteration
The iteration process is shown the following:

![KF Iteration](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/iteration_steps.gif)

KF is an iteration process with two phases called Time Update(*state prediction*) and Measurement Update(*correction*).

To start the process, we need to know the estimate initial values of x0, and P0. And the most remaining painful thing is to determine R and Q. R is rather simple to find out, because, in general, we're quite sure about the noise in the environment. But finding out Q is not so obvious.

## Tutorial for source code

The code could be found [here](https://github.com/hmartiro/kalman-cpp) and reference documents could be found [here](http://www.cs.unc.edu/~welch/media/pdf/kalman_intro.pdf)

## Handwrite Note

![KF Handwrite](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/kalman_handwrite1.jpg)
![KF Handwrite_](https://github.com/bryanibit/bryanibit.github.io/raw/master/img/doc/kalman_handwrite2.jpg)