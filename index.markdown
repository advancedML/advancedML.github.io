---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults
# title: advMLProj
layout: home
title: A Reproduction of “Algorithms for Fitting the Constrained Lasso”
exclude: true
authors: ['Yifu Li', 'Xiaoyu Chen']
---

## Outline
1. [INTRODUCTION](#1-introduction)
2. [CASE STUDY ON TUMOR DATASET](#2-case-study-on-tumor-dataset)
3. [SIMULATION STUDY](#3-simulation-study)
    1. [Sum-to-zero constraint](#31-sum-to-zero-constraint)
    2. [Non-negativity constraint](#32-non-negativity-constraint)
4. [DISCUSSIONS](#4-discussions)
5. [CONCLUSIONS AND FUTURE WORK](#5-conclusions-and-future-work)
6. [REFERENCES](#references)

<hr>
<br>

## 1. INTRODUCTION

The original paper focuses on studying the constrained lasso problem. Specifically, authors studied the generalized lasso problem, which integrates the classical lasso problem with linear equality and inequality constraints. Through simulation studies and real-data applications, the proposed model structure allows users to infused the prior knowledge during the model parameter estimation. More importantly, authors have discovered that a variety of lasso types of problems, including the generalized lasso, can always be reformulated and solved as a constrained lasso problem. This findings provides excellent opportunity to scale up the applications that constrained lasso can handle. The authors derived three different algorithms to easimtae the model parameters for constrained lasso problems as a function of the tuning parameter ρ.
Specifically, these three methods are: 

- quadratic programming (QP), 
- the alternating direction method of multipliers (ADMM), 
- the novel algorithm providing the solution path. 

In the simulation studies, the authors found out that the novel solution path algorithm has the best performance on model estimation time with competitive modeling accuracy. As the size of data grows, ADMM becomes computationally efficient, despite that the performance of ADMM is sensitive to particular values of the tuning parameter. Furthermore, we propose to conduct a case study on the brain tumor data set and try to reproduce the variable selection results for generalized lasso and constrained lasso in the original paper.


## 2. CASE STUDY ON TUMOR DATASET

The tumor data set adopts the comparative genomic hybridization (CGH) measurement data from glioblastoma multiforme brain tumors (Bredel et al. 2005). This data set has already been studied several times in the literature (Tibshirani and Wang 2008). Specifically, people adopt CGH array experiments to capture the DNA copy number in its log2 ratio, which is measured by the number of DNA copies of the gene in tumor cells to the number of DNA copies in reference cells. We are trying to recover the model parameters by modeling the tumor data set and compare the recovered parameters with the ones from the original paper. The reproduction codes are presented in [Jupyter Notebook](https://advancedml.github.io/2019/12/03/jupyter-notebook.html), and in [R Notebook](https://advancedml.github.io/2019/12/03/r-notebook.html). And the [Requirements](https://advancedml.github.io/requirements/) are listed.

The original article does not mention how to perform model selection by selecting tuning parameters to reproduce Figure 5. Therefore, we adopted two strategies in this project: 1) manually selecting the model which produces similar esimation results as the original article; and 2) selecting the model associated with the least root-mean-square-error (RMSE). The results are presented in the following two interactive graphics. 

<script src="https://code.jquery.com/jquery-2.2.4.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/echarts/4.3.0/echarts.min.js"></script>
<div id="chart" style="height:60vh;width:100%"></div>
<div id="chart1" style="height:60vh;width:100%"></div>

## 3. SIMULATION STUDY
### 3.1 Sum-to-zero constraint

In this example, we will solve a problem defined by

<img src="http://latex.codecogs.com/svg.latex?\begin{array}{ll}{\text { minimize }} & {\frac{1}{2} || \mathbf{y}-\mathbf{X} \boldsymbol{\beta}\left\|_{2}^{2}+\rho\right\| \boldsymbol{\beta} \|_{1}} \\ {\text { subject to }} & {\sum_{j} \beta_{j}=0}\end{array}" border="0"/>

First, we generated the predictor matrix X and response vector y. 
To do so, we need a true parameter vector β whose sum equals to 0. 
Note n is the number of observations, and p is the number of predictors.

```Julia
n, p = 50, 100   # (n,p) in {(50,100), (100,500), (500,1000), (1000,2000)}
β = zeros(p)
β[1:10] = 1:10
srand(41)
X = randn(n, p)
y = X * β + randn(n)
bineq = zeros(p)
Aineq = - eye(p)
```

Then, we evaluted three models with `ρ = 2` and `ρ = 6` as described in See et al. (2018).

```Julia
# Path optimization
t1 = time_ns()
β̂path1, ρpath1, objpath, = lsq_classopath(X, y; Aeq = Aeq, beq = beq);
t2 = time_ns()
elaps_path = (t2-t1)/1e9/length(ρpath1)
println(elaps_path)

# ADMM with ρ = 2
ρ = 2.0
t1 = time_ns()
β̂admm = lsq_constrsparsereg_admm(X, y, ρ; proj = x -> x)
t2 = time_ns()
elaps_admm2 = (t2-t1)/1e9
println(elaps_admm2)

# Quadratic programming with ρ = 2
ρ = 2.0
t1 = time_ns()
β̂, = lsq_constrsparsereg(X, y, ρ; Aeq = Aeq, beq = beq);
t2 = time_ns()
elaps_QP2 = (t2-t1)/1e9
println(elaps_QP2)

# ADMM with ρ = 6
ρ = 6.0
t1 = time_ns()
β̂admm = lsq_constrsparsereg_admm(X, y, ρ; proj = x -> x)
t2 = time_ns()
elaps_admm6 = (t2-t1)/1e9
println(elaps_admm6)

# Quadratic programming with ρ = 6
ρ = 6.0
t1 = time_ns()
β̂, = lsq_constrsparsereg(X, y, ρ; Aeq = Aeq, beq = beq);
t2 = time_ns()
elaps_QP6 = (t2-t1)/1e9
println(elaps_QP6)
```

The simulation results are graphically presented:

<div id="chart2" style="height:60vh;width:100%"></div>

### 3.2 Non-negativity constraint

In this example, we will solve a problem defined by

<img src="http://latex.codecogs.com/svg.latex?\begin{array}{ll}{\text { minimize }} & {\frac{1}{2} || \mathbf{y}-\mathbf{X} \boldsymbol{\beta}\left\|_{2}^{2}+\rho\right\| \boldsymbol{\beta} \|_{1}} \\ {\text { subject to }} & {\beta_{j} \geq 0, \forall j}\end{array}" border="0"/>

First, we generated the predictor matrix X and response vector y. 
To do so, we need a true parameter vector β whose sum equals to 0. 
Note n is the number of observations, and p is the number of predictors.

```Julia
n, p = 50, 100   # (n,p) in {(50,100), (100,500), (500,1000), (1000,2000)}
β = zeros(p)
β[1:10] = 1:10
srand(41)
X = randn(n, p)
y = X * β + randn(n)
bineq = zeros(p)
Aineq = - eye(p)
```

Then, we evaluted three models with `ρ = 2` and `ρ = 6` as described in See et al. (2018).

<div id="chart3" style="height:60vh;width:100%"></div>

## 4. DISCUSSIONS

For the brain tumor case study, we find that the generalized Lasso and constrained Lasso almost have the same results in terms of the variable selection results. This finding is roughly the same as the orignal paper and the figure shows that both methods can accurately recover the true model parameters. As a result, we can agree with the authors in the original paper and conclude that the constrained lasso can successfully solve the fused-lasso problem, hence has better generality for solving lasso-type of problems. However, an interesting finding is that the model parameters, which are visually closest to the true values, do not result in the lowest prediction accuracy of model responses.

We found out that the computational time for the simulation study are slightly different from the figure 2 and 3 of the original paper. Specifically, ADMM and quadratic programming generated the slowest performance for simulation 1 and 2 respectively in the orignal paper. However, our finding shows that ADMM generated the slowest performance (on average) for both simulation study. The patterns on the increasing of the computational time for both simulations are almost identical to the original paper.

## 5. CONCLUSIONS AND FUTURE WORK

In this work, we reproduced the simulation study and the brain tumor case study from Gaines (2018). Comparing with the original work, we generated similar results for the simulation study and almost identical result for the brain tumor case study. We proved that the path algorithm is computationally fast to solve the constrained lasso problems in each iterature. Furthermore, we proposed that the constrained lasso can solve many lasso problems in its special case (e.g., fused lasso).
In the future work, we can consider to run more replications of the simulation study, so that we have a more stable performance. Furthermore, we are using Julia 0.7 version, which was published three years ago. As a result, we can expect the latest Julia version can bring better performance with updated computational package. Last but not the least, we can further increase the dimension and the sample size of the data set to million-level, so that we can test which method performs better in a true big data environment. However, this idea requires more powerful computational units (e.g., cloud-computing).

## REFERENCES

- Tibshirani, Robert, et al. "Sparsity and smoothness via the fused lasso." Journal of the Royal Statistical Society: Series B (Statistical Methodology) 67.1 (2005): 91-108.
- Gaines, B. R., Kim, J., and Zhou, H. (2018) "Algorithms for fitting the constrained lasso." Journal of Computational and Graphical Statistics 27.4: 861-871.
- Hoerl, A. E., Kannard, R. W., and Baldwin, K. F. (1975), "Ridge Regression: Some Simulations," Communications in Statistics-Theory and Methods, 4, 105-123.
- Petersen, A., Witten, D., and Simon, N. (2016), "Fused Lasso Additive Model," Journal of Computational and Graphical Statistics, 25, 1005-1025.
- Tibshirani, R. (1996), "Regression Shrinkage and Selection Via the Lasso," Journal of the Royal Statistical Society: Series B (Methodological), 58, 267-288.
- Xin, B., Tian, Y., Wang, Y., and Gao, W. (2015), "Background Subtraction Via Generalized Fused Lasso Foreground Modeling," in Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition, pp. 4676-4684.
- Zhou, Hua, and Kenneth Lange. "A path algorithm for constrained estimation." Journal of Computational and Graphical Statistics 22.2 (2013): 261-283.
- James, Gareth M., Courtney Paulson, and Paat Rusmevichientong. "The constrained lasso." Refereed Conference Proceedings. Vol. 31. 2012.
- Tibshirani, Ryan J., and Jonathan Taylor. "The solution path of the generalized lasso." The Annals of Statistics 39.3 (2011): 1335-1371.
- Bredel, Markus, et al. "High-resolution genome-wide mapping of genetic alterations in human glial brain tumors." Cancer research 65.10 (2005): 4088-4096.
- Tibshirani, Robert, and Pei Wang. "Spatial smoothing and hot spot detection for CGH data using the fused lasso." Biostatistics 9.1 (2007): 18-29.



<script>
    function CSVToArray( strData, strDelimiter ){
        // Check to see if the delimiter is defined. If not,
        // then default to comma.
        strDelimiter = (strDelimiter || ",");

        // Create a regular expression to parse the CSV values.
        var objPattern = new RegExp(
            (
                // Delimiters.
                "(\\" + strDelimiter + "|\\r?\\n|\\r|^)" +

                // Quoted fields.
                "(?:\"([^\"]*(?:\"\"[^\"]*)*)\"|" +

                // Standard fields.
                "([^\"\\" + strDelimiter + "\\r\\n]*))"
            ),
            "gi"
            );


        // Create an array to hold our data. Give the array
        // a default empty first row.
        var arrData = [[]];

        // Create an array to hold our individual pattern
        // matching groups.
        var arrMatches = null;


        // Keep looping over the regular expression matches
        // until we can no longer find a match.
        while (arrMatches = objPattern.exec( strData )){

            // Get the delimiter that was found.
            var strMatchedDelimiter = arrMatches[ 1 ];

            // Check to see if the given delimiter has a length
            // (is not the start of string) and if it matches
            // field delimiter. If id does not, then we know
            // that this delimiter is a row delimiter.
            if (
                strMatchedDelimiter.length &&
                strMatchedDelimiter !== strDelimiter
                ){

                // Since we have reached a new row of data,
                // add an empty row to our data array.
                arrData.push( [] );

            }

            var strMatchedValue;

            // Now that we have our delimiter out of the way,
            // let's check to see which kind of value we
            // captured (quoted or unquoted).
            if (arrMatches[ 2 ]){

                // We found a quoted value. When we capture
                // this value, unescape any double quotes.
                strMatchedValue = arrMatches[ 2 ].replace(
                    new RegExp( "\"\"", "g" ),
                    "\""
                    );

            } else {

                // We found a non-quoted value.
                strMatchedValue = arrMatches[ 3 ];

            }


            // Now that we have our value string, let's add
            // it to the data array.
            arrData[ arrData.length - 1 ].push( parseFloat(strMatchedValue) );
        }

        // Return the parsed data.
        return( arrData );
    }
    myChart = echarts.init(document.getElementById("chart"));
    myChart1 = echarts.init(document.getElementById("chart1"));
    myChart2 = echarts.init(document.getElementById("chart2"));
    myChart3 = echarts.init(document.getElementById("chart3"));
    $(document).ready(function () {
        $.ajax({
            type: "GET",
            url: "y_case_study.csv",
            dataType: "text",
            success: function (raw) { 
                data = [];
                raw = raw.split("\n");
                for(i=0;i<raw.length;i++){
                    data.push([i,parseFloat(raw[i])]);
                }
                $.ajax({
                    type: "GET",
                    url: "genlasso_result.csv",
                    dataType: "text",
                    success: function (raw1) { 
                        data1 = [];
                        raw1 = raw1.split("\n");
                        for(i=0;i<raw1.length;i++){
                            data1.push([i,parseFloat(raw1[i])]);
                        }
                        $.ajax({
                            type: "GET",
                            url: "CLasso_case_study.csv",
                            dataType: "text",
                            success: function (raw2) { 
                                data2 = [];
                                raw2 = raw2.split("\n");
                                for(i=0;i<raw2.length;i++){
                                    data2.push([i,parseFloat(raw2[i])]);
                                }
                                option = {
                                    title: {
                                        text: 'Reproduction of Figure 5: Brain Tumor Data',
                                        left: 'center'
                                    },
                                    tooltip: {
                                        trigger: 'axis',
                                        axisPointer: {
                                            type: 'cross'
                                        }
                                    },
                                    xAxis: {
                                        type: 'value',
                                        splitLine: {
                                            lineStyle: {
                                                type: 'dashed'
                                            }
                                        },
                                        name: 'Genome Order',
                                        nameLocation: 'center',
                                        nameTextStyle: {
                                            fontSize: 20,
                                            lineHeight: 50,
                                        }
                                    },
                                    legend: {
                                        orient: 'vertical',
                                        right: '10%',
                                        top: '20%'
                                    },
                                    yAxis: {
                                        type: 'value',
                                        min: -3,
                                        max: 6,
                                        splitLine: {
                                            lineStyle: {
                                                type: 'dashed'
                                            }
                                        },
                                        name: 'log2 Ratio',
                                        nameLocation: 'center',
                                        nameTextStyle: {
                                            fontSize: 20,
                                            lineHeight: 50,
                                        }
                                    },
                                    series: [{
                                        name: 'Ground Truth',
                                        type: 'scatter',
                                        zlevel: 3,
                                        label: {
                                            emphasis: {
                                                show: false,
                                                position: 'left',
                                                textStyle: {
                                                    color: 'blue',
                                                    fontSize: 16
                                                }
                                            }
                                        },
                                        data: data
                                    },
                                    {
                                        name: 'Generalized Lasso',
                                        type: 'line',
                                        symbol: 'none',
                                        zlevel: 4,
                                        label: {
                                            emphasis: {
                                                show: false,
                                                position: 'left',
                                                textStyle: {
                                                    color: 'blue',
                                                    fontSize: 16
                                                }
                                            }
                                        },
                                        data: data1
                                    },
                                    {
                                        name: 'Constrained Lasso',
                                        type: 'line',
                                        symbol: 'none',
                                        zlevel: 5,
                                        label: {
                                            emphasis: {
                                                show: false,
                                                position: 'left',
                                                textStyle: {
                                                    color: 'blue',
                                                    fontSize: 16
                                                }
                                            }
                                        },
                                        data: data2
                                    }]
                                };
                                myChart.setOption(option);
                            }
                        });
                    }
                });
             }
        });
        $.ajax({
            type: "GET",
            url: "y_case_study.csv",
            dataType: "text",
            success: function (raw) { 
                data = [];
                raw = raw.split("\n");
                for(i=0;i<raw.length;i++){
                    data.push([i,parseFloat(raw[i])]);
                }
                $.ajax({
                    type: "GET",
                    url: "genlasso_nooptimal.csv",
                    dataType: "text",
                    success: function (raw4) { 
                        data4 = [];
                        raw4 = raw4.split("\n");
                        for(i=0;i<raw4.length;i++){
                            data4.push([i,parseFloat(raw4[i])]);
                        }
                        $.ajax({
                            type: "GET",
                            url: "CLasso_case_study_best.csv",
                            dataType: "text",
                            success: function (raw5) { 
                                data5 = [];
                                raw5 = raw5.split("\n");
                                for(i=0;i<raw5.length;i++){
                                    data5.push([i,parseFloat(raw5[i])]);
                                }
                                option = {
                                    title: {
                                        text: 'Reproduction of Figure 5: Brain Tumor Data with lowest RMSEs',
                                        left: 'center'
                                    },
                                    tooltip: {
                                        trigger: 'axis',
                                        axisPointer: {
                                            type: 'cross'
                                        }
                                    },
                                    xAxis: {
                                        type: 'value',
                                        splitLine: {
                                            lineStyle: {
                                                type: 'dashed'
                                            }
                                        },
                                        name: 'Genome Order',
                                        nameLocation: 'center',
                                        nameTextStyle: {
                                            fontSize: 20,
                                            lineHeight: 50,
                                        }
                                    },
                                    legend: {
                                        orient: 'vertical',
                                        right: '10%',
                                        top: '20%'
                                    },
                                    yAxis: {
                                        type: 'value',
                                        min: -3,
                                        max: 6,
                                        splitLine: {
                                            lineStyle: {
                                                type: 'dashed'
                                            }
                                        },
                                        name: 'log2 Ratio',
                                        nameLocation: 'center',
                                        nameTextStyle: {
                                            fontSize: 20,
                                            lineHeight: 50,
                                        }
                                    },
                                    series: [{
                                        name: 'Ground Truth',
                                        type: 'scatter',
                                        zlevel: 3,
                                        label: {
                                            emphasis: {
                                                show: false,
                                                position: 'left',
                                                textStyle: {
                                                    color: 'blue',
                                                    fontSize: 16
                                                }
                                            }
                                        },
                                        data: data
                                    },
                                    {
                                        name: 'Generalized Lasso',
                                        type: 'line',
                                        symbol: 'none',
                                        zlevel: 4,
                                        label: {
                                            emphasis: {
                                                show: false,
                                                position: 'left',
                                                textStyle: {
                                                    color: 'blue',
                                                    fontSize: 16
                                                }
                                            }
                                        },
                                        data: data4
                                    },
                                    {
                                        name: 'Constrained Lasso',
                                        type: 'line',
                                        symbol: 'none',
                                        zlevel: 5,
                                        label: {
                                            emphasis: {
                                                show: false,
                                                position: 'left',
                                                textStyle: {
                                                    color: 'blue',
                                                    fontSize: 16
                                                }
                                            }
                                        },
                                        data: data5
                                    }]
                                };
                                myChart1.setOption(option);
                            }
                        });
                    }
                });
             }
        });
        $.ajax({
            type: "GET",
            url: "simio/sim1.csv",
            dataType: "text",
            success: function (raw6) {
                d6 = CSVToArray(raw6,',');
                xlabeltxt = ['(50, 100)','(100, 500)','(500, 1000)','(1000, 2000)','(50, 100)','(100, 500)','(500, 1000)','(1000, 2000)'];
                data6 = []
                for(i=0;i<d6.length;i++){
                    tmp = [];
                    for(j=0;j<d6[i].length;j++){
                        tmp.push([xlabeltxt[j], d6[i][j]]);
                    }
                    data6.push(tmp);
                }
                console.log(data6[0])
                option = {
                    title: {
                        text: "Figure 2: Simulation 1 Results. Average algorithm runtime (seconds)",
                        left: "center"
                    },
                    tooltip: {
                        trigger: 'axis',
                        axisPointer: {
                            type: 'cross'
                        }
                    },
                    xAxis: {
                        type: 'category',
                        splitLine: {
                            lineStyle: {
                                type: 'dashed'
                            }
                        },
                        name: 'Problem Size (n, p)',
                        nameLocation: 'center',
                        nameTextStyle: {
                            fontSize: 20,
                            lineHeight: 50,
                        }
                    },
                    legend: {
                        orient: 'vertical',
                        left: '15%',
                        top: '15%'
                    },
                    yAxis: {
                        type: 'value',
                        splitLine: {
                            lineStyle: {
                                type: 'dashed'
                            }
                        },
                        name: 'Algorithm Runtime (seconds)',
                        nameLocation: 'center',
                        nameTextStyle: {
                            fontSize: 20,
                            lineHeight: 50,
                        }
                    },
                    series: [{
                        name: 'Solution Path',
                        type: 'line',
                        symbol: 'pin',
                        symbolSize: 25,
                        zlevel: 5,
                        label: {
                            emphasis: {
                                show: false,
                                position: 'left',
                                textStyle: {
                                    color: 'blue',
                                    fontSize: 16
                                }
                            }
                        },
                        data: data6[0].slice(0, 4)
                    },{
                        name: 'QP (ρ_Scale = 0.2)',
                        type: 'line',
                        symbol: 'pin',
                        symbolSize: 25,
                        zlevel: 5,
                        label: {
                            emphasis: {
                                show: false,
                                position: 'left',
                                textStyle: {
                                    color: 'blue',
                                    fontSize: 16
                                }
                            }
                        },
                        data: data6[1].slice(0, 4)
                    },{
                        name: 'ADMM (ρ_Scale = 0.2)',
                        type: 'line',
                        symbol: 'pin',
                        symbolSize: 25,
                        zlevel: 5,
                        label: {
                            emphasis: {
                                show: false,
                                position: 'left',
                                textStyle: {
                                    color: 'blue',
                                    fontSize: 16
                                }
                            }
                        },
                        data: data6[2].slice(0, 4)
                    },{
                        name: 'QP (ρ_Scale = 0.6)',
                        type: 'line',
                        symbol: 'pin',
                        symbolSize: 25,
                        zlevel: 5,
                        label: {
                            emphasis: {
                                show: false,
                                position: 'left',
                                textStyle: {
                                    color: 'blue',
                                    fontSize: 16
                                }
                            }
                        },
                        data: data6[3].slice(0, 4)
                    },{
                        name: 'ADMM (ρ_Scale = 0.6)',
                        type: 'line',
                        symbol: 'pin',
                        symbolSize: 25,
                        zlevel: 5,
                        label: {
                            emphasis: {
                                show: false,
                                position: 'left',
                                textStyle: {
                                    color: 'blue',
                                    fontSize: 16
                                }
                            }
                        },
                        data: data6[4].slice(0, 4)
                    }]
                }
                myChart2.setOption(option);
                option = {
                    title: {
                        text: "Figure 3: Simulation 2 Results. Average algorithm runtime (seconds)",
                        left: "center"
                    },
                    tooltip: {
                        trigger: 'axis',
                        axisPointer: {
                            type: 'cross'
                        }
                    },
                    xAxis: {
                        type: 'category',
                        splitLine: {
                            lineStyle: {
                                type: 'dashed'
                            }
                        },
                        name: 'Problem Size (n, p)',
                        nameLocation: 'center',
                        nameTextStyle: {
                            fontSize: 20,
                            lineHeight: 50,
                        }
                    },
                    legend: {
                        orient: 'vertical',
                        left: '15%',
                        top: '15%'
                    },
                    yAxis: {
                        type: 'value',
                        splitLine: {
                            lineStyle: {
                                type: 'dashed'
                            }
                        },
                        name: 'Algorithm Runtime (seconds)',
                        nameLocation: 'center',
                        nameTextStyle: {
                            fontSize: 20,
                            lineHeight: 50,
                        }
                    },
                    series: [{
                        name: 'Solution Path',
                        type: 'line',
                        symbol: 'pin',
                        symbolSize: 25,
                        zlevel: 5,
                        label: {
                            emphasis: {
                                show: false,
                                position: 'left',
                                textStyle: {
                                    color: 'blue',
                                    fontSize: 16
                                }
                            }
                        },
                        data: data6[0].slice(4, 8)
                    },{
                        name: 'QP (ρ_Scale = 0.2)',
                        type: 'line',
                        symbol: 'pin',
                        symbolSize: 25,
                        zlevel: 5,
                        label: {
                            emphasis: {
                                show: false,
                                position: 'left',
                                textStyle: {
                                    color: 'blue',
                                    fontSize: 16
                                }
                            }
                        },
                        data: data6[1].slice(4, 8)
                    },{
                        name: 'ADMM (ρ_Scale = 0.2)',
                        type: 'line',
                        symbol: 'pin',
                        symbolSize: 25,
                        zlevel: 5,
                        label: {
                            emphasis: {
                                show: false,
                                position: 'left',
                                textStyle: {
                                    color: 'blue',
                                    fontSize: 16
                                }
                            }
                        },
                        data: data6[2].slice(4, 8)
                    },{
                        name: 'QP (ρ_Scale = 0.6)',
                        type: 'line',
                        symbol: 'pin',
                        symbolSize: 25,
                        zlevel: 5,
                        label: {
                            emphasis: {
                                show: false,
                                position: 'left',
                                textStyle: {
                                    color: 'blue',
                                    fontSize: 16
                                }
                            }
                        },
                        data: data6[3].slice(4, 8)
                    },{
                        name: 'ADMM (ρ_Scale = 0.6)',
                        type: 'line',
                        symbol: 'pin',
                        symbolSize: 25,
                        zlevel: 5,
                        label: {
                            emphasis: {
                                show: false,
                                position: 'left',
                                textStyle: {
                                    color: 'blue',
                                    fontSize: 16
                                }
                            }
                        },
                        data: data6[4].slice(4, 8)
                    }]
                }
                myChart3.setOption(option);
            }
        });
    });
   

</script>

