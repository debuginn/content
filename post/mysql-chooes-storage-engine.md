---
title: "怎么优雅的选择 MySQL 存储引擎"
date: 2020-05-01T17:41:00+08:00
draft: false
keywords: "mysql,engin,database"
comments: true
tags: ["mysql","engin","database"]
categories: ["sql"]
image: "https://webp.debuginn.com/202303121916799.jpg"
---

对于数据库这一块询问比较多的就是在 MySQL 中怎么去选择一种合适当前业务需求的存储引擎，而 MySQL 中支持的存储引擎又有很多种，那么 MySQL 中分别又有那些，怎么优雅的使用呢？

## 划分引擎原因

在文件系统中，MySQL 将每个数据库（也可以称之为 schema ）保存为数据目录下的一个子目录。创建表时，MySQL 会在数据库子目录下创建一个和表同名的 .frm 文件保存表的定义。例如创建一个名为 DebugTable 的表，MySQL 会在 DebugTable.frm 文件中保存该表的定义。

因为 MySQL 使用文件系统的目录和文件来保存数据库和表的定义，大小写敏感性和具体的平台密切相关。在 Windows 系统中，大小写是不敏感的；而在类 Unix 系统中则是敏感的。不同的存储引擎保存数据和索引的方式是不同的，但表的定义则是在 MySQL 服务层wk统一处理的。

## 查看支持引擎

想了解 MySQL 中支持的引擎的情况，可以使用如下命令查看：

```sql
show engines;
```

结果如下（MySQL版本：Ver 8.0.19）：

```sql
mysql> show engines;
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| Engine             | Support | Comment                                                        | Transactions | XA   | Savepoints |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| FEDERATED          | NO      | Federated MySQL storage engine                                 | NULL         | NULL | NULL       |
| MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables      | NO           | NO   | NO         |
| InnoDB             | DEFAULT | Supports transactions, row-level locking, and foreign keys     | YES          | YES  | YES        |
| PERFORMANCE_SCHEMA | YES     | Performance Schema                                             | NO           | NO   | NO         |
| MyISAM             | YES     | MyISAM storage engine                                          | NO           | NO   | NO         |
| MRG_MYISAM         | YES     | Collection of identical MyISAM tables                          | NO           | NO   | NO         |
| BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears) | NO           | NO   | NO         |
| CSV                | YES     | CSV storage engine                                             | NO           | NO   | NO         |
| ARCHIVE            | YES     | Archive storage engine                                         | NO           | NO   | NO         |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
9 rows in set (0.00 sec)
```

## 存储引擎分类

MySQL 存储引擎分类有 MyISAM、InnoDB、Memory、Merge等，可以看上面表中列出的支持引擎，但是其中最为常用的就是 MyISAM 和 InnoDB 两个引擎，其中针对于以上讲到的存储引擎，如下表进行对比：

| 对比项目     | MyISAM | InnoDB       | Memory  | Merge       |
|----------|--------|--------------|---------|-------------|
| 存储限制     | 256TB  | 64TB         | RAM 内存表 | /           |
| 是否支持事务   | 否      | 是            | 否       | 否           |
| 是否支持全文索引 | 是      | 否            | 否       | 否           |
| 是否支持数索引  | 是      | 是            | 是       | 否           |
| 是否支持哈希索引 | 否      | 否            | 是       | 否           |
| 是否支持数据缓存 | 否      | 是            | 否       | 否           |
| 是否支持外键索引 | 否      | 是            | 否       | 否           |
| 备注       |        | 支持事务，行级锁定和外键 |         | 指向MyISAM表操作 |

## MyISAM 与 InnoDB 区别

**两种类型最主要的差别是InnoDB支持事务处理与外键和行级锁**。

1. **InnoDB 可借由事务日志**（ Transaction Log ）来**恢复**程序崩溃（ crash ），或非预期结束所造成的数据错误； 而 MyISAM 遇到错误，必须完整扫描后才能重建索引，或修正未写入硬盘的错误。
2. **InnoDB 的修复时间，一般都是固定的**，但 MyISAM 的修复时间，则与数据量的多寡成正比。 
3. 相对而言，随着**数据量的增加，InnoDB 会有较佳的稳定性**。 
4. **MyISAM 必须依靠操作系统来管理读取与写入的缓存，而 InnoDB 则是有自己的读写缓存管理机制**。（ InnoDB 不会将被修改的数据页立即交给操作系统）因此在某些情况下，InnoDB 的数据访问会比 MyISAM 更有效率。 
5. **InnoDB** 目前并不支持 MyISAM 所提供的压缩与 terse row formats（简洁的行格式） ，所以**对硬盘与高速缓存的使用量较大**。 
6. 当操作完全兼容 ACID（事务）时，虽然 InnoDB 会自动合并数笔连接，但每次有事务产生时，仍至少须写入硬盘一次，因此对于某些硬盘或磁盘阵列，会造成每秒 200 次的事务处理上限。 若希望达到更高的性能且保持事务的完整性，就必使用磁盘缓存与电池备援。 当然 InnoDB 也提供数种对性能冲击较低的模式，但相对的也会降低事务的完整性。而MyISAM则无此问题，但这并非因为它比较先进，这只是因为它不支持事务。

## 应用场景

- **MyISAM 管理非事务表**。它提供高速存储和检索，以及全文搜索能力。如果应用中需要执行大量的 **SELECT** 查询，那么 MyISAM 是更好的选择。 
- **InnoDB 用于事务处理应用程序**，具有众多特性，包括 ACID 事务支持。如果应用中需要**执行大量的 INSERT 或 UPDATE 操作**，则应该使用 InnoDB，这样可以提高多用户并发操作的性能。

## 参考文章

1. [Mysql 存储引擎的区别和比较 - zgrgfr - CSDN](https://blog.csdn.net/zgrgfr/article/details/74455547)
2. [Mysql的存储引擎之：MERGE 存储引擎 - 翔之天空 - CSDN](https://blog.csdn.net/fly43108622/article/details/48181049)
3. [MySQL存储引擎之 Merge 引擎](http://www.hhailuo.com/archives/18380)
4. [MySQL存储引擎 - MyISAM与InnoDB区别 - Rocky - 知乎](https://zhuanlan.zhihu.com/p/61437720)
5. [MySQL引擎介绍 - 慕课网 - 知乎](https://zhuanlan.zhihu.com/p/53619907)