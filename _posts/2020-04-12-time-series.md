---
layout: post
category: Mathematics
title: Time series 
tagline: by SunHaozhe
tags: 
  - Mathematics
  - Machine learning
  - Python
  - Pandas
  - API
mathjax: true
comments: true
published: true
---


# Correlation 

To compute the correlation between two time series, we can use the `.corr()` method of `pandas.Series`. 

To visualize the scatter plot of two time series: 

```python
def correlation_between_2_ts(s1, s2, figsize=(5, 5), plot_scatter=True):
    """
    Computes correlation between two time series
    
    s1, s2: named pandas.Series 
    """
    correlation = s1.corr(s2)
    if plot_scatter:
        plt.figure(figsize=figsize)
        plt.scatter(s1, s2)
        plt.grid()
        title = "Scatter plot of {} and {}".format(s1.name, s2.name) 
        title += "\ncorrelation = {:.4f}".format(correlation)
        plt.title(title) 
        plt.show()
    return correlation

correlation = correlation_between_2_ts(s1, s2)
```

# Autocorrelation function (ACF)

The autocorrelation as a function of the lag. It equals one at lag-zero. 

```python
from statsmodels.tsa.stattools import acf

acf(data)
```

```python
from statsmodels.graphics.tsaplots import plot_acf

plot_acf(data, alpha=0.05, lags=30)
plt.show()
```

To compute a single lag-N autocorrelation, we can also use the `.autocorr(lag=N)` method of `pandas.Series`. 




# Stationarity


* **Strong stationarity**: joint distribution of the observations is time-invariant.
* **Weak stationarity**: mean, variance and autocorrelation of the observations are time-invariant. For autocorrelation, $\text{corr}(X_t, X_{t-\tau})$ is only a function of $\tau$, not $t$. 
* Weak stationarity is easier to test than strong stationarity. 
* If a process is not stationary, it becomes difficult to model. Modeling involves estimating a set of parameters. If a process is not stationary and the parameters are different at each point in time, then there are too many parameters to estimate (maybe more parameters than data). 
* A random walk is a common type of non-stationary series, its variance grows with time. Seasonal series are also non-stationary, for example, its mean varies with time. A white noise series with varying mean is non-stationary.  
* A white noise series (with constant mean and variance) is stationary. 
* Many non-stationary series can be made stationary through a simple transformation. For example, if we take the differences (with a lag of $1$) of a random walk series, we get a white noise series. A seasonal series can be made stationary by seasonal adjustments (taking the difference with a lag corresponding to the periodicity). Most economic data published by the government is seasonally adjusted. Sometimes, we may need more than one transformations, for a series that grows exponentially and shows a strong seasonal pattern, we can first take the log of the series to eliminate the exponential growth and then a seasonal difference. These operations often involve `df.diff()`, `np.log()`, `np.sqrt()`, etc. 

#### White noise 

White noise is a series with:

* constant mean
* constant variance
* zero autocorrelations at all lags
* special case: if data has normal distribution, then Gaussian white noise

```python
gaussian_white_noise = np.random.normal(loc=0.02, scale=0.05, size=1000)
```

#### Random walk

$$X_t = X_{t-1} + \epsilon_t$$

In other words, the change is white noise:

$$X_t - X_{t-1} = \epsilon_t$$

One cannot forecast a random walk, the best forecast for the next value is the current value. 

An extension, random walk with drift:

$$X_t = \mu + X_{t-1} + \epsilon_t$$

$$X_t - X_{t-1} = \mu + \epsilon_t$$ 


# Augmented Dickey-Fuller test


The Dickey-Fuller test is the following: 

$$X_t = \mu + \phi X_{t-1} + \epsilon_t$$

Null hypothesis: $H_0 : \phi = 1$

This is equivalent to:

$$X_t - X_{t-1} = \mu + \phi X_{t-1} + \epsilon_t$$ 

Null hypothesis: $H_0 : \phi = 0$

If we add more lagged changes on the right hand side, it's the Augmented Dickey-Fuller test.

The null hypothesis for both Dickey-Fuller test and Augmented Dickey-Fuller test is that the data are non-stationary. 

If we use $5\%$ as the threshold, a p-value less than $5\%$ indicates that:

* we can reject the null hypothesis that the series is non-stationary with $95\%$ confidence.
* this series is stationary.

If we use $5\%$ as the threshold, a p-value larger than $5\%$ indicates that:

* we cannot reject the null hypothesis. 
* this series is non-stationary. 


```python
from statsmodels.tsa.stattools import adfuller

# p-value
adfuller(p1)[1]
```


# Autoregressive model (AR model)

AR model of order $1$, or simply AR$(1)$ model (there is only one lagged value on right hand side):

$$X_t = \mu + \phi X_{t-1} + \epsilon_t$$

If AR parameter $\phi = 1$, then this is a random walk. If $\phi = 0$, then this is white noise. 

In order for the process to be stable and stationary, $\phi$ has to be $- 1 < \phi < 1$. 

Interpretation of AR$(1)$ parameter:

* negative $\phi$: mean reversion 
* positive $\phi$: momentum. 
* the autocorrelation function decays exponentially for an AR$(1)$ time series at a rate of the AR$(1)$ parameter $\phi$. 


The model can be extended to include more lagged values and more $\phi$ parameters:

* AR$(1)$: $X_t = \mu + \phi_1 X_{t-1} + \epsilon_t$
* AR$(2)$: $X_t = \mu + \phi_1 X_{t-1} + \phi_2 X_{t-2} + \epsilon_t$
* etc. 

To simulate AR time series, take care of the convention: we must include the zero-lag coefficient of $1$ and the sign of the other coefficients is opposite what we have been using above. 

