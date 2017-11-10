
<!-- README.md is generated from README.Rmd. Please edit that file -->
auctestr
========

The AUC statistic (Area Under the Receiver Operating Characteristic Curve, or A') is commonly used to evaluate predictive models. However, the AUC statistical also has unique, and uniquely useful, statistical properties that can make statistical significance testing especially simple. This means that instead of just choosing the best model, or making educated guesses about difference in model performance, machine learning researchers can make informed, statistically rigorous comparisons about the performance of two models across several datasets and even with multiple observations within each dataset (as often occurs, for example, when using k-fold cross-validation or when models are trained using data from multiple time points).

Installation
------------

You can install auctestr from github with:

``` r
# install.packages("devtools")
devtools::install_github("jpgard/auctestr")
```

Soon, when auctestr is available on CRAN, you'll be able to install it with `library()`. This README will be updated when that happens!

Example
-------

This is a basic example which shows you how to solve a common problem -- comparing ModelA and ModelB:

``` r
## basic example code
data("sample_experiment_data")
head(sample_experiment_data)
#>         auc precision  accuracy    n n_p  n_n  dataset time model_id
#> 1 0.7957640 0.5354970 0.8207171 1757 350 1407 dataset1    0   ModelA
#> 2 0.7957640 0.5354970 0.8207171 1757 350 1407 dataset1    0   ModelC
#> 3 0.7957640 0.5354970 0.8207171 1757 350 1407 dataset1    0   ModelB
#> 4 0.8459516 0.4772727 0.8471926 1407 199 1208 dataset1    1   ModelA
#> 5 0.7473793 0.6300578 0.8905473 1407 199 1208 dataset1    1   ModelC
#> 6 0.7440098 0.6407186 0.8919687 1407 199 1208 dataset1    1   ModelB
#>   model_variant
#> 1      VariantA
#> 2      VariantA
#> 3      VariantA
#> 4      VariantA
#> 5      VariantA
#> 6      VariantA
z_score <- auc_compare(sample_experiment_data, compare_values = c("ModelA", "ModelB"), filter_value = c("VariantA"), time_col = "time", outcome_col = "auc", compare_col = "model_id", over_col = "dataset", filter_col = "model_variant")
#> fetching comparison results for models ModelA, ModelB in dataset dataset1 with filter value VariantA
#> fetching comparison results for models ModelA, ModelB in dataset dataset2 with filter value VariantA
#> fetching comparison results for models ModelA, ModelB in dataset dataset3 with filter value VariantA
pnorm(-abs(z_score))
#> [1] 0.3336678
```

Based on the *p*-value for this comparison, the difference between these models is not statistically significant at any reasonable significance threshold *α*. Good to know!

You can also compare variants of a specific model (for example, compare two different cost settings for an SVM model):

``` r
z_score <- auc_compare(sample_experiment_data, compare_values = c("VariantA", "VariantB"), filter_value = c("ModelC"), time_col = "time", outcome_col = "auc", compare_col = "model_variant", over_col = "dataset", filter_col = "model_id")
#> fetching comparison results for models VariantA, VariantB in dataset dataset1 with filter value ModelC
#> fetching comparison results for models VariantA, VariantB in dataset dataset2 with filter value ModelC
#> fetching comparison results for models VariantA, VariantB in dataset dataset3 with filter value ModelC
pnorm(-abs(z_score))
#> [1] 0.04894775
```

The difference between these model variants is significant, but only marginally at *α* = 0.05. Again, this is useful to know. Perhaps collecting more data would be a good next step here.

Hopefully, `auctestr` makes comparing your models simple and useful. Future versions of the package will incorporate procedures for multiple-testing corrections, and Bayesian model evaluation techniques.

Generating AUC Data
-------------------

If you have predictive models but need a tool for calculating their AUC scores, check out the following packages: + `proc` [link](https://cran.r-project.org/web/packages/pROC/pROC.pdf) + `caret` [link](http://topepo.github.io/caret/index.html)

References and Further Reading
------------------------------

If you're interested in reading more about the methods used in this package, check the following references, which served as the basis for this implementation: + Hanley and McNeil, "The meaning and use of the area under a receiver operating characteristic (ROC) curve", Radiology, 1982. + Fogarty, Baker and Hudson, "Case Studies in the use of ROC Curve Analysis for Sensor-Based Estimates in Human Computer Interaction" 2008. + Stouffer, S.A.; Suchman, E.A.; DeVinney, L.C.; Star, S.A.; Williams, R.M. Jr. (1949). The American Soldier, Vol.1: Adjustment during Army Life.)
