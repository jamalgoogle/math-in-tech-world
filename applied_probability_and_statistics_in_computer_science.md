# Applied Probability and Statistics in Computer Science

Probability and statistics form the mathematical framework for handling uncertainty, making optimal decisions under incomplete information, quantifying noise, and modeling stochastic processes. From training high-parameter machine learning architectures to ensuring cloud infrastructure uptime and guiding generation in large language models, probabilistic reasoning is central to modern computer systems. This document details five major domains where probability and statistics are applied in computing.

## 1. Machine Learning and Data Science

Machine learning models construct continuous probabilistic distributions over data to perform prediction, classification, and generative modeling under uncertainty.

### 1.1 Maximum Likelihood Estimation (MLE) and Maximum A Posteriori (MAP)

Parameter estimation identifies the optimal model parameters $\boldsymbol{\theta}$ given an observed dataset $\mathcal{D} = \{x_1, x_2, \dots, x_N\}$.

* **Maximum Likelihood Estimation (MLE):** Selects parameters that maximize the probability of observing the data, assuming sample independence:

  $$
  \boldsymbol{\theta}_{\text{MLE}} = \arg\max_{\boldsymbol{\theta}} P(\mathcal{D} \mid \boldsymbol{\theta}) = \arg\max_{\boldsymbol{\theta}} \sum_{i=1}^{N} \ln P(x_i \mid \boldsymbol{\theta})
  $$

* **Maximum A Posteriori (MAP):** Incorporates prior beliefs $P(\boldsymbol{\theta})$ about parameter distributions using Bayes' Theorem, providing regularization against overfitting:

  $$
  \boldsymbol{\theta}_{\text{MAP}} = \arg\max_{\boldsymbol{\theta}} P(\boldsymbol{\theta} \mid \mathcal{D}) = \arg\max_{\boldsymbol{\theta}} \left[ \sum_{i=1}^{N} \ln P(x_i \mid \boldsymbol{\theta}) + \ln P(\boldsymbol{\theta}) \right]
  $$

### 1.2 Bayes' Theorem and Inference

Bayes' Theorem updates the conditional probability of a hypothesis $H$ given empirical evidence $E$:

$$
P(H \mid E) = \frac{P(E \mid H) P(H)}{P(E)} = \frac{P(E \mid H) P(H)}{\sum_{k} P(E \mid H_k) P(H_k)}
$$

* **Prior Probability $P(H)$:** Initial probability distribution before observing evidence.
* **Likelihood $P(E \mid H)$:** Probability of observing the evidence given the hypothesis.
* **Posterior Probability $P(H \mid E)$:** Updated probability distribution after observing evidence.

### 1.3 Expectation-Maximization (EM) Algorithm

For models with unobserved latent variables $\mathbf{Z}$ (e.g., Gaussian Mixture Models - GMMs), the EM algorithm iteratively maximizes the expected log-likelihood:

1. **Expectation Step (E-step):** Compute the expected log-likelihood under the current posterior distribution of latent variables:

   $$
   Q(\boldsymbol{\theta}, \boldsymbol{\theta}^{(t)}) = \mathbb{E}_{\mathbf{Z} \mid \mathbf{X}, \boldsymbol{\theta}^{(t)}} \left[ \ln P(\mathbf{X}, \mathbf{Z} \mid \boldsymbol{\theta}) \right]
   $$

2. **Maximization Step (M-step):** Update parameters by maximizing the expectation:

   $$
   \boldsymbol{\theta}^{(t+1)} = \arg\max_{\boldsymbol{\theta}} Q(\boldsymbol{\theta}, \boldsymbol{\theta}^{(t)})
   $$

---

## 2. A/B Testing, Experimentation, and Analytics

Controlled online experiments (A/B testing) determine whether algorithmic changes (such as new UI layouts, ranking models, or recommendation rules) yield statistically significant improvements in user engagement.

### 2.1 Hypothesis Testing and Error Types

* **Null Hypothesis ($H_0$):** Assumes no true difference exists between variant $A$ (control) and variant $B$ (treatment).
* **Alternative Hypothesis ($H_1$):** Assumes a meaningful difference exists.

| Decision \ True State | $H_0$ is True | $H_1$ is True |
| :--- | :--- | :--- |
| **Reject $H_0$** | **Type I Error ($\alpha$)** (False Positive) | Correct Decision ($1 - \beta$) (Statistical Power) |
| **Fail to Reject $H_0$** | Correct Decision ($1 - \alpha$) | **Type II Error ($\beta$)** (False Negative) |

* **$p$-value:** The probability of obtaining test results at least as extreme as the observed results, assuming $H_0$ is true. $H_0$ is rejected when $p < \alpha$ (commonly $\alpha = 0.05$).

### 2.2 Test Statistics and Sample Size Determination

* **Two-Sample $Z$-Test for Conversion Rates:** Compares sample proportions $p_A$ and $p_B$ over large sample sizes $n_A, n_B$:

  $$
  Z = \frac{p_B - p_A}{\sqrt{\hat{p}(1 - \hat{p}) \left( \frac{1}{n_A} + \frac{1}{n_B} \right)}}, \quad \text{where } \hat{p} = \frac{x_A + x_B}{n_A + n_B}
  $$

