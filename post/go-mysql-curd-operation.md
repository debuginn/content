---
title: "Go 语言操作 MySQL 之 CURD 操作"
date: 2020-07-01T17:08:00+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "go, mysql, curd"
comments: true
tags: ["go", "mysql", "curd"]
categories: ["golang"]
image: "https://webp.debuginn.com/202303031910934.jpg"
---

MySQL 是目前开发中最常见的关系型数据库，使用 Go 语言进行操控数据库需要使用 Go 自带`database/sql`和驱动`go-sql-driver/mysql`来实现，

创建好 Go 项目，需要引用驱动依赖：

```go
go get -u github.com/go-sql-driver/mysql
```

使用 MySQL 驱动：

```go
func Open(driverName, dataSourceName string) (*DB, error)
```

Open 打开一个 dirverName 指定的数据库，dataSourceName 指定数据源，一般至少包括数据库文件名和其它连接必要的信息。

## 初始化连接

```go
var db *sql.DB //声明一个全局的 db 变量

// 初始化 MySQL 函数
func initMySQL() (err error) {
	dsn := "root:password@tcp(127.0.0.1:3306)/dbname"
	db, err = sql.Open("mysql", dsn)
	if err != nil {
		return
	}
	err = db.Ping()
	if err != nil {
		return
	}
	return
}
func main() {
	// 初始化 MySQL
	err := initMySQL()
	if err != nil {
		panic(err)
	}
	defer db.Close()
}
```

初始化连接 MySQL 后需要借助 `db.Ping` 函数来判断连接是否成功。

### SetMaxOpenConns

```go
func (db *DB) SetMaxOpenConns(n int)
```

`SetMaxOpenConns`**设置与数据库建立连接的最大数目**。

如果 n 大于 0 且小于最大闲置连接数，会将最大闲置连接数减小到匹配最大开启连接数的限制。

如果 n <= 0，不会限制最大开启连接数，默认为0（无限制）。

### SetMaxIdleConns

```go
func (db *DB) SetMaxIdleConns(n int)
```

`SetMaxIdleConns`**设置连接池中的最大闲置连接数。**

如果 n 大于最大开启连接数，则新的最大闲置连接数会减小到匹配最大开启连接数的限制。

如果 n <= 0，不会保留闲置连接。

## CURD

进行 CURD 操作，需要对数据库建立连接，同时有供操作的数据（数据库与数据表）：

### 初始化数据

### 建立数据库 sql_demo

```sql
CREATE DATABASE sql_demo;
USE sql_demo;
```

**创建数据表 user**

```sql
CREATE TABLE `user` (
    `id` BIGINT(20) NOT NULL AUTO_INCREMENT,
    `name` VARCHAR(20) DEFAULT '',
    `age` INT(11) DEFAULT '0',
    PRIMARY KEY(`id`)
)ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4;
```

### 查询数据 SELECT

便于接收数据，定义一个 user 结构体接收数据：

```go
type user struct {
	id   int
	age  int
	name string
}
```

**查询一行数据**

`db.QueryRow()` 执行一次查询，并期望返回最多一行结果（即 Row ）。QueryRow 总是返回非 nil 的值，直到返回值的 Scan 方法被调用时，才会返回被延迟的错误。（如：未找到结果）

```go
func (db *DB) QueryRow(query string, args ...interface{}) *Row 
```

实例代码如下：

```go
// 查询一行数据
func queryRowDemo() (u1 *user, err error) {
	// 声明查询语句
	sqlStr := "SELECT id,name,age FROM user WHERE id = ?"
	// 声明一个 user 类型的变量
	var u user
	// 执行查询并且扫描至 u
	err = db.QueryRow(sqlStr, 1).Scan(&u.id, &u.age, &u.name)
	if err != nil {
		return nil, err
	}
	u1 = &u
	return
}

func main() {
	// 初始化 MySQL
	err := initMySQL()
	if err != nil {
		panic(err)
	}
	defer db.Close()

	u1, err := queryRowDemo()
	if err != nil {
		fmt.Printf("err:%s", err)
	}
	fmt.Printf("id:%d, age:%d, name:%s\n", u1.id, u1.age, u1.name)
}
```

结果如下：

```sybase
id:1, age:111, name:22
```

**多行查询**

`db.Query()`执行一次查询，返回多行结果（即 Rows ），一般用于执行 select 命令。参数 args 表示 query 中的占位参数。

```go
func (db *DB) Query(query string, args ...interface{}) (*Rows, error)
```

实例代码如下：

```go
// 查询多行数据
func queryMultiRowDemo() {
	
	sqlStr := "SELECT id,name,age FROM user WHERE id > ?"
	rows, err := db.Query(sqlStr, 0)
	if err != nil {
		fmt.Printf("query data failed，err:%s\n", err)
		return
	}
	// 查询完数据后需要进行关闭数据库链接
	defer rows.Close()

	for rows.Next() {
		var u user
		err := rows.Scan(&u.id, &u.age, &u.name)
		if err != nil {
			fmt.Printf("scan data failed, err:%v\n", err)
			return
		}
		fmt.Printf("id:%d name:%s age:%d\n", u.id, u.name, u.age)
	}
}
```

执行结果：

```sybase
id:1 name:111 age:22
id:3 name:张三 age:22
```

使用 `rows.Next()` 循环读取结果集中的数据。

### 增加数据 INSERT

增加、删除、更新操作均使用 Exec 方法。

```go
func (db *DB) Exec(query string, args ...interface{}) (Result, error)
```

实例代码如下：

```go
// 增加一行数据
func insertRowDemo() {
	sqlStr := "INSERT INTO user(name, age) VALUES(?, ?)"
	result, err := db.Exec(sqlStr, "小羽", 22)
	if err != nil {
		fmt.Printf("insert data failed, err:%v\n", err)
		return
	}
	id, err := result.LastInsertId()
	if err != nil {
		fmt.Printf("get insert lastInsertId failed, err:%v\n", err)
		return
	}
	fmt.Printf("insert success, id:%d\n", id)
}
```

执行结果：

```sybase
insert success, id:4
```

### 更新数据 UPDATE

```go
// 更新一组数据
func updateRowDemo() {
	sqlStr := "UPDATE user SET age = ? WHERE id = ?"
	result, err := db.Exec(sqlStr, 22, 1)
	if err != nil {
		fmt.Printf("update data failed, err:%v\n", err)
		return
	}
	n, err := result.RowsAffected()
	if err != nil {
		fmt.Printf("get rowsaffected failed, err:%v\n", err)
		return
	}
	fmt.Printf("update success, affected rows:%d\n", n)
}
```

### 删除数据 DELETE

```go
// 删除一行数据
func deleteRowDemo() {
	sqlStr := "DELETE FROM user WHERE id = ?"
	result, err := db.Exec(sqlStr, 2)
	if err != nil {
		fmt.Printf("delete data failed, err:%d\n", err)
		return
	}
	n, err := result.RowsAffected()
	if err != nil {
		fmt.Printf("get affected failed, err:%v\n", err)
		return
	}
	fmt.Printf("delete success, affected rows:%d\n", n)
}
```