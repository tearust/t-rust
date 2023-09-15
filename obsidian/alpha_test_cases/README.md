# 运营初期建议

[[proposal_token_launch]]
# New user earn initial credit


New user has zero TEA balance in both layer1 and layer2. New user can get enough knowledge on how to start. How to start telling the new users to go to TeaFluencer to do a retweet task to earn some initial credit.

The retweet test earns `1` credit. This value is configurable. 

If a user has got some credit or TEA from other referrer by being invited to a Video Conference, this user is no longer a new user. he can not use TEAFluencer to earn more credit. 

# Only new user allow to get credit from TEA Fluencer

If a user has credit or TEA in balance, he is not new user. Only new user can use TeaFlucner to earn some initial credit.

But if the user has used all its credit. The credit balance reduced to zero. He is new user again, and can use TeaFluencer to get more credit.
# DAO Reserve initial  [[dao_reserve_credit_pool_initial_value=100000]] credit for launch

Please fill in the number here. This value is in genesis config.
# Credit usage

Credit can only be used to pay gas fee. Users cannot transfer credit. cannot use credit to buy anything else.

# Credit decay

Credit reduce to 90% of previous balance every hour. **Note: Change to every day when launch alpha**. The reduced credit will go back to the DAO Reserver as TEA token. 

When any user has less than [[minimal_credit_balance=0.01]] credit, reduce it to zero. 


# Use TeaParty video conference to air drop credit
这个想法在视频会议完成后可以实现.

在一段时间内 Promotion period. Host and guests can earn [[meeting_promotion_credit=1]] Credit per minute during any video conference. 

# 推荐人奖励计划 Referrer bonus

Host 发出的Invitation Link. 收到的人如果点击Link并metamask login, 就在后台记录 Host是referrer, Guest是Referee. 不过有如下条件
- 如果这个Referee已经有了Referrer, 这次推荐就无效, 无需修改原有的Referrer关系. 
- 如果这个Guest已经有了> 0 的TEA或者Credit. 他不是新用户. 这个推荐无效

这个Referee和Referrer都可以使用视频会议获得Credit. 暂定 [[meeting_promotion_credit=1]]

Credit可以用于支付Gas, 正常decay

当Referee使用Credit支付Gas的时候. 等量的Gas费作为奖励金, 从DAO Reserve发给Referrer. 如果Referrer不存在就不发了.

奖励计划有时效性. 我们可以先定3个月, 然后Sudo如果不停止就继续延长无限期.


