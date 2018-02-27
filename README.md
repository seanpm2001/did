
<!-- README.md is generated from README.Rmd. Please edit that file -->
did
===

The package contains tools for computing average treatment effect parameters in Difference in Differences models with more than two periods and with variation in treatment timing. The main parameters are group-time average treatment effects which are the average treatment effect for a particular group at a a particular time. These can be aggregated into a fewer number of treatment effect parameters, and the package deals with the cases where there is selective treatment timing, dynamic treatment effects, calendar time effects, or combinations of these. There are also functions for testing the Difference in Differences assumption, and plotting group-time average treatment effects.

Installation
------------

You can install from CRAN with:

``` r
install.packages("did")
```

or get the latest version from github with:

``` r
# install.packages("devtools")
devtools::install_github("bcallaway11/did")
```

Example
-------

The following is a simplified example of the effect of states increasing their minimum wages on county-level teen employment rates which comes from Callaway and Sant'Anna (2018).

A subset of the data is available in the package and can be loaded by

``` r
  library(did)
  data(mpdta)
```

The dataset contains 500 observations of county-level teen employment rates from 2003-2007. Some states are first treated in 2004, some in 2006, and some in 2007 (see the paper for details). We are interested in estimating the group-time average treatment effect (which is the average treatment effect in period \(t\) for the group of states first treated in period \(g\) and given by \(ATT(g,t)=E[Y_t(1) - Y_t(0)|G_g=1]\)) under the common trends assumption:
\begin{align*}
  E[\Delta Y_t(0) | X=x, G_g=1] = E[\Delta Y_t(0) | X=x, C=1] a.s. \quad \forall g\leq t
\end{align*}
where \(Y_t(1)\) and \(Y_t(0)\) denote treated and untreated potential outcomes, \(G_g=1\) denotes counties first treated in period \(g\), \(C=1\) denotes control counties that are never treated.

To estimate \(ATT(g,t)\), one can use the method as follows:

``` r
out <- mp.spatt(lemp ~ treat, xformla=~lpop, data=mpdta,
                 panel=TRUE, first.treat.name="first.treat",
                 idname="countyreal", tname="year",
                 bstrap=FALSE, se=TRUE, cband=FALSE)
#> current period: 2004 
#> current group: 2004 
#> set pretreatment period to be 2003 
#> current period: 2005 
#> current group: 2004 
#> set pretreatment period to be 2003 
#> current period: 2006 
#> current group: 2004 
#> set pretreatment period to be 2003 
#> current period: 2007 
#> current group: 2004 
#> set pretreatment period to be 2003 
#> current period: 2006 
#> current group: 2006 
#> set pretreatment period to be 2005 
#> current period: 2007 
#> current group: 2006 
#> set pretreatment period to be 2005 
#> current period: 2007 
#> current group: 2007 
#> set pretreatment period to be 2006
summary(out)
#> 
#> 
#> 
#>  group   time          att          se
#> ------  -----  -----------  ----------
#>   2004   2004   -0.0145484   0.0146508
#>   2004   2005   -0.0764499   0.0217393
#>   2004   2006   -0.1404646   0.0306240
#>   2004   2007   -0.1069326   0.0266771
#>   2006   2004   -0.0008686   0.0154686
#>   2006   2005   -0.0063972   0.0129978
#>   2006   2006    0.0012080   0.0135158
#>   2006   2007   -0.0413082   0.0145542
#>   2007   2004    0.0265561   0.0125174
#>   2007   2005   -0.0046609   0.0136962
#>   2007   2006   -0.0283403   0.0160904
#>   2007   2007   -0.0288948   0.0144835
#> 
#> 
#> P-value for pre-test of DID assumption:
```

and plot the results using the command:

``` r
library(gridExtra)
ggdid(out)
```

![](README-unnamed-chunk-5-1.png)
