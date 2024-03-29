# aqs_flow API 说明

## 期货/期权


请使用 python3.5 及以上版本。

```python
from aqs_flow.futures_data import FuturesData
t1 = FuturesData()
```
### 日志
```shell
/home/$user/aqs_flow.log
```
### 类型说明

#### level1 一种类型(所有交易所)
#### level2 
#### zce 一种类型
#### dce 五种类型 MDBestAndDeep MDTenEntrust MDOrderStatistic MDRealTimePrice MDMarchPriceQty 

### 数据说明

#### 期货现货数据

##### level1 20100101 - 现在
##### level2 
##### dce 20210301 - 现在  
##### zce 20220901 - 现在
##### 目前支持decl2、zcel2
##### levle2 当天数据收盘后一段时间才能查询到

#### 期货期权数据

##### level1 20220802 - 现在

### 相关类型字段含义如下

#### 现货数据

##### level1
```c++
┌─name────────────┬─type───────────┬
│ instr_id        │ String         │
│ trading_day     │ UInt32         │
│ action_day      │ UInt32         │
│ local_ts        │ UInt64         │
│ time            │ UInt16         │
│ second          │ UInt8          │
│ milli_sec       │ UInt16         │
│ lastprice       │ Float64        │
│ day_open        │ Float64        │
│ day_high        │ Float64        │
│ day_low         │ Float64        │
│ tick_close      │ Float64        │
│ tick_volume     │ Int32          │
│ total_volume    │ Int32          │
│ tick_opi_change │ Float64        │
│ total_opi       │ Float64        │
│ tick_turnover   │ Float64        │
│ total_turnover  │ Float64        │
│ upper_limit     │ Float64        │
│ lower_limit     │ Float64        │
│ pre_settlement  │ Float64        │
│ pre_close       │ Float64        │
│ pre_opi         │ Float64        │
│ settlement      │ Float64        │
│ total_average   │ Float64        │
│ tick_average    │ Float64        │
│ bid_price       │ Array(Float64) │
│ bid_volume      │ Array(Float64) │
│ ask_price       │ Array(Float64) │
│ ask_volume      │ Array(Float64) │
└─────────────────┴────────────────┴
```

##### level2

###### dce

###### MDBestAndDeep：最优与五档深度行情
```c++
struct MDBestAndDeep
{
    UINT4       LocalTimestamp;                       //时间戳
    UINT4       Time;                                 //预留字段
    INT1        Exchange[3];                          //交易所
    INT1        Contract[80];                         //合约代码
    BOOL        SuspensionSign;                       //停牌标志
    REAL8       LastClearPrice;                       //昨结算价
    REAL8       ClearPrice;                           //今结算价
    REAL8       AvgPrice;                             //成交均价
    REAL8       LastClose;                            //昨收盘
    REAL8       Close;                                //今收盘
    REAL8       OpenPrice;                            //今开盘
    UINT4       LastOpenInterest;                     //昨持仓量
    UINT4       OpenInterest;                         //持仓量
    REAL8       LastPrice;                            //最新价
    UINT4       MatchTotQty;                          //成交数量
    REAL8       Turnover;                             //成交金额
    REAL8       RiseLimit;                            //最高报价
    REAL8       FallLimit;                            //最低报价
    REAL8       HighPrice;                            //最高价
    REAL8       LowPrice;                             //最低价
    REAL8       PreDelta;                             //昨虚实度
    REAL8       CurrDelta;                            //今虚实度
    REAL8       BuyPriceOne;                          //买入价格1
    UINT4       BuyQtyOne;                            //买入数量1
    UINT4       BuyImplyQtyOne;                       //买1推导量
    REAL8       BuyPriceTwo;                          //买入价格2
    UINT4       BuyQtyTwo;                            //买入数量2
    UINT4       BuyImplyQtyTwo;                       //买2推导量
    REAL8       BuyPriceThree;                        //买入价格3
    UINT4       BuyQtyThree;                          //买入数量3
    UINT4       BuyImplyQtyThree;                     //买3推导量
    REAL8       BuyPriceFour;                         //买入价格4
    UINT4       BuyQtyFour;                           //买入数量4
    UINT4       BuyImplyQtyFour;                      //买4推导量
    REAL8       BuyPriceFive;                         //买入价格5
    UINT4       BuyQtyFive;                           //买入数量5
    UINT4       BuyImplyQtyFive;                      //买5推导量
    REAL8       SellPriceOne;                         //卖出价格1
    UINT4       SellQtyOne;                           //卖出数量1
    UINT4       SellImplyQtyOne;                      //卖1推导量
    REAL8       SellPriceTwo;                         //卖出价格2
    UINT4       SellQtyTwo;                           //卖出数量2
    UINT4       SellImplyQtyTwo;                      //卖2推导量
    REAL8       SellPriceThree;                       //卖出价格3
    UINT4       SellQtyThree;                         //卖出数量3
    UINT4       SellImplyQtyThree;                    //卖3推导量
    REAL8       SellPriceFour;                        //卖出价格4
    UINT4       SellQtyFour;                          //卖出数量4
    UINT4       SellImplyQtyFour;                     //卖4推导量
    REAL8       SellPriceFive;                        //卖出价格5
    UINT4       SellQtyFive;                          //卖出数量5
    UINT4       SellImplyQtyFive;                     //卖5推导量
    INT1        GenTime[13];                          //行情产生时间
    UINT4       LastMatchQty;                         //最新成交量
    INT4        InterestChg;                          //持仓量变化
    REAL8       LifeLow;                              //历史最低价
    REAL8       LifeHigh;                             //历史最高价
    REAL8       Delta;                                //delta
    REAL8       Gamma;                                //gama
    REAL8       Rho;                                  //rho
    REAL8       Theta;                                //theta
    REAL8       Vega;                                 //vega
    INT1        TradeDate[9];                         //行情日期
    INT1        LocalDate[9];                         //本地日期，预留字段
}THYQuote;
```

