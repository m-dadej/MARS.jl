## Mars.jl: MARkov Switching models in Julia

[![Build Status](https://github.com/m-dadej/MARS.jl/actions/workflows/CI.yml/badge.svg?branch=main)](https://github.com/m-dadej/MARS.jl/actions/workflows/CI.yml?query=branch%3Amain)
[![Build Status](https://app.travis-ci.com/m-dadej/MARS.jl.svg?branch=main)](https://app.travis-ci.com/m-dadej/MARS.jl)
[![Build status](https://ci.appveyor.com/api/projects/status/2o7c7dny19u0e18u?svg=true)](https://ci.appveyor.com/project/m-dadej/mars-jl-ovb60)
[![Coverage](https://codecov.io/gh/m-dadej/MARS.jl/branch/main/graph/badge.svg)](https://codecov.io/gh/m-dadej/MARS.jl)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)


Mars is a package for estimation of Markov switching dynamic models in Julia. The package is currently being developed, altough the basic functionality is already available. 

Contact: Mateusz Dadej, m.dadej at unibs.it


## Installation
```julia
Pkg.add("https://github.com/m-dadej/Mars.jl")
# or
] add https://github.com/m-dadej/Mars.jl
```

## Example

Following example will estimate a simple Markov switching model with the form:

```math
\begin{align}
y_t = \mu_s + \beta_s x_t + \epsilon_t, & \epsilon \sim f(0, \mathcal{\Sigma}) 
\end{align}
```

$$y_t = \mu_s + \beta_s x_t + \epsilon_s,  $$
Where,
$$$$

```julia
using Mars

k = 2            # number of regimes
T = 400          # number of generated observations
μ = [1.0, -0.5]  # regime-switching intercepts
β = [-1.5, 0.0]  # regime-switching coefficient for β
σ = [1.1,  0.8]  # regime-switching standard deviation
P = [0.9 0.05    # transition matrix (left-stochastic)
     0.1 0.95]

Random.seed!(123)
# generate artificial data with given parameters
y, s_t, X = generate_mars(μ, σ, P, T, β = β) 

# estimate the model
model = MSModel(y, k, intercept = "switching", exog_switching_vars = reshape(X[:,2],T,1))

# output summary table
summary_mars(model)
````

The `summary_mars(model)`  will output following summary table:

```jldoctest
Markov Switching Model with 2 regimes
=====================================================
# of observations:          400 Loglikelihood:            -576.692 
# of estimated parameters:    8  AIC                      1169.384 
Error distribution:    Gaussian  BIC                      1201.316 
------------------------------------------------------
------------------------------
Summary of regime 1:
------------------------------
Coefficient  |  Estimate  |  Std. Error  |  z value  |  Pr(>|z|)   
-------------------------------------------------------------------
β_0          |     0.824  |       0.132  |    6.242  |    < 1e-3  
β_1          |    -1.483  |        0.12  |  -12.358  |    < 1e-3   
σ            |     1.124  |       0.046  |   24.435  |    < 1e-3   
-------------------------------------------------------------------
Expected regime duration: 11.34 periods
-------------------------------------------------------------------
------------------------------
Summary of regime 2:
------------------------------
Coefficient  |  Estimate  |  Std. Error  |  z value  |  Pr(>|z|)
-------------------------------------------------------------------
β_0          |    -0.516  |       0.052  |   -9.923  |    < 1e-3  
β_1          |    -0.003  |       0.051  |   -0.059  |     0.953  
σ            |     0.843  |       0.022  |   38.318  |    < 1e-3
-------------------------------------------------------------------
Expected regime duration: 28.58 periods
-------------------------------------------------------------------
left-stochastic transition matrix:
          | regime 1   | regime 2
---------------------------------------
 regime 1 |   91.181%  |    3.499%  |
 regime 2 |    8.819%  |   96.501%  |
 ```

```julia
using Plots

plot(smoothed_probs(model), 
     label=["Regime 1" "Regime 2"],
     title = "Transition Probabilities")

```     
![Plot](img/transitino_probs.png)

```julia
plot([smoothed_probs(model)[:,2] s_t.-1],
     label=["Regime 1" "Regime 2"],
     title = "Transition Probabilities")
```
 ![Plot](img/actual_probs.png)