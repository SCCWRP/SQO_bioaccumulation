
# README

[![Travis build
status](https://travis-ci.org/SCCWRP/SQObioaccumulation.svg?branch=master)](https://travis-ci.org/SCCWRP/SQObioaccumulation)
[![AppVeyor build
status](https://ci.appveyor.com/api/projects/status/github/SCCWRP/SQObioaccumulation?branch=master&svg=true)](https://ci.appveyor.com/project/SCCWRP/SQObioaccumulation)

Install the package as follows:

``` r
install.packages('devtools')
library(devtools)
install_github('SCCWRP/SQObioaccumulation')
library(SQObioaccumulation)
```

Run the bioaccumulation model with defaults:

``` r
# data inputs
data(biota)
data(constants)
data(contam)
data(biota_preyprop)

# calculated contaminant inputs
contamcalc <- cntcalc(contam, constants)

# run model
res <- bioaccum_batch(biota, contamcalc, biota_preyprop, constants)

# assign output to separate objects
cbiota <- res$cbiota
bsaf <- res$bsaf
```

Creat a summary table:

``` r
# summary table
indic_sum <- indic_sum_fun(cbiota, contamcalc)
indic_sum
```

    ## # A tibble: 9 x 9
    ##   Guild `Chlordanes BSA~ `Dieldrin BSAF ~ `DDTs BSAF (cal~ `PCBs BSAF (cal~
    ##   <chr>            <dbl>            <dbl>            <dbl>            <dbl>
    ## 1 indi~             5.30            2.10             11.2              9.13
    ## 2 indi~             5.45            1.99             12.4             10.0 
    ## 3 indi~             5.26            2.25              9.55             7.93
    ## 4 indi~             6.33            3.22             13.0             11.1 
    ## 5 indi~             2.28            1.21              4.43             3.84
    ## 6 indi~             5.42            3.89              7.30             6.64
    ## 7 indi~             1.40            0.924             2.05             1.84
    ## 8 indi~             4.15            3.71              3.98             4.03
    ## 9 indi~             4.75            1.70             11.6              9.32
    ## # ... with 4 more variables: `Chlordanes Conc (ng/g)` <dbl>, `Dieldrin
    ## #   Conc (ng/g)` <dbl>, `DDTs Conc (ng/g)` <dbl>, `PCBs Conc (ng/g)` <dbl>

Plot BSAF and tissue concentration estimates for a selected contaminant:

``` r
# plot of bsaf, cbiota by specific contaminant
plo_bsaf(bsaf, cbiota, 'alphaChlordane')
```

![](README_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

Make a table of BSAF and tissue concentration estimates for a selected
contaminant:

``` r
# tabular summary of bsaf, cbiota by specific contaminant
tab_bsaf(bsaf, cbiota, 'alphaChlordane')
```

    ##                    Output Sediment Phytoplankton Submerged Macrophyte
    ## 1 Tissue conc. (ng/g wet)      0.5         0.177                0.166
    ## 2                    BSAF      1.0         0.355                0.331
    ##   Zooplankton Small polychaete (e.g., Harmothoe imbricata)
    ## 1       0.302                                        0.487
    ## 2       0.605                                        0.975
    ##   Large polychaete (e.g., Neanthes) Amphipod Cumacean Mysid
    ## 1                             1.083    0.498    0.381 0.395
    ## 2                             2.166    0.996    0.762 0.789
    ##   Bivalve mollusk Decapod crab Crangon shrimp Forage fish - herbivore
    ## 1           0.328        1.517          0.653                   0.387
    ## 2           0.655        3.035          1.305                   0.774
    ##   Forage fish - planktivore Forage fish - mixed diet i
    ## 1                     1.622                      1.558
    ## 2                     3.244                      3.115
    ##   Forage fish - mixed diet ii Forage fish  - primarily benthivore
    ## 1                       4.289                               2.144
    ## 2                       8.579                               4.288
    ##   Forage fish - benthivore indic1 indic2 indic3 indic4 indic5 indic6
    ## 1                    4.111  3.964  4.139  3.866  4.470  1.589  3.443
    ## 2                    8.223  7.929  8.279  7.732  8.941  3.179  6.886
    ##   indic7 indic8 indic9
    ## 1  0.907  2.374  3.633
    ## 2  1.814  4.747  7.266

Run Monte Carlo simulations (MCS) with results from bioaccumulation
model and additional inputs:

``` r
mcsres <- mcs_fun(4000, indic_sum, mcsparms, constants, propseaf = c(0, 0.5, 0, 0, 0.5, 0, 0, 0, 0))
```

Summarize MCS results:

``` r
mcs_sum_fun(mcsres)
```

    ## # A tibble: 4 x 12
    ## # Groups:   Compound [4]
    ##   Compound    `0%`    `1%`   `5%`  `10%`  `25%` `50%` `75%` `90%` `95%`
    ##   <chr>      <dbl>   <dbl>  <dbl>  <dbl>  <dbl> <dbl> <dbl> <dbl> <dbl>
    ## 1 Chlorda~ 0.0750  0.185   0.295  0.359  0.512  0.752 1.10  1.56   1.93
    ## 2 DDT      0.0734  0.195   0.388  0.518  0.840  1.52  2.74  4.51   6.20
    ## 3 Dieldrin 0.482   0.823   1.23   1.47   1.97   2.75  3.94  5.45   6.62
    ## 4 PCB      0.00191 0.00979 0.0231 0.0344 0.0709 0.155 0.347 0.709  1.08
    ## # ... with 2 more variables: `99%` <dbl>, `100%` <dbl>

Plot cumulative distribution curves for MCS:

``` r
mcs_plo(mcsres, xmax = 3)
```

<img src="README_files/figure-gfm/unnamed-chunk-9-1.png" width="80%" style="display: block; margin: auto;" />

Get overall SQO assessment:

``` r
wgtavg <- wgt_avg_fun(mcsparms, propseaf = c(0, 0.5, 0, 0, 0.5, 0, 0, 0, 0))
sqo_sum_fun(wgtavg, mcsres, tischmthr, constants, finalsiteassess)
```

    ## # A tibble: 4 x 9
    ##   Compound `Observed tissu~ `Chemical expos~ `Estimated tiss~
    ##   <chr>               <dbl> <chr>                       <dbl>
    ## 1 Chlorda~             2.28 Very Low                    1.72 
    ## 2 DDT                  4.85 Very Low                    7.37 
    ## 3 Dieldrin             0.25 Very Low                    0.689
    ## 4 PCB                 36.5  Moderate                    5.67 
    ## # ... with 5 more variables: `Site linkage 25%` <dbl>, `Site linkage
    ## #   50%` <dbl>, `Site linkage 75%` <dbl>, `Site linkage category` <chr>,
    ## #   `Site assessment category` <chr>
