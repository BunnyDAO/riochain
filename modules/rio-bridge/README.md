## Rio Bridge

This module is to implement the cross-chain deposit and withdraw. This is the first version to make the  business logic work. The next version will be more decentralized.

Rio Bridge will implemented with a federated module, and we have decideded to not integrate a light node in the chain and 
该模块主要实现跨链资产的充值与提现，目前还需要与中心化的后台管理钱包配合使用。当中心化的钱包收到对应的充值提现请求后，经过验证将由指定权限的用户发送相关的deposit/withdrwal请求。

对于一次性超过30 BTC的请求，需要列入白名单中才可以正常充值。列入白名单的方式为调用mark_white的接口。如果一个地址提交了一个超过30 BTC的充值请求，但还未通过白名单，则会将其充值标记为pending状态。在将其列入白名单之后，将会自动完成之前的充值请求。

## storage：
AssetId : assetid of sbtc

Paused : Overall swith
        
List : Known List of KYC (whitelist users)
        
Threshold : kyc threshold
        
Admins : admin account of kyc management
        
PendingDepositList : 触发了KYC还没有被认证的存款记录
        
DepositHistory : deposit history
        
PendingWithdraws : pending withdraws
        
PendingWithdrawVault : sbtc vault for withdraw

## interface ：

pause() : pause, root only

resume() : resume，root only

deposit(account_id, amount, tx_hash) : 存入BTC，要提供用户在RIO的用户accountid，金额和BTC的交易hash，只有bridge的admin可以调用

refund(account, amount) : 提现失败的时候返还用户SBTC, 只有bridge的admin可以调用

withdraw_finish(account, amount) : 用户提现成功，只有bridge的admin可以调用

withdraw(amount) : 用户提交提现申请，相应数量的SBTC会自动从用户账户转入vault

mark_black(account) : KYC标记account为黑名单，pending的deposit不会生成SBTC给用户

mark_white(account) : KYC标记account为白名单，pending的deposit会生成SBTC给用户

delete_admin(account) : 删除某一个admin账户，需要root权限

update_admin(account, auth) : 添加/修改admin账户和对应的auth，需要root权限

## event：(rename are required in the next version to follow a event name standard)

AccountMarked(accountid, black_or_white) : triggered when KYC user marked 

Deposit(accountid, balance, txhash) : triggered when KYC deposit

Pending(accountid, balance, txhash)

PendingWithdraw(accountid, balance) : triggered when a user submmited a pending withdraw request

Refund(accountid, balance) : trigged when a sbtc withdrawal request failed

Withdraw(accountid, balance) : triggered when user withdraw
