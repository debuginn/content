---
title: "浅析悲观锁与乐观锁"
date: 2022-03-16T19:45:55+08:00
keywords: "mysql,occ,pcc"
comments: true
weight: 0
tags: ["mysql","occ","pcc"]
categories: ["sql"]
image: "https://webp.debuginn.com/202303161947714.jpg"
---

在关系型数据库中，悲观锁与乐观锁是解决资源并发场景的解决方案，接下来将详细讲解一下这两个并发解决方案的实际使用及优缺点。

首先定义一下数据库，做一个最简单的库存表，如下设计：

```sql
CREATE TABLE `order_stock` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT 'ID',
  `oid` int(50) NOT NULL COMMENT '商品ID',
  `quantity` int(20) NOT NULL COMMENT '库存',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
```

`quantity`代表着不同商品 oid 的库存，接下来 OCC 及 PCC 使用此数据库进行演示。

## 乐观锁 OCC

它假设多用户并发的事务**在处理时不会彼此互相影响**，各事务能够**在不产生锁的情况下处理各自影响的那部分数据**。在提交数据更新之前，每个事务会先检查在该事务读取数据后，有没有其他事务又修改了该数据。如果其他事务有更新的话，正在提交的事务会进行回滚。

即“乐观锁”认为拿锁的用户多半是会成功的，因此在进行完业务操作需要实际更新数据的最后一步再去拿一下锁就好。这样就可以避免使用数据库自身定义的行锁，可以避免死锁现象的产生。

```sql
UPDATE order_stock SET quantity = quantity - 1 WHERE oid = 1 AND quantity - 1 > 0; 
```

乐观并发控制多数**用于数据争用不大、冲突较少的环境**中，这种环境中，偶尔回滚事务的成本会低于读取数据时锁定数据的成本，因此**可以获得比其他并发控制方法更高的吞吐量**。

## 悲观锁 PCC

它可以**阻止一个事务以影响其他用户的方式来修改数据**。如果一个事务执行的操作读某行数据应用了锁，那只有当这个事务把锁释放，其他事务才能够执行与该锁冲突的操作。

这种设计采用了“**一锁二查三更新**”模式，就是采用数据库中自带 `select ... for update` 关键字进行对当前事务添加行级锁先将要操作的数据进行锁上，之后执行对应查询数据并执行更新操作。

```sql
BEGIN
SELECT quantity FROM order_stock WHERE oid = 1 FOR UPDATE;
UPDATE order_stock SET quantity = 2 WHERE oid = 1; 
COMMIT;
```

MySQL还有个问题是`select ... for update`语句执行中所有扫描过的行都会被锁上，这一点很容易造成问题。因此如果在**MySQL中用悲观锁务必要确定走了索引，而不是全表扫描**。

悲观并发控制主要**用于数据争用激烈的环境**，以及发生并发冲突时使用锁保护数据的成本要低于回滚事务的成本的环境中。

## OCC 和 PCC 优缺点

### OCC 优点及缺点

**【优点】**

- 乐观锁相信事务之间的数据竞争(`data race`)的概率是比较小的，因此尽可能直接做下去，直到提交的时候才去锁定，所以不会产生任何锁和死锁； 
- 可以快速响应事务，随着并发量增加，但会出现大量回滚出现； 
- 效率高，但是要控制好锁的力度。

**【缺点】**

- 如果直接简单这么做，还是有可能会遇到不可预期的结果，例如两个事务都读取了数据库的某一行，经过修改以后写回数据库，这时就遇到了问题； 
- 随着并发量增加，但会出现大量回滚出现。

### PCC 优点及缺点

**【优点】**

- “先取锁再访问”的保守策略，为数据处理的安全提供了保证；

**【缺点】**

- 依赖数据库锁，效率低； 
- 处理加锁的机制会让数据库产生额外的开销，还有增加产生死锁的机会； 
- 降低了并行性，一个事务如果锁定了某行数据，其他事务就必须等待该事务处理完才可以处理那行数据。

## References

- [【MySQL】悲观锁&乐观锁 ](https://www.cnblogs.com/zhiqian-ali/p/6200874.html)
- [LearnKu 浅析乐观锁与悲观锁](https://learnku.com/articles/27880) 
- [维基百科 悲观并发控制 && 乐观并发控制](https://zh.wikipedia.org/wiki/%E5%B9%B6%E5%8F%91%E6%8E%A7%E5%88%B6)