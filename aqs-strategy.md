# aqs_strategy

## 配置文件

`/shared/jiaoyi/strategy_conf.json`，

## 交互

### 启动 launcher commander

from aqs_strategy.st_cli import LauncherCli

lc=LauncherCli()

help(tc) 查看接口详细用法和参数说明

### lc.set_flag 使用方式, 向策略推送消息,消息内容为k-v键值对

```python
### 导入相关模块

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