```python
from statsmodels.tsa.arima_process import ArmaProcess

# phi = + 0.9
ar = [1, - 0.9]
ma = [1]
arma_process = ArmaProcess(ar, ma)
simulated_data = arma_process.generate_sample(nsample=1000)

# phi = - 0.9
ar = [1, 0.9]
ma = [1]
arma_process = ArmaProcess(ar, ma)
simulated_data = arma_process.generate_sample(nsample=1000)
```


To estimate an AR model (estimate parameters from data):

```python
from statsmodels.tsa.arima_model import ARMA

# Fit an AR(1) model to the data, true phi = + 0.9
arma = ARMA(data, order=(1, 0))
arma_results = arma.fit()

print(arma_results.summary())
```

```
                              ARMA Model Results                              
==============================================================================
Dep. Variable:                      y   No. Observations:                 1000
Model:                     ARMA(1, 0)   Log Likelihood               -1455.318
Method:                       css-mle   S.D. of innovations              1.036
Date:                Sun, 12 Apr 2020   AIC                           2916.636
Time:                        20:45:48   BIC                           2931.360
Sample:                             0   HQIC                          2922.232
                                                                              
==============================================================================
                 coef    std err          z      P>|z|      [0.025      0.975]
------------------------------------------------------------------------------
const         -0.0938      0.323     -0.290      0.772      -0.727       0.540
ar.L1.y        0.8995      0.014     65.170      0.000       0.872       0.927
                                    Roots                                    
=============================================================================
                  Real          Imaginary           Modulus         Frequency
-----------------------------------------------------------------------------
AR.1            1.1117           +0.0000j            1.1117            0.0000
-----------------------------------------------------------------------------

```

We can do forecasting, both in-sample and out-of-sample using `statsmodels`. The in-sample is a forecast of the next data point using the data up to that point, and the out-of-sample forecasts any number of data points in the future. These forecasts can be made using either the `predict()` method if we want the forecasts in the form of a series of data, or using the `plot_predict()` method if we want a plot of the forecasted data. We supply the starting point for forecasting and the ending point, which can be any number of data points after the data set ends. `plot_predict()` also gives confidence intervals around the out-of-sample forecasts. 


```python
# Plot the original series and the forecasted series
arma_results.plot_predict(start=0, end="2030")
plt.show()
```


To identify the order of an AR model:

* **Partial autocorrelation function (PACF)** meansures the incremental benefit of adding another lag. Each coefficient of PACF represents how significant adding the corresponding lag when we already have all the previous lags included. 
* **Information criteria** computes the goodness-of-fit with the estimated parameters, but apply a penalty function on the number of parameters in the model. In general, the more parameters in a model, the better the model will fit the data. But this can lead to overfitting. There are two popular information criteria: 
    * AIC (Akaike Information Criterion)
    * BIC (Bayesian Information Criterion)

In practice, the way to use the information criteria is to fit several models, each with a different number of parameters, and choose the one with the lowest information criteria. Compared to AIC, BIC penalizes additional model orders more than AIC and so the BIC will sometimes suggest a simpler model. The AIC and BIC will often choose the same model, but when they don't we have to make a choice. If our goal is to identify good predictive models, we should use AIC. If our goal is to identify a good explanatory model, we should use BIC. 

```python
from statsmodels.graphics.tsaplots import plot_pacf

plot_pacf(data, lags=20)
plt.show()
```

```python
from statsmodels.tsa.arima_model import ARMA

bic = []
range_ = range(1, 7)
for p in range_:
    arma = ARMA(simulated_data, order=(p, 0))
    arma_results = arma.fit()  
    bic.append(arma_results.bic)

plt.plot(range_, bic, marker="o")
plt.xlabel("Order of AR Model")
plt.ylabel("BIC")
plt.show()
```


# Moving average model (MA model)

MA model of order $1$, or simply MA$(1)$ model (there is only one lagged error on right hand side):

$$X_t = \mu + \epsilon_t + \theta \epsilon_{t-1}$$

If MA parameter $\theta = 0$, then this is white noise. 

MA models are stationary for all values of $\theta$. 

Interpretation of MA$(1)$ parameter: 

* negative $\theta$: one-period mean reversion
* positive $\theta$: one-period momentum 
* one-period autocorrelation (lag-$1$ autocorrelation) is not $\theta$, it is $\frac{\theta}{1 + \theta^2}$. There is zero autocorrelation for an MA$(1)$ beyond lag-$1$. 
* higher frequency stock data is well modeled by an MA$(1)$ process. For example, one day's prices (on September 1, 2017) for Sprint stock (ticker symbol "S") sampled at a frequency of one minute. The stock market is open for 6.5 hours (390 minutes), from 9:30am to 4:00pm. Such data can be accessed via Google Finance [3]. Stocks trade at discrete one-cent increments (although a small percentage of trades occur in between the one-cent increments) rather than at continuous prices, and when we plot the data we should observe that there are long periods when the stock bounces back and forth over a one cent range. This is sometimes referred to as "bid/ask bounce". The bouncing of the stock price between bid and ask induces a negative first order autocorrelation, but no autocorrelations at lags higher than $1$. 


The model can be extended to include more lagged errors and more $\theta$ parameters:

* MA$(1)$: $X_t = \mu + \epsilon_t + \theta_1 \epsilon_{t-1}$
* MA$(2)$: $X_t = \mu + \epsilon_t + \theta_1 \epsilon_{t-1} + \theta_2 \epsilon_{t-2}$
* etc. 

An MA$(q)$ model has no autocorrelation beyond lag-$q$. 

To simulate MA time series, take care of the convention: we must include the zero-lag coefficient of $1$. However, unlike with the AR simulation, we don't need to reverse the sign of $\theta$, its sign is what we have been using above. 

