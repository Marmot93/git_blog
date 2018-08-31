---
title: redis笔记
date: 2018-08-15 11:03:12
tags: redis
---
## HyperLogLog
HyperLogLog UV数据统计 下低位连续零位的最大长度 k，通过这个 k 值可以估算出随机数的数量 
## Bloom Filter
判断某个对象是否存在时 存在的一定存在，不存在的可能存在 使用大型无偏hash函数
## 限流
限制用户一段时间操作频率
```python
# coding: utf8

import time
import redis

client = redis.StrictRedis()


def is_action_allowed(user_id, action_key, period, max_count):
    key = 'hist:%s:%s' % (user_id, action_key)
    now_ts = int(time.time() * 1000)  # 毫秒时间戳
    with client.pipeline() as pipe:  # client 是 StrictRedis 实例
        # 记录行为
        pipe.zadd(key, now_ts, now_ts)  # value 和 score 都使用毫秒时间戳
        # 移除时间窗口之前的行为记录，剩下的都是时间窗口内的
        pipe.zremrangebyscore(key, 0, now_ts - period * 1000)
        # 获取窗口内的行为数量
        pipe.zcard(key)
        # 设置 zset 过期时间，避免冷用户持续占用内存
        # 过期时间应该等于时间窗口的长度，再多宽限 1s
        pipe.expire(key, period + 1)
        # 批量执行
        _, _, current_count, _ = pipe.execute()
    # 比较数量是否超标
    return current_count <= max_count


for i in range(20):
    print(is_action_allowed("laoqian", "reply", 60, 5))

```
漏斗限流
```python
import time


class Funnel(object):

    def __init__(self, capacity, leaking_rate):
        self.capacity = capacity  # 漏斗容量
        self.leaking_rate = leaking_rate  # 漏嘴流水速率
        self.left_quota = capacity  # 漏斗剩余空间
        self.leaking_ts = time.time()  # 上一次漏水时间

    def make_space(self):
        now_ts = time.time()
        delta_ts = now_ts - self.leaking_ts  # 距离上一次漏水过去了多久
        delta_quota = delta_ts * self.leaking_rate  # 又可以腾出不少空间了
        if delta_quota < 1:  # 腾的空间太少，那就等下次吧
            return
        self.left_quota += delta_quota  # 增加剩余空间
        self.leaking_ts = now_ts  # 记录漏水时间
        if self.left_quota > self.capacity:  # 剩余空间不得高于容量
            self.left_quota = self.capacity

    def watering(self, quota):
        self.make_space()
        if self.left_quota >= quota:  # 判断剩余空间是否足够
            self.left_quota -= quota
            return True
        return False


funnels = {}  # 所有的漏斗


# capacity  漏斗容量
# leaking_rate 漏嘴流水速率 quota/s
def is_action_allowed(user_id, action_key, capacity, leaking_rate):
    key = '%s:%s' % (user_id, action_key)
    funnel = funnels.get(key)
    if not funnel:
        funnel = Funnel(capacity, leaking_rate)
        funnels[key] = funnel
    return funnel.watering(1)


for i in range(20):
    print(is_action_allowed('laoqian', 'reply', 15, 0.5))

```

#### redis中直接使用(需要安装redis-cell)  
```cl.throttle user:reply 15 30 60 1```  
用户回复 容量15 30次操作60s 需要容量1可选参数

## GeoHash（附近的**）
在 Redis 的集群环境中，集合可能会从一个节点迁移到另一个节点，如果单个 key 的数据过大，会对集群的迁移工作造成较大的影响，
在集群环境中单个 key 对应的数据量不宜超过 1M，否则会导致集群迁移出现卡顿现象，影响线上服务的正常运行，如果数据量过亿甚至更大，
就需要对 Geo 数据进行拆分，按国家拆分、按省拆分，按市拆分，在人口特大城市甚至可以按区拆分
建议 Geo 的数据使用单独的 Redis 实例部署，不使用集群环境  
  
  
geoadd 指令携带集合名称以及多个经纬度名称三元组，注意这里可以加入多个三元组  
geodist 指令可以用来计算两个元素之间的距离，携带集合名称、2 个名称和距离单位  
geopos 指令可以获取集合中任意元素的经纬度坐标，可以一次获取多个    
geohash 可以获取元素的经纬度编码字符串  
georadiusbymember 指令是最为关键的指令，它可以用来查询指定元素附近的其它元素，三个可选参数 withcoord `withdist` withhash 用来携带附加参  
georadius 根据坐标值来查询附近的元素
zrem key member 删除  