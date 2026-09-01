# LASSO Estimation

## Overview

This repository contains R code for performing **Least Absolute Shrinkage and Selection Operator (LASSO)** regression using the `glmnet` package.

LASSO is a regularized regression technique that can be used for:

* Variable selection
* Reducing model complexity
* Handling potential multicollinearity
* Improving predictive performance
* Identifying important explanatory variables

The method applies an **L1 penalty** to the regression coefficients, which can shrink some coefficients exactly to zero.

---

# Model Structure

The analysis uses one outcome variable and seven explanatory variables.

| Variable | Role                        |
| -------- | --------------------------- |
| `A`      | Response / outcome variable |
| `B`      | Predictor                   |
| `C`      | Predictor                   |
| `D`      | Predictor                   |
| `E`      | Predictor                   |
| `F`      | Predictor                   |
| `G`      | Predictor                   |
| `H`      | Predictor                   |

The model can therefore be represented as:

```text
A = f(B, C, D, E, F, G, H)
```

where LASSO determines the contribution of each predictor while applying regularization.

---

# Required R Package

The analysis uses the `glmnet` package.

Install the package if it is not already installed:

```r
install.packages("glmnet")
```

Load the package:

```r
library(glmnet)
```

---

# 1. Preparing the Data

The predictor variables are converted into a matrix:

```r
x <- as.matrix(
  WORKING_FILE[, c("B", "C", "D", "E", "F", "G", "H")]
)
```

The response variable is defined as:

```r
y <- WORKING_FILE$A
```

Thus:

* `x` contains predictors `B` through `H`.
* `y` contains the outcome variable `A`.

The `glmnet` package requires the predictor variables to be supplied as a matrix.

---

# 2. LASSO Estimation

The LASSO model is estimated using:

```r
lasso_model <- glmnet(
  x,
  y,
  alpha = 1
)
```

The parameter:

```r
alpha = 1
```

specifies a **LASSO regression**, corresponding to an L1 regularization penalty.

The estimated coefficients can be examined using:

```r
coef(lasso_model)
```

This returns the coefficients for the different values of the regularization parameter `λ`.

---

# 3. Cross-Validation

Cross-validation is used to identify an appropriate value of the regularization parameter.

```r
cv_lasso <- cv.glmnet(
  x,
  y,
  alpha = 1
)
```

The cross-validation results can be visualized with:

```r
plot(cv_lasso)
```

The resulting plot shows the cross-validated prediction error across different values of `λ`.

---

# 4. Selecting the Optimal Lambda

Two important values of `λ` can be extracted from the cross-validation results.

### Lambda with Minimum Cross-Validated Error

```r
cv_lasso$lambda.min
```

`lambda.min` is the value of `λ` that produces the **minimum cross-validated error**.

### Lambda Within One Standard Error

```r
cv_lasso$lambda.1se
```

`lambda.1se` represents a more regularized model whose cross-validated error is within one standard error of the minimum.

This generally produces a **simpler model** with stronger regularization.

---

# 5. Extracting the Best Model

The value of `lambda.min` is assigned to:

```r
best_lambda <- cv_lasso$lambda.min
```

The coefficients corresponding to the selected value of `λ` are then extracted:

```r
coef(
  cv_lasso,
  s = "lambda.min"
)
```

This provides the estimated coefficients for the LASSO model using the value of `λ` that minimizes the cross-validated error.

---

# Complete Code

```r
# ------------------------------------------------------------
# LASSO ESTIMATION
# ------------------------------------------------------------

# Install package if not already installed
install.packages("glmnet")

library(glmnet)


# ------------------------------------------------------------
# PREPARE DATA
# ------------------------------------------------------------

# Predictors
x <- as.matrix(
  WORKING_FILE[, c("B", "C", "D", "E", "F", "G", "H")]
)

# Response variable
y <- WORKING_FILE$A


# ------------------------------------------------------------
# LASSO MODEL
# ------------------------------------------------------------

lasso_model <- glmnet(
  x,
  y,
  alpha = 1
)

# Display coefficients
coef(lasso_model)


# ------------------------------------------------------------
# CROSS-VALIDATED LASSO
# ------------------------------------------------------------

cv_lasso <- cv.glmnet(
  x,
  y,
  alpha = 1
)

# Plot cross-validation results
plot(cv_lasso)


# ------------------------------------------------------------
# SELECT OPTIMAL LAMBDA
# ------------------------------------------------------------

# Lambda giving minimum cross-validated error
cv_lasso$lambda.min

# Simpler model within one standard error
cv_lasso$lambda.1se


# ------------------------------------------------------------
# EXTRACT FINAL LASSO COEFFICIENTS
# ------------------------------------------------------------

best_lambda <- cv_lasso$lambda.min

coef(
  cv_lasso,
  s = "lambda.min"
)
```

---

# Interpretation

LASSO estimates coefficients while simultaneously applying a penalty controlled by `λ`.

As the value of `λ` increases:

* More coefficients are shrunk toward zero.
* Some coefficients may become exactly zero.
* The resulting model becomes more parsimonious.

A coefficient equal to zero means that the corresponding predictor has effectively been **excluded from the LASSO model at that value of `λ`**.

The `lambda.min` model prioritizes predictive performance based on cross-validation, while `lambda.1se` generally favors a more parsimonious specification.

---

# Analytical Workflow

The analysis follows this workflow:

```text
                 WORKING_FILE
                      │
                      ▼
                Variables A–H
                      │
              ┌───────┴───────┐
              ▼               ▼
         Outcome A       Predictors B–H
              │               │
              └───────┬───────┘
                      ▼
                LASSO Model
                      │
                      ▼
              Coefficient Paths
                      │
                      ▼
             Cross-Validation
                      │
              ┌───────┴───────┐
              ▼               ▼
         lambda.min       lambda.1se
              │               │
              ▼               ▼
        Selected Model   Simpler Model
```

---

# Reproducibility

To reproduce the analysis:

1. Install R and RStudio.
2. Install the `glmnet` package.
3. Load the `WORKING_FILE` dataset.
4. Ensure that variables `A` through `H` are available.
5. Convert the predictors to a matrix.
6. Estimate the LASSO model.
7. Perform cross-validation.
8. Examine `lambda.min` and `lambda.1se`.
9. Extract the coefficients using the selected value of `λ`.

---

# Notes

* `alpha = 1` specifies LASSO regression.
* `lambda.min` selects the value of `λ` associated with the minimum cross-validated error.
* `lambda.1se` provides a more regularized and generally simpler alternative.
* The final coefficient estimates depend on the selected value of `λ`.
* LASSO can be particularly useful when the number of potential predictors is relatively large or when predictors may be correlated.

---

# Purpose

This repository provides a reproducible implementation of **LASSO regression with cross-validation** for variable selection and regularized estimation.

The approach combines coefficient shrinkage with cross-validation to identify a suitable level of regularization and determine which predictors contribute to the final model.

---

# License

MIT License

Copyright (c) 2026 Muhammad Waqas Khalid

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

```

This is ready to paste into your GitHub repository as **`README.md`**.
```
