---
layout: page
title: Requirements
permalink: /requirements/
exclude: false
---

To execute the constrained Lasso by using

- path optimization
- quadratic programming
- ADMM

the following requirements should be satisfied:

1. Install [Julia 0.7.0](https://julialang-s3.julialang.org/bin/winnt/x64/0.7/julia-0.7.0-win64.exe)
2. `add` and `build` packages in `Julia`:
    - `using Pkg`
    - `Pkg.add("IJulia")`
    - `Pkg.build("IJulia")`
    - `using IJulia`
    - `notebook()`, which will install and open a `Jupyter Notebook` with `Julia` kernel embedded.
3. In the Jupyter Notebook, find the [Class Project.ipynb](https://github.com/advancedML/advancedML.github.io/blob/master/res/Course%20Project.ipynb)