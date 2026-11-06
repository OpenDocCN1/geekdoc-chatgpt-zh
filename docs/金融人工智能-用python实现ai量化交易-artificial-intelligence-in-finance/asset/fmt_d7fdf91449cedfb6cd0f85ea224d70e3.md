
# 基于事件的回测

```python
#
# 基于事件的回测
# --基类 (2)
#
# (c) Dr. Yves J. Hilpisch
#
from backtesting import *
class BacktestingBaseRM(BacktestingBase):

    def set_prices(self, price):
        ''' 设置用来进行性能回测的价格，比如测试跟踪止损是否命中
        '''
        self.entry_price = price  ➊
        self.min_price = price  ➋
        self.max_price = price  ➌

    def place_buy_order(self, bar, amount=None, units=None, gprice=None):
        ''' 对于给定的bar和给定的amount或给定的units数量下一个买入单
        '''
        date, price = self.get_date_price(bar)
        if gprice is not None:
            price = gprice
        if units is None:
            units = int(amount / price)
        self.current_balance -= (1 + self.ptc) * units * price + self.ftc
        self.units += units
        self.trades += 1
        self.set_prices(price)  ➍
        if self.verbose:
            print(f'{date} | buy {units} units for {price:.4f}')
            self.print_balance(bar)

    def place_sell_order(self, bar, amount=None, units=None, gprice=None):
        ''' 对于给定的bar和给定的amount或给定的units数量下一个卖出单
        '''
        date, price = self.get_date_price(bar)
        if gprice is not None:
            price = gprice
        if units is None:
            units = int(amount / price)
        self.current_balance += (1 - self.ptc) * units * price - self.ftc
        self.units -= units
        self.trades += 1
        self.set_prices(price)  ➍
        if self.verbose:
            print(f'{date} | sell {units} units for {price:.4f}')
            self.print_balance(bar)
```

➊ 设置最近交易的**进入**价格。
➋ 设置自最近交易以来的初始**最低**价格。
➌ 设置自最近交易以来的初始**最高**价格。
❹ 设置交易执行后的相关价格。

基于这个新的基类，11.7.4 节提供了一个新的回测类 `TBBacktesterRM`，它允许包含止损单、跟踪止损单和止盈单。相关代码部分将在接下来的内容中进行讨论。如上一节所计算的，回测示例的参数将定位在大约 2% 的 ATR 水平上。

> ![](Images/image00323.jpeg)　**EUT 和风控措施**
>
> EUT、MVP 和 CAPM（参见第 3 章和第 4 章）假设金融主体知道金融工具收益率的未来分布。MPT 和 CAPM 进一步假设收益率是呈正态分布的，并且在市场投资组合的收益率和交易的金融工具的收益率之间存在线性关系。止损单、跟踪止损单和止盈单的使用除了会导致杠杆作用外，还会导致杠杆作用与保证金止损相结合形成“有保证的非正态”分布，以及交易头寸相对于交易工具的高度不对称、非线性回报。

### 11.5.1　止损

第一个风控措施是止损单。它固定了一个特定的价格水平，或者更常见的是，固定了能够触发平仓的百分比值。如果一个非杠杆头寸的进入价格为 100，止损水平被设置为 5%，则多头头寸会在 95 处平仓，而空头头寸会在 105 处平仓。

下面的 Python 代码是处理止损单的 `TBBacktesterRM` 类的相关部分。对止损单来说，该类允许用户指定订单的价格水平是否有保证。2 使用有保证的止损价格水平可能会导致过于乐观的性能结果。

2有担保的止损单可能只适用于某些司法管辖区的某些经纪客户群体，比如散户投资者 / 交易员。

```python
# 止损单
if sl is not None and self.position != 0:  ➊
    rc = (price - self.entry_price) / self.entry_price  ➋
    if self.position == 1 and rc < -self.sl:  ➌
        print(50 * '-')
        if guarantee:
            price = self.entry_price * (1 - self.sl)
            print(f'*** STOP LOSS (LONG  | {-self.sl:.4f}) ***')
        else:
            print(f'*** STOP LOSS (LONG  | {rc:.4f}) ***')
        self.place_sell_order(bar, units=self.units, gprice=price)  ➍
        self.wait = wait  ➎
        self.position = 0  ➏
    elif self.position == -1 and rc > self.sl:  ➐
        print(50 * '-')
        if guarantee:
            price = self.entry_price * (1 + self.sl)
            print(f'*** STOP LOSS (SHORT | -{self.sl:.4f}) ***')
        else:
            print(f'*** STOP LOSS (SHORT | -{rc:.4f}) ***')
        self.place_buy_order(bar, units=-self.units, gprice=price)  ➑
        self.wait = wait  ➎
        self.position = 0  ➏
```