###### MDTenEntrust：最优价位上十笔委托

```c++
struct MDTenEntrust
{
    UINT4       LocalTimestamp;                       //时间戳
    INT1        Contract[80];                         //合约代码
    REAL8       BestBuyOrderPrice;                    //最优买价格
    UINT4       BestBuyOrderQtyOne;                   //委托量1
    UINT4       BestBuyOrderQtyTwo;                   //委托量2
    UINT4       BestBuyOrderQtyThree;                 //委托量3
    UINT4       BestBuyOrderQtyFour;                  //委托量4
    UINT4       BestBuyOrderQtyFive;                  //委托量5
    UINT4       BestBuyOrderQtySix;                   //委托量6
    UINT4       BestBuyOrderQtySeven;                 //委托量7
    UINT4       BestBuyOrderQtyEight;                 //委托量8
    UINT4       BestBuyOrderQtyNine;                  //委托量9
    UINT4       BestBuyOrderQtyTen;                   //委托量10
    REAL8       BestSellOrderPrice;                   //最优卖价格
    UINT4       BestSellOrderQtyOne;                  //委托量1
    UINT4       BestSellOrderQtyTwo;                  //委托量2
    UINT4       BestSellOrderQtyThree;                //委托量3
    UINT4       BestSellOrderQtyFour;                 //委托量4
    UINT4       BestSellOrderQtyFive;                 //委托量5
    UINT4       BestSellOrderQtySix;                  //委托量6
    UINT4       BestSellOrderQtySeven;                //委托量7
    UINT4       BestSellOrderQtyEight;                //委托量8
    UINT4       BestSellOrderQtyNine;                 //委托量9
    UINT4       BestSellOrderQtyTen;                  //委托量10
    INT1        GenTime[13];                          //生成时间
}TENENTRUST;
```

###### MDOrderStatistic：加权平均以及委托总量行情

```c++
struct MDOrderStatistic
{
    UINT4       LocalTimestamp;                       //时间戳
    INT1        ContractID[80];                       //合约号
    UINT4       TotalBuyOrderNum;                     //买委托总量
    UINT4       TotalSellOrderNum;                    //卖委托总量
    REAL8       WeightedAverageBuyOrderPrice;         //加权平均委买价格
    REAL8       WeightedAverageSellOrderPrice;        //加权平均委卖价格
}ORDERSTATISTIC;
```
###### MDRealTimePrice：实时结算价

```c++
struct MDRealTimePrice
{
    UINT4       LocalTimestamp;                       //时间戳
    INT1        ContractID[80];                       //合约号
    REAL8       RealTimePrice;                        //实时结算价
}REALTIMEPRICE;
```

###### MDMarchPriceQty：分价位成交

