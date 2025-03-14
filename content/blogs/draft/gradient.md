---
draft: true
authors:
  - admin
title: gradient
date: 2025-03-14
summary: Brief description of the note.
---

The gradient of a scalar-valued function $f(x_1, x_2, ..., x_n)$ is a vector: $\nabla f(x) = \left(\frac{\partial f}{\partial x_1}, \frac{\partial f}{\partial x_2}, ..., \frac{\partial f}{\partial x_n}\right)$

![](/attachment/Figure_1.png)

**Mathematical Notation:**

* Function: $f(x)$ where $x = (x_1, x_2, ..., x_n)$
* Gradient: $\nabla f(x)$ or $\nabla f$
* Partial derivative: $\frac{\partial f}{\partial x_i}$ (where $i$ represents the $i$-th variable)

**Example:**

* Let $f(x, y) = x^2 + 2xy + y^3$
* Then $\nabla f(x, y) = \left(\frac{\partial f}{\partial x}, \frac{\partial f}{\partial y}\right)$
* $\frac{\partial f}{\partial x} = 2x + 2y$
* $\frac{\partial f}{\partial y} = 2x + 3y^2$
* Therefore, $\nabla f(x, y) = (2x + 2y, 2x + 3y^2)$

**Key Points:**

* The gradient is a vector of partial derivatives.
* It points in the direction of the steepest ascent of the function.
* The magnitude of the gradient indicates the rate of change.
* It is used in optimization algorithms like gradient descent.