```python
from statsmodels.tsa.arima_process import ArmaProcess

# theta = + 0.9
ar = [1]
ma = [1, 0.9]
arma_process = ArmaProcess(ar, ma)
simulated_data = arma_process.generate_sample(nsample=1000)

# theta = - 0.9
ar = [1]
ma = [1, - 0.9]
arma_process = ArmaProcess(ar, ma)
simulated_data = arma_process.generate_sample(nsample=1000)
```

To estimate an MA model (estimate parameters from data):

```python
from statsmodels.tsa.arima_model import ARMA

# Fit an MA(1) model to the data, true theta = + 0.9
arma = ARMA(data, order=(0, 1))
arma_results = arma.fit()

print(arma_results.summary())
```

```
                              ARMA Model Results                              
==============================================================================
Dep. Variable:                      y   No. Observations:                 1000
Model:                     ARMA(0, 1)   Log Likelihood               -1404.378
Method:                       css-mle   S.D. of innovations              0.985
Date:                Mon, 13 Apr 2020   AIC                           2814.756
Time:                        16:10:50   BIC                           2829.479
Sample:                             0   HQIC                          2820.352
                                                                              
==============================================================================
                 coef    std err          z      P>|z|      [0.025      0.975]
------------------------------------------------------------------------------
const         -0.0664      0.060     -1.112      0.266      -0.184       0.051
ma.L1.y        0.9203      0.012     75.882      0.000       0.896       0.944
                                    Roots                                    
=============================================================================
                  Real          Imaginary           Modulus         Frequency
-----------------------------------------------------------------------------
MA.1           -1.0867           +0.0000j            1.0867            0.5000
-----------------------------------------------------------------------------
```

Just as with AR models, we can use MA models to do forecasting, both in-sample and out-of-sample using `statsmodels`. This can be done via either the `predict()` method if we want the forecasts in the form of a series of data, or the `plot_predict()` method if we want a plot of the forecasted data. One big difference between out-of-sample forecasts with an MA$(1)$ model and an AR$(1)$ model is that the MA$(1)$ forecasts more than one period in the future are simply the mean of the sample. 


```python
# Plot the original series and the forecasted series
arma_results.plot_predict(start=0, end="2030")
plt.show()
```

# Autoregressive moving average model (ARMA model)


An ARMA model is a combination of an AR model and an MA model. 

ARMA$(1, 1)$ model:

$$X_t = \mu + \phi X_{t-1} + \epsilon_t + \theta \epsilon_{t-1}$$

ARMA models are generally denoted ARMA$(p, q)$ where parameters $p$ and $q$ are non-negative integers, $p$ is the order of the AR model, $q$ is the order of the MA model. 

ARMA models can be converted to pure AR or pure MA models. Here is an example of converting an AR$(1)$ model into an an MA$(\infty)$: 

$$X_t = \mu + \phi X_{t-1} + \epsilon_t$$

$$X_t = \mu + \phi (\mu + \phi X_{t-2} + \epsilon_{t-1}) + \epsilon_t$$

$$X_t = \frac{\mu}{1-\phi} + \epsilon_t + \phi \epsilon_{t-1} + \phi^2 \epsilon_{t-2} + \phi^3 \epsilon_{t-3} + ...$$

This demonstrates that an AR$(1)$ model is equivalent to an MA$(\infty)$ model with the appropriate parameters. 


#### Using ACF and PACF to choose model order



|      | AR$(p)$ | MA$(q)$ | ARMA$(p, q)$ |
|-------------|----------|---------|---------------------|
| ACF     | tails off  | cuts off after lag-$q$  | tails off              |
| PACF     | cuts off after lag-$p$ | tails off    | tails off              |


The time series must be made stationary before making these plots. If the ACF values are high and tail off very very slowly, this is the sign that the data is non-stationary, so it needs to be differenced. If the autocorrelation at lag-$1$ is very negative, this is the sign that we have taken the difference too many times. 


In the case of ARMA$(p, q)$, we cannot deduce the model orders $p$ and $q$ from the plots. However, we can use AIC and BIC to find the most appropriate $p$ and $q$. Sometimes when searching over model orders we will attempt to fit an order that leads to an error, for example, `ValueError: Non-stationary starting augoregressive parameters found with enforce_stationary set to True`. This `ValueError` tells us that we have tried to fit a model which would result in a non-stationary set of AR coefficients. We can use `try/except` blocks to skip this one. 





# Cointegration model 

The idea behind cointegration (协整) is that even if the prices of two different assets both follow random walks, it is still possible that a linear combination of them is not a random walk. If that's true, then even though these two assets are not forecastable because they are random walks, the linear combination is forecastable. We say that these two assets are cointegrated. One analogy is that a dog owner walking his dog with a retractable leash. The position of the dog owner and the position of the dog may both follow random walks, but the distance between them may very well be mean reverting. Examples can be found by looking at economic substitutes: heating oil and natural gas, platinum and palladium, corn and wheat, corn and sugar, Bitcoin and Ethereum, etc. For stocks, a natural starting point for identifying cointegrated pairs are stocks in the same industry. However competitors are not necessarily economic substitutes, think of Apple and Blackberry. 

Two steps to test for cointegration:

* regress the level of one series $P_t$ on the level of the other series $Q_t$ to get the slope coefficient $c$ or the cointegration vector (coefficients of the linear combination). 
* run Augmented Dickey-Fuller test on $P_t - c Q_t$ or on the residuals of the regression. 

Alternatively, `statsmodels` has a function `coint` that combines both steps, its null hypothesis is no cointegration. 

```python
from statsmodels.tsa.stattools import coint

# p-value
coint(s1, s2)[1]
```

