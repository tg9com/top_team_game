# Top Team(TT) 游戏生态

#### 游戏玩法

10名玩家组成一局进行游戏, 每次下注20USDT, 每次产生一个中奖玩家, 中奖玩家获得矿机, 其余玩家返回本金并获得2USDT打赏, 单需要支付0.2矿工费

可质押 USDT 自动参与. 当天游戏结束, 返回质押 USDT, 并给予未中奖场次打赏(扣除矿工费), 给予中奖场次矿机, 并扣除中奖场次USDT

#### 思路

###### 单独服务

手动玩家点击参与即可排队, 无论本场次是否人数已足够. 按排队顺序分批次开奖(如果人数一直充足就是每30秒开奖一次).

初步考虑, 自动参与游戏玩家每30秒(后台指定)自动参与排队.

今日开奖时间结束后, 质押玩家(自动参与)与已排队但未参与的玩家进行自动退还.

- 问题点:
    - 点击参与的间隔时间, 假设玩家非常多, 玩家点击参与很久才能开奖, 玩家自己多次点击参与并且都未开奖, 那么这个玩家点击参与时必须间隔9人, 为了不使自己的两次排队参与到同一场次
    - 质押玩家是自动参与场次完成后就退还, 还是统一在当天游戏结束后退还.

###### websocket

进入游戏页面 和 参与游戏后都会自动检测余额, 余额不足则自动踢出.

每场次实时参与实时开奖, 如果点击了已满员或者正在开奖的场次则无法参与.

自动参与玩家在场次开始时随机进入.

- 问题点:
    - 自动参与问题, 若自动参与的玩家自动进入场次, 如果自动参与人数过多, 则会导致手动参与玩家无法参与
    - 多人同时点击异常情况, 若N多人同时要参加同一场次, 可能会导致数据处理中时又出现新的参与请求发送, 导致数据异常.

##### 疑问

1. 游戏每30秒一局, 应该可以理解为, 如果30秒内玩家占满, 那么到30秒开奖, 如果30秒后玩家没占满, 那么等到玩家占满立即开奖.
2. 自动质押玩家能不能手动参与


#### 质押自动游戏说明

质押参与后, 生成一个质押记录, 每分钟执行参与函数, 质押记录仅增加参与已玩局数, 游戏队列中增加一条数据, 当进行游戏并结算时, 自动参与的玩家不发放奖金和矿机, 而是在质押记录中修改数据(手动参与的会发放).