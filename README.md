# Evaluating Classifier Performance for Predicting Passenger Survival Aboard the Titanic
**Author:** Joseph Ouyang

---

The sinking of the RMS Titanic in 1912 remains one of the most well-known maritime disasters in history. This project applies four machine learning classification methods to demographic and travel-related passenger data in order to predict survival outcomes and evaluate which classifier performs best.

---

## Table of Contents
- [Dataset](#dataset)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Modeling](#modeling)
- [Results](#results)
- [Final Recommendation](#final-recommendation)
- [Tech Stack](#tech-stack)
- [Repository Structure](#repository-structure)

---

## Dataset

The dataset originates from Titanic passenger records curated by Frank Harrell at the Department of Biostatistics, Vanderbilt University. A random sample of 889 passengers is split into a training set of 622 passengers and a test set of 267 passengers.

### Variables

| Variable | Type | Description |
|---|---|---|
| `Survived` | Response | Survived (1) or died (0) |
| `Pclass` | Categorical | Ticket class: 1st, 2nd, or 3rd |
| `Gender` | Categorical | Male or female |
| `SibSp` | Quantitative | Number of siblings and spouses aboard |
| `Parch` | Quantitative | Number of parents and children aboard |
| `Fare` | Quantitative | Passenger fare in modern British pounds |
| `Embarked` | Categorical | Port of embarkation: C = Cherbourg, Q = Queenstown, S = Southampton |

### Class Balance

In the training set of 622 passengers, 234 survived (37.6%) and 388 did not (62.4%), indicating a moderately imbalanced response.

---

## Exploratory Data Analysis

### Quantitative Predictors

Boxplots of each quantitative predictor against survival outcome reveal the following:

- **Fare**: passengers who survived paid notably higher fares on average, suggesting a meaningful relationship with survival
- **Parch**: survivors show a wider spread in number of parents and children aboard, ranging from 0 to 2 at the median to Q3, while non-survivors are concentrated at 0
- **SibSp**: distributions are similar across both groups, with no clear separation at first glance

A pairs plot of all three quantitative predictors shows no clean linear separation between survivors and non-survivors. The most informative signal comes from Fare, where higher-paying passengers tend to survive more.

### Categorical Predictors

Conditional proportions of survival across each categorical variable reveal strong patterns:

- **Passenger class**: survival rate decreases sharply from 1st class (61%) to 2nd class (44%) to 3rd class (25%)
- **Gender**: females survived at a rate of 75% compared to 19% for males, making gender the strongest single predictor
- **Port of embarkation**: passengers from Cherbourg survived at a higher rate (57%) than those from Queenstown (36%) or Southampton (33%)

---

## Modeling

Four classification methods are trained on the same training set and evaluated on the same test set. LDA and QDA use only the quantitative predictors (SibSp, Parch, Fare), while the classification tree and logistic regression use all predictors including the categorical variables.

### Linear Discriminant Analysis

LDA assumes equal covariance matrices across classes and fits linear decision boundaries in the quantitative feature space.

Confusion matrix on test data:

|  | Predicted 0 | Predicted 1 |
|---|---|---|
| Actual 0 | 149 | 83 |
| Actual 1 | 12 | 23 |

- Overall error rate: 0.3558
- Error rate for survivors: 0.783
- Error rate for non-survivors: 0.0745

### Quadratic Discriminant Analysis

QDA relaxes the equal-covariance assumption, allowing curved decision boundaries in the quantitative feature space.

Confusion matrix on test data:

|  | Predicted 0 | Predicted 1 |
|---|---|---|
| Actual 0 | 146 | 73 |
| Actual 1 | 15 | 33 |

- Overall error rate: 0.3296
- Error rate for survivors: 0.689
- Error rate for non-survivors: 0.0932

### Classification Tree

The classification tree partitions the feature space recursively using binary splits and can incorporate all predictors including categorical variables.

The fitted tree uses gender as the primary split, sending the roughly two thirds of passengers who are male to a terminal node with an estimated survival probability of 0.19. Among females, passenger class is the next split. Women in 1st or 2nd class reach a terminal node with a survival probability of 0.95. For women in 3rd class, fare and port of embarkation further divide survival outcomes, ranging from 0.11 to 0.71 across the remaining terminal nodes.

Confusion matrix on test data:

|  | Predicted 0 | Predicted 1 |
|---|---|---|
| Actual 0 | 141 | 32 |
| Actual 1 | 20 | 74 |

- Overall error rate: 0.1948
- Error rate for survivors: 0.3019
- Error rate for non-survivors: 0.1242

### Binary Logistic Regression

Logistic regression models the log-odds of survival as a linear combination of all predictors. Predicted probabilities are converted to class labels using a threshold of 0.5.

Confusion matrix on test data:

|  | Predicted 0 | Predicted 1 |
|---|---|---|
| Actual 0 | 128 | 29 |
| Actual 1 | 33 | 77 |

- Overall error rate: 0.2322
- Error rate for survivors: 0.2736
- Error rate for non-survivors: 0.2049

---

## Results

Summary of test-set error rates across all four classifiers:

| Model | Overall Error | Survivor Error | Non-Survivor Error |
|---|---|---|---|
| LDA | 0.3558 | 0.783 | 0.0745 |
| QDA | 0.3296 | 0.689 | 0.0932 |
| Logistic Regression | 0.2322 | 0.2736 | 0.2049 |
| Classification Tree | **0.1948** | 0.3019 | 0.1242 |

LDA and QDA both perform poorly on survivors, achieving error rates above 0.68, because they rely solely on the quantitative predictors and cannot leverage the strong signal from gender and passenger class. The classification tree and logistic regression improve substantially by incorporating categorical variables.

---

## Final Recommendation

The classification tree achieves the lowest overall test error rate of 0.1948 and is the recommended model. It also provides a transparent, interpretable structure that clearly communicates how the predictors relate to survival. Logistic regression is a close second with an overall error rate of 0.2322 and a slightly lower survivor error rate (0.2736 vs. 0.3019), but its higher non-survivor error rate and lower interpretability make it a weaker choice for this application.

For future work, random forests could address potential overfitting in a single tree by averaging predictions across many trees. Incorporating additional variables such as age and cabin location may also reveal interactions not captured by the current predictor set.

---

## Tech Stack

| Tool | Use |
|---|---|
| R | Primary analysis language |
| MASS | LDA and QDA via lda() and qda() |
| tree, rpart, rpart.plot | Classification tree fitting and visualization |
| stats | Logistic regression via glm() |
| dplyr, readr | Data wrangling and CSV ingestion |
| knitr, kableExtra, pander | Report generation and table formatting |

---

## Repository Structure

```
titanic-classifier/
├── Titanic.Rmd            # R Markdown source file
├── FinalTitanic.pdf       # compiled writeup
├── titanic_train.csv      # training data (622 passengers)
├── titanic_test.csv       # test data (267 passengers)
└── README.md
```

---

*Completed for 36-202 Methods for Statistics and Data Science, Carnegie Mellon University, December 2025.*
