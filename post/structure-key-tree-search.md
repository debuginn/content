---
title: "数据结构 键树查找法"
date: 2017-11-29T18:49:22+08:00
keywords: "笔记,数据结构"
comments: true
tags: ["笔记", "数据结构"]
categories: ["algorithm"]
image: "https://static.debuginn.com/202302221903175.jpg"
---

## 定义

**键树查找法** 又称数字查找树（根节点子树>=2个），键树节点存储的不是某个关键字，而是组成关键字的单个符号。

> 如果关键字本身是字符串，则键树中的一个结点只包含有一个字符；如果关键字本身是数字，则键树中的一个结点只包含一个数位。每个关键字都是从键树的根结点到叶子结点中经过的所有结点中存储的组合。

例如，当使用键树表示查找表

```shell
{ CAI，CAO，CHEN，LI，LAN，ZHAO }
```

时，为了查找方便，首先对该查找表中关键字按照首字符进行分类（相同的在一起）：

```shell
{ { CAI,CAO,CHEN}, {LI,LAN} , { ZHAO}}
```

然后继续分割，按照第二个字符、第三个字符、…，最终得到的查找表为：

```shell
{ {CAI,CAO},{ CHEN},{ LI,LAN},{ ZHAO}}
```

然后使用键树结构表示该查找表，如下图所示：

![键树](https://static.debuginn.com/202304141856996.png)

> 注意 ：键树中叶子结点的特殊符号 \$ 为结束符，表示字符串的结束。使用键树表示查找表时，为了方便后期的查找和插入操作，约定键树是有序树（兄弟结点之间自左至右有序），同时约定结束符 ‘\$’ 小于任何字符。

## 键树的存储结构

键树的存储结构有两种，分别是：

- 双链树 ：通过使用树的孩子兄弟表示法来表示键树。
- 字典树 ：以树的多重链表表示键树。

### 双链树

当使用孩子兄弟表示法表示键树时，树的节点构成以下3部分：

- symobl域：存储关键字的一个字符。 
- first域：存储指向孩子节点的指针。 
- next域：存储指向兄弟节点的指针。

> 注意：对于叶子结点来说，由于其没有孩子结点，在构建叶子结点时，将 first 指针换成 infoptr 指针，用于指向该关键字。当叶子结点（结束符 ‘\$’ 所在的结点）中使用 infoptr 域指向该自身的关键字时，此时的键树被称为双链树。

上图中键树用孩子兄弟表示法表示为双链树时，如下图所示：

![孩子兄弟表示法](https://static.debuginn.com/202304141900499.png)

> 提示：每个关键字的叶子结点 \$ 的 infoptr 指针指向的是各自的关键字，通过该指针就可以找到各自的关键字的首地址。

### 双链树查找功能的具体实现

在使用孩子兄弟表示法表示的键树中做查找操作，从树的根结点出发，依次同被查找的关键字进行比对，如果比对成功，进行下一字符的比对；反之，如果比对失败，则跳转至该结点的兄弟结点中去继续比对，直至比对成功或者为找到该关键字。

#### 实现代码

```c
#include <stdio.h>
typedef enum{LEFT,BRANCH}NodeKind;//定义结点的类型，是叶子结点还是其他类型的结点
typedef  struct {
    char a[20];//存储关键字的数组
    int num;//关键字长度
}KeysType;
//定时结点结构
typedef struct DLTNode{
    char symbol;//结点中存储的数据
    struct DLTNode *next;//指向兄弟结点的指针
    NodeKind *kind;//结点类型
    union{//其中两种指针类型每个结点二选一
        struct DLTNode* first;//孩子结点
        struct DLTNode* infoptr;//叶子结点特有的指针
    };
}*DLTree;
//查找函数，如果查找成功，返回该关键字的首地址，反则返回NULL。T 为用孩子兄弟表示法表示的键树，K为被查找的关键字。
DLTree SearchChar(DLTree T, KeysType k){
    int i = 0;
    DLTree p = T->first;//首先令指针 P 指向根结点下的含有数据的孩子结点
    //如果 p 指针存在，且关键字中比对的位数小于总位数时，就继续比对
    while (p && i < k.num){
        //如果比对成功，开始下一位的比对
        if (k.a[i] == p->symbol){
            i++;
            p = p->first;
        }
        //如果该位比对失败，则找该结点的兄弟结点继续比对
        else{
            p = p->next;
        }
    }
    //比对完成后，如果比对成功，最终 p 指针会指向该关键字的叶子结点 $，通过其自有的 infoptr 指针找到该关键字。
    if ( i == k.num){
        return p->infoptr;
    }
    else{
        return NULL;
    }
}
```

### Trie树（字典树）

若以树的多重链表表示键树，则树中如同双链树一样，会含有两种结点：

1. 叶子结点：叶子结点中含有关键字域和指向该关键字的指针域； 
2. 除叶子结点之外的结点（分支结点）：含有 d 个指针域和一个整数域（记录该结点中指针域的个数）；

> d 表示每个结点中存储的关键字的所有可能情况，如果存储的关键字为数字，则 d= 11（0—9，以及 \$），同理，如果存储的关键字为字母，则 d=27（26个字母加上结束符 \$）。

开始的键树，采用字典树表示如下图所示：

![字典树](https://static.debuginn.com/202304141903096.png)

> 注意：在 Trie 树中，如果从某个结点一直到叶子结点都只有一个孩子，这些结点可以用一个叶子结点来代替，例如 ZHAO 就可以直接作为叶子结点。

### 字典树查找功能的具体实现

使用 Trie 树进行查找时，从根结点出发，沿和对应关键字中的值相对应的指针逐层向下走，一直到叶子结点，如果全部对应相等，则查找成功；反之，则查找失败。

#### 实现代码

```c
typedef enum{LEFT,BRANCH}NodeKind;//定义结点类型
typedef struct {//定义存储关键字的数组
    char a[20];
    int num;
}KeysType;
//定义结点结构
typedef struct TrieNode{
    NodeKind kind;//结点类型
    union{
        struct { KeysType k; struct TrieNode *infoptr; }lf;//叶子结点
        struct{ struct TrieNode *ptr[27]; int num; }bh;//分支结点
    };
}*TrieTree;
//求字符 a 在字母表中的位置
int ord(char  a){
    int b = a - 'A'+1;
    return b;
}
//查找函数
TrieTree SearchTrie(TrieTree T, KeysType K){
    int i=0;
    TrieTree p = T;
    while (i < K.num){
        if (p && p->kind==BRANCH && p->bh.ptr[ord(K.a[i])]){
            i++;
            p = p->bh.ptr[ord(K.a[i])];
        }
        else{
            break;
        }
    }
    if (p){
        return p->lf.infoptr;
    }
    return p;
}
```

> 使用 Trie 树进行查找的过程实际上是走了一条从根结点到叶子结点的路径，所以使用 Trie 进行的查找效率取决于该树的深度

## 总结

双链树和字典树是键树的两种表示方法，各有各的特点，具体使用哪种方式表示键树，需要根据实际情况而定。例如，若键树中结点的孩子结点较多，则使用字典树较双链树更为合适。

本博文从 [键树查找法（双链树和字典树）及C语言实现 严长生](http://data.biancheng.net/view/62.html) 转载而来，表示感谢。