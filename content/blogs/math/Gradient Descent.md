---
draft: false
authors:
  - admin
title: Gradient Descent
date: 2025-03-14
summary: Overview of Gradient Descent related to LLM math essentials.
tags:
  - Optimization
  - Algorithm
---

* **Objective:**
    * Gradient descent aims to minimize a function, often called a "cost function" or "loss function." This function represents the error of a model's predictions.
* **Mechanism:**
    * It iteratively adjusts the parameters of the model by moving in the direction of the steepest decrease of the cost function.
    * This "steepest decrease" direction is determined by the negative [[gradient]][^1] of the function.
* **Analogy:**
    * Imagine a ball rolling down a hill. Gradient descent is like the ball, and the cost function is the shape of the hill. The algorithm guides the ball to the lowest point (the minimum).

## Key Components:

* **Gradient:**
    * The gradient is a vector of partial derivatives that indicates the direction of the steepest increase of a function. The negative gradient points in the direction of the steepest decrease.
* **Learning Rate:**
    * The learning rate is a crucial parameter that controls the size of the steps taken during each iteration.
    * A small learning rate leads to slow convergence, while a large learning rate can cause the algorithm to overshoot the minimum.
* **Iterations:**
    * Gradient descent is an iterative process. It repeatedly updates the parameters until a stopping criterion is met (e.g., reaching a minimum, exceeding a maximum number of iterations).

## Types of Gradient Descent:

* **Batch Gradient Descent:**
    * Calculates the gradient using the entire training dataset in each iteration.
    * Accurate but computationally expensive for large datasets.
* **Stochastic Gradient Descent ([[SGD]]):**
    * Calculates the gradient using only one randomly selected training example in each iteration.
    * Faster than batch gradient descent but can be noisy.
* **Mini-Batch Gradient Descent:**
    * Calculates the gradient using a small batch of training examples in each iteration.
    * A compromise between batch and stochastic gradient descent, offering a balance of speed and stability.

## Challenges:

* **Local Minima:**
    * Gradient descent can get stuck in local minima, which are points where the function is minimized within a local region but not globally.
* **Saddle Points:**
    * These are points where the function is flat in some directions and steep in others, which can slow down or stall the algorithm.
* **Learning Rate Selection:**
    * Choosing an appropriate learning rate is essential for convergence.

## Applications

* Gradient descent is the backbone of many machine learning algorithms, including:
    * [[gradient boosting]] to find weak learner
    * Neural networks during backpropagation

[^1]: The gradient of a scalar-valued function $f(x_1, x_2, ..., x_n)$ is a vector: $\nabla f(x) = \left(\frac{\partial f}{\partial x_1}, \frac{\partial f}{\partial x_2}, ..., \frac{\partial f}{\partial x_n}\right)$