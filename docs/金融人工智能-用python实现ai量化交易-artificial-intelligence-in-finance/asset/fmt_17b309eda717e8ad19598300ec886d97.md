
```
#
# 基于事件的回测
# --交易机器人回测（包含风险管理）
#
# (c) Dr. Yves J. Hilpisch
#
import numpy as np
import pandas as pd
import backtestingrm as btr


class TBBacktesterRM(btr.BacktestingBaseRM):
    def _reshape(self, state):
        ''' 用来对状态对象进行重塑的辅助函数
        '''
        return np.reshape(state, [1, self.env.lags, self.env.n_features])

    def backtest_strategy(self, sl=None, tsl=None, tp=None,
                          wait=5, guarantee=False):
        ''' 基于事件的交易机器人性能回测，包括止损、跟踪止损和止盈
        '''
        self.units = 0
        self.position = 0
        self.trades = 0
        self.sl = sl
        self.tsl = tsl
        self.tp = tp
        self.wait = 0
        self.current_balance = self.initial_amount
        self.net_wealths = list()
        for bar in range(self.env.lags, len(self.env.data)):
            self.wait = max(0, self.wait - 1)
            date, price = self.get_date_price(bar)
            if self.trades == 0:
                print(50 * '=')
                print(f'{date} | *** START BACKTEST ***')
                self.print_balance(bar)
                print(50 * '=')
        # 止损单
        if sl is not None and self.position != 0:
            rc = (price - self.entry_price) / self.entry_price
            if self.position == 1 and rc < -self.sl:
                print(50 * '-')
                if guarantee:
                    price = self.entry_price * (1 - self.sl)
                    print(f'*** STOP LOSS (LONG  | {-self.sl:.4f}) ***')
                else:
                    print(f'*** STOP LOSS (LONG  | {rc:.4f}) ***')
                self.place_sell_order(bar, units=self.units, gprice=price)
                self.wait = wait
                self.position = 0
            elif self.position == -1 and rc > self.sl:
                print(50 * '-')
                if guarantee:
                    price = self.entry_price * (1 + self.sl)
                    print(f'*** STOP LOSS (SHORT | -{self.sl:.4f}) ***')
                else:
                    print(f'*** STOP LOSS (SHORT | -{rc:.4f}) ***')
                self.place_buy_order(bar, units=-self.units, gprice=price)
                self.wait = wait
                self.position = 0

        # 跟踪止损单
        if tsl is not None and self.position != 0:
            self.max_price = max(self.max_price, price)
            self.min_price = min(self.min_price, price)
            rc_1 = (price - self.max_price) / self.entry_price
            rc_2 = (self.min_price - price) / self.entry_price
            if self.position == 1 and rc_1 < -self.tsl:
                print(50 * '-')
                print(f'*** TRAILING SL (LONG | {rc_1:.4f}) ***')
                self.place_sell_order(bar, units=self.units)
                self.wait = wait
                self.position = 0
            elif self.position == -1 and rc_2 < -self.tsl:
                print(50 * '-')
                print(f'*** TRAILING SL (SHORT | {rc_2:.4f}) ***')
                self.place_buy_order(bar, units=-self.units)
                self.wait = wait
                self.position = 0

        # 止盈单
        if tp is not None and self.position != 0:
            rc = (price - self.entry_price) / self.entry_price
            if self.position == 1 and rc > self.tp:
                print(50 * '-')
                if guarantee:
                    price = self.entry_price * (1 + self.tp)
                    print(f'*** TAKE PROFIT (LONG | {self.tp:.4f}) ***')
                else:
                    print(f'*** TAKE PROFIT (LONG | {rc:.4f}) ***')
                self.place_sell_order(bar, units=self.units, gprice=price)
                self.wait = wait
                self.position = 0
            elif self.position == -1 and rc < -self.tp:
                print(50 * '-')
                if guarantee:
                    price = self.entry_price * (1 - self.tp)
                    print(f'*** TAKE PROFIT (SHORT | {self.tp:.4f}) ***')
                else:
                    print(f'*** TAKE PROFIT (SHORT | {-rc:.4f}) ***')
                self.place_buy_order(bar, units=-self.units, gprice=price)
                self.wait = wait
                self.position = 0

        state = self.env.get_state(bar)
        action = np.argmax(self.model.predict(
            self._reshape(state.values))[0, 0])
        position = 1 if action == 1 else -1
        if self.position in [0, -1] and position == 1 and self.wait == 0:
            if self.verbose:
                print(50 * '-')
                print(f'{date} | *** GOING LONG ***')
            if self.position == -1:
                self.place_buy_order(bar - 1, units=-self.units)
            self.place_buy_order(bar - 1, amount=self.current_balance)
            if self.verbose:
                self.print_net_wealth(bar)
            self.position = 1
        elif self.position in [0, 1] and position == -1 and self.wait == 0:
            if self.verbose:
                print(50 * '-')
                print(f'{date} | *** GOING SHORT ***')
            if self.position == 1:
                self.place_sell_order(bar - 1, units=self.units)
            self.place_sell_order(bar - 1, amount=self.current_balance)
            if self.verbose:
                self.print_net_wealth(bar)
            self.position = -1
        self.net_wealths.append((date, self.calculate_net_wealth(price)))
    self.net_wealths = pd.DataFrame(self.net_wealths,
                                    columns=['date', 'net_wealth'])
    self.net_wealths.set_index('date', inplace=True)
    self.net_wealths.index = pd.DatetimeIndex(self.net_wealths.index)
    self.close_out(bar)
```
# 第 12 章　执行与部署

