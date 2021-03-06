
---
output: html_document
editor_options:
  chunk_output_type: console
---
# Child Labor Laws as an IV

2SLS estimates of the returns to schooling using child labor laws as instruments for years of schooling [@AcemogluAngrist2000].
This replicates Table 6.3 of *Mastering 'Metrics*.


```r
library("AER")
library("sandwich")
library("clubSandwich")
library("tidyverse")
library("broom")
```

Load the `child_labor` data.

```r
data("child_labor", package = "masteringmetrics")
child_labor <- mutate(child_labor,
                      year = factor(year),
                      yob_fct = factor(yob),
                      sob = factor(sob))
```

## First stages and reduced forms

Column 1. Years of Schooling.

```r
mod1 <- lm(indEduc ~ year + yob_fct + sob + cl7 + cl8 + cl9,
           data = child_labor, weights = weight)
# coef_test(mod1, vcov = vcovCR(mod1, cluster = child_labor[["sob"]]))
```

Column 2. Years of Schooling. State of birth dummies x linear year of birth trends.

```r
mod2 <- lm(indEduc ~ year + yob_fct + sob + sob:yob + cl7 + cl8 + cl9,
           data = child_labor, weights = weight)
# coef_test(mod2, vcov = vcovCR(mod2, cluster = child_labor[["sob"]]))
```

Column 3. Log weekly wages.

```r
mod3 <- lm(lnwkwage ~ year + yob_fct + sob + cl7 + cl8 + cl9,
           data = child_labor, weights = weight)
# coef_test(mod3, vcov = vcovCR(mod1), cluster = child_labor[["state"]])
```

Column 4. Log weekly wages. State of birth dummies x linear year of birth trends.

```r
mod4 <- lm(lnwkwage ~ year + yob_fct + sob + sob:yob + cl7 + cl8 + cl9,
           data = child_labor, weights = weight)
# coef_test(mod4, vcov = vcovCR(mod2), cluster = child_labor[["state"]])
```

## IV returns

Column 3. Log weekly wages.

```r
mod5 <- ivreg(lnwkwage ~ year + yob_fct + sob + indEduc |
               . - indEduc + cl7 + cl8 + cl9,
              data = child_labor, weights = weight)
# coef_test(mod5, vcov = vcovCR(mod1), cluster = child_labor[["state"]])
```

Column 4. Log weekly wages. State of birth dummies x linear year of birth trends.

```r
mod6 <- ivreg(lnwkwage ~ year + yob_fct + sob + sob:yob + indEduc |
               . - indEduc + cl7 + cl8 + cl9,
              data = child_labor, weights = weight)
# coef_test(mod6, vcov = vcovCR(mod2), cluster = child_labor[["state"]])
```

## References {-}

-   <http://masteringmetrics.com/wp-content/uploads/2015/02/ReadMe_ChildLaborLaws.txt>
-   <http://masteringmetrics.com/wp-content/uploads/2015/02/AA_regs.do>

