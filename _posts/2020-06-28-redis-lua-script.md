---
title: Redis原子性事务Lua应用
author: 谇雨
layout: post
permalink: /redis-lua-script.html
categories:
  - Linux
tags:
  - redis
  - lua
---

Redis 平时用的挺多，大多是情况用于队列消费和缓存，很少用上事务，最近因为队列高并发消费问题，处理完成后整理出相关的信息；

### 背景

大量业务需要从 Redis List Pop 出任务进行处理，部分任务处理可能出现因为第三方接口频率限制或者异常等情况，需要进行重试。那在高并发消费的情况下就可能出现「从redis pop后的任务因为tomcat重启等各种异常丢失」因此我们需要一个事务，当消费失败以后，回滚任务；

### 问题

Redis有MULTI事务，但实际上是Pineline模式，中途是无法获取到返回值的做下一步业务逻辑判断的，同时担心Pop出的任务因为消费时间长，服务重启等干扰因素，需要一个类似 `Kafka` 的消费完成后删除任务的机制；

### 方案一

可以利用`LPOPRPUSH`这样的命令进行操作，丢到一个备用队列，消费完成去备用队列删除，完成一次类似于「Commit」操作，此方案有几个问题  
1. 利用`LPOPRPUSH`无法更改 List Value 的值，也就是List信息没法修改，并不知道这个Value的业务属性，比如更新一个任务有效时间；
2. LREM 操作 List 删除值在List长度特别长的时候计算复杂度非常之高，并发高时特别消耗Redis CPU；

### 方案二

因为Redis事务支持的是pineline模式，无法获取返回值的，其实不满足原子性，为了避免这样的问题，需要引入 Lua 脚本，由于 Lua 脚本是提交到 Redis server 进行一次性执行的，整个执行过程中不会被其他任何工作打断，其它任何脚本或者命令都无法执行，也就不会引起竞争条件，从而本身就实现了事务的原子性。

那么我们可以利用 LuaScript 实现pop出队列的值，进入以当前时间戳为Score的ZSet，若30秒内为完成任务作业，我们利用同样的方式把 ZSet 中的 Score 超时任务还原回原来的队列；

```
    private static final DefaultRedisScript<List<String>> leftPopAndAddIntoZSetRedisScript = new DefaultRedisScript<>("" +
            "local values = {}; " +
            "for i = 1, ARGV[1], 1 do " +
            "   local value = redis.call('LPOP', KEYS[1]); " +
            "   if not value then " +
            "       return values; " +
            "   end; " +
            "   redis.call('ZADD', KEYS[2], ARGV[2], value); " +
            "   table.insert(values, value); " +
            "end; " +
            "return values;", (Class<List<String>>) Arrays.<String>asList().getClass());

    private static final DefaultRedisScript<Long> popZSetAndRightPushRedisScript = new DefaultRedisScript<>("" +
            "local set = redis.call('ZRANGEBYSCORE', KEYS[1], '0', ARGV[1]); " +
            "for k, v in pairs(set) do " +
            "    redis.call('ZREM', KEYS[1], v); " +
            "    redis.call('RPUSH', KEYS[2], v); " +
            "end; " +
            "return table.getn(set); ", Long.class);

    public String leftPopAndAddIntoZSet(String listKey, String zSetKey) {
        List<String> values = leftPopAndAddIntoZSet(listKey, zSetKey, 1);
        return (values.isEmpty()) ? null : values.get(0);
    }

    public List<String> leftPopAndAddIntoZSet(String listKey, String zSetKey, int sizes) {
        return stringRedisTemplate.execute(leftPopAndAddIntoZSetRedisScript, Arrays.asList(listKey, zSetKey), String.format("%s", sizes), String.format("%s", Instant.now(clock).toEpochMilli()));
    }

    public long popZSetAndRightPush(String zSetKey, String listKey, long maxScore) {
        return stringRedisTemplate.execute(popZSetAndRightPushRedisScript, Arrays.asList(zSetKey, listKey), String.format("%s", maxScore));
    }
```
