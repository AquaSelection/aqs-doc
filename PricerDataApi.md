# PriceDataApi使用说明

## 1.概述

PriceDataApi是一套期货行情数据访问接口，提供期货行情历史数据访问和实时数据订阅推送功能。

PriceDataApi内部基于Cython实现，兼顾c++的速度和python的易用性。对外发布时编译成PriceDataApi.so文件(linux)或者PriceDataApi.pyd文件(windows)，可以屏蔽内部实现，使用者无须关心具体的实现代码。

PriceDataApi编译后自动生成python扩展库，在python应用中可以直接导入使用，和一般的python库使用上几乎没有差异，如想在python应用中使用期货行情数据接口，直接import PriceDataApi。

## 2.编译安装

针对常用环境，我们预编译了PriceDataApi的二进制库文件PriceDataApi.cp38-win_amd64.pyd(windows)和PriceDataApi.cpython-36m-x86_64-linux-gnu.so(linux)，分别对应window64位环境下的python3.8和linux64位环境下的python3.6。如果您的使用环境与上述环境有较大差异，可以联系我们，我们可以针对您的使用环境帮您编译对应的库。

如果您想自己编译，也可以向我们索取源代码，自行按如下方法进行编译:

PriceDataApi库的编译需要依赖Cython和python-devel，编译前需要首先安装这两个依赖项，在centos系统下安装这两个依赖项的指令是:

- yum install python-devel 
- pip3 install  Cython

PriceDataApi的本地编译指令：

- python3 setup.py build_ext --inplace

PriceDataApi的编译安装指令:

- python3 setup.py install

## 3.使用

PriceDataApi运行时目前需要依赖python的pandas和pymongo两个库，安装指令为:

- pip3 install  pandas
- pip3 install  pymongo

在windows下使用PriceDataApi还需要依赖VC运行时环境，发行包里已经包含了这个安装程序（VC_redist.x64.exe），如果你的系统中还没有安装，可以自行安装。

在linux下使用PriceDataApi还需要依赖libstdc++.so.6，如果你的系统中还没有安装，可以使用如下命令安装(centos):

```shell
yum install libstdc++.so.6 -y
```



各个接口的具体使用方法，参见下面各个接口的说明：

## 4.接口使用说明

PriceDataApi提供期货行情历史和实时数据的访问接口，安装完DataApi库后，在python应用中直接import PriceDataApi即可使用，其具体api接口和使用示例如下所示：

### 4.1 构造期货行情数据api实例

接口：____init____(self,string ip,int port,string user,string passwd)

示例：api = PriceDataApi.PriceDataApi(b"192.168.1.32",27017,b"futdbRO",b"futdbRO")



### 4.2 读取所有的产品信息,返回list< ProductInfo >

接口：ReadProductInfo(self)

示例：lst = api.ReadProductInfo()

ProductInfo对象定义：

时间段：

```cython
	ctypedef struct TimeSpan:
    	int start_time #开始时间
    	int end_time   #结束时间
```

产品信息：

```cython
ctypedef struct ProductInfo:     
    string exchange_id #交易所Id    
    string product_id #产品Id    
    string product_name #产品名称
    int product_calss   #产品类型
    int multiple    #合约乘数
    double price_tick #最小变动价位
    int max_market_order #市价单最大下单手数
    int min_market_order #市价单最小下单手数
    int max_limit_order #限价单最大下单手数
    int min_limit_order #限价单最小下单手数
    double long_margin_ratio #多头保证金比率
    double short_margin_ratio #空头保证金比率
    double open_fee_ratio #开仓手续费率
    double open_fee_money #开仓手续费金额
    double close_fee_ratio #平仓手续费率
    double close_fee_money #平仓手续费金额
    double close_today_fee_ratio #平今手续费率
    double close_today_fee_money #平今手续费金额
    int underlying_multiple #标的合约乘数    
    vector[int] auction_times #可能的集合竞价时间点
    vector[int] open_times #可能的开盘时间点
    vector[int] close_time #可能的收盘时间点
    vector[TimeSpan] trade_times #可能的交易时间段
```



### 4.3 读取一个合约在某交易日的所有历史tick数据,返回DataFrame

接口：ReadInstOneDayHisTickInfo(self,string instr_id,int trading_day)

