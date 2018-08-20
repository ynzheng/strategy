## 官方说明: [http://blockquants.com/grid.html](http://blockquants.com/grid.html)
## 策略说明
### 策略标签
提供流动性，挂单实现低买高卖。

### 策略容量
无限制。

### 代码安全
已经通过生产环境实盘测试，表现良好。不惧极端行情，盘口，断网。

### 策略介绍

网格交易指以某个价位为基点，每上涨或下跌一定点数挂一定数量空单或多单，设定盈利目标，适合震荡市场。网格交易的具体过程为：

1.	设定一个资产的预期价格S0。（当资产的市场价格等于该预期价格时，策略的持仓为0。当资产的市场价格高于该预期价格时，策略会持有资产的空头。相反，当资产的市场价格低于该预期价格时，策略会持有多头。）

2.	设定网格的宽度W和每次挂单的量V。以BTC期货为例，设定S为$8000，W为$100，V为100张（标准期货合约，一张合约对应1美元的BTC）。挂单基本过程如下：

	（1）在$8100的价位挂100张卖单，在$7900的价位挂100张买单。

	（2）当挂单成交后，进行补挂单。若$8100的卖单被成交，则补挂$8000的买单和$8200的卖单；若$7900的买单被成交，则补挂$7800的买单和$8000的卖单。以此类推，实现对BTC的低买高卖。

3．程序化挂单，自动化程序根据实时的委托、成交和持仓信息，进行网格挂单和补挂单。

![grid](http://BlockQuants.com/img/grid.png)

 - 网格交易策略适用于短期价格波动剧烈，价格水平上下往复的资产。通过低买高卖在资产价格的上下震荡之中获取买卖价差。

 - 网格交易在当前数字货币市场收益率较为可观。以BTC/USD为例，在中轴（S0）正负800美元内，每隔30美元挂单，超过设定范围停止交易，保证低杠杆。一天可以成交20-30个来回，平均每天千分之五的回报。 如果选择货币对网格交易（如ETH/BTC）作为网格策略的交易对象，则策略的风险会更低，但预期收益会有所下降。

### 策略风险

虽然从长期看，网格交易策略的收益水平较高，但当出现市场的单边行情时会策略造成损失。因此，需要通过严格控制网格交易策略风险（设置阈值保证永不爆仓），以保证策略的可持续性。
我们建议的网格交易的风险管理方法包括：

1.调整网格宽度或每次的挂单量，使得对于相同的资金量，扩大挂单的价格区间。

2.设定最大持仓量，避免爆仓风险。

## 操作说明
### 参数设定

网格策略的参数包括：

- 挂单量：每一次挂单的委托数量（对于挂单量过小的委托，部分交易所会视为垃圾委托。以bitmex的XBT合约为例，委托量小于25手的委托被视为垃圾委托。）
- 中心价位：当资产价格在中心价位时，策略的持仓量为0。
- 挂单间隔：网格的宽度。
- 最大（最小）持仓：为了控制风险，当策略持仓达到最大持仓时，不再买入资产；当策略持仓达到最小持仓时，不再卖出资产。

网格策略的参数与挂单表是一一对应的，一组参数决定了唯一的一张挂单表。假设参数设定如下：

![para](http://BlockQuants.com/img/grid_par.png)
<div align="center">
<img src="http://BlockQuants.com/img/grid_par.png"  >

 </div>

则对应的挂单表如下：

![table](http://BlockQuants.com/img/grid_table.png)

挂单表决定了策略的执行过程。当策略启动或者账户的持仓发生变化时，程序会根据账户的持仓和挂单表找到要挂的买卖单的委托价格。例如目前账户持仓为60，则根据挂单表得到的买卖挂单价格分别为7840和7900。若此时这两个价位没有已挂单，则程序会自动进行挂单。

### 操作流程

在打开策略界面之后：

![ui](http://BlockQuants.com/img/grid_ui.png)

- 点击账户登录：程序会读取connect.json中的参数，并尝试从交易所获取账户信息。如果登录信息和网络连接正常，则策略日志会显示登录成功。
- 设定策略参数：参照上一节的内容，根据自身的需求设定策略参数。在设定好参数后，点击生成挂单表。则程序会将生成的挂单表显示在右侧。
- 点击开始执行：程序会按照挂单表确定的规则进行自动挂单。每次挂单，策略日志都会显示挂单的具体参数。
- 中止执行：点击中止执行，策略会暂停所有的下单。
- 全部撤单：点击全部撤单，策略会将所有活动委托撤销。
- 成交记录导出：将近日的所有成交导出到根目录的trade_recording.csv文件之中。