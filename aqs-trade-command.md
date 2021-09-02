# AQS Trade 使用指南

## 安装 AQS Trade

> 准备：`/shared` 目录下存放两个文件
> * quick-trader-x.y.z.tar.gz
> * executive_conf.json

1. 解压 `quick-trader-x.y.z.tar.gz`:
   ```bash
    tar -xzvf /shared/quick-trader-x.y.z.tar.gz
   ```
2. 安装 `AQS Trade`:
   ```bash
   cd /shared/quick-trader-x.y.z
   sudo python setup.py install
   ```

## 启动 AQS Trade

```bash
task_launch_trader.sh SJWX1
```

## 交易

> 条件单（包括时间条件、价格条件）只能撤单，不能追单。

```python
# 启动 trade commander
from aqs_trade.trade_commander import TradeCommander
tc=TradeCommander()

# 查看帮助文档，按 q 退出
help(TradeCommander)

# 限价单
tc.limit(contract='SA109', price=2269, volume=-2, isopen=0, time=10, brokers=['hjl'])
# 指定 time 之后未成交就转市价单
tc.limit(contract='i2201', price=1040, volume=4, isopen=1, time=10, brokers=['msb'])
# 使用配置文件的默认参数
tc.limit(contract='fu2106', price=2463, volume=2)
tc.limit('hc2201', 5811, -10, brokers=['sym'])

# 市价单
tc.market(contract='jm2109', volume=-3, isopen=0, brokers=['msb', 'sym', 'hjl'], strategy='apple')
# 使用配置文件的默认参数
tc.market(contract='jm2109', volume=3)
tc.market('i2201', -3)

# 对手价单
tc.best(contract='CF109', volume=10, isopen=1, brokers=['msb', 'sym', 'hjl'], strategy='apple')
# 指定 time 之后未成交就转市价单
tc.best(contract='rb2110', volume=-3, isopen=0, time=60, brokers=['msb', 'sym', 'hjl'])
# 使用配置文件的默认参数
tc.best(contract='rb2110', volume=-3)
tc.best('rb2110', 2)

# 按市值下单
# 限价单指定 time 之后未成交就转市价单，time为0表示不转市价
# value 的单位是万，正/负分别表示买/卖
# 市价单
tc.marketmv(contract='i2109', mv=20, isopen=0, brokers=['msb', 'sym', 'hjl'], strategy='apple')
tc.marketmv(contract='i2109', mv=20)
tc.marketmv('i2109', 20)
# 限价单
tc.limitmv(contract='i2105', price=1220, mv=-10, isopen=1, time=0, brokers=['msb', 'sym', 'hjl'], strategy='apple')
tc.limitmv(contract='i2105', price=1220, mv=-10)
tc.limitmv('i2109', 1220, -10)
# 对手价单
tc.bestmv(contract='i2201', mv=10, isopen=1, time=5, brokers=['msb', 'sym', 'hjl'], strategy='apple')
tc.bestmv(contract='i2201', mv=10)
tc.bestmv('i2201', 10)

# 抢单
tc.compete_order(contract='i2109', price=890, volume=5, time_cancel_remain=3, isopen=1, brokers=['sym'], strategy='apple')
tc.compete_order(contract='fu2109', price=2347, volume=2, time_cancel_remain=60, stoploss_base_ratio=0.01, stoploss_mid_time=60, stoploss_mid_ratio=0.05, stoploss_end_time=120, isopen=1, brokers=['msb', 'sym', 'hjl'])
# 使用配置文件的默认参数
tc.compete_order(contract='i2109', price=1043, volume=5, time_cancel_remain=3)
tc.compete_order(contract='fu2109', price=2600, volume=2, time_cancel_remain=60, stoploss_base_ratio=0.01, stoploss_mid_time=60, stoploss_mid_ratio=0.05, stoploss_end_time=120)

# 条件单
# 按市值
tc.mvc(contract='i2109', direction='lt', price=893, mv=10, isopen=1, time=10, brokers=['msb', 'sym', 'hjl'], strategy='apple')
# 使用配置文件的默认参数
tc.mvc(contract='i2201', direction='gt', price=1020, mv=15, brokers=['hjl'], strategy='apple')
# 按手数
tc.volc(contract='i2201', direction='lt', price=1010, volume=3, isopen=1, time=60, brokers=['msb'], strategy='apple')

# 开仓+止盈/止损
tc.complete(contract='cu2108', volume=2, stoploss_ratio=0.001, stoptarget_ratio=0.001, time=10, brokers=['msb'], strategy='pear')

# 套利单
# price为两组套利合约的价差
# slippage为价差滑点
# pu为价格系数，vu为手数系数，mu为市值系数
# isopen必须写
# 1. 按手数下单，判断价差
# volume为基础手数。各合约的实际手数为volume乘vu中的系数
tc.darb(contract='i2109,i2201', direction='lt', price=120, slippage=10, volume=2, isopen=1, pu=[1, 1], vu=[1, 1], brokers=['sym'])
tc.darb(contract='sn2109,sn2201', direction='lt', price=8000, slippage=100, volume=-5, isopen=0, pu=[1, 1], vu=[1, 1], brokers=['sym'])
# vu和pu为可选参数，分别是合约的手数系数和价格系数。如不填，使用配置文件中arb的默认参数
tc.darb(contract='i2109,i2201', direction='lt', price=50, slippage=10, volume=-2, isopen=0)
# len_sep_group可选参数，默认contract中第一个合约为第一组，其他合约为第二组
tc.darb(contract='b2109,m2109,y2109', direction='lt', price=0, slippage=10, volume=1, isopen=1, pu=[1, 1], vu=[1, 2], len_sep_group = 1, brokers=['sym'], strategy='trader')
# 2. 按市值下单，判断价差
tc.darbmv(contract='i2109,i2201', direction='gt', price=150, slippage=10, mv=10, isopen=1, pu=[1, 1], mu=[1, 1], brokers=['sym'])
tc.darbmv(contract='i2109,i2201', direction='gt', price=50, slippage=10, mv=-50, isopen=0, brokers=['sym'])
tc.darbmv(contract='b2109,m2109,y2109', direction='gt', price=0, slippage=10, mv=10, isopen=1, pu=[1, 1], mu=[1, 1.2, 1.3], len_sep_group = 1, brokers=['sym'], strategy='trader')
# 3. 按手数下单，判断价比
tc.rarb(contract='i2109,i2201', direction='lt', ratio=1.5, slippage=0.1, volume=2, isopen=1, pu=[1, 1], vu=[1, 1], brokers=['sym'])
tc.rarb(contract='sn2109,sn2201', direction='lt', ratio=1.5, slippage=0.1, volume=-5, isopen=0, pu=[1, 1], vu=[1, 1], brokers=['sym'])
# vu和pu为可选参数，分别是合约的手数系数和价格系数。如不填，使用配置文件中arb的默认参数
tc.rarb(contract='i2109,i2201', direction='lt', ratio=1.5, slippage=0.1, volume=2, isopen=1)
# len_sep_group可选参数，默认contract中第一个合约为第一组，其他合约为第二组
tc.rarb(contract='b2109,m2109,y2109', direction='lt', ratio=1.5, slippage=0.1, volume=1, isopen=1, pu=[1, 1], vu=[1, 2], len_sep_group = 1, brokers=['sym'], strategy='trader')
# 4. 按市值下单，判断价比
tc.rarbmv(contract='i2109,i2201', direction='gt', ratio=1.5, slippage=0.1, mv=10, isopen=1, pu=[1, 1], mu=[1, 1], brokers=['sym'])
tc.rarbmv(contract='i2109,i2201', direction='gt', ratio=1.5, slippage=0.1, mv=-50, isopen=0, brokers=['sym'])
tc.rarbmv(contract='b2109,m2109,y2109', direction='gt', ratio=1.5, slippage=0.1, mv=10, isopen=1, pu=[1, 1], mu=[1, 1.2, 1.3], len_sep_group = 1, brokers=['sym'], strategy='trader')

# 查询所有交易
# 查询订单，如果done为wait，则还未发出委托
tc.query_order()
tc.query_order(brokers=['msb', 'sym', 'hjl'], strategies=['apple', 'pear'])
tc.query_order(csv_file='/shared/order.csv')

# 查询仓位
tc.query_position()
tc.query_position()
tc.query_position(brokers=['hjl', 'msb', 'sym'])
# 保存到文件
tc.query_position(brokers=['SJWX1', 'GXWX1', 'ZSWX1'], csv_file='/shared/wx1.csv')

# 询价
tc.query_price(contract='cu2109')

# 查询策略仓位
tc.query_strategy_position(strategies=['trader'], brokers=['hjl'])

# 保存订单的csv文件
tc.save_order(brokers=['hjl'])

# 查询策略pnl
tc.query_strategy_pnl(strategies=['trader'], brokers=['hjl'])

# 撤单，按指定账号、ID
tc.cancel(ids=[4, 5], brokers=['msb', 'sym', 'hjl'], strategies=['apple', 'pear'])
# 可以省略id/strategies，默认值为“全部”
tc.cancel(brokers=['msb', 'sym', 'hjl'])
# 撤单后追单
# 指定market参数为1，转市价
tc.cancel(ids=[4], market=1, brokers=['msb', 'sym', 'hjl'])
# 指定market参数为2，转对手价
tc.cancel(ids=[4], market=2, brokers=['msb', 'sym', 'hjl'])

# 平仓，按指定比例、账号和合约
tc.flatten(ratio=1, contracts=['i2105', 'j2105', 'fu2105'], strategies=['apple', 'pear'], brokers=['msb', 'sym', 'hjl'], end=False)
# contracts = 'all' 时，平仓所有合约
tc.flatten(ratio=0.5, contracts='all', brokers=['msb', 'sym', 'hjl'])
# 使用配置文件的默认参数
tc.flatten(0.1, 'all', end=True)
tc.flatten(ratio=0.2, contracts=['fu2109', 'sn2109'])

# 对手价平仓
tc.bestft(ratio=0.1, contracts=['sn2109'], time=30, strategies=['apple', 'pear'], brokers=['msb', 'sym', 'hjl'])
# 使用配置文件的默认参数
tc.bestft(1, ['sn2109', 'sn2201'], brokers=['sym'], time=10)

# 重载配置文件中除了 frontend_address, backend_address, limit_max 以外的配置
tc.reload_config()

# 设置交易单位
# 当持续提示“CJ, bc, ss, lh have no volume multiplier, query instrument again”时，需要使用此功能手动设置交易单位
tc.set_volume_multiplier(symbol='CJ', value=5)
tc.set_volume_multiplier(symbol='bc', value=5)
tc.set_volume_multiplier(symbol='ss', value=5)
tc.set_volume_multiplier(symbol='lh', value=16)
tc.set_volume_multiplier(symbol='lu', value=10)
tc.set_volume_multiplier(symbol='PF', value=5)
tc.set_volume_multiplier(symbol='PK', value=5)
tc.set_volume_multiplier(symbol='SA', value=20)
tc.set_volume_multiplier(symbol='nr', value=10)

# 启动 launcher commander
from aqs_trade.launcher_cli import LauncherCli
lc=LauncherCli()

# 设置策略是否实盘
lc.set_live(live=0, sid='all')

# 改变信号个数
lc.sig_count(count, sid) 注意count是增量

# 改变信号有效时段
lc.sig_time(stime, etime, sid)

```

## 停止 AQS Trade

```bash
ps -ef | grep trade | grep -v grep | awk '{print $2}' | xargs kill -9
```

## 重启账号

``` bash
# restart_trader.sh [账户1] [账户2] ... [账户n]
restart_trader.sh SIMNOW1 SIMNOW2
```

## 日志

### 交易日志

* 位置：`/shared/trade_log/interactive_log`
* 内容：下单命令、下单回报

### 工作日志

* 位置：`/shared/trade_log/cron_log`
* 内容：程序的版本信息、故障信息
