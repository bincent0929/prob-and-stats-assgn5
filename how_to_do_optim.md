# R / Stats Chat Notes

## Natural Log in R

In R, the natural logarithm is `log()` (not `ln()`):

```r
log(x)        # natural log (base e)
log(x, base)  # log with a custom base
log10(x)      # log base 10
log2(x)       # log base 2
```

Examples:
```r
log(1)        # 0
log(exp(1))   # 1
log(100, 10)  # 2
```

There is no `ln()` in base R — `log()` defaults to natural log.

---

## Poisson Log-Likelihood in R

The Poisson log-likelihood is:

$$\ell(\lambda) = \sum_{i=1}^{n} \left[ x_i \log(\lambda) - \lambda - \log(x_i!) \right]$$

`x_i` are your **observed data values** (counts in your sample).

```r
poisson_loglik <- function(lambda, x) {
  sum(x * log(lambda) - lambda - lfactorial(x))
}
```

Or using `dpois` with `log = TRUE`:

```r
poisson_loglik <- function(lambda, x) {
  sum(dpois(x, lambda, log = TRUE))
}
```

- Use `lfactorial()` instead of `factorial()` to avoid overflow on large values.

---

## Syntax Highlighting for Function Parameters

Function parameters like `lambda` and `x` typically **don't get special syntax highlighting** in R — this is normal behavior.

R syntax highlighters color keywords, built-in functions, strings, numbers, and operators — but plain variable/parameter names stay as default text color.

---

## MLE for Lambda Using `optim`

`optim` does **not** take a derivative — you pass the log-likelihood function directly and it numerically searches for the optimum.

Since `optim` minimizes by default, negate the log-likelihood:

```r
x <- c(3, 1, 4, 1, 5, 2)  # observed data

result <- optim(
  par = 1,                                         # starting guess for lambda
  fn = function(l) -poisson_loglik(l, x),          # negate to minimize
  method = "Brent",                                # use Brent for 1D optimization
  lower = 0.001, upper = 100
)

result$par  # MLE estimate of lambda
```

For the Poisson distribution, the MLE has a **closed-form solution**:

$$\hat{\lambda} = \bar{x}$$

So `mean(x)` is the exact MLE. Use `optim` to verify or when the assignment requires it.
