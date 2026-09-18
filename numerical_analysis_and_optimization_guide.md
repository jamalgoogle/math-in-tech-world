# Comprehensive Guide: Numerical Analysis and Optimization

Numerical analysis and optimization form the mathematical engine powering modern artificial intelligence, industrial operations, scientific computation, and quantitative finance. While theoretical mathematics deals with exact closed-form solutions, real-world continuous systems demand algorithmic approximations, iterative solvers, and robust handling of finite-precision hardware limitations.

---

## 1. Scientific Computing & Floating-Point Arithmetic

Digital computers approximate the continuous real number line $\mathbb{R}$ using discrete floating-point representations. Understanding hardware arithmetic constraints is critical to preventing catastrophic failures in simulation and machine learning.

### 1.1 IEEE 754 Floating-Point Standard
Real numbers are represented in binary scientific notation as:
$$x = (-1)^s \times (1 + m) \times 2^{e - B}$$

Where:
*   $s$ is the sign bit.
*   $m$ is the mantissa (fractional component).
*   $e$ is the biased exponent.
*   $B$ is the exponent bias ($B = 127$ for single precision `FP32`, $B = 1023$ for double precision `FP64`).

| Precision | Total Bits | Sign Bits | Exponent Bits | Mantissa Bits | Dynamic Range | Machine Epsilon ($\epsilon_{mach}$) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **FP64 (Double)** | 64 | 1 | 11 | 52 | $\approx 10^{-308} \text{ to } 10^{308}$ | $\approx 2.22 \times 10^{-16}$ |
| **FP32 (Single)** | 32 | 1 | 8 | 23 | $\approx 10^{-38} \text{ to } 10^{38}$ | $\approx 1.19 \times 10^{-7}$ |
| **FP16 (Half)** | 16 | 1 | 5 | 10 | $\approx 6 \times 10^{-5} \text{ to } 6.5 \times 10^{4}$ | $\approx 9.77 \times 10^{-4}$ |
| **BF16 (Bfloat16)**| 16 | 1 | 8 | 7 | $\approx 10^{-38} \text{ to } 10^{38}$ | $\approx 7.81 \times 10^{-3}$ |
| **FP8 (E4M3 / E5M2)**| 8 | 1 | 4 / 5 | 3 / 2 | Extremely constrained | Very High |

### 1.2 Errors, Condition Number, and Stability
*   **Absolute Error:** $\Delta x = |\hat{x} - x|$
*   **Relative Error:** $\delta x = \frac{|\hat{x} - x|}{|x|}$
*   **Catastrophic Cancellation:** Subtraction of two nearly identical floating-point numbers results in a severe loss of significant digits.
    $$\text{Example: } f(x) = \sqrt{x+1} - \sqrt{x} \implies \text{Reformulate as } \frac{1}{\sqrt{x+1} + \sqrt{x}}$$