# Autoregressive integrated moving average model (ARIMA model)


An ARIMA model is a generalization of an ARMA model. ARIMA models are applied in some cases where data show evidence of non-stationarity, where an initial differencing step (corresponding to the "integrated" part of the model) can be applied one or more times to eliminate the non-stationarity. 

ARIMA models are generally denoted ARIMA$(p, d, q)$ where parameters $p$, $d$, and $q$ are non-negative integers, $p$ is the order of the AR model, $d$ is the degree of differencing, $q$ is the order of the MA model. 


Using the ARIMA module on a random walk series is identical to using the ARMA module on the first-order difference of that series followed by taking cumulative sums of these differences to get the original series forecast. 


```python
from statsmodels.tsa.arima_model import ARIMA

arima = ARIMA(data, order=(1,1,1))
arima_results = arima.fit()

arima_results.plot_predict(start="1872-01-01", end="2046-01-01")
plt.show()
```


In terms of forecasting, sometimes the estimate of the drift will have a much bigger impact on long range forecasts than the ARMA parameters. 


# ARMAX model

Exogenous ARMA that uses external variables as well as time series. 

$$\text{ARMAX} = \text{ARMA} + \text{linear regression}$$

ARMAX$(1, 1)$ model:

$$X_t = \mu + \phi X_{t-1} + \epsilon_t + \theta \epsilon_{t-1} + \eta Z_t$$

where $Z_t$ is the exogenous input. 

```python
from statsmodels.tsa.arima_model import ARMA

arma = ARMA(data, order=(p, q), exog=df["..."])
arma_results = arma.fit()

print(arma_results.summary())
```

# SARIMAX model 

Seasonal AutoRegressive Integrated Moving Average with eXogenous regressors model

```python
from statsmodels.tsa.statespace.sarimax import SARIMAX

sarimax = SARIMAX(df, order=(p, d, q)) # trend="c"

sarimax_results = sarimax.fit()

# make in-sample prediction for last 25 values  
prediction_results = sarimax_results.get_prediction(start=-25)
# the central value of the forecast 
predicted_mean = prediction_results.predicted_mean
# confidence interval of forecasts, 
# use plt.fill_between to visualize 
confidence_intervals = prediction_results.conf_int()

# dynamic prediction (predict one step ahead and then use 
# this predicted value to forecast the next value after that, 
# and so on)
prediction_results = sarimax_results.get_prediction(start=-25, dynamic=True)

# make out-of-sample prediction (forecast future), 
# this is also a dynamic forecast 
prediction_results = sarimax_results.get_forecast(steps=5)
predicted_mean = prediction_results.predicted_mean
confidence_intervals = prediction_results.conf_int()
```

For an ideal model, the residuals (difference between one-step-ahead predictions and the real values) should be uncorrelated white Gaussian noise centered on zero. We can use the `plot_diagnostics()` method to evaluate this, this method generates 4 plots. The first plot is standardized residual, if our model is working correctly, there should be no obvious structure in the residuals. Another of the four plots shows the distribution of the residuals where the histogram shows the measured distribution, the orange line shows a smoothed version of this histogram and the green line shows a normal distribution. If our model is good, these two lines should be almost the same. The normal Q-Q plot is another way to show how the distribution of the model residuals compares to a normal distribution. If our residuals are normally distributed then all the points should lie along the red line, except perhaps some values at either end. The last plot is the correlogram, which is just an ACF plot of the residuals rather than the data. $95\%$ of the correlations for lag greater than zero should not be significant. If there is significant correlation in the residuals, it means that there is information in the data that our model hasn't captured. 

In the output of the `summary()` method, `Prob(Q)` is the p-value associated with the null hypothesis that the residuals have no correlation structure. `Prob(JB)` is the p-value associated with the null hypothesis that the residuals are Gaussian normally distributed. 

```python
sarimax_results.plot_diagnostics(figsize=(16, 8))
plt.show()
```

For seasonal data, the full time series can be decomposed into 3 parts: the trend, the seasonal component and the residual. We can use `statsmodels.tsa.seasonal.seasonal_decompose`to separate out any time series into these three components. 

```python
from statsmodels.tsa.seasonal import seasonal_decompose

# freq corresponds the number of data points in each repeated cycle 
decompose_result = seasonal_decompose(df["..."], freq=12) 

decompose_result.plot()
plt.show()
```

We can use ACF to identify the frequency/period. In the case of a seasonal time series, the ACF will show periodic correlation pattern. To find the frequency we look for a lag greater than one, which is a peak in the ACF plot. In order to use ACF to identify the period of a non-stationary time series, it might be useful to detrend it first, we can substract long rolling average over $N$ steps. Any large window size $N$ will work for this, we could use a window size of any value bigger than the likely period.

```python
df = (df - df.rolling(N).mean()).dropna()
```

$$\text{SARIMA} = \text{Seasonal ARIMA}$$

Fitting a SARIMA model is like fitting two different ARIMA models at once, one to the seasonal part and another to the non-seasonal part. Since we have these two models, we will have two sets of orders: 

$$\text{SARIMA}(p, d, q)(P, D, Q)_S$$

* Non-seasonal orders:
    * $p$: autoregressive order
    * $d$: differencing order
    * $q$: moving average order
* Seasonal orders:
    * $P$: seasonal autoregressive order
    * $D$: seasonal differencing order
    * $Q$: seasonal moving average order 

There is also a new order $S$ which is the length of the seasonal cycle. 

Comparison of ARIMA and SARIMA models:

* ARIMA$(2, 0, 1)$:
    * $X_t = \mu + \phi_1 X_{t-1} + \phi_2 X_{t-2} + \epsilon_t + \theta_1 \epsilon_{t-1}$
