# Tobet Crash Verify

验证工具网页请访问：https://tobetone.github.io/CrashGame/

在使用此网页工具之前，请仔细阅读以下说明。你可以根据如下说明，自行开发程序验证。
## 开奖结果计算
  1. 根据游戏数据生成随机种子,并签名：  
    seed = GameId+BetPlayersCount+BetAmount+LastBetTime;  
    seed_sign = sign(seed)  
  2. 对随机种子hash（SH256）运算，结果转换为16进制  
    hash_hex = hex(sha256(seed_sign))   
  4. 截取hash_hex前13位参与计算，计算结果为1到2^52/100的一个双曲函数  
  
```javascript
if(hashcode(hash_hex)%101 == 0){  
    result = 1.00;  
}else{  
    h = Long.parseLong(hash_hex.slice(0, 52 / 4), 16);  
    e = Math.pow(2, 52);  
    result = Math.floor((100 * e - h) / (e - h)) / 100;  
}
```
## 随机因子说明
   seed = GameId+BetPlayersCount+BetAmount+LastBetTime
*  GameId:游戏ID
*  BetPlayersCount:参与游戏玩家个数
*  BetAmoun:本局游戏玩家总投注金额
*  LastBetTime:最后一位投注的玩家的投注时间
## 签名验证
   验证签名使用Tobet的公钥（EOS6CG8VwJ8G1iFn6x781PMojmfD7i4kqqzsgd1AjWwAaEz35QGhn），对seed_sign进行ecc签名验证，验证结果通过即可证明随机种子未被篡改。
