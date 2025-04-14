# 连接

redis-cli

monitor

# Redis

基本介绍

Redis 安装好后，默认有 16 个数据库，初始默认使用 0 号库, 编号是 0...15

## **基本命令**

keys *：查看redis当前所有key

select index：切换redis数据库

dbsize：查看当前数据库的 key-val 数量

flushdb/flushall：清空当前数据库的key-val/清空所有数据库的key-val

## String

set/get/mset/mget/del

```redis
set name zs
set name 10 zs
get name

del name

mset name zs age 18
mget name age
```

==set[如果存在就相当于修改，不存在就是添加]==

## Hash

hset/hget/hmset/hmget/hgetall/hdel/hlen/hexist

```
hset user1 name zs
hset user1 age 10
hget user1 name

hmset user1 name zs age 10
hmget user1 name age
hgetall user1
hdel user1
hlen user1
hexist user1 name
```

## List

lpush/rpush/lrange/lpop/rpop/del/llen

```
lpush city a b c
rpush city d e f

lrange city 0 -1

lpop city
rpop city

del city
```

lrange使用：左边第一个元素下标为0，右边第一个元素下标为-1

## 12.2.5 Set

go连接RedisRedis 的 Set 是 string 类型的无序集合，不可重复

sadd/smembers/sismember/serm

```
sadd emails a b c

smembers emails

sismember emails a

serm emails a
```
