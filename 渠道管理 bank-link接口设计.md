##渠道管理 bank-link接口设计

[TOC]

#### 一、接口分类

1. 账户接口
2. 汇率接口
3. 换汇接口
4. 分发(提现)接口



#### 二、账户接口

​	说明：我们在渠道方拥有账户，并可以进行账户余额查询、账户状态查询或其他操作。

每个渠道的账户跟渠道编号(banktype)相关

​	

##### 	1.账户余额查询

​	*BigDecimal queryAccBalance(Integer bankType,String accNo);*

​	__入参__

|  参数名  |  类型   |                     说明                     |
| :------: | :-----: | :------------------------------------------: |
| bankType | Integer |                非空，渠道编码                |
|  accNo   | String  | 可空，在渠道方的账号<br />如果非唯一，则必填 |

##### 	2.账户状态查询

​	**String queryAccState()Integer bankType,String accNo);**

​	__入参__

|  参数名  |  类型   |                     说明                     |
| :------: | :-----: | :------------------------------------------: |
| bankType | Integer |                非空，渠道编码                |
|  accNo   | String  | 可空，在渠道方的账号<br />如果非唯一，则必填 |

##### 	3.最大垫资金额查询

​	*BigDecimal queryMaxOverdraft(Integer bankType,String accNo)*

​	__入参__

|  参数名  |  类型   |                     说明                     |
| :------: | :-----: | :------------------------------------------: |
| bankType | Integer |                非空，渠道编码                |
|  accNo   | String  | 可空，在渠道方的账号<br />如果非唯一，则必填 |

#### 三、汇率接口

#####	1.汇率查询

​	*RateResult queryRate(RateReq req,Integer bankType);*

​	__输入参数：__

|      参数名       |  类型   | 必填 |     说明      |
| :---------------: | :-----: | :--: | :-----------: |
|     bankType      | Integer |  是  |   渠道编码    |
| **RateReq**[^注1] |         |      |   请求实体    |
|    curtypeFrom    | String  |  是  | 卖出币种：CNY |
|     curtypeTo     | String  |  是  | 买入币种：USD |

​	__返回参数：RateResult 实体字段__

|     参数名     |    类型    | 必填 |     说明      |
| :------------: | :--------: | :--: | :-----------: |
|     rateId     |   String   |  否  |    汇率Id     |
|  curtypeFrom   |   String   |  是  | 卖出币种：CNY |
|   curtypeTo    |   String   |  是  | 买入币种：USD |
| settlementRate | BigDecimal |  是  |   结汇汇率    |
|  purchaseRate  | BigDecimal |  是  |   购汇汇率    |
|    rateDate    |    Date    |  是  |   汇率时间    |
|      desc      |   String   |  否  |     说明      |

#### 四、换汇接口

##### 1.询价(可锁价交易)

​	接口说明，根据不同的渠道设计

​	*RatePriceResult queryRatePrice(TradePriceReq req,Integer bankType);*

​	__输入参数：__

|         参数名          |    类型    | 必填 |                    说明                    |
| :---------------------: | :--------: | :--: | :----------------------------------------: |
|        bankType         |  Integer   |  是  |                  渠道编码                  |
| **TradePriceReq**[^注2] |            |      |                  请求实体                  |
|         queryNo         |   String   |  否  | 查询流水号：有些<br>渠道需要我方查询订单号 |
|       curtypeFrom       |   String   |  是  |               卖出币种：CNY                |
|        curtypeTo        |   String   |  是  |               买入币种：USD                |
|      exchangeType       |  Integer   |  否  |    交易类型：<br>1:买入交易  2:卖出交易    |
|       tradeAmount       | BigDecimal |  否  |                  交易金额                  |

​	__返回参数:RatePriceResult实体字段 __

|    参数名    |    类型    | 必填 |                    说明                    |
| :----------: | :--------: | :--: | :----------------------------------------: |
|   queryNo    |   String   |  否  | 查询流水号：有些<br>渠道需要我方查询订单号 |
| curtypeFrom  |   String   |  是  |               卖出币种：CNY                |
|  curtypeTo   |   String   |  是  |               买入币种：USD                |
|  feeCurType  |   String   |  是  |              手续费币种：USD               |
|  feeAmount   | BigDecimal |  否  |                 手续费金额                 |
|  payAmount   | BigDecimal |  是  |                 支付总金额                 |
|     rate     | BigDecimal |  是  |                  询价汇率                  |
| exchangeType |  Integer   |  否  |    交易类型：<br>1:买入交易  2:卖出交易    |
|     date     |    Date    |  否  |                    时间                    |

##### 2.创建换汇单

​	*TradeResult createExchangeOrder(ExchangeOrderReq req, String bankType);*

​	__输入参数：__

|        参数名        |    类型    | 必填 |                 说明                 |
| :------------------: | :--------: | :--: | :----------------------------------: |
|       bankType       |  Integer   |  是  |               渠道编码               |
| **ExchangeOrderReq** |            |      |               请求实体               |
|       orderNo        |   String   |  是  |              交易流水号              |
|     curtypeFrom      |   String   |  是  |            卖出币种：CNY             |
|      curtypeTo       |   String   |  是  |            买入币种：USD             |
|     exchangeType     |  Integer   |  否  | 交易类型：<br>1:买入交易  2:卖出交易 |
|     tradeAmount      | BigDecimal |  否  |               交易金额               |
|         rate         | BigDecimal |  否  |               交易汇率               |
|        rateId        |   String   |  否  |              交易汇率ID              |
|     queryRateNo      |   String   |  否  |          锁汇流水号(如需要)          |

​	__返回参数：__

|    参数名    |    类型    | 必填 |    说明    |
| :----------: | :--------: | :--: | :--------: |
|     code     |  Integer   |  是  |  结果编码  |
|   message    |   String   |  否  |  结果说明  |
|    rateId    |   String   |  否  | 成交汇率ID |
|     rate     | BigDecimal |  否  |  交易汇率  |
| bankSerialNo |   String   |  否  | 渠道流水号 |



##### 3.查询交易单

​	*QueryResult queryExchangeOrder(Integer bankType,String orderNo)*

​	__输入参数：__

|    参数名    |  类型   | 必填 |    说明    |
| :----------: | :-----: | :--: | :--------: |
|   bankType   | Integer |  是  |  渠道编码  |
|   orderNo    | String  |  否  | 交易流水号 |
| bankSerialNo | String  |  否  | 渠道流水号 |

​	__返回参数：__

| 参数名 | 类型 | 必填 | 说明 |
| :----: | :--: | :--: | :--: |
|        |      |      |      |
|        |      |      |      |
|        |      |      |      |
|        |      |      |      |
|        |      |      |      |
|        |      |      |      |
|        |      |      |      |
|        |      |      |      |



#### 五、分发接口

##### 1.单笔分发

##### 2.批量分发

##### 3.交易查询











[^注1]: 以后根据需求增加字段，为2个字段创建一个实体，防止以后不同渠道会新增字段，有新需求字段时，只需增加字段而不用修改接口。
[^注2]: 以后根据需求增加字段(增而不减)