*   **Condition Number ($\kappa$):** Measures the sensitivity of a function $f(x)$ or system $A x = b$ to small perturbations in input.
    $$\kappa(f) = \left| \frac{x f'(x)}{f(x)} \right|, \quad \kappa(A) = \|A\| \|A^{-1}\| = \frac{\sigma_{max}(A)}{\sigma_{min}(A)}$$
    *   If $\kappa(A) \approx 1$, the problem is **well-conditioned**.
    *   If $\kappa(A) \gg 1$, the problem is **ill-conditioned** (small input errors produce massive output errors).

---

## 2. Optimization in Training AI Models

Deep learning relies on unconstrained continuous optimization over non-convex loss surfaces $\mathcal{L}(\boldsymbol{\theta})$ defined over millions or billions of parameters $\boldsymbol{\theta} \in \mathbb{R}^d$.

```
                        Gradient Descent Paradigms
                                     |
         +---------------------------+---------------------------+
         |                                                       |
  First-Order Methods                                    Second-Order / Adaptive
  - Stochastic Gradient Descent (SGD)                    - AdaGrad / RMSProp
  - Momentum & Nesterov (NAG)                            - Adam / AdamW
  - Heavy Ball Acceleration                              - L-BFGS & K-FAC (Natural Gradient)
```

### 2.1 First-Order Optimization Algorithms

#### Stochastic Gradient Descent (SGD) with Momentum
Accelerates convergence along directions of persistent gradient and dampens oscillations:
$$v_{t+1} = \beta v_t + \eta \nabla_{\boldsymbol{\theta}} \mathcal{L}(\boldsymbol{\theta}_t)$$
$$\boldsymbol{\theta}_{t+1} = \boldsymbol{\theta}_t - v_{t+1}$$

Where $\eta$ is learning rate and $\beta \in [0, 1)$ is momentum coefficient (typically $0.9$).

#### Nesterov Accelerated Gradient (NAG)
Evaluates gradient at the "look-ahead" point:
$$v_{t+1} = \beta v_t + \eta \nabla_{\boldsymbol{\theta}} \mathcal{L}(\boldsymbol{\theta}_t - \beta v_t)$$
$$\boldsymbol{\theta}_{t+1} = \boldsymbol{\theta}_t - v_{t+1}$$

### 2.2 Adaptive Learning Rate Methods

#### Adam (Adaptive Moment Estimation)
Maintains exponentially decaying averages of past gradients ($m_t$) and squared gradients ($v_t$):

$$\begin{aligned}
m_t &= \beta_1 m_{t-1} + (1 - \beta_1) g_t \\
v_t &= \beta_2 v_{t-1} + (1 - \beta_2) g_t^2 \\
\hat{m}_t &= \frac{m_t}{1 - \beta_1^t}, \quad \hat{v}_t = \frac{v_t}{1 - \beta_2^t} \\
\boldsymbol{\theta}_{t+1} &= \boldsymbol{\theta}_t - \frac{\eta}{\sqrt{\hat{v}_t} + \epsilon} \hat{m}_t
\end{aligned}$$

Where standard defaults are $\beta_1 = 0.9$, $\beta_2 = 0.999$, $\epsilon = 10^{-8}$.

#### AdamW (Decoupled Weight Decay)
Decouples $\mathbf{L_2}$ regularization from adaptive gradient updates, preventing weight decay strength from being inappropriately scaled by historical gradient variance:
$$\boldsymbol{\theta}_{t+1} = \boldsymbol{\theta}_t - \eta \lambda \boldsymbol{\theta}_t - \frac{\eta}{\sqrt{\hat{v}_t} + \epsilon} \hat{m}_t$$

```python
# Python Pseudocode: AdamW Optimizer Step
import numpy as np

def adamw_step(params, grads, m, v, t, lr=0.001, beta1=0.9, beta2=0.999, eps=1e-8, weight_decay=0.01):
    t += 1
    # Decoupled weight decay
    params = params * (1.0 - lr * weight_decay)
    
    # Update biased first & second moment estimate
    m = beta1 * m + (1.0 - beta1) * grads
    v = beta2 * v + (1.0 - beta2) * (grads ** 2)
    
    # Compute bias-corrected estimates
    m_hat = m / (1.0 - beta1 ** t)
    v_hat = v / (1.0 - beta2 ** t)
    
    # Update parameters
    params -= lr * m_hat / (np.sqrt(v_hat) + eps)
    
    return params, m, v, t
```

### 2.3 Second-Order Methods & Natural Gradient
*   **Newton's Method:** Uses full Hessian matrix $H$:
    $$\boldsymbol{\theta}_{t+1} = \boldsymbol{\theta}_t - H^{-1} \nabla \mathcal{L}(\boldsymbol{\theta}_t)$$
    *Infeasible for high dimensions due to $\mathcal{O}(d^3)$ inversion cost.*
*   **L-BFGS (Limited-memory BFGS):** Approximates inverse Hessian using a memory-efficient history of recent gradient vectors.
*   **Natural Gradient Descent:** Computes updates along the Riemannian manifold of probability distributions using the Fisher Information Matrix $F$:
    $$\boldsymbol{\theta}_{t+1} = \boldsymbol{\theta}_t - \eta F^{-1} \nabla \mathcal{L}(\boldsymbol{\theta}_t)$$

---

## 3. Resource Allocation, Logistics, and Scheduling

Operational research optimizes decision-making under constrained environments using linear, integer, and convex programming.

```
                  Optimization Problem Classification
                                   |
         +-------------------------+-------------------------+
         |                                                   |
  Continuous Domains                                  Discrete / Mixed Domains
  - Linear Programming (LP)                           - Integer Programming (IP)
  - Convex Optimization (QP, SOCP, SDP)               - Mixed-Integer LP (MILP)
  - Solvers: Simplex, Interior-Point                  - Solvers: Branch & Bound, Cutting Planes
```

### 3.1 Linear Programming (LP) & Convex Optimization
A standard Linear Program is formulated as:
$$\begin{aligned}
\text{minimize} \quad & \mathbf{c}^T \mathbf{x} \\
\text{subject to} \quad & \mathbf{A} \mathbf{x} \le \mathbf{b} \\
& \mathbf{x} \ge \mathbf{0}
\end{aligned}$$

#### Primary Solution Algorithms
1.  **Simplex Method:** Traverses extreme points (vertices) along the polyhedral feasible region boundary. High performance in practice, though worst-case exponential time $\mathcal{O}(2^n)$.
2.  **Interior-Point Methods (Barrier Methods):** Follows a central path through the interior of the feasible region in polynomial time $\mathcal{O}(n^{3.5})$.

### 3.2 Karush-Kuhn-Tucker (KKT) Conditions
For non-linear convex optimization problems with inequality constraints:
$$\begin{aligned}
\text{minimize} \quad & f(\mathbf{x}) \\
\text{subject to} \quad & g_i(\mathbf{x}) \le 0, \quad i = 1, \dots, m \\
& h_j(\mathbf{x}) = 0, \quad j = 1, \dots, p
\end{aligned}$$

The **KKT First-Order Necessary Conditions** for optimal $\mathbf{x}^*$ and multipliers $(\boldsymbol{\lambda}^*, \boldsymbol{\nu}^*)$ are:

1.  **Stationarity:** $\nabla f(\mathbf{x}^*) + \sum_{i=1}^{m} \lambda_i^* \nabla g_i(\mathbf{x}^*) + \sum_{j=1}^{p} \nu_j^* \nabla h_j(\mathbf{x}^*) = \mathbf{0}$
2.  **Primal Feasibility:** $g_i(\mathbf{x}^*) \le 0 \quad \forall i, \quad h_j(\mathbf{x}^*) = 0 \quad \forall j$
3.  **Dual Feasibility:** $\lambda_i^* \ge 0 \quad \forall i$
4.  **Complementary Slackness:** $\lambda_i^* g_i(\mathbf{x}^*) = 0 \quad \forall i$

### 3.3 Mixed-Integer Linear Programming (MILP)
When dynamic decision variables are restricted to discrete integers ($\mathbf{x} \in \mathbb{Z}^n$), the problem becomes NP-hard.

#### Branch-and-Bound Algorithm Structure
```
                     [ Root LP Relaxation ]
                            /      \
             x1 <= floor(x1*)      x1 >= ceil(x1*)
                          /          \
                [ Subproblem 1 ]    [ Subproblem 2 ]
                   (Solve LP)          (Solve LP)
                     /      \
             (Prune if Infeasible / Bounds Exceeded)
```

1.  **Relaxation:** Solve linear continuous relaxation (drop integer constraints).
2.  **Branching:** Select a non-integer variable $x_k^* = 3.4$ and branch into two subproblems ($x_k \le 3$ and $x_k \ge 4$).
3.  **Bounding:** Prune subproblems whose local objective lower bound exceeds the current global best integer solution upper bound.

---

## 4. Financial and Engineering Modeling

Continuous physics, structural engineering, and quantitative finance require root-finding, curve fitting, and stochastic sampling.

### 4.1 Root-Finding & Non-Linear Solvers

Solving non-linear scalar or system equations $f(x) = 0$:

#### Newton-Raphson Method
Uses local tangent slope to achieve quadratic convergence rate $\mathcal{O}((\Delta x)^2)$:
$$x_{k+1} = x_k - \frac{f(x_k)}{f'(x_k)}$$

For multivariate systems $\mathbf{F}(\mathbf{x}) = \mathbf{0}$:
$$\mathbf{x}_{k+1} = \mathbf{x}_k - \mathbf{J}(\mathbf{x}_k)^{-1} \mathbf{F}(\mathbf{x}_k)$$
Where $\mathbf{J}$ is the Jacobian matrix $J_{ij} = \frac{\partial F_i}{\partial x_j}$.

#### Brent's Method
Hybrid root-finding algorithm combining Bisection (guaranteed convergence), Secant, and Inverse Quadratic Interpolation (fast convergence). Preferred choice in standard libraries (e.g., `scipy.optimize.brentq`).

### 4.2 Non-Linear Least Squares & System Identification
Fitting model parameter vectors $\boldsymbol{\beta}$ to empirical observations $(y_i, \mathbf{x}_i)$:
$$\min_{\boldsymbol{\beta}} S(\boldsymbol{\beta}) = \sum_{i=1}^{n} \left[ y_i - f(\mathbf{x}_i, \boldsymbol{\beta}) \right]^2$$

#### Levenberg-Marquardt (LM) Algorithm
Interpolates smoothly between Gradient Descent and Gauss-Newton algorithm using a damping parameter $\lambda$:
$$\left( \mathbf{J}^T \mathbf{J} + \lambda \text{diag}(\mathbf{J}^T \mathbf{J}) \right) \boldsymbol{\Delta \beta} = \mathbf{J}^T \mathbf{r}$$
*   Large $\lambda$: Behaves like robust Gradient Descent (far from minimum).
*   Small $\lambda$: Behaves like fast Gauss-Newton (near minimum).

### 4.3 Monte Carlo Simulations & Variance Reduction
Used for high-dimensional financial derivative pricing and structural risk assessment.

#### Pricing Derivative via Geometric Brownian Motion (GBM)
Stock price dynamic differential equation:
$$dS_t = \mu S_t dt + \sigma S_t dW_t \implies S_T = S_0 \exp\left( \left(\mu - \frac{\sigma^2}{2}\right)T + \sigma \sqrt{T} Z \right)$$
Where $Z \sim \mathcal{N}(0, 1)$.

#### Variance Reduction Techniques
1.  **Antithetic Variates:** For every standard random draw $Z_i$, evaluate corresponding complementary draw $-Z_i$ to reduce sample variance $\text{Var}\left(\frac{f(Z) + f(-Z)}{2}\right)$.
2.  **Importance Sampling:** Shifts sampling probability distribution $P(x)$ toward critical high-impact regions (e.g., rare extreme financial crash events or structural failure thresholds).

---

## 5. Summary Comparison Matrix

| Problem Category | Core Algorithm / Technique | Convergence Rate | Strengths | Limitations | Primary Use Case |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Large-Scale AI** | AdamW / SGD + Momentum | First-Order | Scales to $10^{11}$ parameters | Hyperparameter sensitive | Training Deep Nets, LLMs |
| **Structured LP** | Interior-Point / Simplex | Polynomial / Exponential worst-case | Global optimum guaranteed | Poor scalability on dynamic inputs | Supply chain, LP relaxation |
| **Integer Programming** | Branch-and-Cut (MILP) | Non-deterministic NP-hard | Exact combinatorial optimum | Exponential time worst-case | Vehicle routing, Scheduling |
| **Root-Finding** | Brent's Method / Newton | Superlinear to Quadratic | Extremely fast near root | Requires derivative or bracket | Implied volatility, Mechanics |
| **High-Dim Pricing** | Monte Carlo + Variance Red. | $\mathcal{O}(1/\sqrt{N})$ | Handles high-dimensional state space | Slow convergence rate | Option pricing, Risk analysis |

---

## 6. Practical Implementation Exercises

1.  **Optimization:** Implement AdamW from scratch in NumPy and optimize a 2D non-convex Rastrigin function.
2.  **Logistics:** Formulate a multi-facility supply chain transportation problem using `PuLP` or `SciPy` linear programming routines.
3.  **Financial Engineering:** Build a European option pricing engine using Monte Carlo simulations with antithetic variates and compare results against closed-form Black-Scholes equations.
4.  **Scientific Computation:** Benchmark double precision (`FP64`) vs half precision (`FP16`) matrix multiplications to measure loss of precision and catastrophic cancellation in ill-conditioned linear systems.