* **Sample Size Calculation:** To detect a minimum standardized effect size $\delta$ with significance level $\alpha$ and statistical power $1 - \beta$:

  $$
  n \approx \frac{2 (Z_{\alpha/2} + Z_{\beta})^2 \sigma^2}{\delta^2}
  $$

### 2.3 Multi-Armed Bandits for Dynamic Experimentation

Traditional A/B testing spends equal traffic on sub-optimal variants during experimentation. **Multi-Armed Bandit (MAB)** algorithms dynamically balance exploration (learning variant performance) and exploitation (routing users to the current best variant).

* **Thompson Sampling:** A Bayesian approach that samples parameters from each variant's posterior reward distribution (e.g., $\text{Beta}(\alpha_i, \beta_i)$ for binary conversion rates) and selects the variant with the highest sampled value:

  $$
  \theta_i \sim \text{Beta}(\alpha_i + \text{successes}_i, \, \beta_i + \text{failures}_i)
  $$

* **Upper Confidence Bound (UCB1):** Chooses variant $i$ maximizing its estimated mean plus an uncertainty bound based on total interactions $N$ and variant pulls $n_i$:

  $$
  S_i = \bar{x}_i + \sqrt{\frac{2 \ln N}{n_i}}
  $$

---

## 3. Spam Filtering, Naive Bayes, and Anomaly Detection

Probabilistic classification models detect malicious traffic, filter unwanted emails, and identify unusual system behaviors in real time.

### 3.1 The Naive Bayes Classifier

The Naive Bayes classifier computes the class probability $P(y \mid \mathbf{x})$ for an input feature vector $\mathbf{x} = (x_1, x_2, \dots, x_d)$ (e.g., word occurrences in an email) under the assumption of conditional feature independence given class $y$:

$$
P(y \mid x_1, \dots, x_d) \propto P(y) \prod_{i=1}^{d} P(x_i \mid y)
$$

* **Laplace (Add-One) Smoothing:** Prevents zero-probability estimates for unseen features during inference over vocabulary size $|V|$:

  $$
  P(x_i \mid y) = \frac{\text{count}(x_i, y) + 1}{\text{count}(y) + |V|}
  $$

### 3.2 Anomaly Detection via Gaussian Distributions

Identifying outliers or anomalous system states relies on modeling baseline feature vectors using multi-variate Gaussian distributions.

* **Mahalanobis Distance:** Measures the distance of a data point $\mathbf{x}$ from distribution mean $\boldsymbol{\mu}$ scaled by covariance matrix $\boldsymbol{\Sigma}$:

  $$
  D_M(\mathbf{x}) = \sqrt{(\mathbf{x} - \boldsymbol{\mu})^T \boldsymbol{\Sigma}^{-1} (\mathbf{x} - \boldsymbol{\mu})}
  $$

  Points exceeding a critical $\chi^2$ threshold probability are flagged as structural anomalies.

### 3.3 Evaluation Metrics for Probabilistic Classifiers

Evaluating probabilistic classifiers on imbalanced datasets requires metrics beyond simple accuracy:

* **Precision and Recall:**

  $$
  \text{Precision} = \frac{TP}{TP + FP}, \quad \text{Recall} = \frac{TP}{TP + FN}
  $$

* **Receiver Operating Characteristic (ROC) & AUC:** Plots the True Positive Rate ($\text{TPR} = \frac{TP}{TP + FN}$) against the False Positive Rate ($\text{FPR} = \frac{FP}{FP + TN}$) across continuous probability decision thresholds. The Area Under the Curve (AUC) measures overall class separation performance.

---

## 4. System Reliability, Site Reliability Engineering (SRE), and Performance Monitoring

Site Reliability Engineers (SREs) model service uptime, failure probabilities, and network latency dynamics using continuous probability distributions and stochastic point processes.

### 4.1 Request Arrivals and Poisson Processes

System request arrivals across a network link are modeled as a **Poisson Process** with rate parameter $\lambda$ (average requests per unit time).

* **Poisson Distribution:** Probability of observing exactly $k$ requests in time interval $t$:

  $$
  P(N(t) = k) = \frac{(\lambda t)^k e^{-\lambda t}}{k!}
  $$

* **Exponential Distribution:** The inter-arrival time $T$ between consecutive requests is memoryless and follows an exponential distribution:

  $$
  f(t) = \lambda e^{-\lambda t}, \quad \mathbb{E}[T] = \frac{1}{\lambda}
  $$

### 4.2 Availability Metrics and High-Availability Modeling

System component lifespans are modeled using **Survival Analysis** and Weibull/Exponential hazard functions.

* **Mean Time Between Failures (MTBF) & Mean Time To Repair (MTTR):**

  $$
  \text{Availability} = \frac{\text{MTBF}}{\text{MTBF} + \text{MTTR}}
  $$

* **Target Availability ("Nines"):**

  $$\text{Five Nines } (99.999\%) \implies \text{Total Downtime } \le 5.26 \text{ minutes/year}$$

