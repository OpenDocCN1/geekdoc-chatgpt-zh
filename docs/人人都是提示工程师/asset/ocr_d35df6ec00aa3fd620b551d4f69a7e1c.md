```
data_df['cum_return'] = (1 + data_df['strategy_return']).cumprod()
# 输出结果
print(data_df.tail())
```

至此，我们完成了整个量化策略的编写工作。接下来，我们可以对这个策略进行回测，以保证策略可以获得比较好的收益。

在 ChatGPT 中输入如下内容。

好的，对于上述的策略进行回测。假设我们要回测的股票是中国平安（601318.SH），回测开始时间是 2021 年 1 月 1 日，结束时间是 2021 年 12 月 31 日。使用的回测框架是 Backtrader，并且已经安装完毕。请写出回测代码

ChatGPT 输出如下内容。

好的，请确认 Baostock 已经安装，并且账户已经授权。

!pip install baostock

然后，我们可以使用以下代码进行回测，注意修改股票代码和回测起止时间。

```
import backtrader as bt
import baostock as bs
import pandas as pd

# 登录 Baostock
lg = bs.login()
print('login respond error_code:' + lg.error_code)
print('login respond error_msg:' + lg.error_msg)
```