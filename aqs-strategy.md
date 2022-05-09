# aqs_strategy

## 配置文件

`/shared/jiaoyi/strategy_conf.json`，

## 交互

### 启动 launcher commander

```python
from aqs_strategy.st_cli import LauncherCli

lc=LauncherCli()

help(lc)
```

### lc接口说明

```python

new_signal(sid, contract, market_value, sig_px, wait_time, use_multi, limit_px=-1)
    """
    生成信号：
    Args:
        sid(str): strategy name
        contract(str): strategy contract
        market_value(int): 市值单位为万元,正数表示开多头，负数表示开空头
        sig_px(float): 策略信号价格
        wait_time(int): 限价转twap的时间，只有limit_px大于0时才有效
        use_multi(int): 对应tc的use_multi，用法相同,只有limit_px大于0时才有效
        limit_px(float): 挂限价的价格，默认为-1，表示直接下twap单，大于0才会挂限价单
    """

open_delay_signal(sid, contract, market_value, sig_px, limit_px=-1)
    """
    盘前delay信号，和tc的open_delay相同
    Args:
        sid(str): strategy name
        contract(str): strategy contract
        market_value(int): 市值单位为万元,正数表示开多头，负数表示开空头
        sig_px(float): 策略信号价格
        limit_px(int): 挂限价的价格，不填的话为-1，则必须在开盘前一分钟之前挂出，由系统指定价格
    """

open_regular_signal(sid, contract, market_value, sig_px, limit_px=-1)
    """
    盘前regular信号，和tc的open_regular相同
    Args:
        sid(str): strategy name
        contract(str): strategy contract
        market_value(int): 市值单位为万元,正数表示开多头，负数表示开空头
        sig_px(float): 策略信号价格
        limit_px(int): 挂限价的价格，不填的话为-1，则必须在开盘前一分钟之前挂出，由系统指定价格
    """

flatten_signal(sig_id, sid, ratio=1, is_signal=0, is_twap=1)
    """
    平仓信号，若信号单还没成交，会先撤单再平仓
    Args:
        sig_id(int): 信号id
        sid(str): 信号所属策略名称
        ratio(float)：平仓比例
        is_signal(0/1)：是否由信号发出，为1的话该策略必须打开，否则无效，为0则直接平
        is_twap(0/1)：是否用twap平仓
    """

flatten(sid, ratio=1, direction=0, live=-1, is_signal=0, is_best=0)
    """
    平仓信号，若信号单还没成交，会先撤单再平仓
    Args:
        sid(list): 策略list
        ratio(float)：平仓比例
        direction(0/1/-1)：1只平净持仓为多头的策略，-1只平净持仓为空头的策略，0平所有list中的策略
        live(-1/0/1): -1表示平仓后不改变策略live状态，否则将策略状态改为live的值
        is_signal(0/1)：是否由信号发出，为1的话该策略必须打开，否则无效，为0则直接平
        is_best(0/1)：是否用best平仓,默认为0，使用twap平仓
    """

cancel(sig_id)
    """
    撤销信号挂单
    """
```


```python
#lc.set_flag 使用方式, 向策略推送消息,消息内容为k-v键值对

data = {'key1': 1, 'key2': 2}
lc.set_flag(strategy='longterm_i', kwargs=**data)

from aqs_strategy.st_base import Strategy, Config

class Sample(Strategy):
    def __init__(self, sid, conf):
        super(Sample, self).__init__(sid, conf)
        self._value = 0

    def sample_run(self):
        self.run()
        strategy1 = 'longterm_i'
        data1 = {'key1': 1, 'key2': 2}
        self._cli.set_info(strategy1, **data1)

        strategy2 = 'longterm_i'
        data2 = {'dir': 0 , 'key3': 3}
        self._cli.set_info(strategy2, **data2)

        strategy3 = 'hs'
        data3 = {'live': 1, 'key4': 4}
        self._cli.set_info(strategy3, **data3)

        strategy4 = 'all'
        data4 = {'dir': 1, 'key5': 5}
        self._cli.set_info(strategy4, **data4)
        
        #print
        val = self.get_flag()
        print('val: %s' % (val))

        #open
        self._cli.new_signal(sid='longterm_i', contract='i2205', market_value=1000000, sig_px=853, wait_time=3, is_best=0)
        time.sleep(6)
        self._cli.new_signal(sid='longterm_i', contract='i2205', market_value=1000000, sig_px=853, wait_time=3, is_best=1)
        time.sleep(6)

        #flatten
        self._cli.flatten(sid='longterm_i', ratio=0.5, direction=0, is_best=1)
        time.sleep(6)
        self._cli.flatten(sid='longterm_i', ratio=0.5, direction=0, is_best=0)
        time.sleep(6)
    
if __name__ == '__main__':
    config = Config['longterm_i']
    strategy = 'longterm_i'
    s1 = Sample(strategy, config)
    s1.sample_run()
    val = s1.get_info() # val(dict) 
```

## 注意事项

`lc.set_live`, `lc.flatten`, `lc.set_dir`中的`sid`除了填`all`和具体的策略名(如`longterm_i`)等，还可以填板块名称，如`hs`代表黑色板块,`ncp`代表农产品
