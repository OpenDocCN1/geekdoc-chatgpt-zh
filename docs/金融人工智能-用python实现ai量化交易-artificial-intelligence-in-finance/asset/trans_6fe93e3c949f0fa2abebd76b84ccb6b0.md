
# 基于事件的回测

```
#
# 基于事件的回测
# --基类 (1)
#
# (c) Dr. Yves J. Hilpisch
# Artificial Intelligence in Finance
#


class BacktestingBase:
    def __init__(self, env, model, amount, ptc, ftc, verbose=False):
        self.env = env  ➊
        self.model = model  ➋
        self.initial_amount = amount  ➌
        self.current_balance = amount  ➌
        self.ptc = ptc  ➍
        self.ftc = ftc  ➎
        self.verbose = verbose  ➏
        self.units = 0  ➐
        self.trades = 0  ➑

    def get_date_price(self, bar):
        ''' 返回给定的bar所对应的日期和价格
        '''
        date = str(self.env.data.index[bar])[:10]  ➒
        price = self.env.data[self.env.symbol].iloc[bar]  ➓
        return date, price

    def print_balance(self, bar):
        ''' 打印给定的bar所对应的现金余额
        '''
        date, price = self.get_date_price(bar)
        print(f'{date} | current balance = {self.current_balance:.2f}')  ⓫

    def calculate_net_wealth(self, price):
        return self.current_balance + self.units * price  ⓬

    def print_net_wealth(self, bar):
        ''' 打印给定的bar所对应的净资产（现金+头寸）
        '''
        date, price = self.get_date_price(bar)
        net_wealth = self.calculate_net_wealth(price)
        print(f'{date} | net wealth = {net_wealth:.2f}')  ⓭

    def place_buy_order(self, bar, amount=None, units=None):
        ''' 对于给定的bar和给定的amount或给定的units数量下一个买入单
        '''
        date, price = self.get_date_price(bar)
        if units is None:
            units = int(amount / price)  ⓮
            # units = amount / price  ⓮
        self.current_balance -= (1 + self.ptc) * \
            units * price + self.ftc  ⓯
        self.units += units  ⓰
        self.trades += 1  ⓱
        if self.verbose:
            print(f'{date} | buy {units} units for {price:.4f}')
            self.print_balance(bar)

    def place_sell_order(self, bar, amount=None, units=None):
        ''' 对于给定的bar和给定的amount或给定的units数量下一个卖出单
        '''
        date, price = self.get_date_price(bar)
        if units is None:
            units = int(amount / price)  ⓮
            # units = amount / price  ⓮
        self.current_balance += (1 - self.ptc) * \
            units * price - self.ftc  ⓯
        self.units -= units  ⓰
        self.trades += 1  ⓱
        if self.verbose:
            print(f'{date} | sell {units} units for {price:.4f}')
            self.print_balance(bar)

    def close_out(self, bar):
        ''' 在给定的bar上关闭所有未平仓头寸
        '''
        date, price = self.get_date_price(bar)
        print(50 * '=')
        print(f'{date} | *** CLOSING OUT ***')
        if self.units < 0:
            self.place_buy_order(bar, units=-self.units)  ⓲
        else:
            self.place_sell_order(bar, units=self.units)  ⓳
        if not self.verbose:
            print(f'{date} | current balance = {self.current_balance:.2f}')
        perf = (self.current_balance / self.initial_amount - 1) * 100  ⓴
        print(f'{date} | net performance [%] = {perf:.4f}')
        print(f'{date} | number of trades [#] = {self.trades}')
        print(50 * '=')
```

➊ 相关 `Finance` 环境。
➋ 相关的 DNN 模型（来自交易机器人）。
➌ 初始 / 当前余额。
❹ 成比例交易成本。
➎ 固定交易成本。
➏ 打印是否冗长。
➐ 交易金融工具的初始单位数。
➑ 执行的初始交易数量。
➒ 给定 `bar` 下的相关**日期**。
➓ 给定 `bar` 下的**金融工具相关价格**。
⓫ 给定 `bar` 下的**日期**和**当前余额**的输出。
⓬ 根据当前余额和工具头寸计算**净资产**。
⓭ 给定 `bar` 下的**日期**和**净资产**输出。
⓮ 在给定交易金额的情况下进行交易的单位数。
⓯ 交易和相关成本对当前余额的影响。
⓰ 调整持有单位数量。
⓱ 调整交易数量。
⓲ 了解**空头**头寸……
⓳ ……或是**多头**头寸。
⓴ 给定初始金额和最终流动余额的净收益。

### 11.7.4 回测类

以下是 Python 模块，其中 `TBBacktesterRM` 类用于基于事件的回测，该回测包括风控措施（止损单、跟踪止损单、止盈单）。