```c++
struct MDMarchPriceQty
{
    UINT4       LocalTimestamp;                       //时间戳
    INT1        ContractID[80];                       //合约号
    REAL8       PriceOne;                             //价格
    UINT4       PriceOneBOQty;                        //买开数量
    UINT4       PriceOneBEQty;                        //买平数量
    UINT4       PriceOneSOQty;                        //卖开数量
    UINT4       PriceOneSEQty;                        //卖平数量
    REAL8       PriceTwo;                             //价格
    UINT4       PriceTwoBOQty;                        //买开数量
    UINT4       PriceTwoBEQty;                        //买平数量
    UINT4       PriceTwoSOQty;                        //卖开数量
    UINT4       PriceTwoSEQty;                        //卖平数量
    REAL8       PriceThree;                           //价格
    UINT4       PriceThreeBOQty;                      //买开数量
    UINT4       PriceThreeBEQty;                      //买平数量
    UINT4       PriceThreeSOQty;                      //卖开数量
    UINT4       PriceThreeSEQty;                      //卖平数量
    REAL8       PriceFour;                            //价格
    UINT4       PriceFourBOQty;                       //买开数量
    UINT4       PriceFourBEQty;                       //买平数量
    UINT4       PriceFourSOQty;                       //卖开数量
    UINT4       PriceFourSEQty;                       //卖平数量
    REAL8       PriceFive;                            //价格
    UINT4       PriceFiveBOQty;                       //买开数量
    UINT4       PriceFiveBEQty;                       //买平数量
    UINT4       PriceFiveSOQty;                       //卖开数量
    UINT4       PriceFiveSEQty;                       //卖平数量
}MARCHPRICEQTY;
```

##### zce
```c++
┌─name───────────┬─type───────────┬
│ contract       │ String         │
│ trading_day    │ UInt32         │
│ ts             │ UInt64         │
│ volume         │ Int32          │
│ turnover       │ Float64        │
│ opi            │ Float64        │
│ lastprice      │ Float64        │
│ bid_px         │ Array(Float64) │
│ bid_vol        │ Array(Float64) │
│ ask_px         │ Array(Float64) │
│ ask_vol        │ Array(Float64) │
│ total_buy_vol  │ Int32          │
│ total_sell_vol │ Int32          │
│ avg_buy_px     │ Float64        │
│ avg_sell_px    │ Float64        │
└────────────────┴────────────────┴
```

#### 期货数据

##### level1

```c++
┌─name────────────┬─type───────────┬
│ instr_id        │ String         │
│ trading_day     │ UInt32         │
│ action_day      │ UInt32         │
│ local_ts        │ UInt64         │
│ time            │ UInt16         │
│ second          │ UInt8          │
│ milli_sec       │ UInt16         │
│ lastprice       │ Float64        │
│ day_open        │ Float64        │
│ day_high        │ Float64        │
│ day_low         │ Float64        │
│ tick_close      │ Float64        │
│ tick_volume     │ Int32          │
│ total_volume    │ Int32          │
│ tick_opi_change │ Float64        │
│ total_opi       │ Float64        │
│ tick_turnover   │ Float64        │
│ total_turnover  │ Float64        │
│ upper_limit     │ Float64        │
│ lower_limit     │ Float64        │
│ pre_settlement  │ Float64        │
│ pre_close       │ Float64        │
│ pre_opi         │ Float64        │
│ settlement      │ Float64        │
│ total_average   │ Float64        │
│ tick_average    │ Float64        │
│ bid_price       │ Array(Float64) │
│ bid_volume      │ Array(Float64) │
│ ask_price       │ Array(Float64) │
│ ask_volume      │ Array(Float64) │
└─────────────────┴────────────────┴
```
### 接口示例

#### get_contract_his_tick

查询指定合约的历史tick数据。

参数：
* contracts: 合约列表
* from_date: 开始的交易日（包含）
* to_date: 结束的交易日（包含）
* fields: 显示的列名，不填则显示所有列的数据
* is_dcel2：是否为大商所l2行情，默认为False
* is_dcel2：是否为郑商所l2行情，默认为False
* is_option：是否为期权行情，默认为False
* is_type：levle2行情类型, 1为MDBestAandDeep；2为MDTenEntrust；3为MDRealTimePrice，4为MDOrderStatistic；5为MDMarchPriceQty，默认值为1

```python
df = t1.get_contract_his_tick(contracts=['pg2209-C-5250', 'MO2208-C-6800'], from_date=20210301, to_date=20210307, is_option=True)
df = t1.get_contract_his_tick(contracts=['i2201', 'j2202'], from_date=20220101, to_date=20220109, fields=['instr_id', 'lastprice'])
```