* SARIMA$(0, 0, 0)(2, 0, 1)_{7}$:
    * $X_t = \mu + \phi_7 X_{t-7} + \phi_{14} X_{t-14} + \epsilon_t + \theta_7 \epsilon_{t-7}$

The SARIMA$(0, 0, 0)(2, 0, 1)_{7}$ model will be able to capture seasonal, weekly patterns, but won't be able to capture local, day to day patterns. If we construct a SARIMA model and include non-seasonal orders as well, then we can capture both of these patterns. 

Fitting a SARIMA model:

```python
from statsmodels.tsa.statespace.sarimax import SARIMAX

sarimax = SARIMAX(df, order=(p, d, q), seasonal_order=(P, D, Q, S))
sarimax_results = sarimax.fit()
```

The value of $S$ can be found by using the ACF. The next task is to find the order of differencing. To make a time series stationary we may need to apply seasonal differencing. In seasonal differencing, instead of substracting the most recent time series value, we substract the time series value from one cycle ago. 

```python
# take the seasonal difference 
df = df.diff(S)
```

For example, if the time series shows a trend then we take the (normal) difference. If there is a strong seasonal cycle, then we will also take the seasonal difference. Once we have found the two orders of differencing and made the time series stationary, we need to find the other model orders. To find the non-seasonal orders, we plot the ACF and the PACF of the differenced time series. To find the seasonal orders, we plot the ACF and the PACF of the differenced time series at multiple seasonal steps, this can be done as follows: 

```python
# plot seasonal ACF and PACF 
# S is the length of the seasonal cycle 
plt.figure(figsize=(15, 8))
plt.subplot(211)
plot_acf(df, lags=[S, 2*S, 3*S, 4*S, 5*S, 6*S], ax=plt.gca())
plt.subplot(212)
plot_pacf(df, lags=[S, 2*S, 3*S, 4*S, 5*S, 6*S], ax=plt.gca())
plt.show()
```

Here we set the `lags` parameter to a list of lags instead of a maximum, this plots the ACF and PACF at these specific lags only. 

Sometimes we will have the choice of whether to apply seasonal differencing, non-seasonal differencing or both to make a time series stationary. Some good rules of thumb are:

* Never use more than one order of seasonal differencing, $0 \leqslant D \leqslant 1$ 
* Never more than two orders of differencing in total, $0 \leqslant d + D \leqslant 2$ 

Sometimes we will be able to make a time series stationary by using either one seasonal differencing or one non-seasonal differencing, we might build models for each in this case and see which one makes better predictions. 

A time series is said to have a weak seasonality, meaning that the seasonal oscillations don't always look the same and are harder to identify. When we have a time series that has a strong seasonality, we should always use one order of seasonal differencing. This will ensure that the seasonal oscillation will remain in our dynamic predictions far into the future without fading out. 

* Weak seasonal pattern
    * Use seasonal differencing if necessary
* Strong seasonal pattern
    * Always use seasonal differencing 

Just like in ARIMA modeling sometimes we need to use other transformations on our time series before fitting. Whenever the seasonality is additive we should not need to apply any transforms except for differencing. Additive seasonality is where the seasonal pattern just adds or takes away a little from the trend. When seasonality is multiplicative, the SARIMA model cannot fit this without extra transforms. If the seasonality is multiplicative the amplitude of the seasonal oscillations will get larger as the data trends up or smaller as it trends down. 

* Additive seasonality $=$ trend $+$ season
    * Proceeds as usual with differencing
* Multiplicative seasonality $=$ trend $\times$ season
    * Apply log transform first `np.log()`



Searching over SARIMA model orders using `for` loops may be complex, there is a package that will do most of this work for us. The `auto_arima` function from this package loops over model orders to find the best one. The object returned by the function is the results object of the best model found by the search, this object is almost exactly a `statsmodels` `SARIMAXresults` object and has the `summary()` and the `plot_diagnostics()` method. 

```python
import pmdarima as pm

results1 = pm.auto_arima(df)

# create model for SARIMAX(p,1,q)(P,1,Q)_{7} 
results2 = pm.auto_arima(df,
                         seasonal=True, m=7,
                         d=1, D=1, 
                         start_p=1, start_q=1,
                         max_p=3, max_q=3,
                         max_P=2, max_Q=2,
                         trace=True,
                         error_action='ignore',
                         suppress_warnings=True) 

print(results2.summary())
```

The `pmdarima` package can also be used to update the model (incorporate data we have collected since then)

```python
# add new observations and update the model parameters
results.update(df_new)
```

However this does not choose the model orders again, so if we are updating with a large amount of new data, it might be best to go back to the start of Box-Jenkins method. 





# Box-Jenkins method

From raw data $\Rightarrow$ production model:

* identification
    * Determine whether it is seasonal? If so, find its seasonal period. 
    * Is it stationary? What (seasonal and non-seasonal) differencing will make it stationary? What transforms will make it stationary? 
    * What values of $p$ and $q$ are the most promising? 
    * Tools: plot the time series, augmented Dickey-Fuller test, ACF, PACF, etc. 
* estimation
    * Use the `fit()` method. 
    * Choose between models using AIC and BIC 
* model diagnostics
    * Evaluate the best fitting model 
    * Are the residuals uncorrelated? Are the residuals normally distributed? 
    * Tools: `plot_diagnostics()`, `summary()` 
    * Is this model good enough or do we need to go back and rework it? 



# GARCH model 

Generalized AutoRegressive Conditional Heteroskedasticity model 

This is a popular approach to model volatility. A common assumption in time series modeling is that volatility (standard deviation, variance) remains constant over time. However it is frequently observed in financial return data that the presence of varying volatility systematically over time, this is called heteroskedasticity. 

