---
layout: post
category: Python
title: statsmodels memo
tagline: by SunHaozhe
tags: 
  - Python
  - memo
  - Machine learning
mathjax: true
comments: true
published: true
---

Import statsmodels:

```python
import statsmodels.api as sm
```

# Ordinary Least Squares

Fit OLS:

```python
regression_results = sm.OLS(y_train, X_train).fit()
# regression_results is a 
# statsmodels.regression.linear_model.RegressionResultsWrapper
# Replace X_train by X_train.assign(const=1) if needed
```

Summarize the regression results:

```python
print(regression_results.summary())
```

```
                            OLS Regression Results                            
==============================================================================
Dep. Variable:                revenue   R-squared:                       0.604
Model:                            OLS   Adj. R-squared:                  0.604
Method:                 Least Squares   F-statistic:                     2140.
Date:                Tue, 24 Mar 2020   Prob (F-statistic):               0.00
Time:                        17:29:10   Log-Likelihood:                -55237.
No. Observations:                2804   AIC:                         1.105e+05
Df Residuals:                    2801   BIC:                         1.105e+05
Df Model:                           2                                         
Covariance Type:            nonrobust                                         
==============================================================================
                 coef    std err          t      P>|t|      [0.025      0.975]
------------------------------------------------------------------------------
budget         2.4760      0.047     52.976      0.000       2.384       2.568
popularity    2.9e+06   1.57e+05     18.433      0.000    2.59e+06    3.21e+06
const      -1.415e+07   2.19e+06     -6.467      0.000   -1.84e+07   -9.86e+06
==============================================================================
Omnibus:                     1912.035   Durbin-Watson:                   2.027
Prob(Omnibus):                  0.000   Jarque-Bera (JB):            57009.692
Skew:                           2.809   Prob(JB):                         0.00
Kurtosis:                      24.364   Cond. No.                     5.88e+07
==============================================================================

Warnings:
[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.
[2] The condition number is large, 5.88e+07. This might indicate that there are
strong multicollinearity or other numerical problems.
```



***Forward variable selection***

```python
def forward_variable_selection(X, y, early_stop_pvalue=None, k=None, 
                               const_col_name="const"):
    """
    Forward variable selection for regression problems
    
    X, y: pandas DataFrame
    
    If X has already been augmented by a constant column, 
    that column must be named by const_col_name. Otherwise 
    no columns should be named by const_col_name. 
    """
    if k is None: 
        k = X.shape[1]
        if const_col_name in X.columns:
            k -= 1 
    used = {const_col_name: True} 
    target = sm.OLS(y, pd.DataFrame([[1]], index=X.index)).fit().resid
    selected = []
    for _ in range(k):
        tuples = [] 
        for feature in X.columns:
            if not used.get(feature, False): 
                regression_results = sm.OLS(target, X.loc[:, [feature]]).fit()
                tuples.append((feature, regression_results.resid, 
                               regression_results.tvalues.iloc[0], 
                               regression_results.pvalues.iloc[0])) 
        chosen_tuple = max(tuples, key=lambda x: x[2]) 
        if early_stop_pvalue is not None:
            if chosen_tuple[3] > early_stop_pvalue:
                break 
        selected.append(chosen_tuple[0])
        used[chosen_tuple[0]] = True
        target = chosen_tuple[1]
    return selected

selected = forward_variable_selection(X_train, y_train)
```

***Leverage statistics***

High-leverage points are those observations, if any, made at extreme or outlying values of the independent variables such that the lack of neighboring observations means that the fitted regression model will pass close to that particular observation. 

In the linear regression model, the leverage score for the `i`-th observation is defined as:

$$h_{ii} = [H]_{ii}$$

the `i`-th diagonal element of the projection matrix $H$ where

$$H = X(X^{\intercal}X)^{-1}X^{\intercal}$$

```python
leverage = regression_results.get_influence().hat_matrix_diag
```

As leverage is independent of label vector `y`, on can instead do the following:

```python
def build_leverage(df, visualize=True, xticks_labels=None, rotation=85, 
                   title=None, augment_const=True):
    """
    If constant columns has not already been augmented, use augment_const=True, 
    otherwise use augment_const=False. 
    """
    if augment_const:
        X = df.assign(const=1)
    else:
        X = df
    regression_results = sm.OLS(pd.DataFrame([[1]], index=X.index), X).fit()
    leverage = regression_results.get_influence().hat_matrix_diag
    if visualize:
        plt.plot(leverage)
        plt.grid()
        if title is not None:
            plt.title(title)
        if xticks_labels is not None:
            plt.xticks(range(len(countries)), countries, rotation=rotation)
        plt.show()
    return leverage

leverage = build_leverage(X_train)
```


***Variance inflation factor (VIF) for multicollinearity***

```python
from statsmodels.stats.outliers_influence import variance_inflation_factor

def build_vif(df):
    vif = pd.DataFrame()
    vif["feature"] = df.columns
    vif["vif"] = [variance_inflation_factor(df.values, i) for i in range(df.shape[1])]
    vif.set_index("feature", inplace=True)
    return vif
```

