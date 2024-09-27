---
title: "Redis Data Structure"
description: "Redis数据结据"
tags:
 - redis
---

> Docs
>
> https://github.com/redis/redis-doc/blob/master/topics/data-types-intro.md
>
> https://github.com/redis/redis-doc/blob/master/topics/data-types.md

> Learning Test Env
>
> ```bash
> docker run --rm -dt --name learning redis:5.0.9 redis-cli && docker exec -it learning redis-cli
> ```

## Note

1. About key

  - `KEY`设置太长不是一件好事，如大于`1024byte`，对于内存及寻找都会有影响，建议可以用`SHA1`压缩
  - `KEY`设置太短不是一件好事，因为可读性会变低
  - `KEY`的最大值为`512MB`

2. About creation and removal of keys

  - 当添加值到不存在键中，添加时会创建一个相应数据结构
  - 当删除值使键中数据类型为空，键会自动销毁，`Stream`除外
  - 调用如`LLEN`空键只读命令或删除命令，总返回与空链一致结果

  ```
  127.0.0.1:6379> get a
  (nil)
  127.0.0.1:6379> lpush a a
  (integer) 1
  127.0.0.1:6379> get a
  (error) WRONGTYPE Operation against a key holding the wrong kind of value
  127.0.0.1:6379> lpop a
  "a"
  127.0.0.1:6379> get a
  (nil)
  ```


## Commands

- `EXISTS KEY [KEY ...]`
- `TYPE KEY`
- `DEL KEY [KEY ...]`
- `EXPIRE KEY SECONDS`
- `TTL KEY`
- `PEXPIRE KEY MILLISECONDS`
- `PTTL KEY`
- `PERSIST KEY`

```
127.0.0.1:6379> set ex a
OK
127.0.0.1:6379> del ne
(integer) 0
127.0.0.1:6379> type ex
string
127.0.0.1:6379> type ne
none
127.0.0.1:6379> exists ex
(integer) 1
127.0.0.1:6379> exists ne
(integer) 0
127.0.0.1:6379> del ex
(integer) 1
127.0.0.1:6379> del ne
(integer) 0
127.0.0.1:6379> set ep a
OK
127.0.0.1:6379> expire ep 5
(integer) 1
127.0.0.1:6379> get ep
"a"
127.0.0.1:6379> get ep
(nil)
127.0.0.1:6379> set ep a
OK
127.0.0.1:6379> expire ep 100
(integer) 1
127.0.0.1:6379> ttl ep
(integer) 97
127.0.0.1:6379> pttl ep
(integer) 82937
127.0.0.1:6379> persist ep
(integer) 1
127.0.0.1:6379> persist ep
(integer) 0
127.0.0.1:6379> pttl ep
(integer) -1
127.0.0.1:6379> ttl ep
(integer) -1
```

## String

Binary-safe strings.

### Note

- About value

    - `VALUE`最大值为`512MB`

### Commands

- `SET KEY VALUE [expiration EX seconds|PX milliseconds] [NX|XX]`
- `GET KEY`
- `INCR KEY`
- `INCRBY KEY INCREMENT`
- `DECR KEY`
- `DECRBY KEY DECREMENT`
- `MSET KEY VALUE [KEY VALUE ...]`
- `MGET KEY [KEY ...]`

```
127.0.0.1:6379> set t1 1
OK
127.0.0.1:6379> get t1
"1"
127.0.0.1:6379> incr t2
(integer) 1
127.0.0.1:6379> incrby t2 10
(integer) 11
127.0.0.1:6379> decr t2
(integer) 10
127.0.0.1:6379> decrby t2 10
(integer) 0
127.0.0.1:6379> decr t2
(integer) -1
127.0.0.1:6379> incr t1
(integer) 2
127.0.0.1:6379> get t1
"2"
127.0.0.1:6379> set t3 a
OK
127.0.0.1:6379> incr t3
(error) ERR value is not an integer or out of range
127.0.0.1:6379> mset t4 1 t5 b
OK
127.0.0.1:6379> mget t4 t5 t6
1) "1"
2) "b"
3) (nil)
127.0.0.1:6379> set t7 c nx
OK
127.0.0.1:6379> set t8 c xx
(nil)
127.0.0.1:6379> mget t7 t8
1) "c"
2) (nil)
```

## List

collections of string elements sorted according to the order of insertion. They are basically linked lists.

### Note

  - 内部车现为链表结构

### Commands

- `LPUSH KEY VALUE [VALUE ...]`
- `RPUSH KEY VALUE [VALUE ...]`
- `LRANGE KEY START STOP`
- `LTRIM KEY START STOP`
- `LPOP KEY`
- `RPOP KEY`
- `BLPOP KEY [KEY ...] TIMEOUT`
- `BRPOP KEY [KEY ...] TIMEOUT`
- `LLEN KEY`

```
127.0.0.1:6379> lpush tl 1 2 3 4 a b c d
(integer) 8
127.0.0.1:6379> rpush tl q w e r 5 6 7 8
(integer) 16
127.0.0.1:6379> lrange tl 0 -1
 1) "d"
 2) "c"
 3) "b"
 4) "a"
 5) "4"
 6) "3"
 7) "2"
 8) "1"
 9) "q"
10) "w"
11) "e"
12) "r"
13) "5"
14) "6"
15) "7"
16) "8"
127.0.0.1:6379> ltrim tl 1 -2
OK
127.0.0.1:6379> lrange tl 0 -1
 1) "c"
 2) "b"
 3) "a"
 4) "4"
 5) "3"
 6) "2"
 7) "1"
 8) "q"
 9) "w"
10) "e"
11) "r"
12) "5"
13) "6"
14) "7"
127.0.0.1:6379> lpop tl
"c"
127.0.0.1:6379> rpop tl
"7"
127.0.0.1:6379> lrange tl 0 -1
 1) "b"
 2) "a"
 3) "4"
 4) "3"
 5) "2"
 6) "1"
 7) "q"
 8) "w"
 9) "e"
10) "r"
11) "5"
12) "6"
127.0.0.1:6379> del tl
(integer) 1
127.0.0.1:6379> lrange tl 0 -1
(empty list or set)
127.0.0.1:6379> blpop tl 1
(nil)
(1.00s)
127.0.0.1:6379> blpop tl 10
1) "tl"
2) "10"
(8.31s)
127.0.0.1:6379> brpop tl ne 10
1) "tl"
2) "ee"
(5.93s)
127.0.0.1:6379> brpop tl ne 10
1) "tl"
2) "e2"
127.0.0.1:6379> lpush tl 1 2 3
127.0.0.1:6379> llen tl
(integer) 3
```

## Set

collections of unique, unsorted string elements.

## Sorted Set

similar to Sets but where every string element is associated to a floating number value, called score. The elements are always taken sorted by their score, so unlike Sets it is possible to retrieve a range of elements (for example you may ask: give me the top 10, or the bottom 10).

## Hashe

which are maps composed of fields associated with values. Both the field and the value are strings. This is very similar to Ruby or Python hashes.

## Bit Array

it is possible, using special commands, to handle String values like an array of bits: you can set and clear individual bits, count all the bits set to 1, find the first set or unset bit, and so forth.

## HyperLogLogs

this is a probabilistic data structure which is used in order to estimate the cardinality of a set. Don't be scared, it is simpler than it seems... See later in the HyperLogLog section of this tutorial.

## Streams

append-only collections of map-like entries that provide an abstract log data type. They are covered in depth in the Introduction to Redis Streams.