```python
from arch import arch_model

# GARCH(1, 1) model 
model = arch_model(data, p=1, q=1, 
                   mean="constant", 
                   vol="GARCH", 
                   dist="normal")
```







# Machine learning for time series 


Unpivot a DataFrame from wide to long format (melt):

```python
df = data.melt(id_vars="Product_Code", var_name="Week", value_name="Sales")
```

```
              W0  W1  W2  W3  W4  W5  W6  W7  W8  W9  ...  W42  W43  W44  W45  \
Product_Code                                          ...                       
P1            11  12  10   8  13  12  14  21   6  14  ...    4    7    8   10   
P2             7   6   3   2   7   1   6   3   3   3  ...    2    4    5    1   
P3             7  11   8   9  10   8   7  13  12   6  ...    6   14    5    5   
```

```
                   Sales
Product_Code Week       
P1           0        11
             1        12
             2        10
             3         8
             4        13
...                  ...
P99          47        8
```

Adding lags as basic feature engineering:

```python
def add_lag(original_df, lag=3, groupby_col="Product_Code", col="Sales", diff=False, dropna=True):
    df = original_df.copy()
    columns = []
    for i in range(1, lag + 1):
        c = "lag_" + str(i)
        columns.append(c)
        df[c] = df.groupby(groupby_col)[col].shift(i)
    if diff: # pay attention for multicollinearity
        for i in range(1, lag + 1):
            c = "diff_" + str(i)
            columns.append(c)
            df[c] = df.groupby(groupby_col)["lag_" + str(i)].diff(1)
    if dropna:
        df.dropna(inplace=True, axis=0, how="any")
        df[columns] = df[columns].astype("int")
    return df
    
df = add_lag(df, 3)
```

Time series cross validation:

```python
from sklearn.model_selection import TimeSeriesSplit

def split_test_ts(X, y, test_size=1):
    """
    X: pd.DataFrame
    y: pd.Series
    
    reserves the last test_size elements of the time series dataset 
    as the test set of the time series problem. 
    """
    assert test_size > 0, "test_size must be larger than 0."
    X_tmp, X_test = X.iloc[: - test_size, :], X.iloc[- test_size :, :]
    y_tmp, y_test = y.iloc[: - test_size], y.iloc[- test_size :]
    return X_tmp, X_test, y_tmp, y_test 

def split_train_validation_ts(X, y, n_splits=3, max_train_size=None):
    """
    X: pd.DataFrame
    y: pd.Series
    
    returns a generator that generates training sets and 
    validation sets for time series cross validation. 
    
    If a separate test set is needed, call split_test_time_series() 
    before calling this function. 
    """
    tss = TimeSeriesSplit(n_splits=n_splits, max_train_size=max_train_size)
    for train_idx, validation_idx in tss.split(X):
        X_train, X_validation = X.iloc[train_idx, :], X.iloc[validation_idx, :]
        y_train, y_validation = y.iloc[train_idx], y.iloc[validation_idx]
        yield X_train, X_validation, y_train, y_validation 
        
def get_dataset_with_specific_lag(df, lag, target="Sales", test_size=3600):
    df = add_lag(df, lag)
    y = df.loc[:, target]
    X = df.drop(target, axis=1)
    X_train, X_test, y_train, y_test = split_test_ts(X, y, test_size=test_size)
    return X_train, X_test, y_train, y_test
```

```python
X, X_test, y, y_test = split_test_ts(X, y, test_size=3600)

train_validation_generator = split_train_validation_ts(X, y, n_splits=10)
for X_train, X_validation, y_train, y_validation in train_validation_generator:
    pass 
```


# Time series feature extraction with tsfresh

`tsfresh` is a python package which automatically calculates a large number of time series features. Further the package contains methods to evaluate the explaining power and importance of such features for regression or classification tasks.

Let's look at one example. Suppose we have the following dataframe `df` where the length of `A`'s time series is 1000, the length of `B`'s time series is 1200: 

```
     id     value
0     A  0.008801
1     A  0.008801
2     A  0.008801
3     A  0.008803
4     A  0.008808
...  ..       ...
2195  B  0.001495
2196  B  0.001498
2197  B  0.001500
2198  B  0.001503
2199  B  0.001506

[2200 rows x 2 columns]
```

`tsfresh` can extract 756 features with version `0.15.1`. To extract all features, we do use `tsfresh.extract_features()`. This function can be very time-consuming if the input's size is large. The returned `feature_matrix` has the shape: 

$$(\text{nb_ids}, \text{nb_extracted_features_per_time_series} \times \text{nb_time_series})$$

```python
import tsfresh

feature_matrix = tsfresh.extract_features(df, column_id="id", column_value="value")
```

We end up with a `DataFrame` `feature_matrix` with all possible features. Then we should:

* first, remove all `NaN` values that were created by feature calculators that cannot be used on the given data, e.g. because it has too low statistics. 
    * `tsfresh.utilities.dataframe_functions.impute()` replaces all `NaN`s and `inf`s from the input `DataFrame` with average/extreme values from the same columns.
        * `-inf -> min`
        * `+inf -> max`
        * `NaN -> median` 
    * `tsfresh.utilities.dataframe_functions.impute()` is in-place. 
* second, select only the relevant features
    * `tsfresh.feature_selection.selection.select_features()` checks the significance of all features (columns) with respect to target vector `y` and return a possibly reduced feature matrix only containing relevant features.

```python
tsfresh.utilities.dataframe_functions.impute(feature_matrix)
filtered_feature_matrix = tsfresh.select_features(feature_matrix, y)
```

We can also perform the extraction, imputing and filtering at the same time with the `tsfresh.extract_relevant_features()` function. 

```python
filtered_feature_matrix = tsfresh.extract_relevant_features(df, y, column_id="id", column_value="value")
```


