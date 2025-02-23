---
title: "Go 语言操作 MySQL 之 SQLX 包"
date: 2020-07-07T11:35:00+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "go,mysql,sqlx"
comments: true
tags: ["go","mysql"]
categories: ["golang"]
image: "https://webp.debuginn.com/202303011912531.jpg"
---

## SQLX 库

sqlx是 Go 的软件包，它在出色的内置`database/sql`软件包的基础上提供了一组扩展。

该库兼容 sql 原生包，同时又提供了更为强大的、优雅的查询、插入函数。

该库提供四个处理类型，分别是：

- sqlx.DB - 类似原生的sql.DB； 
- sqlx.Tx - 类似原生的sql.Tx； 
- sqlx.Stmt - 类似原生的 sql.Stmt, 准备 SQL 语句操作； 
- sqlx.NamedStmt - 对特定参数命名并绑定生成 SQL 语句操作。

提供两个游标类型，分别是：

- sqlx.Rows - 类似原生的 sql.Rows, 从 Queryx 返回； 
- sqlx.Row - 类似原生的 sql.Row, 从 QueryRowx 返回。

## 安装 SQLX 库

```shell
go get github.com/jmoiron/sqlx
```

## 使用操作

### 连接数据库

```go
// 初始化数据库
func initMySQL() (err error) {
	dsn := "root:password@tcp(127.0.0.1:3306)/database"
	db, err = sqlx.Open("mysql", dsn)
	if err != nil {
		fmt.Printf("connect server failed, err:%v\n", err)
		return
	}
	db.SetMaxOpenConns(200)
	db.SetMaxIdleConns(10)
	return
}
```

`SetMaxOpenConns` 和 `SetMaxIdleConns` 分别为设置最大连接数和最大空闲数。

## 数据表达及引用

在这里提前声明一个用户结构体 user，将 `*sqlx.DB` 作为一个全局变量使用，当然也要提前引用 MySQL 的驱动包，如下设计：

```go
import (
	"fmt"
	_ "github.com/go-sql-driver/mysql"
	"github.com/jmoiron/sqlx"
)

var db *sqlx.DB

type user struct {
	Id   int    `db:"id"`
	Age  int    `db:"age"`
	Name string `db:"name"`
}
```

## 查询操作

### 查询一行数据

查询一行数据使用 sqlx 库中的 Get 函数实现：

```go
func (db *DB) Get(dest interface{}, query string, args ...interface{}) error
```

dest 是用户声明变量接收查询结果，query 为查询 SQL 语句，args 为绑定参数的赋值。

```go
// 查询一行数据
func queryRow() {
	sqlStr := "SELECT id, name, age FROM user WHERE id = ?"

	var u user
	if err := db.Get(&u, sqlStr, 1); err != nil {
		fmt.Printf("get data failed, err:%v\n", err)
		return
	}
	fmt.Printf("id:%d, name:%s, age:%d\n", u.Id, u.Name, u.Age)
}
```

### 查询多行数据

而查询多行数据则使用的是Select 函数：

```go
func (db *DB) Select(dest interface{}, query string, args ...interface{}) error
```

使用 Select 函数进行查询的时候，需要先声明一个结构体数组接收映射过来的数据：

```go
// 查询多行数据
func queryMultiRow() {
	sqlStr := "SELECT id, name, age FROM user WHERE id > ?"
	var users []user
	if err := db.Select(&users, sqlStr, 0); err != nil {
		fmt.Printf("get data failed, err:%v\n", err)
		return
	}

	for i := 0; i < len(users); i++ {
		fmt.Printf("id:%d, name:%s, age:%d\n", users[i].Id, users[i].Name, users[i].Age)
	}
}
```

### 插入、更新、删除操作

在 sqlx 库中，使用插入、更新、删除操作是和原生 sql 库实现是一致的，都是采用 Exec 函数来实现的：

#### 插入操作

```go
// 插入数据
func insertRow() {
	sqlStr := "INSERT INTO user(name, age) VALUES(?, ?)"
	result, err := db.Exec(sqlStr, "Meng小羽", 22)
	if err != nil {
		fmt.Printf("exec failed, err:%v\n", err)
		return
	}
	insertID, err := result.LastInsertId()
	if err != nil {
		fmt.Printf("get insert id failed, err:%v\n", err)
		return
	}
	fmt.Printf("insert data success, id:%d\n", insertID)
}
```

#### 更新操作

