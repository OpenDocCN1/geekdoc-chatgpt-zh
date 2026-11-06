
# 数据预处理与特征工程

```
In [17]: rets = np.log(data / data.shift(1))  ➊

In [18]: rets.dropna(inplace=True)

In [19]: dfs = {}
         for sym in data:
             df, cols = add_lags(rets, sym, lags)  ➋
             mu, std = df[cols].mean(), df[cols].std()  ➌
             df[cols] = (df[cols] - mu) / std  ➌
             dfs[sym] = df

In [20]: dfs[sym].head()  ➍
Out[20]:                GLD   lag_1   lag_2   lag_3   lag_4   lag_5   lag_6   lag_7
         Date
         2010-01-14  0.0044  0.9570 -2.1692  1.3386  0.4959 -0.6434  1.6613 -0.1028
         2010-01-15 -0.0105  0.4379  0.9571 -2.1689  1.3388  0.4966 -0.6436  1.6614
         2010-01-19  0.0059 -1.0842  0.4385  0.9562 -2.1690  1.3395  0.4958 -0.6435
         2010-01-20 -0.0234  0.5967 -1.0823  0.4378  0.9564 -2.1686  1.3383  0.4958
         2010-01-21 -0.0145 -2.4045  0.5971 -1.0825  0.4379  0.9571 -2.1680  1.3384

In [21]: adfuller(dfs[sym]['lag_1'])  ➎
Out[21]: (-51.568251505825536,
          0.0,
          0,
          2507,
          {'1%': -3.4329610922579095,
           '5%': -2.8626935681060375,
           '10%': -2.567384088736619},
          7017.165474260225)

In [22]: dfs[sym].corr()  ➏
Out[22]:           GLD   lag_1   lag_2       lag_3   lag_4       lag_5   lag_6   lag_7
         GLD    1.0000 -0.0297  0.0003  1.2635e-02 -0.0026 -5.9392e-03  0.0099 -0.0013
         lag_1 -0.0297  1.0000 -0.0305  8.1418e-04  0.0128 -2.8765e-03 -0.0053  0.0098
         lag_2  0.0003 -0.0305  1.0000 -3.1617e-02  0.0003  1.3234e-02 -0.0043 -0.0052
         lag_3  0.0126  0.0008 -0.0316  1.0000e+00 -0.0313 -6.8542e-06  0.0141 -0.0044
         lag_4 -0.0026  0.0128  0.0003 -3.1329e-02  1.0000 -3.1761e-02  0.0002  0.0141
         lag_5 -0.0059 -0.0029  0.0132 -6.8542e-06 -0.0318  1.0000e+00 -0.0323  0.0002
         lag_6  0.0099 -0.0053 -0.0043  1.4115e-02  0.0002 -3.2289e-02  1.0000 -0.0324
         lag_7 -0.0013  0.0098 -0.0052 -4.3869e-03  0.0141  2.1707e-04 -0.0324  1.0000
```

➊ 计算价格数据的对数收益率。
➋ 滞后对数收益率数据。
➌ 对特征数据进行**高斯归一化**（Gaussian normalization）。4
4该方法也被称为 **z-score 归一化**。
❹ 展示一个滞后对数收益率实例。
➎ 测试时间序列数据的稳定性。
➏ 展示数据特征的相关性。

# 样本内模型评估

## OLS 回归

首先，实现 OLS 回归并获得回归的预测结果。此分析是在完整的数据集上进行的，它将显示算法在样本内的表现。除了一个特例，通过 OLS 回归的方法预测明日市场走向的准确率均能略高于 50%。

```
In [23]: from sklearn.metrics import accuracy_score

In [24]: %%time
         for sym in data:
             df = dfs[sym]
             reg = np.linalg.lstsq(df[cols], df[sym], rcond=-1)[0]  ➊
             pred = np.dot(df[cols], reg)  ➋
             acc = accuracy_score(np.sign(df[sym]), np.sign(pred))  ➌
             print(f'OLS | {sym:10s} | acc={acc:.4f}')
         OLS | AAPL.O     | acc=0.5056
         OLS | MSFT.O     | acc=0.5088
         OLS | INTC.O     | acc=0.5040
         OLS | AMZN.O     | acc=0.5048
         OLS | GS.N       | acc=0.5080
         OLS | SPY        | acc=0.5080
         OLS | .SPX       | acc=0.5167
         OLS | .VIX       | acc=0.5291
         OLS | EUR=       | acc=0.4984
         OLS | XAU=       | acc=0.5207
         OLS | GDX        | acc=0.5307
         OLS | GLD        | acc=0.5072
         CPU times: user 201 ms, sys: 65.8 ms, total: 267 ms
         Wall time: 60.8 ms
```

➊ 模型回归。
➋ 模型预测。
➌ 准确率分析。

## MLPRegressor 神经网络

然后，使用基于 `scikit-learn` 包构建的神经网络模型进行学习和预测，并对模型结果进行同样的分析。在测试样本内，预测结果准确率明显超过了 50%，并且有一些结果超过了 60%。