Visualize long table (of vif) in Jupyter notebook:

```python
with pd.option_context("display.max_rows", None, "display.max_columns", None):
    display(vif)
```

# Logistic Regression

Fit logistic regression:

```python
binary_results = sm.Logit(y_train , X_train).fit(method="bfgs", maxiter=1000)
# binary_results is a 
# statsmodels.discrete.discrete_model.BinaryResultsWrapper
# Replace X_train by X_train.assign(const=1) if needed
```

Check all available output which is dependent on the solver:

```python
binary_results.mle_retvals
```

Summarize the regression results:

```python
print(binary_results.summary())
```

```
                           Logit Regression Results                           
==============================================================================
Dep. Variable:               Survived   No. Observations:                  623
Model:                          Logit   Df Residuals:                      612
Method:                           MLE   Df Model:                           10
Date:                Sat, 04 Jan 2020   Pseudo R-squ.:                  0.3209
Time:                        15:19:25   Log-Likelihood:                -278.95
converged:                       True   LL-Null:                       -410.79
Covariance Type:            nonrobust   LLR p-value:                 7.167e-51
=================================================================================
                    coef    std err          z      P>|z|      [0.025      0.975]
---------------------------------------------------------------------------------
Age              -0.0324      0.009     -3.494      0.000      -0.051      -0.014
SibSp            -0.2659      0.118     -2.244      0.025      -0.498      -0.034
Parch            -0.1157      0.144     -0.802      0.422      -0.398       0.167
Fare              0.0038      0.003      1.246      0.213      -0.002       0.010
const             3.5595      0.549      6.486      0.000       2.484       4.635
Pclass_2         -0.3538      0.356     -0.993      0.321      -1.052       0.345
Pclass_3         -1.6995      0.357     -4.757      0.000      -2.400      -0.999
Sex_male         -2.6076      0.236    -11.040      0.000      -3.071      -2.145
Embarked_Q       -0.1484      0.451     -0.329      0.742      -1.032       0.735
Embarked_S       -0.6639      0.285     -2.326      0.020      -1.223      -0.105
Embarked_null     9.3897    407.838      0.023      0.982    -789.958     808.738
=================================================================================
```

```python
print(binary_results.summary2())
```

```
                         Results: Logit
=================================================================
Model:              Logit            Pseudo R-squared: 0.321     
Dependent Variable: Survived         AIC:              579.8962  
Date:               2020-01-04 15:19 BIC:              628.6762  
No. Observations:   623              Log-Likelihood:   -278.95   
Df Model:           10               LL-Null:          -410.79   
Df Residuals:       612              LLR p-value:      7.1671e-51
Converged:          1.0000           Scale:            1.0000    
-----------------------------------------------------------------
               Coef.  Std.Err.    z     P>|z|    [0.025   0.975] 
-----------------------------------------------------------------
Age           -0.0324   0.0093  -3.4939 0.0005   -0.0505  -0.0142
SibSp         -0.2659   0.1185  -2.2438 0.0248   -0.4981  -0.0336
Parch         -0.1157   0.1442  -0.8024 0.4223   -0.3982   0.1669
Fare           0.0038   0.0030   1.2464 0.2126   -0.0022   0.0097
const          3.5595   0.5488   6.4864 0.0000    2.4839   4.6351
Pclass_2      -0.3538   0.3564  -0.9926 0.3209   -1.0524   0.3448
Pclass_3      -1.6995   0.3573  -4.7565 0.0000   -2.3998  -0.9992
Sex_male      -2.6076   0.2362 -11.0399 0.0000   -3.0705  -2.1447
Embarked_Q    -0.1484   0.4509  -0.3292 0.7420   -1.0322   0.7353
Embarked_S    -0.6639   0.2854  -2.3261 0.0200   -1.2233  -0.1045
Embarked_null  9.3897 407.8381   0.0230 0.9816 -789.9584 808.7378
=================================================================
```


Test classification performance:

```python
from sklearn.metrics import accuracy_score, roc_auc_score

def test_perf(X_test, y_test, y_score):
    accuracy = accuracy_score(y_test, y_score > 0.5)
    roc_auc = roc_auc_score(y_test, y_score)
    print("Accuracy: {:.4}\nROC_AUC: {:.4}".format(accuracy, roc_auc))

test_perf(X_test, y_test, binary_results.predict(X_test))
```

# Regularized Logistic Regression

Fit regularized logistic regression:

```python
l1binary_results = sm.Logit(y_train, X_train)\
.fit_regularized(method="l1_cvxopt_cp", alpha=1e-2, maxiter=1000)
# l1binary_results is a 
# statsmodels.discrete.discrete_model.L1BinaryResultsWrapper
# Replace X_train by X_train.assign(const=1) if needed
```

Summarize the regression results:

```python
print(l1binary_results.summary())
print(l1binary_results.summary2())
```



