```go
// 更新数据
func updateRow() {
	sqlStr := "UPDATE user SET age = ? WHERE id = ?"
	result, err := db.Exec(sqlStr, 22, 6)
	if err != nil {
		fmt.Printf("exec failed, err:%v\n", err)
		return
	}
	affectedRows, err := result.RowsAffected()
	if err != nil {
		fmt.Printf("get affected failed, err:%v\n", err)
		return
	}
	fmt.Printf("update data success, affected rows:%d\n", affectedRows)
}
```

#### 删除操作

```go
// 删除一行
func deleteRow() {
	sqlStr := "DELETE FROM user WHERE id = ?"
	result, err := db.Exec(sqlStr, 4)
	if err != nil {
		fmt.Printf("exec failed, err:%v\n", err)
		return
	}
	affectedRows, err := result.RowsAffected()
	if err != nil {
		fmt.Printf("get affected failed, err:%v\n", err)
		return
	}
	fmt.Printf("delete data success, affected rows:%d\n", affectedRows)
}
```

#### 参数绑定

在库中提供最常用的就是`NamedQuery`和`NamedExec`函数，一个是执行对查询参数命名并绑定，另一个则是对 CUD 操作的查询参数名的绑定：

##### NamedQuery

```go
// 绑定查询
func selectNamedQuery() {
	sqlStr := "SELECT id, name, age FROM user WHERE age = :age"
	rows, err := db.NamedQuery(sqlStr, map[string]interface{}{
		"age": 22,
	})
	if err != nil {
		fmt.Printf("named query failed failed, err:%v\n", err)
		return
	}
	defer rows.Close()
	for rows.Next() {
		var u user
		if err := rows.StructScan(&u); err != nil {
			fmt.Printf("struct sacn failed, err:%v\n", err)
			continue
		}
		fmt.Printf("%#v\n", u)
	}
}
```

##### NamedExec

```go
// 使用 named 方法插入数据
func insertNamedExec() {
	sqlStr := "INSERT INTO user(name, age) VALUES(:name, :age)"
	result, err := db.NamedExec(sqlStr, map[string]interface{}{
		"name": "里斯",
		"age":  18,
	})
	if err != nil {
		fmt.Printf("named exec failed, err:%v\n", err)
		return
	}
	insertId, err := result.LastInsertId()
	if err != nil {
		fmt.Printf("get last insert id failed, err:%v\n", err)
		return
	}
	fmt.Printf("insert data success, id:%d\n", insertId)
}
```

#### 事务操作

使用`Begin`函数、`Rollback`函数及`Commit`函数实现事务操作：

```go
// 开启事务
func (db *DB) Begin() (*Tx, error) 
// 回滚事务
func (tx *Tx) Rollback() error 
// 提交事务
func (tx *Tx) Commit() error
```

**示例代码：**

```go
// 事务操作
func updateTransaction() (err error) {
	tx, err := db.Begin()
	if err != nil {
		fmt.Printf("transaction begin failed, err:%v\n", err)
		return err
	}

	defer func() {
		if p := recover(); p != nil {
			_ = tx.Rollback()
			panic(p)
		} else if err != nil {
			fmt.Printf("transaction rollback")
			_ = tx.Rollback()
		} else {
			err = tx.Commit()
			fmt.Printf("transaction commit")
			return
		}
	}()

	sqlStr1 := "UPDATE user SET age = ? WHERE id = ? "
	reuslt1, err := tx.Exec(sqlStr1, 18, 1)
	if err != nil {
		fmt.Printf("sql exec failed, err:%v\n", err)
		return err
	}
	rows1, err := reuslt1.RowsAffected()
	if err != nil {
		fmt.Printf("affected rows is 0")
		return
	}
	sqlStr2 := "UPDATE user SET age = ? WHERE id = ? "
	reuslt2, err := tx.Exec(sqlStr2, 19, 5)
	if err != nil {
		fmt.Printf("sql exec failed, err:%v\n", err)
		return err
	}
	rows2, err := reuslt2.RowsAffected()
	if err != nil {
		fmt.Printf("affected rows is 0\n")
		return
	}

	if rows1 > 0 && rows2 > 0 {
		fmt.Printf("update data success\n")
	}
	return
}
```

## 开源项目

最后将此开源项目放在此处，大家要是感兴趣可以给这个开源项目一个 Star，感谢。

[https://github.com/jmoiron/sqlx](https://github.com/jmoiron/sqlx)

## References

- [http://jmoiron.github.io/sqlx/](http://jmoiron.github.io/sqlx/)
- [sqlx库使用指南 - 李文周的博客](https://www.liwenzhou.com/posts/Go/sqlx/)

