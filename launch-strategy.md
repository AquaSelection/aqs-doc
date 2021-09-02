# launch-strategy 使用指南

该系统主要用于股指的日内策略

- [launch-strategy 使用指南](#launch-strategy-使用指南)
  - [修改配置文件](#修改配置文件)
  - [手动启动分布式 md 服务器和策略](#手动启动分布式-md-服务器和策略)
    - [删除 IPC 文件](#删除-ipc-文件)
    - [手动启动分布式 md 服务器](#手动启动分布式-md-服务器)
    - [手动启动策略](#手动启动策略)
  - [自动启动分布式 md 服务器和策略](#自动启动分布式-md-服务器和策略)
  - [查看进程](#查看进程)
  - [杀死进程](#杀死进程)
  - [交互命令](#交互命令)
    - [使用方法](#使用方法)
    - [查看命令状态](#查看命令状态)
  - [在其他 python 程序中使用 md, td](#在其他-python-程序中使用-md-td)

## 修改配置文件

配置文件为 `/shared/launch.json`。

* `DEFAULT` 为所有账号共享的属性。
* 路径必须填**绝对路径**。

```json
{
    "DEFAULT": { // 默认
        "host": "GuoXinQiHuo", // 机器名称
        "log_directory": "/shared/log/", // 日志目录
        "instr_dict": { // 订阅的合约及其波动率
            "IF2101": 1.6,
            "IH2101": 1.6,
            "IC2101": 2.4
        },
        "symbol_conf": "/home/msq/strategy/conf/cffex.20201216", // 配置文件，合约列表
        "flatten_time": [40, 50] // 默认尾盘平仓时间，秒（此处为14:59:45开始，14:59:50结束）
    },
    "WANG": { // broker 名称
        "broker": "WANG", // broker 名称
        "interactive_port": "i_wang", // 交互接口
        "md_port": "", // 分布式 md 接口
        "max_vol": 5, // 单笔平仓单的最大手数
        "flatten": 1, // 是否开启尾盘平仓
        "flatten_time": [], // 若为空列表，使用默认的尾盘平仓时间；否则，使用此处的尾盘平仓时间
        "list_no_flatten": ["IF2103"], // 不尾盘平仓的合约
        "check_position": 1, // 是否开启检查仓位
        "ts_file": "",
        "risk_conf": "/home/msq/strategy/risk_info/risk_GX_WANG.conf", // 配置文件，风险控制
        "list_strategy_conf": [ // 配置文件，策略
            "/home/msq/strategy/conf/pricerX_strategy_info.climb_step2.sm.20210106"
        ],
        "list_no_running": [ // 不实盘交易的策略
            "IH.jump.1", 
            "IH.jump.3"
        ],
        "list_double": [ // 以下策略的size*2
            "open.1",
            "open.2"
        ],
        "list_quadruple": [ // 以下策略的size*4
            "future_bar.1",
            "future_bar.3"
        ],
        "list_flatten": [], // 按顺序通知策略平仓
        "stop_loss": 1, // 是否开启止损
        "realized_loss": -15000, // 已平仓的亏损
        "total_loss": -20000, // 总亏损
        "list_no_stoploss": [], // 不止损的策略
        "slippage": "px_flat", // 滑点的处理方式
        "delay_enter": 30,
        "delay_exit": 30,
        "offset": -35, // 下单时间差
        "base_size": 2, // 设置所有策略的基础size
        "base_iceberg_size": 2, // 设置所有策略的基础iceberg size
        "account_live": 1, // 是否实盘交易
        "skip_non_live": 1, // 是否跳过非实盘交易的策略
        "backtest": 0 // 是否开启回测
    }
}
```

## 手动启动分布式 md 服务器和策略

### 删除 IPC 文件

1. 在 `launch.json` 中查看所有即将启动的账户的 `md_port` 和 `interactive_port`。
2. 进入 `/shared/log/ipc_files` 目录，删除对应 `md_port` 的 `pub` 和 `sub` 文件，以及对应 `interactive_port` 的文件。

### 手动启动分布式 md 服务器

在终端执行以下命令，手动启动分布式 md 服务器。

格式为：

```bash
md_server.py --trading_date <date> --broker <broker>
# or
md_server.py -td <date> -b <broker>
```

例如，`md_server.py -td 20201026 -b TEST`。

执行 `md_server.py -h` 可查看命令的帮助。

### 手动启动策略

在终端执行以下命令，手动启动策略。

格式为：

```bash
entrance.py --trading_date <date> --broker <broker>
# or
entrance.py -td <date> -b <broker>
```

例如，`entrance.py -td 20201026 -b TEST`。

执行 `entrance.py -h` 可查看命令的帮助。

## 自动启动分布式 md 服务器和策略

1. 执行 `sudo vim /usr/local/bin/task_on_time.sh`，修改账户。
2. 执行 `crontab -e`，设置定时任务。

    例如，如果要在每个工作日的 9:00 启动策略，那么添加一行：

    ```
    00 9 * * 1-5 bin/bash /shared/launch-strategy/task_on_time.sh
    ```
    
3. 定时任务启动后，到 `/shared/log/cron_log/<broker>` 目录下查找 log 文件，并执行 `tail -f <filename>` 来刷新和查看文件。

## 查看进程

1. 查看所有分布式 md 进程：

    ```bash
    ps -ef | grep md_server
    ```

2. 查看所有策略进程：
   
    ```bash
    ps -ef | grep entrance
    ```

## 杀死进程

1. 杀死所有分布式 md 进程：

    ```bash
    ps -ef | grep md_server | grep -v grep | awk '{print $2}' | xargs kill -9
    ```

2. 杀死所有策略进程：

    ```bash
    ps -ef | grep entrance | grep -v grep | awk '{print $2}' | xargs kill -9
    ```

3. 同时杀死所有分布式 md 和策略进程：

    ```bash
    ps -ef | grep 'md_server\|entrance' | grep -v grep | awk '{print $2}' | xargs kill -9
    ```

## 交互命令

### 使用方法

1. 在终端执行 `python` 启动 python。

2. 导入交互程序：

```python
from launch_strategy.interactive.pricer_client import *
```

3. 创建交易对象：

```python
xm = PricerClient('XM')
bx = PricerClient('BX')
```

4. 使用交互命令（以 XM 为例）：

> **注意：**
>
> 如果要交易新合约，必须先更新配置文件（symbol conf / risk conf），否则下单时整个程序会崩溃！

```python
# 下限价单
xm.place_order(contract, price, volume)
# 例：xm.place_order('c2105', 2650, -1)

# 取消挂单。参数 id 为某个 place_order 命令返回的 id
xm.cancel_order(id)
# 例：xm.cancel_order(1)

# 列出所有 live 策略
xm.list_live_strategies()
# 显示详细信息
xm.list_live_strategies(only_name=False)

# 改变某个策略的 size 和 iceberg_size
xm.change_size(strategy_name, new_size)
# 例：xm.change_size('open.1', 2)

# 停止某个策略（live 设为 0，并平仓）
xm.stop_strategy(strategy_name)
# 例：xm.stop_strategy('open.1')

# 运行某个 non-live 的策略（live 设为 1）
xm.run_strategy(strategy_name)
# 例：xm.run_strategy('open.1')

# 按比例和时间平仓所有策略（平仓，但 live 还是 1）
# total_flatten_time 为 0 表示每隔一秒发一部分平仓单
xm.flatten(ratio, total_flatten_time)
# 例：xm.flatten(ratio=0.5, total_flatten_time=5) 表示5秒内平完一半仓位

# 设置交易方向
# 0:都开，1:只做多头，-1:只做空头
xm.set_dir(dir)
# 例：xm.set_dir(0)
# 例：xm.set_dir(1)
# 例：xm.set_dir(-1)

# 停止所有策略（live 设为 0，并平仓）
xm.stop_all()

# 重置保证金
xm.reset_margin(diff)
# 例：xm.reset_margin() 查询当前保证金
# 例：xm.reset_margin(-1000) 保证金减1000
# 例：xm.reset_margin(2000) 保证金加2000
# 在cron_log中抓取margin关键词，查看保证金

# 重置止损点
xm.reset_stop_loss(total, realized)
# 例：xm.reset_stop_loss(total=-1000)
# 例：xm.reset_stop_loss(realized=-500)
# 例：xm.reset_stop_loss(total=-2000, realized=-1500)
```

### 查看命令状态

每下一个命令，会返回一个消息：

```
[broker] Received reply from server: [type], id: [id], status: [0/1]
```

* **broker:** 交易对象
* **type:** 命令类型
* **id:** 命令序号
* **status:** 命令状态
  - 1：服务器处理正常，命令下达成功。
  - 0：服务器处理异常，命令下达失败。

## 在其他 python 程序中使用 md, td

```python
from launch_conf.market_src import get_md_td

md, td, _ = get_md_td('WANG1')
```