> 如果自动驾驶汽车想要在城市混合交通、暴雨和大雪、未铺的路面和地图上未标明的道路，以及无线接入不可靠的地方可靠地运行，那么还需要取得相当大的进展才行。

> 

> ——Todd Litman，2020 年

> 

> 从事算法交易的投资公司应拥有适合其经营业务的系统和风控，以确保其交易系统在适当的交易门槛和限制下仍具有弹性和足够的能力，并防止发出错误的指令，或者系统以错误的方式运行从而造成或助长无序市场。

> 

> ——MiFID II，第 17 条

第 11 章基于历史数据以 Q 学习金融智能体的形式训练了一款交易机器人。它引入了基于事件的回测，以作为一种足够灵活的方法来考虑典型的风控措施，比如跟踪止损单或获利目标。然而，所有这些都是在仅基于历史数据的沙箱环境中异步发生的。与自动驾驶汽车一样，现实世界中也存在部署人工智能的问题。对自动驾驶汽车来说，这意味着将人工智能与汽车硬件相结合，并在测试街道和公共街道上部署它。对交易机器人而言，这意味着将交易机器人与交易平台连接并对其进行部署，以便自动执行订单。换句话说，算法方面是很清楚的——现在需要添加执行和部署来实现算法交易。

本章介绍了算法交易的 Oanda 交易平台。因此，重点是平台的 `v20` API，而不是为用户提供手动交易接口的应用。为了简化代码，我们引入并使用了 `tpqoa` 包。它依赖于 Oanda 的 `v20` Python 包，并提供了更加 Python 化的用户界面。

12.1 节详细说明了使用 Oanda 演示账户的先决条件。12.2 节展示了如何从 API 检索历史数据和实时（流）数据。12.3 节涉及买卖订单的执行，可能包括其他订单，比如跟踪止损单。12.4 节根据 Oanda 的历史日内数据训练交易机器人，并以向量化方式对其性能进行回测。12.5 节展示了如何以实时和自动化的方式部署交易机器人。

## 12.1　Oanda 账户

本章中的代码依赖于 Python 包 `tpqoa`。此软件包可通过 `pip` 安装。

要使用此软件包，拥有 Oanda 的演示账户就足够了。打开账户后，会在账户页面生成一个**访问令牌**（登录后）。然后，访问令牌和**账户 id**（也可在账户页面找到）会被存储在配置文本文件中，如下所示。

```
[oanda]
account_id = XYZ-ABC-...
access_token = ZYXCAB...
account_type = practice
```

如果配置文件的名称是 `aiif.cfg`，并且被存储在当前工作目录中，那么 `tpqoa` 包可以以如下方式使用。

```
import tpqoa
api = tpqoa.tpqoa('aiif.cfg')
```

> ![](Images/image00324.jpeg)　**风险免责声明和披露**

> 

> Oanda 是一个**外汇**（FX）和**差价合约**（CFD）交易平台。这些工具涉及相当大的风险，特别是在利用杠杆交易时。强烈建议你在继续之前仔细阅读 Oanda 网站上的所有相关风险声明和披露（查看适当的管辖范围）。

> 

> 本章提供的所有代码及示例只作技术说明，并不构成任何投资建议或类似建议。

## 12.2　数据检索

像往常一样，先导入并配置一些 Python 包。

```
In [1]: import os
        import time
        import numpy as np
        import pandas as pd
        from pprint import pprint
        from pylab import plt, mpl
        plt.style.use('seaborn')
        mpl.rcParams['savefig.dpi'] = 300
        mpl.rcParams['font.family'] = 'serif'
        pd.set_option('mode.chained_assignment', None)
        pd.set_option('display.float_format', '{:.5f}'.format)
        np.set_printoptions(suppress=True, precision=4)
        os.environ['PYTHONHASHSEED'] = '0'
```

根据账户的相关管辖区，Oanda 提供了许多可交易的外汇和差价合约工具。以下 Python 代码会检索给定账户的可用工具。

```
In [2]: import tpqoa  ➊

In [3]: api = tpqoa.tpqoa('../aiif.cfg')  ➋

In [4]: ins = api.get_instruments()  ➌

In [5]: ins[:5]  ➍
Out[5]: [('AUD/CAD', 'AUD_CAD'),
         ('AUD/CHF', 'AUD_CHF'),
         ('AUD/CNY', 'AUD_CNY'),
         ('AUD/JPY', 'AUD_JPY'),
         ('AUD/NZD', 'AUD_NZD')]
```

➊ 导入 `tpqoa` 包。

➋ 实例化给定账户凭证的 API 对象。