示例：df = api.ReadInstOneDayHisTickInfo(b"y2109",20210802)

### 4.4 读取一个合约从from_date到to_date的所有历史tick数据,返回DataFrame

接口：ReadInstPeriodHisTickInfo(self,string instr_id,int from_date,int to_date)

示例：df = api.ReadInstPeriodHisTickInfo(b"y2109",20210731,20210802)

### 4.5 读取一个产品在某一交易日的主力合约的全部历史tick数据,返回DataFrame

接口：ReadProductOneDayHisTickInfo(self,string product_id,int main_inst_order,int trading_day)

其中main_inst_order表示主力合约排序，0表示主力合约，1表示次主力合约，依次类推，下同。

示例：df = api.ReadProductOneDayHisTickInfo(b"cu",0,20210728)

### 4.6 读取一个产品从from_date到to_date的主力合约的全部历史tick数据,返回DataFrame

接口：ReadProductPeriodHisTickInfo(self,string product_id,int main_inst_order,int from_date,int to_date)

示例：df = api.ReadProductPeriodHisTickInfo(b"SR",0,20210728,20210729)

### 4.7 读取一个合约本交易日到目前为止的所有tick数据,返回DataFrame

接口：ReadInstTodayTickInfo(self,string instr_id)

示例：df = api.ReadInstTodayTickInfo(b"y2109")

### 4.8 读取一个合约本交易日的最新tick数据,返回TickInfo

接口：ReadInstLastTickInfo(self,string instr_id)

示例：tick = api.ReadInstLastTickInfo(b"y2109")

TickInfo对象定义如下：

行情深度信息:

```cython
ctypedef struct DepthInfo:
    double bid_price    #申买价
    int bid_volume      #申买量
    double ask_price    #申卖价
    int ask_volume      #申卖量
```

TickInfo:

```cython
ctypedef struct TickInfo:
    string product_id #产品Id
    string instr_id   #合约
    long long local_ts  #本地时间戳
    int trading_day  #交易日
    int action_day   #自然日
    int time         #时间
    int second       #秒
    int milli_sec    #毫秒
    double lastprice #最新价
    double day_open  #当天开盘价 
    double day_high  #当天最高价    
    double day_low   #当天最低价
    double tick_close #tick的收盘价    
    int tick_volume   #tick的成交量
    int total_volume  #当天总成交量    
    int tick_opi_change #tick持仓量变化   
    int total_opi       #当天总持仓量
    double tick_turnover    #成交额变化
    double total_turnover   #总成交额
    double upper_limit      #涨停价
    double lower_limit      #跌停价
    double pre_settlement   #昨结算价
    double pre_close        #昨收盘价
    int pre_opi             #昨持仓量    
    double settlement       #今结算价
    double total_average    #当天加权平均价
    double tick_average     #tick加权平均价    
    vector[DepthInfo] depth #深度行情
```



###  4.9 读取一个合约在某交易日的所有历史分钟线数据,返回DataFrame

接口：ReadInstOneDayHisMinuteBar(self,string instr_id,int interval,int trading_day)

其中interval表示分钟线的时间维度，应小于24*60，如果数据库中不存在相应维度的分钟线,会自动基于1分钟线进行合成，下同。

示例：df = api.ReadInstOneDayHisMinuteBar(b"SR109",5,20210802)

### 4.10 读取一个合约从from_date到to_date的所有历史分钟线数据,返回DataFrame

接口：ReadInstPeriodHisMinuteBar(self,string instr_id,int interval,int from_date,int to_date)

示例：df = api.ReadInstPeriodHisMinuteBar(b"IF2108",5,20210802,20210802)

### 4.11 读取一个产品在某一交易日的主力合约的全部历史分钟线数据,返回DataFrame

接口：ReadProductOneDayHisMinuteBar(self,string product_id,int interval,int main_inst_order,int trading_day)

示例：df = api.ReadProductOneDayHisMinuteBar(b"SR",3,0,20210802)

### 4.12 读取一个产品从from_date到to_date的主力合约的全部历史分钟线数据,返回DataFrame

接口：ReadProductPeriodHisMinuteBar(self,string product_id,int interval,int main_inst_order,int from_date,int to_date)

示例：df = api.ReadProductPeriodHisMinuteBar(b"SR",3,0,20210728,20210803)

