
[Ripple 创始人：Ripple是比特币的一次演化][1]

[比特币 VS Ripple：不同“货币哲学”的碰撞][2]

[ripple白皮书][3]

[对话ripple发行商二：从网络金融的角度解读比特币][4]

[对话ripple发行商二：ripple和比特币是相反的][5]

[支付在前，虚拟货币在后，ripple和bitcoin有何不同][6]

[比特币媒体报道列表][7]

[ripple骗局揭秘][8]

[采访本杰明·科恩 ，听大师谈RIPPLE][9]

[月球币色色币发行经验][10]

[钱包答疑][11]

[1]: http://www.kuailiyu.com/article/5876.html 
[2]: http://business.sohu.com/20131101/n389359181.shtml
[3]: https://www.xrpchina.net/topic/52938a91aa10520612000010
[4]: http://www.pingwest.com/an-interview-with-chris-larsen-part-1
[5]: http://www.pingwest.com/an-interview-with-chris-larsen-part-2
[6]: http://www.pingwest.com/demo/differences-between-ripple-and-bitcoin
[7]: http://btcchina.org
[8]: http://www.ripplechina.net/forum.php?mod=viewthread&tid=819
[9]: http://p2pbucks.com/?p=2623
[10]: https://ripple.com/forum/viewtopic.php?f=7&t=3240
[11]: http://bitcoin.stackexchange.com/questions/10220/what-is-the-relationship-between-the-ripple-secret-key-and-the-wallet-name-passp


# ripple 程序自动交易套利系统
在ripple交易系统中，gateway扮演了重要的角色，同时也构成了相对封闭的交易生态环境。网关A中的成交价可能比网关B中的成交价低，由于现在ripple中交易成本很低，故可以在不同网关中低买高卖做差价，实现套利

## TODO：做市商
ripple中有做市商的机制，其作用是连接不同网关，实现跨网关交易。本质上和我上述系统是一样的，不知道ripple是否有内置机制实现，现在市场中是否存在做市商，待研究。

## 基本原理
假定市场上有 `g1, g2, g3, ..., gn` 共 n 个网关，则在某一个时间点 t1, 必然存在一系列买入价和卖出价(假设usd/ripple，即以ripple为标的)，且买入价小于等于卖出价：
价格\网关 | g1     | g2    | g3    | ...   | gn    |
--------- | ------ | ----- | ----- | ----- | ----- |
买入价    | \$11   | \$12  | \$14  | ...   | \$12  |  
卖出价    | \$14   | \$13  | \$15  | ...   | \$12  |

假设在 `t2 - t1 = tt` 时间内没有新投资者进入，则上述网关中除 gn 均无法产生成交，但跨网关成交能产生：

- 从 n 个网关中选中最低卖出价，即 min(sell_price), 为 g2.sell_price = 13 
- 从 n 个网关中选中最高买入价，即 max(buy_price), 为 g3.buy_price = 14
 

我们可以从 g2 买入在 g3 卖出，从而实现套利。也就是说只要 max_buy_price > min_sell_price, 我们就可以实现盈利。我们更进一步：

 - 所有市场中卖出价从低到高依次为 s1, s2, s3, .. sm, 其成交量依次为ps1, ps2, ps3, .. psn, 其总价为 s1*ps1 + s2*ps2 + .. sn*psm = sell_amount.
 - 考虑所有市场中买入价从高到低依次为 b1, b2, b3, .. bn, 其成交量依次为pb1, pb2, pb3, .. pbn, 其总价为 b1*pb1 + b2*pb2 + .. bn*pbn = buy_amount.

我们如果可以找到那么一个卖出价 sm和买入价 bn ， 使得定量货币能买到的市值 sell_amout 大于能卖出的市值 buy_amount, 我们也可以实现不同网关间套利。

## 概要设计
### 本地行情系统
实现一个本地行情系统，可以和多网关通信。
### 决策系统
根据上述套利原理做决策，需考虑行情系统延迟及手续费等
### 操作系统
根据决策系统的决策实现自动化交易