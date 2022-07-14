This is a system buffer account. It stored the fund before paying to the Public Services and Global Token dividend.

At the end of every day, a cron job dump all fund from [[Collection_Pool]] to this Distribution_pool. If the balance is higher than the [[Distribution_pool_overflow]] (this number is subject to change), it will send exceed fund to [[Global_bonding_curve]] as dividend to all Global token investors. If the balance is lower than [[Distribution_pool_overflow]], see [[Corner_cases#Distribution pool cannot cover public service]]
