---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults
title: advMLProj
layout: page
---
# A Reproduction of “Algorithms for Fitting the Constrained Lasso”

===
## INTRODUCTION

The original paper focuses on studying the constrained lasso problem. Specifically, authors studied the generalized lasso problem, which integrates the classical lasso problem with linear equality and inequality constraints. Through simulation studies and real-data applications, the proposed model structure allows users to infused the prior knowledge during the model parameter estimation. More importantly, authors have discovered that a variety of lasso types of problems, including the generalized lasso, can always be reformulated and solved as a constrained lasso problem. This findings provides excellent opportunity to scale up the applications that constrained lasso can handle. The authors derived three different algorithms to easimtae the model parameters for constrained lasso problems as a function of the tuning parameter ρ.
Specifically, these three methods are: 

- quadratic programming (QP), 
- the alternating direction method of multipliers (ADMM), 
- a novel algorithm providing the solution path. 

In the simulation studies, the authors found out that the novel solution path algorithm has the best performance on model estimation time with competitive modeling accuracy. As the size of data grows, ADMM becomes computationally efficient, despite that the performance of ADMM is sensitive to particular values of the tuning parameter. 
