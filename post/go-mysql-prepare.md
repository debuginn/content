---
title: "Go 语言操作 MySQL 之 预处理"
date: 2020-07-02T16:46:10+08:00
draft: false
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "go,mysql,prepare"
comments: true
weight: 0
tags: ["go","mysql","prepare"]
categories: ["golang"]
image: "https://webp.debuginn.com/202303031903089.jpg"
---

## 预处理 

预处理是 MySQL 为了防止客户端频繁请求的一种技术，是对相同处理语句进行预先加载在 MySQL 中，将操作变量数据用占位符来代替，减少对 MySQL 的频繁请求，使得服务器高效运行。

在这里客户端并不是前台后台之间的 C/S 架构，而是后台程序对数据库服务器进行操作的 C/S 架构，这样就可以简要地理解了后台程序作为 Client 向 MySQL Server 请求并处理结果了。

**普通 SQL 执行处理过程：**

1. 在客户端准备 SQL 语句； 
2. 发送 SQL 语句到 MySQL 服务器； 
3. 在 MySQL 服务器执行该 SQL 语句； 
4. 服务器将执行结果返回给客户端。

**预处理执行处理过程：**

1. 将 SQL 拆分为结构部分与数据部分； 
2. 在执行 SQL 语句的时候，首先将前面相同的命令和结构部分发送给 MySQL 服务器，让 MySQL 服务器事先进行一次预处理（此时并没有真正的执行 SQL 语句）； 
3. 为了保证 SQL 语句的结构完整性，在第一次发送 SQL 语句的时候将其中可变的数据部分都用一个数据占位符来表示； 
4. 然后把数据部分发送给 MySQL 服务端，MySQL 服务端对 SQL 语句进行占位符替换； 
5. MySQL 服务端执行完整的 SQL 语句并将结果返回给客户端。

## 预处理优点

- 预处理语句大大**减少了分析时间**，只做了一次查询（虽然语句多次执行）； 
- 绑定参数**减少了服务器带宽**，只需发送查询的参数，而不是整个语句； 
- 预处理语句针对 **SQL** 注入是非常有用的，因为参数值发送后使用不同的协议，保证了数据的合法性。

## Go 语言实现

在 Go 语言中，使用 `db.Prepare()` 方法实现预处理：

```go
func (db *DB) Prepare(query string) (*Stmt, error)
```

Prepare 执行预处理 SQL 语句，并返回 Stmt 结构体指针，进行数据绑定操作。

查询操作使用 `db.Prepare()` 方法声明预处理 SQL，使用 `stmt.Query()` 将数据替换占位符进行查询，更新、插入、删除操作使用 `stmt.Exec()` 来操作。

### 预处理查询示例

```go
// 预处理查询数据
func prepareQuery() {
	sqlStr := "SELECT id,name,age FROM user WHERE id > ?"
	stmt, err := db.Prepare(sqlStr)
	if err != nil {
		fmt.Printf("prepare sql failed, err:%v\n", err)
		return
	}
	rows, err := stmt.Query(1)
	if err != nil {
		fmt.Printf("exec failed, err:%v\n", err)
		return
	}
	defer rows.Close()

	for rows.Next() {
		var u user
		err := rows.Scan(&u.id, &u.name, &u.age)
		if err != nil {
			fmt.Printf("scan data failed, err:%v\n", err)
			return
		}
		fmt.Printf("id:%d, name:%s, age:%d\n", u.id, u.name, u.age)
	}
}
```

### 预处理更新示例

```go
// 预处理更新数据
func prepareUpdate() {
	sqlStr := "UPDATE user SET age = ? WHERE id = ?"
	stmt, err := db.Prepare(sqlStr)
	if err != nil {
		fmt.Printf("prepare sql failed, err:%v\n", err)
		return
	}
	_, err = stmt.Exec(18, 2)
	if err != nil {
		fmt.Printf("exec failed, err:%v\n", err)
		return
	}
	fmt.Printf("prepare update data success")
}
```

### 预处理插入示例

```go
// 预处理更新数据
func prepareUpdate() {
	sqlStr := "UPDATE user SET age = ? WHERE id = ?"
	stmt, err := db.Prepare(sqlStr)
	if err != nil {
		fmt.Printf("prepare sql failed, err:%v\n", err)
		return
	}
	_, err = stmt.Exec(18, 2)
	if err != nil {
		fmt.Printf("exec failed, err:%v\n", err)
		return
	}
	fmt.Printf("prepare update data success")
}
```

### 预处理删除示例

```go
// 预处理删除数据
func prepareDelete() {
	sqlStr := "DELETE FROM user WHERE id = ?"
	stmt, err := db.Prepare(sqlStr)
	if err != nil {
		fmt.Printf("prepare sql failed, err:%v\n", err)
		return
	}
	result, err := stmt.Exec(3)
	n, err := result.RowsAffected()
	if err != nil {
		fmt.Printf("delete rows failed, err:%v\n", err)
		return
	}
	if n > 0 {
		fmt.Printf("delete data success")
	} else {
		fmt.Printf("delete data error")
	}
}
```
