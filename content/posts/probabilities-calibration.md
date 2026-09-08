---
title: "Probability Calibration in Machine Learning: A Practical Introduction"
date: 2026-09-06T22:40:53+01:00
draft: false

description: "A practical introduction to probability calibration in machine learning, explaining model confidence, reliability, and calibration techniques."

tags:
  - Probability Calibration
  - Machine Learning
  - Deep Learning
  - Statistics
  - Tutorial

categories:
  - Machine Learning
  - Artificial Intelligence

math: true
---


### Probability calibration
Probability calibration is a post-processing step in machine learning and statistics that consists of ensuring that the probabilities predicted by a model correspond to real-world realities.

### Why is this necessary?
Many classification algorithms (such as support vector machines, random forests, or deep neural networks) produce scores or percentages, but these do not always reflect a true statistical probability.

For example, imagine a medical diagnostic model that predicts a patient has a 90% chance of having a disease. If this model is calibrated, it means that among all patients for whom it predicted 90%, approximately 90% are indeed ill. The model’s confidence thus corresponds to its actual accuracy.

The problem is that many modern models, particularly deep neural networks, are overfitted and produce overly extreme probabilities. For example, a network may predict a class with 99% confidence but be correct only 80% of the time. This represents a major issue of poor calibration.

### Numerical Example: The Bank Fraud Detector

Imagine you are developing a fraud detection model for a bank. Your model analyzes 1,000 transactions and outputs probabilities of fraud.
| Confidence Interval | Number of Predictions | Observed Fraud Rate |
| :--- | :---: | ---: |
| 90-100% | 100 | 65% |
| 80-90% | 150 | 55% |
| 70-80% | 200| 45% |
| <70 | 550| 10% |

The model predicts 90–100% confidence for 100 transactions, but only 65% are actually fraudulent. This means that 35% of the transactions the model identifies as “almost certainly fraudulent” (with 95% confidence) are in fact legitimate.

In a banking context, this could imply blocking 35 innocent accounts or triggering 35 unnecessary alerts, creating a disastrous customer experience.

**Key Point**: *Calibration does not make the model smarter (it does not improve its predictive ability); it makes its predictions more honest. It is the difference between an expert who acknowledges uncertainty and an “expert” who is always confident, even when wrong.*
Here is an equation embedded directly in the text: $E = mc^2$. This is what we call an "inline" equation.

If I want to display a more complex formula centred on its own line, I use an equation block:

$$
f(x) = \frac{1}{\sigma \sqrt{2\pi}} e^{-\frac{1}{2}\left(\frac{x-\mu}{\sigma}\right)^2}
$$


### The main calibration methods
To correct the gap between raw scores and true probabilities, two main approaches are used on a validation dataset (distinct from the training set):

1. Platt Scaling: A parametric method that applies logistic regression to the model's scores. It works well when the probability distortion follows a sigmoid shape.
2. Isotonic Regression: A more flexible non-parametric method that fits a piecewise constant, non-decreasing function to map scores to true probabilities. It is very effective when plenty of data is available.

### In which cases is it crucial?
Calibration is essential whenever the probability value itself guides a critical decision, and not just the final class (0 or 1):
* In medicine: To assess the actual risk of a patient developing a condition in order to adapt treatment.
* In finance and insurance: To estimate the exact probability of a credit default or a claim in order to price products fairly.
* In marketing: To target campaigns based on expected value (e.g., calculating customer lifetime value or CLV).