# Analyzing time series motifs and anomalies with stumpy

For the `stumpy` package, time series motifs are approximately repeated subsequences found within a longer time series. 

`stumpy` compares all subsequences within a time series by computing the pairwise z-normalized Euclidean distances and then storing only the index to its nearest neighbor. This nearest neighbor distance is referred to as the matrix profile and the index to each nearest neighbor within the time series is referred to as the matrix profile index. 

The global minima from the matrix profile correspond to the locations of the two subsequences that make up the motif pair. The matrix profile index also tells us which subsequence within the time series does not have nearest neighbor that resembles itself, which is referred to as a discord, novelty, or anomaly. 

```python
import stumpy

window_size = 640  
matrix_profile = stumpy.stump(data, m=window_size)
```


The returned `matrix_profile` is a `numpy array` of shape `(data.size, 4)` (suppose `data` is 1-D). The first column consists of the matrix profile (the distance), the second column consists of the matrix profile indices, the third column consists of the left matrix profile indices, and the fourth column consists of the right matrix profile indices.


```python
def ts_stumpy_motif(data, matrix_profile, window_size, plot=True):
    """Time series motif discovery (stumpy package)"""
    from matplotlib.patches import Rectangle as patch_rect_
    idx = matrix_profile[:, 0].argpartition(0)[0]
    indices = np.array([idx, matrix_profile[idx, 1]]) 
    if plot:
        fig = plt.figure(figsize=(14, 5))
        plt.suptitle("Time series motif discovery - window size = {}".format(window_size), 
                     fontsize="xx-large")
        ax = plt.subplot(211)
        plt.plot(data)
        plt.gca().set_ylabel("Time series", fontsize="xx-large")
        plt.xticks(fontsize="xx-large")
        plt.yticks(fontsize="xx-large")
        bottom_, top_ = plt.ylim()
        rect_height = top_ - bottom_
        for idx in indices:
            rect = patch_rect_((idx, 0), window_size, rect_height, facecolor="lightgrey")
            plt.gca().add_patch(rect)
        plt.subplot(212, sharex=ax)
        plt.plot(matrix_profile[:, 0])
        plt.gca().set_ylabel("Matrix profile", fontsize="xx-large")
        plt.xticks(fontsize="xx-large")
        plt.yticks(fontsize="xx-large")
        for idx in indices:
            plt.axvline(x=idx, linestyle="dashed")
        plt.show()
    return indices
```

![XXX](/assets/images/blog/stumpy_motif.png)

```python
def ts_stumpy_anomaly(data, matrix_profile, window_size, plot=True):
    """Time series anomaly discovery (stumpy package)"""
    from matplotlib.patches import Rectangle as patch_rect_
    idx = matrix_profile[:, 0].argpartition(matrix_profile[:, 0].size - 1)[-1]
    if plot:
        plt.figure(figsize=(14, 5))
        plt.suptitle("Time series anomaly discovery - window size = {}".format(window_size), 
                     fontsize="xx-large")
        ax = plt.subplot(211)
        plt.plot(data)
        plt.gca().set_ylabel("Time series", fontsize="xx-large")
        bottom_, top_ = plt.ylim() 
        rect = patch_rect_((idx, 0), window_size, top_-bottom_, facecolor='lightgrey')
        plt.gca().add_patch(rect)
        plt.xticks(fontsize="xx-large")
        plt.yticks(fontsize="xx-large")
        plt.subplot(212, sharex=ax)
        plt.plot(matrix_profile[:, 0])
        plt.gca().set_ylabel('Matrix profile', fontsize="xx-large")
        plt.axvline(x=idx, linestyle="dashed")
        plt.xticks(fontsize="xx-large")
        plt.yticks(fontsize="xx-large")
        plt.show()
    return idx
```

![XXX](/assets/images/blog/stumpy_anomaly.png)




# Machine learning for audio signals 


For a classification problem whose inputs are audio data (for example, heartbeat time series), one can extract summary statistics from the envelope. The envelope is obtained by smoothing the absolute value of the original centered data so that the total amount of sound energy over time is distinguishable. This is done by first applying `np.abs()` to the original centered audio waveform, then by using the `.rolling()` method. This is shown as follows:

```python
# suppose that audio1, audio2 are both 1-D arrays 

# smooth by applying a rolling mean
audio_envelope1 = audio1.apply(np.abs).rolling(20).mean()
audio_envelope2 = audio2.apply(np.abs).rolling(window=50).mean()
```

Then we can use `np.mean()`, `np.std()`, `np.max()`, etc. to extract statistics from the obtained envelope. 

The envelope calculation is also a common technique in computing tempo (每分钟节拍数，beats per minute) and rhythm (节奏，韵律) features. The tempogram (which estimates the tempo of a sound over time) can be calculated by using the `librosa` package. 

Some basics of the `librosa` package,

* The sampling rate, sometimes denoted by `sr`, is a positive integer which indicates the number of samples per second of a time series. 
* A frame is a short slice of a time series used for analysis purposes. This usually corresponds to a single column of a spectrogram matrix. 
* A window is a vector or function used to weight samples within a frame when computing a spectrogram. 
* `n_fft` is a positive integer which indicates the number of samples in an analysis window (or frame), this can be called the frame length. 
* `hop_length` is a positive integer which indicates the number of samples between successive frames, e.g., the columns of a spectrogram. 
* `librosa.core.stft()` returns a complex-valued matrix `D` such that:
    * `np.abs(D)` is the magnitude 
    * `np.angle(D)` is the phase 