### 4.13 读取一个合约本交易日到目前为止的所有分钟线数据,返回DataFrame

接口：ReadInstTodayMinuteBar(self,string instr_id,int interval)

示例：df = api.ReadInstTodayMinuteBar(b"cu2109",5)

### 4.14 读取一个合约本交易日的最后一根1分钟线数据,返回MinuteBar

接口：ReadInstLastMinuteBar(self,string instr_id)

示例：bar = api.ReadInstLastMinuteBar(b"IF2108")

MinuteBar对象的定义如下：

```cython
ctypedef struct MinuteBar:
    string instr_id   #合约    
    int trading_day  #交易日
    int action_day   #自然日
    int time         #时间      
    double open      #开盘价
    double high      #最高价
    double low       #最低价
    double close     #收盘价
    int volume       #成交量
    int opi          #持仓量
```



### 4.15 读取一个合约在某一交易日的历史日线数据,返回DayBar对象

接口：ReadInstOneDayHisDayBar(self,string instr_id,int trading_day)

示例：day = api.ReadInstOneDayHisDayBar(b"y2109",20210730)

 DayBar对象的定义如下:

```cython
ctypedef struct DayBar:
    string product_id #产品Id
    string instr_id   #合约    
    int trading_day  #交易日
    int time         #时间      
    double open      #开盘价
    double high      #最高价
    double low       #最低价
    double close     #收盘价
    int volume       #成交量
    int opi          #持仓量
    double pre_settlement #昨结算价
    double pre_close      #昨收盘价
    int pre_opi       #昨持仓量
    double upper_limit    #涨停价
    double lower_limit    #跌停价
```



### 4.16 读取一个合约从from_date到to_date的所有历史日线数据,返回DataFrame 

接口：ReadInstPeriodHisDayBar(self,string instr_id,int from_date,int to_date)

示例：df = api.ReadInstPeriodHisDayBar(b"IF2108",20210728,20210802)

### 4.17 读取一个产品在某一交易日的主力合约的历史日线数据,返回DayBar

接口：ReadProductOneDayHisDayBar(self,string product_id,int main_inst_order,int trading_day)

示例：day = api.ReadProductOneDayHisDayBar(b"IF",0,20210730)

### 4.18 读取一个产品从from_date到to_date的主力合约的全部历史日线数据,返回DataFrame

接口：ReadProductPeriodHisDayBar(self,string product_id,int main_inst_order,int from_date,int to_date)

示例：df = api.ReadProductPeriodHisDayBar(b"cu",0,20210728,20210803)

### 4.19 读取一个合约本交易日到目前为止的日线数据,返回DayBar 

接口：ReadInstTodayDayBar(self,string instr_id)

示例：day = api.ReadInstTodayDayBar(b"IF2108")

### 4.20 订阅一组合约的实时tick行情数据 

PriceDataApi提供一组接口，共同完成订阅一组合约的实时tick行情，如下：

- 订阅合约tick数据接口，传入一个合约列表

     def SubscribeTick(self,vector[string] insts)

- 取消订阅合约tick数据接口，传入一个合约列表

​          def UnsubscribeTick(self,vector[string] insts)

- 阻塞当前线程，等待合约tick数据推送

​             def WaitTickUpdate(self)

- 合约tick数据推送回调函数，使用者一般应重写该函数以实现自己的业务逻辑

     def OnTickUpdate(self,tick)

- 一个完整的行情订阅使用实例如下所示：

```python
#继承PriceDataApi实现，重写OnTickUpdate回调函数
class MyPriceDataApi(PriceDataApi.PriceDataApi):
    def OnTickUpdate(self,tick):
        print("----------------OnTickUpdate------------------")
        print(tick)
        print("----------------OnTickUpdate------------------")
        
#构造期货行情数据api实例
api = MyPriceDataApi(b"192.168.1.32",27017,b"futdbRO",b"futdbRO")

#订阅tick数据，传入一个合约列表
api.SubscribeTick([b"IF2108",b"IF2109",b"IF2112"])

#取消订阅tick数据，传入一个合约列表
api.UnsubscribeTick([b"IF2112"])

#阻塞当前线程，等待合约tick数据推送
api.WaitTickUpdate()

```