### 4.3 Tail Latency, Percentiles, and Anomaly Alerting

Evaluating system performance using mean latency gives a misleading picture due to skewed, heavy-tailed response distributions. High-percentile metrics ($p_{95}, p_{99}, p_{99.9}$) reflect the experience of worst-case requests.

* **Dynamic Alerting Thresholds via $Z$-score:** Flags dynamic metric anomalies when observed value $x_t$ exceeds moving average $\mu_t$ by $k$ standard deviations $\sigma_t$:

  $$
  Z = \frac{x_t - \mu_t}{\sigma_t} > k \quad (k \text{ typically between } 3 \text{ and } 5)
  $$

* **Exponential Weighted Moving Average (EWMA):** Tracks baseline shifts over time:

  $$
  \mu_t = \alpha x_t + (1 - \alpha) \mu_{t-1}
  $$

---

## 5. Probabilistic Language Models and Token Prediction

Modern Large Language Models (LLMs) model text generation as sequence completion over a vocabulary $V$ by predicting the probability distribution of the next token conditioned on preceding context.

### 5.1 Chain Rule of Probability and $n$-gram Models

The joint probability of a sequence of tokens $W = (w_1, w_2, \dots, w_T)$ is factored via the chain rule of probability:

$$
P(w_1, w_2, \dots, w_T) = \prod_{t=1}^{T} P(w_t \mid w_1, w_2, \dots, w_{t-1})
$$

* **Markov Property ($n$-gram approximation):** Assumes the probability of token $w_t$ depends only on the previous $n-1$ tokens:

  $$
  P(w_t \mid w_1, \dots, w_{t-1}) \approx P(w_t \mid w_{t-n+1}, \dots, w_{t-1}) = \frac{\text{count}(w_{t-n+1}, \dots, w_t)}{\text{count}(w_{t-n+1}, \dots, w_{t-1})}
  $$

### 5.2 Softmax Distribution in Neural Language Models

Neural networks project context embeddings to a vector of unnormalized log-probabilities (logits) $\mathbf{z} \in \mathbb{R}^{|V|}$. The **Softmax function** transforms logits into a valid categorical probability distribution:

$$
P(w_i \mid \text{context}) = \frac{e^{z_i / T}}{\sum_{j=1}^{|V|} e^{z_j / T}}
$$

* **Temperature Scaling ($T$):**
  * $T \to 0$: Distribution becomes deterministic (sharp peak around $\arg\max_i z_i$).
  * $T > 1$: Distribution becomes flatter, increasing generation diversity.

### 5.3 Decoding and Sampling Strategies

Converting predicted probability distributions into generated text tokens involves controlled sampling:

* **Top-$k$ Sampling:** Truncates the vocabulary candidate pool to the $k$ most probable tokens before re-normalizing probabilities:

  $$
  V^{(k)} = \arg\max_{S \subset V, |S|=k} \sum_{i \in S} P(w_i)
  $$

* **Top-$p$ (Nucleus) Sampling:** Dynamically selects the smallest subset of tokens whose cumulative probability exceeds threshold $p \in (0, 1]$:

  $$
  V^{(p)} = \left\{ w \in V : \sum_{i=1}^{|V^{(p)}|} P(w_i) \ge p \right\}
  $$

### 5.4 Cross-Entropy Loss and Perplexity

The quality of a language model $Q$ relative to empirical text distribution $P$ is measured using information-theoretic metrics.

* **Cross-Entropy Loss:**

  $$
  H(P, Q) = -\frac{1}{T} \sum_{t=1}^{T} \ln Q(w_t \mid w_1, \dots, w_{t-1})
  $$

* **Perplexity ($\text{PPL}$):** Exponentiated cross-entropy, measuring the effective branching factor when predicting the next token:

  $$
  \text{PPL}(W) = \exp\left( H(P, Q) \right) = \left( \prod_{t=1}^{T} \frac{1}{Q(w_t \mid w_{1:t-1})} \right)^{\frac{1}{T}}
  $$

  Lower perplexity indicates higher confidence and accuracy in next-token predictions.

---

## Summary Matrix

| Domain / Concept | Primary Probabilistic Basis | Core CS Application |
| :--- | :--- | :--- |
| **Machine Learning** | Maximum Likelihood (MLE), MAP, Bayes' Theorem, EM Algorithm | Model fitting, parameter estimation, density estimation |
| **A/B Testing & Analytics** | Hypothesis Testing ($Z$/$t$-tests), $p$-values, Thompson Sampling | Feature validation, conversion optimization, dynamic traffic routing |
| **Spam & Anomaly Filtering** | Naive Bayes, Mahalanobis Distance, ROC-AUC | Email spam classification, network intrusion detection, outlier discovery |
| **System Reliability & SRE** | Poisson Processes, Exponential Distribution, Weibull Survival | Cloud uptime tracking ($99.999\%$), tail latency ($p_{99}$), $Z$-score alerting |
| **Language Models** | Categorical Distributions, Softmax, Cross-Entropy, Perplexity | LLM next-token prediction, Temperature/Top-$p$ decoding, text evaluation |
