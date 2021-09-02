# aqs_strategy

## 启动

### 单进程启动所有策略

`nohup aqs_launcher.py &`

### 每个进程启动一个策略

`nohup aqs_launcher.py -s simple1_i -m mp &`

## 配置文件

`/shared/jiaoyi/strategy_conf.json`，

## simple

### 策略名称

`simple1_i, simple1_j...`，每个策略只运行一个合约

### 具体配置：

`path`: 数据回放的路径，注意只有`backtrade`为`1`时才有效

`backtrade`: `1`表示回放数据，回放数据时`live`填`1`或者`0`都不会跑实盘

`broker`: 交易的账户，可以填多个

`flatten`: 是否开启盘尾自动平仓

`live`: `1`表示实际交易，`0`只跑信号，主要`live`生效`backtrade`必须为`0``

`session`: `night`为夜盘，`day`为日盘

`contract`: 交易合约，一个策略只交易一个合约

`stime, etime`: 信号有效的区间

`sig_count`: 交易的信号个数

`vol`: 每个信号交易的手数

`instr_multiply`: 合约价格校准参数

`wait_time`: 转市价的时间

其他参数为策略参数，需要咨询策略开发人员修改

## longterm

### 策略名称

`longterm_i, longterm_j...`，每个策略只运行一个合约

### 具体配置：

`path`: 数据回放的路径，注意只有`backtrade`为`1`时才有效

`backtrade`: `1`表示回放数据，回放数据时`live`填`1`或者`0`都不会跑实盘

`flatten`: 是否开启盘尾自动平仓

`broker`: 交易的账户，可以填多个

`live`: `1`表示实际交易，`0`只跑信号，主要`live`生效`backtrade`必须为`0`

`session`: `night`为夜盘，`day`为日盘

`end_check`: 该品种的夜盘休盘时间

`market_value`: 信号下单的合约市值，注意单位是万元，如果填`20`，表示市值为`200000`

`wait_time`: 转市价的时间

`open_long, flat_long, open_short, flat_short`: 策略信号参数

`prev_vol, prev_open, prev_high, prev_low, prev_close`: 开盘前20个半小时线数据，分别为成交量和开高低收

## 交互

### 启动 launcher commander
from aqs_trade.launcher_cli import LauncherCli
lc=LauncherCli()

### 设置策略是否实盘
lc.set_live(live=0, sid='all')

### 改变信号个数
lc.sig_count(count, sid) 
    
注意`count`是增量, 例如原来`sigcout`为`1`，`count`填`1`时，`sigcount`为`2`，`count`填`-1`，`sigcount`为`0`

### 改变信号有效时段
lc.sig_time(stime, etime, sid)

### 平仓
lc.flatten(sid='all', ratio=1, live=0)

`sid`可以填具体策略名，默认所有策略，`ratio`为平仓比例，默认为1，`live`为平仓后是否继续交易，默认改为`0``

## 注意事项

1. 如果是手动起的策略，需要把定时任务起的策略进程杀掉，以免跑两次策略
2. 为了安全，`lc``交互每次只能有一个人用，第二个人使用无效
