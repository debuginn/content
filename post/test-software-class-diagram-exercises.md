---
title: "软件工程 类图习题"
date: 2019-07-03T21:45:49+08:00
keywords: "软件工程"
comments: true
tags: ["软件工程"]
categories: ["software"]
image: "https://static.debuginn.com/202303202156985.jpg"
---

- 依赖（Dependency）: 虚线箭头表示 
- 关联（Association）：实线箭头表示 
- 聚合（Aggregation）：带空心菱形头表示（整体和局部关系） 
- 组合（Composition）：带实心菱形头的实线表示 
- 泛化（Generalization）： 带空心箭头的实线线表示（ 继承关系） 
- 实现（Realization）：空心箭头和虚线表示

## 一、选择题
**1、在认识过程中，下面哪个不是对象的要素（ D ）**

A：认识的指向物                    B：认识者

C：认识指向物在认识者主观中的反映  D：认识的背景

**2、下面哪一个对对象的说法不正确（ B ）**

A：客观实体            B：事物的对立面

C：认识的指向物        D：软件的一个基本单位

**3、下面属性命名不正确的是（ A ）**

A  *BirthDay:Date

B  #studentBirthDay:Date=1999-10-21

C  -price:float=12.01{R/W}

D  +studentName:String=“张敏”

**4、指出下面不合适的类名（ B ）**

A：材料        B：事物 C：订单        D：会员

**5、在类图中，下面哪个符号表示继承关系（    C   ）**

![类图元素](https://static.debuginn.com/202303202150359.png)

**6、在类图中，“#”表示的可见性是（   B    ）**

（A）Public      （B）Protected      （C）Private    （D）Package

**7、在类图中，下面哪个符号表示实现关系（   B    ）**

![类图指示线](https://static.debuginn.com/202303202151210.png)

**8、在类图中，哪种关系表达总体与局部的关系（  D    ）**

（A）泛化         （B）实现         （C）依赖     （D）聚合

**9、UML中类的有三种，下面哪个不是其中之一（ D   ）**

A.实体类 B.边界类 C.控制类 D.主类

**10、在UML中，类之间的关系有一种为关联关系，其中多重性用来描述类之间的对应关系，下面哪个不是其中之一（ D ）**

A. 0..1 B. 0..* C. 1..* D. `*..*`

![重数性关联](https://static.debuginn.com/202303202151846.png)

**11、通常对象有很多属性，但对于外部对象来说某些属性应该不能被直接访问，下面哪个不是UML中的类成员访问限定性（  C ）**

A.公有的（public）

B.受保护的（protected）

C.友员（friendly）

D.私有的（private）

**12、在一个课程注册系统中，定义了类CourseSchedule和类Course，并在类CourseSchedule中定义了方法add（c:Course）和方法remove（c:Course），则类CourseSchedule和类Course之间的关系是：（B）**

A、泛化关系        B、组成关系          C、依赖关系      D、包含关系

**13、类A的一个操作调用类B的一个操作，且这两个类之间不存在其他关系，那么类A和类B之间是（ C ）关系。**

A 实现    B、关联     C、 依赖     D、 泛化

**14、在UML中下列图形代表什么关系？（A）**

![](https://static.debuginn.com/202303202152498.png)

A、组合关系         B、 依赖关系

C、聚合关系          D、泛化关系

**15、在UML中下列图形代表什么关系？( D  )**

![](https://static.debuginn.com/202303202153725.png)

A、组成关系         B、 依赖关系

C、聚集关系          D、泛化关系

**16、汽车（Car）由轮子、发动机、油箱、座椅、方向盘等组成。那么car类和其他类（Wheel、Engin、Tank、Chair、SteeringWheel）之间的关系是：（  A ）**

A、泛化关系              B、实现关系

C、包含关系              D、组合关系

**17、在下面的图例中，哪个用来描述注释（ B ）**

![图例](https://static.debuginn.com/202303202153624.png)

**18、消息传递是对象间通信的手段，一个对象通过向另一个对象发送消息来请求其服务，一个消息通常包括：（ A ）**

A、发送消息的对象的标识、调用的发送方的操作名和必要的参数

B、发送消息的类名和接收消息的类名

C、接收消息的对象的标识、调用的接收方的操作名和必要的参数

D、接收消息的类名

**19、在一个网络游戏系统中，定义了类Cowboy和类Castle，并在类Cowboy中定义了方法open（c：Castle）和方法Close（c：Castle），则类Cowboy和类Castle之间的关系是：……（ C ）**

A、依赖（dependency）关系             B、组成（composition）关系

C、泛化（generalization）关系         D、包含（include）关系

**20、根据下面的代码，判断下面那些叙述是正确的？（  B   ）**

```java
public class HouseKeeper{
    private TimeCard timecard;
    public void clockIn(){
        timecard.punch();
    }
}
```

A、类HouseKeeper和类TimeCard之间存在关联（Association）关系；

B、类HouseKeeper和类TimeCard之间存在泛化（Generalization）关系；

C、类HouseKeeper和类TimeCard之间存在实现（Realization）关系；

D、类HouseKeeper和类TimeCard之间存在依赖（dependency）关系

**21、UML关系包括关联、聚合、泛化、实现、依赖等5种类型，对应的编号分别为A、B、C、D和E，请将合适的关系填写在下列描述的（  ）中。**

① 用例及参与者之间是（ E ）关系。

②类A的一个操作调用类B的一个操作，且这两个类之间不存在其他关系，那么类A和类B之间是（ C ）关系。

③在学校中，一个学生可以选修多门课程，一门课程可以由多个学生选修，那么学生和课程之间是（ A  ）关系。

④森林和树木之间是（ B  ）关系。

**22、已知类A需要类B提供的服务，下列所描述的四种情况中，哪种情况不好把类A和类B之间的关系定义成依赖关系 （  C  ）**

A、类A中存在两个操作都需要访问类B的同一个对象

B、类A的某个操作内部创建了类B的对象，而其他操作均与类B无关

C、类A的某个操作其参数是类B的对象，而其他操作均与类B无关

D、类B是一个全局变量

**23、“一个研究生在软件学院做助教（teaching assistant），同时还在校园餐厅打工做收银员（cashier）。也就是说，这个研究生有3种角色：学生、助教、收银员，但在同一时刻只能有一种角色。”**

根据上面的陈述，下面哪个设计是最合理的？（ C  ）

![](https://static.debuginn.com/202303202155826.png)

**24、类X与类Y有许多相同的属性和行为，但是它的行为与类Y稍微有所不同，这时可以认为类X是类Y的一种特例；则类X和类Y之间是（  A   ）关系。**

A 、泛化关系      B、 关联关系      C、 依赖关系      D、 实现关系

**25、关于类和对象的关系，下列说法中哪个是错误的 （  C    ）**

A、每个对象都是某个类的实例

B、每个类某一时刻必定存在对象实体

C、类是静态的描述

D、对象是动态的实例

## 二、填空题
**1、 下图中类的名字是： _ Login_类中的成员属性是： _sName、sPass _ 类中的行为（方法）是：___checkUser___。**

![login方法](https://static.debuginn.com/202303202156676.png)

2、类描述具有相同性质的一组对象的（集合），类用（new）来表示。

3、在设计阶段，可以把类分为（控制类）、边界类和（实体类）等类型。