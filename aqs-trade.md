# AQS Trade 使用指南

## 生成安装压缩包

1. 进入 aqs trade 根目录
2. 执行 `python setup_trade.sh sdist`，生成 dist/aqs-trade-x.y.z.tar.gz
3. 执行 `python setup_conf.sh sdist`，生成 dist/aqs-conf-x.y.z.tar.gz

## 安装 AQS Trade

> 准备：`/shared` 目录下存放
> * AQS Trade 压缩包：aqs-trade-x.y.z.tar.gz
> * AQS Conf 压缩包：aqs-conf-x.y.z.tar.gz
> * [配置文件：executive_conf.json](###/shared/executive_conf.json)
> * [配置文件：account_risk_conf.json](###/shared/account_risk_conf.json)

1. 解压 `aqs-trade-x.y.z.tar.gz` 和 `aqs-conf-x.y.z.tar.gz`:
   ```bash
    tar -xzvf /shared/aqs-trade-x.y.z.tar.gz
    tar -xzvf /shared/aqs-conf-x.y.z.tar.gz
   ```
2. 安装 `AQS Trade` 和 `AQS Conf`:
   ```bash
   cd /shared/aqs-trade-x.y.z
   sudo python setup_trade.py install
   cd /shared/aqs-conf-x.y.z
   sudo python setup_conf.py install
   ```

## 启动 AQS Trade

```bash
task_launch_trader.sh MD(eg: SJWX1)
```

## 配置文件

### /shared/executive_conf.json
```json
{
    "flatten": { // 尾盘平仓
        "day": { // 日盘
            "default": {
                "time": ["14:59:00", "14:59:30"], // 起止时间
                "list_symbol": [] // 永远写空列表。不在其他group里的品种用默认平仓时间
            }
        },
        "night": { // 夜盘
            "default": {
                "time": ["22:59:00", "22:59:30"],
                "list_symbol": []
            },
            "group1": {
                "time": ["23:59:00", "23:59:30"],
                "list_symbol": ["ag", "au"] // 这些品种用group1里的平仓时间
            },
            "group2": {
                "time": ["01:59:00", "01:59:30"],
                "list_symbol": ["sn", "cu"] // 这些品种用group2里的平仓时间
            }
        }
    },
    "close_method": { // 指定品种的平仓方式
        "today_prior": ["i", "c"], // 优先平今
        "yesterday_prior": ["ag", "IF"], // 优先平昨
        "open_opposite": ["TA"] // 锁仓
    },
    "order_manager": { // 订单管理设置(order_manger下所有的参数都只对非twap单有效)
        "market_num": 2, // 除尾盘时间外：每几条行情触发一次市价单
        "tick_size": { // 自成交价格变动量(自成交目前没有启用，该参数无效)
            "default": 0,
            "i2201": 1
        },
        "tick_multiplier": { // 盘口容量除数
            "default": 5,
            "i2201": 2
        },
        "least_open": { //最小下单手数(合约必须在list_contract中)
            "ZC205": 4,
            "CJ209": 4
        }
    },
    "list_contract": { // 订阅的合约
        "cu2111": ["cu1", 30, 0], // 合约：【别名，对手价转市价时间，限价转市价时间】
        "cu2110": ["cu", 30, 0],
        "ZC205": ["ZC", 30, 0],
        "CJ209": ["CJ", 10, 0]
    },
    "twap_params": { // 合约的twap参数，所有合约也必须在list_contract中
        'bu2209':["bu1", 30, 0.2], // 合于：【别名，twap下单总时间，twap时间间隔】
        'a2209':['a1',30, 0.2]
    }
}
```

### /shared/account_risk_conf.json
```json
{
    "cmd_brokers": ["hjl", "msb", "sym"], // trade commander的默认参数brokers
    "max_size": {
        "MA1": [90.6, 90.7], // 合约别名：【最大仓位，最大手数】
        "sc": [10.0, 10.0]
    },
    "running_brokers": { // 启动的账户
        "hjl": { // 账户
            "multiplier": 1 // 下单乘数
        },
        "msb": {
            "multiplier": 2
        },
        "sym": {
            "multiplier": 1
        }
    }
}
```

## 注意事项

> tc目前下单种类有：限价单，市价单，对手价单，时间条件单，价格条件单，twap单，自带止盈止损的complete单

> 条件单（包括时间条件、价格条件）只能撤单，不能追单。

> 下单类型分两种，twap中各个账户的单不会排队，也不会处理自成交，非twap单会集合每个账户的下单，排队并且处理自成交（自成交逻辑目前禁用）

> tc接口里面的contract可以填合约的简化的别名，参数在list_contract里指定

> tc所有功能接口和参数信息可以在help(tc)中详细查看

```python
# 启动 trade commander
from aqs_trade.trade_commander import TradeCommander
tc=TradeCommander()

# 查看帮助文档，按 q 退出
help(TradeCommander)

# 限价单
tc.limit(contract='SA109', price=2269, volume=-2, isopen=0, time=10, brokers=['hjl'])

# 市价单
tc.market(contract='jm2109', volume=-3, isopen=0, brokers=['msb', 'sym', 'hjl'], strategy='apple')

# 对手价单
tc.best(contract='CF109', volume=10, isopen=1, brokers=['msb', 'sym', 'hjl'], strategy='apple')

# 按市值下单
# 限价单指定 time 之后未成交就转市价单，time为0表示不转市价
# value 的单位是万，正/负分别表示买/卖

# 市价单
tc.marketmv(contract='i2109', mv=20, isopen=0, brokers=['msb', 'sym', 'hjl'], strategy='apple')

# 限价单
tc.limitmv(contract='i2105', price=1220, mv=-10, isopen=1, time=0, brokers=['msb', 'sym', 'hjl'], strategy='apple')

# 对手价单
tc.bestmv(contract='i2201', mv=10, isopen=1, time=5, brokers=['msb', 'sym', 'hjl'], strategy='apple')

# 条件单
# 按市值
tc.mvc(contract='i2109', direction='lt', price=893, mv=10, isopen=1, time=10, brokers=['msb', 'sym', 'hjl'], strategy='apple')
# 按手数
tc.volc(contract='i2201', direction='lt', price=1010, volume=3, isopen=1, time=60, brokers=['msb'], strategy='apple')

# 开仓+止盈/止损
# 按手数
tc.complete(contract='cu2108', volume=2, stoploss_ratio=0.001, stoptarget_ratio=0.001, time=10, brokers=['msb'], strategy='pear')
# 按市值
tc.complete_mv(contract='i', mv=20, stoploss_ratio=0.001, stoptarget_ratio=0.001, time=10, brokers=['sym'], strategy='pear')

# 开仓
# 按手数
tc.twapmv(contract='i', volume=10, strategy= 'pear', period=10, interval=0.5)
# 按市值、总时间period(s)以及发单间隔interval(s)
tc.twapmv(contract='i', mv=20, brokers=['msb', 'sym', 'hjl'], strategy= 'pear', period=10, interval=0.5)

# 查询所有交易
# 查询订单，如果done为wait，则还未发出委托
tc.query_order()
tc.query_order(brokers=['msb', 'sym', 'hjl'], strategies=['apple', 'pear'])

# 查询仓位
tc.query_position()
tc.query_position()
tc.query_position(brokers=['hjl', 'msb', 'sym'])

# 询价
tc.query_price(contract='cu2109')

# 查询策略仓位
tc.query_strategy_position(strategies=['trader'], brokers=['hjl'])
tc.query_strategy_position(brokers=['sym'])

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
tc.cancel(market=1, brokers=['msb'])
# 制定market参数为3，转twap
tc.cancel(ids=[4], market=3, brokers=['msb', 'sym', 'hjl'])

# 平仓，按指定比例、账号和合约
tc.flatten(ratio=1, contracts=['i2105', 'j2105', 'fu2105'], strategies=['apple', 'pear'], brokers=['msb', 'sym', 'hjl'], end=False)
# 使用twap下单算法平仓，总时间period(s)以及发单间隔interval(s)，按指定比例、账号和合约
tc.flatten(ratio=1, contracts=['i2105', 'j2105', 'fu2105'], strategies=['apple', 'pear'], brokers=['msb', 'sym', 'hjl'], twap=1, end=False)
# contracts = 'all' 时，平仓所有合约
tc.flatten(ratio=0.5, contracts='all', brokers=['msb', 'sym', 'hjl'])
# 使用配置文件的默认参数
tc.flatten(0.1, 'all', end=True)
tc.flatten(ratio=0.2, contracts=['fu2109', 'sn2109'])

# 平仓，按指定比例、job_id
tc.flatten_by_job_id(job_id=[103], ratio=1)

# 重新加载配置文件中除了 frontend_address, backend_address, limit_max 以外的配置
tc.reload_config()

# 重新加载数据库中订单信息
tc.reload_order()

# 关闭账户hjl
tc.switch(1, ['hjl'])

# 设置交易单位
# 当持续提示“CJ, bc, ss, lh have no volume multiplier, query instrument again”时，需要使用此功能手动设置交易单位
tc.set_volume_multiplier(symbol='CJ', value=5)
tc.set_volume_multiplier(symbol='nr', value=10)
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