* Spectral centroid and spectral bandwidth are only defined with real-valued non-negative input. `magnitude, phase = librosa.magphase(D)` separates a complex-valued STFT `D` into its magnitude and phase components. 
* `librosa` functions tend to only operate on `numpy` arrays instead of `DataFrames`, so we'll access `pandas` data as a `numpy` array with the `.values` attribute. 


```python
import librosa

tempo = librosa.beat.tempo(audio, sr=sampling_rate, hop_length=2**6, aggregate=None)
```

`tempo` and `audio` are both 1-D arrays, their sizes satisfy the following relation: 

$$\text{tempo.size} = \text{nb_frames} \approx \frac{\text{audio.size}}{\text{hop_length}}$$

Then we can extract statistics from the tempogram by using `np.mean(tempo)` (the average tempo of this particular audio signal), `np.std(tempo)`, `np.max(tempo)`, etc. 



Spectrograms (时频谱) are common in time-series analysis. By definition, the spectrogram is the squared magnitude of the short-time Fourier transform (STFT). The Fourier transform (FFT) describes, for a window of time, the presence of fast and slow oscillations in a time series. We can do the spectral feature engineering by using the spectrogram as a base. For example, we can calculate the spectral centroids and spectral bandwidth which describe where most of the spectral energy is at each moment of time. One way to do this is to use the `librosa` packages (other packages can also be used, for example `scipy.signal.spectrogram`). Under the assumption that the temporal features and spectral features provide independent information we can combine them to train our machine learning model. 


```python
import librosa

audio, sampling_rate = librosa.load(audio_file_path)
time_axis = np.arange(0, audio.size) / sampling_rate # seconds since the beginning 

n_fft = 2 ** 7
hop_length = 2 ** 4
stft = librosa.stft(audio, hop_length=hop_length, n_fft=n_fft)

magnitude, phase = librosa.magphase(stft)
```

```python
import librosa.display

# convert into decibels
spec_db = librosa.amplitude_to_db(magnitude)

# compare the raw audio to the spectrogram of the audio
plt.figure(figsize=(10, 10))
ax = plt.subplot(211)
plt.plot(time_axis, audio)
plt.subplot(212, sharex=ax)
librosa.display.specshow(spec_db, sr=sampling_rate, x_axis="time", 
                         y_axis="hz", hop_length=hop_length)
plt.show()
```

![XXX](/assets/images/blog/audio_signal_spectrogram.png)

```python
# calculate the spectral centroid and bandwidth for the spectrogram from time-series input
centroids = librosa.feature.spectral_centroid(y=audio, sr=sampling_rate, 
                                              hop_length=hop_length, n_fft=n_fft)[0]
bandwidths = librosa.feature.spectral_bandwidth(y=audio, sr=sampling_rate, 
                                                hop_length=hop_length, n_fft=n_fft)[0]

# calculate the spectral centroid and bandwidth for the spectrogram from spectrogram input
centroids = librosa.feature.spectral_centroid(S=magnitude)[0]
bandwidths = librosa.feature.spectral_bandwidth(S=magnitude)[0]
```

The obtained `centroids` and `bandwidths` are both 1-D arrays and their sizes satisfy the following relations:

$$\text{centroids.size} = \text{nb_frames} \approx \frac{\text{audio.size}}{\text{hop_length}}$$

$$\text{bandwidths.size} = \text{nb_frames} \approx \frac{\text{audio.size}}{\text{hop_length}}$$



```python
# visualize spectral centroid and bandwidth on top of the spectrogram
plt.figure(figsize=(10, 5))
librosa.display.specshow(spec_db, x_axis='time', y_axis='hz', hop_length=hop_length)
times_spec = np.arange(0, spec_db.shape[1])
plt.plot(times_spec, centroids)
plt.fill_between(times_spec, centroids - bandwidths / 2, centroids + bandwidths / 2, alpha=.5)
plt.show()
```

![XXX](/assets/images/blog/audio_signal_spectral_features.png)



To use the spectral features as machine learning features, we can use `np.mean(centroids)` (the average spectral centroid of this particular audio signal, which is a frequency), `np.std(centroids)`, `np.max(centroids)`, `np.mean(bandwidths)` (the average spectral bandwidth of this particular audio signal, which is a frequency range), `np.std(bandwidths)`, `np.max(bandwidths)`, etc. 






# References


[1] Https://plus.google.com/u/0/+Datacamp/. (n.d.). Sign in. DataCamp. https://learn.datacamp.com/courses/time-series-analysis-in-python 

[2] E. E. Holmes, M. D. Scheuerell, and E. J. Ward. (2020, February 3). 5.3 Dickey-Fuller and augmented Dickey-Fuller tests - Applied time series analysis for fisheries and environmental sciences. NWFSC Time-Series Analysis. https://nwfsc-timeseries.github.io/atsa-labs/sec-boxjenkins-aug-dickey-fuller.html 

[3] 6 ways to download free intraday and tick data for the U.S. stock market. (n.d.). QuantShare Trading Software. https://www.quantshare.com/sa-426-6-ways-to-download-free-intraday-and-tick-data-for-the-us-stock-market 

[4] Autoregressive integrated moving average. (2005, February 15). Wikipedia, the free encyclopedia. Retrieved April 14, 2020, from https://en.wikipedia.org/wiki/Autoregressive_integrated_moving_average 

[5] Https://plus.google.com/u/0/+Datacamp/. (n.d.). Sign in. DataCamp. https://learn.datacamp.com/courses/arima-models-in-python 

[6] Https://plus.google.com/u/0/+Datacamp/. (n.d.). Sign in. DataCamp. https://learn.datacamp.com/courses/garch-models-in-python 

[7] Https://plus.google.com/u/0/+Datacamp/. (n.d.). Sign in. DataCamp. https://learn.datacamp.com/courses/machine-learning-for-time-series-data-in-python 





































