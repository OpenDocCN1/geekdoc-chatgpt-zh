```
marked_set=complete_set.merge(company_list,how='left', on=['CODE'])
marked_set=marked_set.fillna(0)
y=np.array(marked_set.TARGET)
X2=sm.add_constant(X)
est=sm.OLS(y, X2)
est2=est.fit()
print(est2.summary()) #输出统计结果检验概述,如图 6.35 所示
```

In [559]: print(est2.summary())
OLS Regression Results
Dep. Variable: y R-squared: 0.067
Model: OLS Adj. R-squared: 0.052
Method: Least Squares F-statistic: 4.644
Date: Mon, 09 Apr 2018 Prob (F-statistic): 7.23e-07
Time: 15:01:23 Log-Likelihood: 327.01
No. Observations: 726 AIC: -630.0
Df Residuals: 714 BIC: -575.0
Df Model: 11
Covariance Type: nonrobust
coef std err t P>|t| [95.0% Conf. Int.]
const 0.0888 0.025 3.553 0.000 0.040 0.138
x1 -0.0017 0.000 -3.712 0.000 -0.003 -0.001
x2 -0.0041 0.001 -5.839 0.000 -0.005 -0.003
x3 -5.697e-05 4.73e-05 -1.203 0.229 -0.000 3.6e-05
x4 5.513e-06 0.001 0.008 0.993 -0.001 0.001
x5 0.0001 0.000 0.311 0.756 -0.001 0.001
x6 -0.0004 0.000 -1.181 0.238 -0.001 0.000
x7 0.0022 0.007 0.330 0.741 -0.011 0.015
x8 -0.0052 0.007 -0.717 0.474 -0.020 0.009
x9 5.539e-07 1.43e-05 0.039 0.969 -2.76e-05 2.87e-05
x10 7.071e-13 2.36e-12 0.300 0.764 -3.92e-12 5.33e-12
x11 -3.486e-12 2.84e-12 -1.229 0.219 -9.06e-12 2.08e-12
Omnibus: 798.993 Durbin-Watson: 0.904
Prob(Omnibus): 0.000 Jarque-Bera (JB): 32473.690
Skew: 5.507 Prob(JB): 0.00
Kurtosis: 33.858 Cond. No. 1.54e+10

图 6.35 统计结果检验概述

```
print(list(est2.pvalues)) # 输出各指标的 P-Value,如图 6.36 所示
```

![](bbox=[0.28, 0.82, 0.6, 0.95])

图 6.36 输出各指标的 P-Value