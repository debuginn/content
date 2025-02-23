---
title: "Go 语言操作 MySQL 之 事务操作"
date: 2020-07-02T15:04:00+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "go,mysql,transaction"
comments: true
tags: ["go","mysql","transaction"]
categories: ["golang"]
image: "https://webp.debuginn.com/202303031854061.jpg"
---

## 事务

数据库事务( transaction )是访问并可能操作各种数据项的一个数据库操作序列，这些操作要么全部执行,要么全部不执行，是一个不可分割的工作单位。事务由事务开始与事务结束之间执行的全部数据库操作组成。

MySQL 存储引擎分类有 MyISAM、InnoDB、Memory、Merge等，但是其中最为常用的就是 MyISAM 和 InnoDB 两个引擎，这两个引擎中，支持事务的引擎就是 Innodb（MySQL 默认引擎），在创建数据库中要注意对应引擎。

这里可以看一下针对 MySQL 选择引擎的文章：

[怎么优雅的选择 MySQL 存储引擎](https://blog.debuginn.com/mysql-chooes-storage-engine/)

## 事务 ACID

通常事务必须满足4个条件（ ACID ）：原子性（Atomicity，或称不可分割性）、一致性（Consistency）、隔离性（Isolation，又称独立性）、持久性（Durability）。


| 条件  | 解释                                                                                                                                                           |
|-----|--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 原子性 | 一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。                                                     |
| 一致性 | 在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。                                                                         |
| 隔离性 | 数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交（Read uncommitted）、读提交（read committed）、可重复读（repeatable read）和串行化（Serializable）。 |
| 持久性 | 事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。                                                                                                                             |

## Go 操作 MySQL 使用事务

Go语言中使用以下三个方法实现MySQL中的事务操作：

```go
// 开始事务
func (db *DB) Begin() (*Tx, error)
// 回滚事务
func (tx *Tx) Rollback() error
// 提交事务
func (tx *Tx) Commit() error
```

**示例代码：**

```go
// 事务更新操作
func transActionUpdate() {
	tx, err := db.Begin()
	if err != nil {
		if tx != nil {
			_ = tx.Rollback()
		}
		fmt.Printf("begin trans action failed, err:%v\n", err)
		return
	}
	sqlStr1 := "UPDATE user SET age = ? WHERE id = ?"
	result1, err := tx.Exec(sqlStr1, 20, 1)
	if err != nil {
		_ = tx.Rollback()
		fmt.Printf("exec failed, err:%v\n", err)
		return
	}
	n1, err := result1.RowsAffected()
	if err != nil {
		_ = tx.Rollback()
		fmt.Printf("exec result1.RowsAffected() failed, err:%v\n", err)
		return
	}
	sqlStr2 := "UPDATE user SET age = ? WHERE id = ?"
	result2, err := tx.Exec(sqlStr2, 20, 6)
	if err != nil {
		_ = tx.Rollback()
		fmt.Printf("exec failed, err:%v\n", err)
		return
	}
	n2, err := result2.RowsAffected()
	if err != nil {
		_ = tx.Rollback()
		fmt.Printf("exec result1.RowsAffected() failed, err:%v\n", err)
		return
	}

	if n1 == 1 && n2 == 1 {
		_ = tx.Commit()
		fmt.Printf("transaction commit success\n")
	} else {
		_ = tx.Rollback()
		fmt.Printf("transaction commit error, rollback\n")
		return
	}
}
```