➊ 检查是否定义了止损，头寸是否为中性。
➋ 根据最后一笔交易的进入价格计算收益。
➌ 检查是否给定了一个**多头**头寸的止损事件。
❹ 以当前价格或保证价格平仓**多头**头寸。
➎ 设置在下一个交易发生之前等待的条数。
➏ 设置头寸为中性。
➐ 检查是否为**空头**头寸给出了一个止损事件。
➑ 以当前价格或保证价格平仓**空头**头寸。

下面的 Python 代码分别对没有止损单和有止损单的交易机器人的交易策略进行了回测。对于给定的参数，止损单对策略性能有负面影响。

```python
In [69]: import tbbacktesterrm as tbbrm

In [70]: env = test_env

In [71]: tb = tbbrm.TBBacktesterRM(env, agent.model, 10000,
                                   0.0, 0, verbose=False)  ➊

In [72]: tb.backtest_strategy(sl=None, tsl=None, tp=None, wait=5)  ➋
         ==================================================
         2018-01-17 | *** START BACKTEST ***
         2018-01-17 | current balance = 10000.00
         ==================================================
         ==================================================
         2019-12-31 | *** CLOSING OUT ***
         2019-12-31 | current balance = 10936.79
         2019-12-31 | net performance [%] = 9.3679
         2019-12-31 | number of trades [#] = 186
         ==================================================

In [73]: tb.backtest_strategy(sl=0.0175, tsl=None, tp=None,
                              wait=5, guarantee=False)  ➌
         ==================================================
         2018-01-17 | *** START BACKTEST ***
         2018-01-17 | current balance = 10000.00
         ==================================================
         --------------------------------------------------
         *** STOP LOSS (SHORT | -0.0203) ***
         ==================================================
         2019-12-31 | *** CLOSING OUT ***
         2019-12-31 | current balance = 10717.32
         2019-12-31 | net performance [%] = 7.1732
         2019-12-31 | number of trades [#] = 188
         ==================================================

In [74]: tb.backtest_strategy(sl=0.017, tsl=None, tp=None,
                              wait=5, guarantee=True)  ➍
         ==================================================
         2018-01-17 | *** START BACKTEST ***
         2018-01-17 | current balance = 10000.00
         ==================================================
         --------------------------------------------------
         *** STOP LOSS (SHORT | -0.0170) ***
         ==================================================
         2019-12-31 | *** CLOSING OUT ***
         2019-12-31 | current balance = 10753.52
         2019-12-31 | net performance [%] = 7.5352
         2019-12-31 | number of trades [#] = 188
         ==================================================
```

➊ 实例化风险管理的回测类。
➋ 在没有任何风控措施的情况下，对交易机器人的性能进行回测。
➌ 使用止损单（无保证金）对交易机器人的性能进行回测。
❹ 使用止损单（有保证金）对交易机器人的性能进行回测。

### 11.5.2　跟踪止损

与常规的止损单不同，只要在基本指令下达后观察到新的高点，跟踪止损单就会进行调整。假设无杠杆多头头寸的基础订单的进入价格为 95，跟踪止损被设置为 5%。如果工具价格达到 100 并回落到 95，则意味着这是一个跟踪止损事件，头寸会在进入价格水平结束。如果价格达到 110，并回落到 104.5，则意味着这是另一个跟踪止损事件。

下面的 Python 代码是处理跟踪止损单的 `TBBacktesterRM` 类的相关部分。要正确处理这样的风控措施，需要跟踪最高价格和最低价格。最高价格适用于多头头寸，最低价格则适用于空头头寸。

```python
# 跟踪止损单
if tsl is not None and self.position != 0:
    self.max_price = max(self.max_price, price)  ➊
    self.min_price = min(self.min_price, price)  ➋
    rc_1 = (price - self.max_price) / self.entry_price  ➌
    rc_2 = (self.min_price - price) / self.entry_price  ➍
    if self.position == 1 and rc_1 < -self.tsl:  ➎
        print(50 * '-')
        print(f'*** TRAILING SL (LONG | {rc_1:.4f}) ***')
        self.place_sell_order(bar, units=self.units)
        self.wait = wait
        self.position = 0
    elif self.position == -1 and rc_2 < -self.tsl:  ➏
        print(50 * '-')
        print(f'*** TRAILING SL (SHORT | {rc_2:.4f}) ***')
        self.place_buy_order(bar, units=-self.units)
        self.wait = wait
        self.position = 0
```

➊ 如有必要，就更新**最高**价格。
➋ 如有必要，就更新**最低**价格。
➌ 计算**多头**头寸的相关收益。
❹ 计算**空头**头寸的相关收益。
➎ 检查是否为**多头**头寸给出了一个跟踪止损事件。
➏ 检查是否为**空头**头寸给出了一个跟踪止损事件。

正如下面的回测结果所示，与没有跟踪止损单的策略相比，在给定参数的情况下使用跟踪止损单会降低总体性能。