#### get_contract_today_tick

查询指定合约当前交易日的tick数据。

参数：
* contracts: 合约列表
* fields: 显示的列名，不填则显示所有列的数据
* is_dcel2：是否为大商所l2行情，默认为False
* is_zcel2：是否为郑商所l2行情，默认为False
* is_option：是否为期权行情，默认为False
* is_type：levle2行情类型, 1为MDBestAandDeep；2为MDTenEntrust；3为MDRealTimePrice，4为MDOrderStatistic；5为MDMarchPriceQty，默认值为1

```python
df = t1.get_contract_today_tick(contracts=['i2201', 'j2202'], fields=['instr_id', 'lastprice'])
df = t1.get_contract_today_tick(contracts=['au2212C408', 'IO2209-C-4400'], fields=['instr_id', 'lastprice'], is_option=True)
```


#### get_symbol_his_tick

查询指定品种的历史tick数据。

参数：
* symbols: 品种列表
* from_date: 开始的交易日（包含）
* to_date: 结束的交易日（包含）
* fields: 显示的列名，不填则显示所有列的数据
* is_dcel2：是否为大商所l2行情，默认为False
* is_zcel2：是否为郑商所l2行情，默认为False
* is_option：是否为期权行情，默认为False
* is_type：levle2行情类型, 1为MDBestAandDeep；2为MDTenEntrust；3为MDRealTimePrice，4为MDOrderStatistic；5为MDMarchPriceQty，默认值为1

```python
# 查询i、j的合约的历史tick数据
df = t1.get_symbol_his_tick(symbol=['i', 'j'], from_date=20220101, to_date=20220109, fields=['instr_id', 'lastprice'])
df = t1.get_symbol_his_tick(symbol=['MO', 'MA'], from_date=20220802, to_date=20220802, is_option=True, fields=['instr_id'])
```

#### get_product_info

查询指定品种和日期所有交易的合约。

参数：
* symbols: 品种列表，不填则返回所有品种的结果
* from_date: 开始的交易日（包含）
* to_date: 结束的交易日（包含）

返回：list

```python
list_contracts = t1.get_product_info(from_date=20220101, to_date=20220501, symbols=['i', 'j'])
```

### 示例代码
```python
from aqs_flow.futures_data import FuturesData

if __name__ == '__main__':
    t1 = FuturesData()
    
    # 获取某一段时间参与交易的合约
    list_contracts = t1.get_product_info(from_date=20220101, to_date=20220501, symbols=['i', 'j'])

    # 获取level1行情
    df = t1.get_contract_today_tick(contracts=['i2206', 'j2206'])
    df = t1.get_symbol_his_tick(from_date=20190101, to_date=20190107, symbols=['i', 'j'])
    df = t1.get_contract_his_tick(from_date=20190101, to_date=20190507, contracts=['rb1909'])

    # 获取level2行情
    df = t1.get_contract_today_tick(contracts=['i2206', 'j2206'], is_dcel2=True, is_type=1)
    df = t1.get_contract_today_tick(contracts=['i2206', 'j2206'], is_dcel2=True, is_type=5)
    df = t1.get_symbol_his_tick(from_date=20210301, to_date=20210307, symbols=['a', 'b'], is_dcel2=True, is_type=1)
    df = t1.get_symbol_his_tick(from_date=20210301, to_date=20210307, symbols=['a', 'b'], is_dcel2=True, is_type=5)
    df = t1.get_contract_his_tick(from_date=20210301, to_date=20210307, contracts=['a2105', 'b2105'], is_dcel2=True, is_type=1)
    df = t1.get_contract_his_tick(from_date=20210301, to_date=20210307, contracts=['a2105', 'b2105'], is_dcel2=True, is_type=5)
    df = t1.get_contract_his_tick(from_date=20220901, to_date=20220901, contracts=['TA301', 'AP201'], is_zcel2=True)

    # 获取期权数据
    df = t1.get_contract_today_tick(contracts=['au2212C408', 'IO2209-C-4400'], fields=['instr_id', 'lastprice'], is_option=True)
    df = t1.get_symbol_his_tick(from_date=20210301, to_date=20210307, symbols=['pg', 'MO'], is_option=True)
    df = t1.get_contract_his_tick(from_date=20210301, to_date=20210307, contracts=['pg2209-C-5250', 'MO2208-C-6800'], is_option=True)
```


