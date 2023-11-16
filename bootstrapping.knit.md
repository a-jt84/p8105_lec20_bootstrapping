---
title: "Bootstrapping"
author: "Andy Turner"
date: "2023-11-16"
output: html_document
---



## Generate a relevant example


```r
n_samp = 250

sim_df_const= 
  tibble(
    x= rnorm(n_samp, 1, 1),
    error= rnorm(n_samp, 0, 1), 
    y = 2 + 3 * x + error
  )

sim_df_nonconst=
  sim_df_const |> 
  mutate(error= error *.76 * x,
         y= 2 + 3 * x + error)
  

sim_df_const |> 
  ggplot(aes(x=x, y=y))+ geom_point()
```

<img src="bootstrapping_files/figure-html/unnamed-chunk-1-1.png" width="672" />

```r
sim_df_nonconst |> 
  ggplot(aes(x= x, y=y))+ geom_point()
```

<img src="bootstrapping_files/figure-html/unnamed-chunk-1-2.png" width="672" />

fit some linear models


```r
sim_df_const |> 
  lm(y~x, data= _) |> 
  broom::tidy()
```

```
## # A tibble: 2 × 5
##   term        estimate std.error statistic   p.value
##   <chr>          <dbl>     <dbl>     <dbl>     <dbl>
## 1 (Intercept)     1.98    0.0981      20.2 3.65e- 54
## 2 x               3.04    0.0699      43.5 3.84e-118
```

```r
sim_df_nonconst |> 
  lm(y~x, data=_) |> 
  broom::tidy()
```

```
## # A tibble: 2 × 5
##   term        estimate std.error statistic   p.value
##   <chr>          <dbl>     <dbl>     <dbl>     <dbl>
## 1 (Intercept)     1.93    0.106       18.2 1.34e- 47
## 2 x               3.11    0.0757      41.1 9.17e-113
```

## Draw and analyze a bootstrap sample

Start with a little function 


```r
boot_strap = function(df) {
  
  sample_frac(df, replace= TRUE)
}
```

Let's see how this works


```r
sim_df_nonconst |> 
  boot_strap() |> 
  ggplot(aes(x=x, y=y))+
  geom_point(alpha=0.5)+ 
  stat_smooth(method = "lm")
```

```
## `geom_smooth()` using formula = 'y ~ x'
```

<img src="bootstrapping_files/figure-html/unnamed-chunk-4-1.png" width="672" />


