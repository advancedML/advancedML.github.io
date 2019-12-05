---
layout: page
title: Proposal
permalink: /proposal/
exclude: false
---

# Project Proposal for Advanced Machine Learning: A Reproduction of “Algorithms for Fitting the Constrained Lasso”

Yifu Li and Xiaoyu Chen

Department of Industrial and Systems Engineering, Virginia Tech

<hr>

### Content
1. [INTRODUCTION](#1-introduction)
2. [PRELIMINARY PLAN](#2-preliminary-plan)
3. [EVALUATION METHODS](#3-evaluation-methods)
4. [DELIVERABLES](#4-deliverables)
5. [REFERENCES](#references)

<hr>

### 1. INTRODUCTION

Variable selection is an important topic in machine learning and statistical learning to identify significant variables to facilitate better interpretations of correlations among dependent and independent variables. Many variable selection methods have been proposed, such as the Lasso (Tibshirani, 1996) and its variants. In this project, the paper entitled “Algorithms for Fitting the Constrained Lasso” by Gaines et al. (2018) is selected to understand and reproduce a constrained variable selection model Constrained Lasso by using alternating direction method of multipliers (ADMM) associated with an efficient solution path algorithm. The team will reproduce the results in application study in both “4. Simulation Examples” and “5.2. Brain Tumor Data” by comparing with benchmark methods such as the Lasso and fused Lasso. Specifically, Figure 2 and Figure 3 will be reproduced. 

### 2. PRELIMINARY PLAN

Python programming language will be used to implement the proposed ADMM solver and solution path algorithm in (Gaines et al., 2018). Mr. Chen will implement ADMM solver and reproduce the results for simulation examples, and Mr. Li will implement solution path algorithm and reproduce the results for Brain Tumor data analysis. For simulation examples, we will follow the same settings as provided in the paper. And for case study in Brain Tumor Data, the data set from the link will be adopted.

### 3. EVALUATION METHODS

We would like to validate if Constrained Lasso generates better performance compared with methods that perform variable selection, such as linear regression, ridge regression (Hoerl et al., 1975), lasso regression (Tibshirani, 1996), fused Lasso (Petersen et al., 2016) or other advanced machine learning methods. The accuracy, variable selection results, and solution paths from benchmark models will be compared with the Constrained Lasso model. The reproduced Figure 2 and 3 will be compared with original Figure 2 and 3. The differences will be further analyzed by using statistical tests and will be interpreted in terms of algorithms.

### 4. DELIVERABLES

A website will be created 1) to summarize the result from the original paper; 2) to describe the procedure you took to reproduce the result; 3) to present measurements and/or analysis of what discovered; and 4) to provide references to relevant papers.

### REFERENCES

- Gaines, B. R., Kim, J., and Zhou, H. (2018) "Algorithms for fitting the constrained lasso." Journal of Computational and Graphical Statistics 27.4: 861-871.
- Hoerl, A. E., Kannard, R. W., and Baldwin, K. F. (1975), "Ridge Regression: Some Simulations," Communications in Statistics-Theory and Methods, 4, 105-123.
- Petersen, A., Witten, D., and Simon, N. (2016), "Fused Lasso Additive Model," Journal of Computational and Graphical Statistics, 25, 1005-1025.
- Tibshirani, R. (1996), "Regression Shrinkage and Selection Via the Lasso," Journal of the Royal Statistical Society: Series B (Methodological), 58, 267-288.
- Xin, B., Tian, Y., Wang, Y., and Gao, W. (2015), "Background Subtraction Via Generalized Fused Lasso Foreground Modeling," in Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition, pp. 4676-4684.