```
In [25]: from sklearn.neural_network import MLPRegressor

In [26]: %%time
         for sym in data.columns:
             df = dfs[sym]
             model = MLPRegressor(hidden_layer_sizes=[512],
                                  random_state=100,
                                  max_iter=1000,
                                  early_stopping=True,
                                  validation_fraction=0.15,
                                  shuffle=False)  ➊
             model.fit(df[cols], df[sym])  ➋
             pred = model.predict(df[cols])  ➌
             acc = accuracy_score(np.sign(df[sym]), np.sign(pred))  ➍
             print(f'MLP | {sym:10s} | acc={acc:.4f}')
         MLP | AAPL.O     | acc=0.6005
         MLP | MSFT.O     | acc=0.5853
         MLP | INTC.O     | acc=0.5766
         MLP | AMZN.O     | acc=0.5510
         MLP | GS.N       | acc=0.6527
         MLP | SPY        | acc=0.5419
         MLP | .SPX       | acc=0.5399
         MLP | .VIX       | acc=0.6579
         MLP | EUR=       | acc=0.5642
         MLP | XAU=       | acc=0.5522
         MLP | GDX        | acc=0.6029
         MLP | GLD        | acc=0.5259
         CPU times: user 1min 37s, sys: 6.74 s, total: 1min 44s
         Wall time: 14 s
```

➊ 模型实例化。
➋ 模型拟合。
➌ 模型预测。
❹ 准确率计算。

## Keras DNN

最后，使用基于 `keras` 包构建的神经网络再次进行同样的分析。此方法的准确率与 `MLPRegressor` 近似，但是平均准确率更高。

```
In [27]: import tensorflow as tf
         from keras.layers import Dense
         from keras.models import Sequential
         Using TensorFlow backend.

In [28]: np.random.seed(100)
         tf.random.set_seed(100)

In [29]: def create_model(problem='regression'):  ➊
             model = Sequential()
             model.add(Dense(512, input_dim=len(cols),
                             activation='relu'))
             if problem == 'regression':
                 model.add(Dense(1, activation='linear'))
                 model.compile(loss='mse', optimizer='adam')
             else:
                 model.add(Dense(1, activation='sigmoid'))
                 model.compile(loss='binary_crossentropy', optimizer='adam')
             return model

In [30]: %%time
         for sym in data.columns[:]:
             df = dfs[sym]
             model = create_model()  ➋
             model.fit(df[cols], df[sym], epochs=25, verbose=False)  ➌
             pred = model.predict(df[cols])  ➍
             acc = accuracy_score(np.sign(df[sym]), np.sign(pred))  ➎
             print(f'DNN | {sym:10s} | acc={acc:.4f}')
         DNN | AAPL.O     | acc=0.6292
         DNN | MSFT.O     | acc=0.5981
         DNN | INTC.O     | acc=0.6073
         DNN | AMZN.O     | acc=0.5781
         DNN | GS.N       | acc=0.6196
         DNN | SPY        | acc=0.5829
         DNN | .SPX       | acc=0.6077
         DNN | .VIX       | acc=0.6392
         DNN | EUR=       | acc=0.5845
         DNN | XAU=       | acc=0.5881
         DNN | GDX        | acc=0.5829
         DNN | GLD        | acc=0.5666
         CPU times: user 34.3 s, sys: 5.34 s, total: 39.6 s
         Wall time: 23.1 s
```

➊ 模型创建函数。
➋ 模型实例化。
➌ 模型拟合。
❹ 模型预测。
➎ 准确率分析。

这个简单的例子表明，在**样本内**，神经网络在预测明日价格走势上可以显著优于 OSL 回归。那么测试两类模型在数据**样本外**的表现，又会有怎样的变化呢？

# 样本外模型评估

为此，继续重复上述分析过程，只不过使用全部数据的 80% 进行模型训练，剩下的 20%进行效果测试。首先，分析 OSL 回归模型。OSL 回归模型在样本外的结果与样本内准确率近似，都在 50% 左右。

```
In [31]: split = int(len(dfs[sym]) * 0.8)

In [32]: %%time
         for sym in data.columns:
             df = dfs[sym]
             train = df.iloc[:split]  ➊
             reg = np.linalg.lstsq(train[cols], train[sym], rcond=-1)[0]
             test = df.iloc[split:]  ➋
             pred = np.dot(test[cols], reg)
             acc = accuracy_score(np.sign(test[sym]), np.sign(pred))
             print(f'OLS | {sym:10s} | acc={acc:.4f}')
         OLS | AAPL.O     | acc=0.5219
         OLS | MSFT.O     | acc=0.4960
         OLS | INTC.O     | acc=0.5418
         OLS | AMZN.O     | acc=0.4841
         OLS | GS.N       | acc=0.4980
         OLS | SPY        | acc=0.5020
         OLS | .SPX       | acc=0.5120
         OLS | .VIX       | acc=0.5458
         OLS | EUR=       | acc=0.4482
         OLS | XAU=       | acc=0.5299
         OLS | GDX        | acc=0.5159
         OLS | GLD        | acc=0.5100
         CPU times: user 200 ms, sys: 60.6 ms, total: 261 ms
         Wall time: 61.7 ms
```

➊ 创建训练数据子集。
➋ 创建测试数据子集。

`MLPRegressor` 在样本外的表现相比于样本内要差得多，其结果接近于 OSL 回归。
