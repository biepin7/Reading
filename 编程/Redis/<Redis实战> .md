[TOC]
# 入门
# 章一 初识Redis

Redis is an **in-memory remote database** that offers high performance（性能）, replication, and a unique data model to produce a platform for solving problems.

？ ：having to perform the conceptual gymnastics 

![](https://gitee.com/biepin/imgurl/raw/master/20201126135352.png)

Additional features like replication, **persistence持久化**, and **client-side sharding客户端分片** allow Redis to scale from a convenient way to prototype a system, all the way up to hundreds of gigabytes of data and millions of requests per second.



### 1.1.2 附加特性
使用内存数据库时候：“当服务器被关闭时，服务器存储的数据将何去何从”
Redis有两种持久化方法：

The first method is a point-in-time dump either when certain conditions are met (**a number of writes in a given period**) or when **one of the two dump-to-disk commands is called.**

1. 时间点转储（point-in-time dump）,转储操作即可以在 “**在指定时间短哪有指定数量的写操作执行**”或者调用两条转储到硬盘命令中的任何一条命令来执行

2. 第二种持久化方法将**所有修改了数据库的命令都写入一个只追加( append-only)文件里面**,用户可以根据数据的重要程度，将只追加写人设置为从不同步(sync)、每秒同步一次或者每写入一个命令就同步一次。

**The other method uses an append-only file that writes every command that alters data in Redis to disk as it happens.** Depending on how careful you want to be with your data, append-only writing can be configured to never sync, sync once per second, or sync at the completion of every operation.



 To support higher rates of read performance (along with **handling failover 故障转移** if the server that Redis is running on crashes), Redis supports **master/slave replication 主从复制** where slaves connect to the master and receive an initial copy of the full database. 

之后主服务器执行的写命令，都会被发送给所有连接着的从服务器去执行，从而实时地更新从服务器的数据集。因为从服务器包含的数据会不断地进行更新，所以客户端可以向任意一个从服务器发送读请求，以此来避免对主服务器进行集中式的访问。

### 1.1.3 使用Redis的理由

数据库的一个常见用法是**存储长期的报告数据**，并将这些报告数据用作固定时间范围内的**聚合数据( aggregates )**。

One common use of databases is to **store long-term reporting data** as **aggregates** over fixed time ranges.

收集聚合数据的常见做法是:先将各个行插入一个报告表里面，之后再通过扫描这些行来收集聚合数据,并根据收集到的聚合数据来更新聚合表中已有的那些行。

To collect these aggregates, it’s not uncommon to insert rows into a reporting table and then later to scan over those rows to collect the aggregates,which then update existing rows in an aggregation table. 

之所以使用插入行的方式来存储，是因为对于大部分数据库来说，插入行操作的执行速度非常快(插入行只会在硬盘文件末尾进行写入)。

Rows are inserted because, for most databases, inserting rows is a very fast operation (inserts write to the end of an on-disk file, not unlike Redis’s append-only log). 

不过，对表里面的行进行更新却是一个速度相当慢的操作，因为这种更新除了会引起一次随机读( random read) 之外，还可能会引起一次随机写( randomwrite )。

But updating an existing row in a table is fairly slow (it can cause a random read and may cause a random write). 

而在Redis里面，用户可以直接使用原子的( atomic ) INCR命令及其变种来计算聚合数据，并且因为Redis将数据存储在内存里面，而且发送给Redis的命令请求并不需要经过典型的查询分析器( parser )或者查询优化器( optimizer )进行处理，所以对Redis存储的数据执行随机
写的速度总是非常迅速的。

In Redis, you’d calculate the aggregates directly using one of the atomic INCR commands—random writes which then update existing rows in an aggregation table. Rows are inserted because, for most databases, inserting rows is a very fast operation (inserts write to the end of an on-disk file, not unlike Redis’s append-only log). But updating an existing row in a table is fairly slow (it can cause a random read and may cause a random write). 

## 1.2 Redis 数据结构简介

![](https://gitee.com/biepin/imgurl/raw/master/20201126151519.png)

![](https://gitee.com/biepin/imgurl/raw/master/20201126151457.png)

### 1.2.1 Strings in Redis

![](https://gitee.com/biepin/imgurl/raw/master/20201126151612.png)

```
127.0.0.1:6379> set hello world
OK
127.0.0.1:6379> get hello
"world"
127.0.0.1:6379> del hello
(integer) 1
127.0.0.1:6379> get hello
(nil)
```

### 1.2.2 Lists in Redis

![](https://gitee.com/biepin/imgurl/raw/master/20201126151859.png)

```
127.0.0.1:6379> rpush list-key item
(integer) 1
127.0.0.1:6379> rpush list-key item2
(integer) 2
127.0.0.1:6379> rpush list-key item
(integer) 3
127.0.0.1:6379> lrange list-key 0 -1
1) "item"
2) "item2"
3) "item"
127.0.0.1:6379> lindex list-key 1
"item2"
127.0.0.1:6379> lpop list-key
"item"
127.0.0.1:6379> lrange list-key 0 -1
1) "item2"
2) "item"
127.0.0.1:6379>
```

### 1.2.3 Sets in Redis

`list`是可以出现重复值的，而`set`不行（ Redis SETs use a hash table to keep all strings unique）

`list`是有序的（可以r/l），而`set`不行（Because Redis SETs are unordered, we can’t push and pop items from the ends like we did with LISTs.）

We can also find out whether an item is in the SET quickly with `SISMEMBER`, or fetch the entire set with `SMEMBERS` (this can be slow for large SETs, so be careful). 

另外还可以通过`SISMEMBER`命令快速地检查一个元素 是否已经存在于集合中，或者使用`SMEMBERS`命令获取集合包含的所有元素(如果集合包含的元素非常多，那么`SMEMBERS`命令的执行速度可能会很慢，所以请谨慎地使用这个命令)。

![](https://gitee.com/biepin/imgurl/raw/master/20201126153512.png)

```
127.0.0.1:6379> sadd set-key item
(integer) 1
127.0.0.1:6379> sadd set-key item2
(integer) 1
127.0.0.1:6379> sadd set-key item3
(integer) 1
127.0.0.1:6379> sadd set-key item
(integer) 0
127.0.0.1:6379> smembers set-key
1) "item3"
2) "item2"
3) "item"
127.0.0.1:6379> sismember set-key item4
(integer) 0
127.0.0.1:6379> sismember set-key item
(integer) 1
127.0.0.1:6379> srem set-key item2
(integer) 1
127.0.0.1:6379> srem set-key item2
(integer) 0
127.0.0.1:6379> smembers set-key
1) "item3"
2) "item"
```

### 1.2.4 Hashes in Redis

![](https://gitee.com/biepin/imgurl/raw/master/20201126153619.png)

```
127.0.0.1:6379> hset hash-key sub-key1 value1
(integer) 1
127.0.0.1:6379> hset hash-key sub-key2 value2
(integer) 1
127.0.0.1:6379> hset hash-key sub-key1 value1
(integer) 0
127.0.0.1:6379> hgetall hash-key
1) "sub-key1"
2) "value1"
3) "sub-key2"
4) "value2"
127.0.0.1:6379> hdel hash-key sub-key2
(integer) 1
127.0.0.1:6379> hdel hash-key sub-key2
(integer) 0
127.0.0.1:6379> hget hash-key sub-key1
"value1"
127.0.0.1:6379> hgetall hash-key
1) "sub-key1"
2) "value1"
```

### 1.2.5 Sorted sets in Redis

Like Redis HASHes, ZSETs also hold a type of key and value. 

The keys (called **members**) are **unique**, and the values (called scores) are limited to floating-point numbers.

 ZSETs have the unique property in Redis of being able to be accessed by member (like a HASH), but items can also be accessed by the sorted order and values of the scores. 

![](https://gitee.com/biepin/imgurl/raw/master/20201126154230.png)

```
127.0.0.1:6379> zadd zset-key 728 member1
(integer) 1
127.0.0.1:6379> zadd zset-key 728 member0
(integer) 1
127.0.0.1:6379> zadd zset-key 728 member0
(integer) 0
127.0.0.1:6379> zrange zset-key 0 -1
1) "member0"
2) "member1"
127.0.0.1:6379> zrange zset-key 0 -1 withscores
1) "member0"
2) "728"
3) "member1"
4) "728"
127.0.0.1:6379> zadd zset-key 928 member0
(integer) 0
127.0.0.1:6379> zrange zset-key 0 800 withscores
1) "member1"
2) "728"
3) "member0"
4) "928"
127.0.0.1:6379> zrangebyscore zset-key 0 800 withscores
1) "member1"
2) "728"
127.0.0.1:6379> zrem zset-key member1
(integer) 1
127.0.0.1:6379> zrange zset-key 0 -1 withscores
1) "member0"
2) "928"
```

## 1.3 Hello Redis

下面是使用Redis实现 一个简单的文章投票网站的后端

we’ll build a Redis-based back end for a simple version of this kind of site. 



PS：这里使用了四个数据结构，我来进行一下列表：

| 名字          | 类型 | 字段                          | 备注                           |
| ------------- | ---- | ----------------------------- | ------------------------------ |
| article:92617 | HASH | Title, link,poster,time,votes | 用来存储文章的具体内容         |
| time          | zset | article:100408\|1332065417.47 | 存储文章ID和对应创建时间       |
| score         | zset | article:100408\|1332174713.47 | 存储文章ID和对应文章的实时分数 |
| voted:100408  | set  | user:ID                       | 维护文章投票者，创建后一周删除 |



### 1.3.1 对文章进行投票

 Let’s say that 1,000 articles are submitted each day. Of those 1,000 articles, about 50 of them are interesting enough that we want them to be in the top-100 articles for at least one day. All of those 50 articles will receive at least 200 up votes. We won’t worry about down votes for this version.

1. 每天发布100篇文章
2. 其中有50篇符合要求，会被置于top-100列表至少一天
3. 符合要求的条件是：获得了至少200票

为了产生一个能够随着时间流逝而不断减少的评分，程序需要根据文章的发布时间和当前时间来计算文章的评分，具体的计算方法为:将文章得到的支持票数量乘以一个常量，然后加上文章的发布时间，得出的结果就是文章的评分。

我们使用从UTC时区1970年1月1日到现在为止经过的秒数来计算文章的评分，这个值通常被称为Unix时间。

之所以选择使用Unix时间，是因为在所有能够运行Redis 的平台上面，使用编程语言获取这个值都是一件非常 简单的事情。

另外,计算评分时与支持票数量相乘的常量为432,这个常量是通过将一天的秒数( 86400 )除以文章展示一天所需的支持票数量(200)得出的:文章每获得一张支持票，程序就需要将文章的评分增加432分。

构建文章投票网站除了需要计算文章评分之外，还需要使用Redis结构存储网站上的各种信息。

对于网站里的每文章，程序都使用一个 `HASH`**来存储文章的标题、指向文章的网址、发布文章的用户、文章的发布时间、文章得到的投票数量等信息**

![](https://gitee.com/biepin/imgurl/raw/master/20201126155804.png)

我们的文章投票网站将使用两个有序集合来有序地存储文章:第一个有序集合的成员为文章ID,分值为文章的发布时间;第二个有序集合的成员同样为文章ID,而分值则为文章的评分。

通过这两个有序集合,网站既可以根据文章发布的先后顺序来展示文章，又可以根据文章评分的高低来展示文章

![](https://gitee.com/biepin/imgurl/raw/master/20201126155915.png)

为了防止一个用户对同一篇文章进行投票，需要为每一篇文章记录一个已投票用户名单：

![](https://gitee.com/biepin/imgurl/raw/master/20201130111934.png)

为了节约内存，规定一篇文章发布期满一周，删除投票用户名单，固定其分数不变

When someone tries to vote on an article, we first verify that the article was posted within the last week by checking the article’s post time with ZSCORE.

当用户尝试对一篇文章投票的时候，首先使用`ZSCORE`命令检查文章投票时间是否过期

If we still have time, we then try to add the user to the article’s voted SET with SADD. 

如果没过期，那么我们尝试使用`SADD`命令将用户添加到已投票用户名单的集合里面

Finally, if the user didn’t previously vote on that article, we increment the score of the article by 432 (which we calculated earlier) with ZINCRBY (a command that increments the score of a member), and update the vote count in the HASH with HINCRBY (a command that increments a value in a hash). 

最后，如果用户之前没有为文章投票过，我们使用`ZINCRBY`给文章加分432分，同时使用`HINCRBY`命令更新文章投票数（一个是HASH的文章信息里有投票数，一个是ZSET的文章排名，对应分数）

```python
ONE_WEEK_IN_SECONDS = 7 * 86400 
VOTE_SCORE = 432

def article_vote(conn,user,article):
    cutoff = time.time()-ONE_WEEK_IN_SECONDS #计算文章投票截止时间
    if conn.zscore('time:',article,VOTE_SCORE)<cutoff: #查看是否还能对文章进行投票
        return
    article_id = article.partition(':')[-1] #取出文章ID
    if conn.sadd('voted:'+article_id,user):
        conn.zincrby('score:',article,VOTE_SCORE)
        conn.hincrby(article,'votes',1)
```

### 1.3.2 发布并获取文章

To post an article, we first create an article ID by incrementing a counter with INCR.

未来发布一篇新文章，我们首先需要一个文章ID，通过对一个计数器使用`INCR`命令

We then create the voted SET by adding the poster’s ID to the SET with SADD. 

使用`SADD`命令将发布者ID添加到 the voted SET

To ensure that the SET is removed after one week, we’ll give it an expiration time with the EXPIRE command, which lets Redis automatically delete it. 

为了确保这个 the voted SET 在一周后会自动删除，我们使用`EXPIRE`给这个SET设置一个过期时间

We then store the article information with HMSET.

之后，使用`HMSET`来存储文章信息

Finally, we add the initial score and posting time to the relevant ZSETs with ZADD. 

最后，使用两个`ZADD`来将文章初识评分和发布时间加入ZSETs

```python
# 发布文章
def post_article(conn, user, title, link):
    article_id = str(conn.incr('article:'))  # 生成文章ID

    voted = 'voted:' + article_id
    conn.sadd(voted, user)
    conn.expire(voted, ONE_WEEK_IN_SECONDS)

    now = time.time()  # 取出当前时间
    article = 'article:' + article_id
    conn.hmset(article, {
        'title': title,
        'link': link,
        'poster': user,
        'time': now,
        'votes': 1
    })
    conn.zadd('score:', article, now + VOTE_SCORE)
    conn.zadd('time', article, now)

    return article_id
```

接下来考虑如何去除评分最高的文章和如何取出最新发布的文章

For that, we can use ZRANGE to fetch the articleIDs,

使用`ZRANGE`取出多个文章ID

and then we can make calls to HGETALL to fetch information about each article.

对每个文章ID都执行`HGETALL`来取出文章的详细信息

The only tricky part is that we must remember that ZSETs are sorted in ascending order by their score. But we can fetch items based on the reverse order with ZREVRANGEBYSCORE. 

要注意的是：ZSETs会根据成员的分值从小到大地排列元素，所以我们用`ZREVRANGEBYSCORE`来从大到小的取出数据

### 1.3.3 对文章进行分组 Grouping articles



