---
title: "数据结构 浅谈归并排序"
date: 2020-03-30T08:00:00+08:00
keywords: "algorithm,sort,structure"
comments: true
tags: ["algorithm","sort","structure"]
categories: ["algorithm"]
image: "https://webp.debuginn.com/202302221903175.jpg"
---

在 v2ex 社区看到有人提问怎么把十万个电话号码排出出现次数最多的十个电话号码，我看到这个问题的时候第一时间想到的是将十万个电话号码读出来放到 Redis 中，之后做一个动态计数器，使用 foreach 函数对这个电话号码进行遍历，以电话号码为索引 key，计数器 value 进行自增，最后求出最多的电话号码，这样最后时间复杂度为 O(n)，不是一个好的解决方案，之后我看到评论区，有人提出使用归并排序，原理是一样的，不过可以将十万个电话号码平均分成十组，之后每组查找电话号码最多的十个号码，最后将十组最多的号码取出来再次进行相加排序，最后得到的最多的十个号码就是十万个电话号码中出现次数最多的号码。

看到这个问题，我不由得想到了我刚来去某浪面试的时候，面试官问我的问题和这个问题基本一致，不过是数的基数比较大，当时的我解决方案和现在我想的一样，很遗憾，没有结果，不得不说技术还是太菜了。之后我查了一下归并排序是采用的分治法的思想，即将一个问题分为若干个小的子问题进行解决，最后问题的解就是子问题结果的解的合并，接下来就详细的了解一下归并排序吧！


## 基本思想

归并排序 mergesort，是创建在归并操作上的一种有效的排序算法，效率为O(nlogn)。1945年由约翰·冯·诺伊曼首次提出。该算法是采用分治法（Divide and Conquer）的一个非常典型的应用，且各层分治递归可以同时进行。

采用**分治思想**，将一个问题拆分为若干个问题，之后将若干个问题解决，最后将若干个结果进行合并，即最终结果。

![归并排序](https://webp.debuginn.com/202303122243108.jpeg)

## 分治合并

在合并结果阶段，可以看到两个子结果的求解数组为`[1, 2, 6]` 和 `[3, 4, 5]`，将子数组合并排序为 `[1, 2, 3, 4, 5, 6]`。

![分治合并](https://webp.debuginn.com/202303122244127.jpeg)

## 算法实现

在这里使用的是 PHP，其实算法思想一致，用啥语言都可以实现，不过一种语言有一种语言的语法

```php
<?php
class MergeSort
{
    /**
     * 初始化函数
     * MergeSort constructor.
     * @param array $arr
     */
    public function __construct(array $arr)
    {
        // 设置初始数组，左侧下标，及数组总下标大小
        $this->mSort($arr, 0, count($arr) - 1);
        print_r($arr);
    }

    /**
     * 递归拆分数组
     * @param array $arr 原始数组
     * @param int $left 左侧索引下标
     * @param int $right 右侧索引下标
     */
    public function mSort(array &$arr, int $left, int $right)
    {
        if ($left < $right) {
            $center = floor(($left + $right) / 2);
            $this->mSort($arr, $left, $center);
            $this->mSort($arr, $center + 1, $right);
            $this->mergeArray($arr, $left, $right, $center);
        }
    }

    /**
     * 合并数组
     * @param array $arr
     * @param int $left
     * @param int $right
     * @param int $center
     */
    public function mergeArray(array &$arr, int $left, int $right, int $center)
    {
        echo 'sort before:' . $left . ' - ' . $center . ' - ' . $right . ' - ' . implode(',', $arr) . "\n";

        $left_i = $left;
        $right_i = $center + 1;
        $temp = [];
        while ($left_i <= $center && $right_i <= $right) {
            // 当数组A和数组B都没有越界时
            if ($arr[$left_i] < $arr[$right_i]) {
                $temp[] = $arr[$left_i++];
            } else {
                $temp[] = $arr[$right_i++];
            }
        }
        // 判断 数组A内的元素是否都用完了，没有的话将其全部插入到 temp 数组内：
        while ($left_i <= $center) {
            $temp[] = $arr[$left_i++];
        }
        // 判断 数组B内的元素是否都用完了，没有的话将其全部插入到 temp 数组内：
        while ($right_i <= $right) {
            $temp[] = $arr[$right_i++];
        }

        // 将 $arr 内排序好的部分，写入到 $arr 内：
        for ($i = 0, $len = count($temp); $i < $len; $i++) {
            $arr[$left + $i] = $temp[$i];
        }

        echo 'sort after :' . $left . ' - ' . $center . ' - ' . $right . ' - ' . implode(',', $arr) . "\n";
    }
}

$arr = [2, 1, 6, 3, 5, 4];
new MergeSort($arr);
```
**输出结果：**

```shell
sort before:0 - 0 - 1 - 2,1,6,3,5,4
sort after :0 - 0 - 1 - 1,2,6,3,5,4
sort before:0 - 1 - 2 - 1,2,6,3,5,4
sort after :0 - 1 - 2 - 1,2,6,3,5,4
sort before:3 - 3 - 4 - 1,2,6,3,5,4
sort after :3 - 3 - 4 - 1,2,6,3,5,4
sort before:3 - 4 - 5 - 1,2,6,3,5,4
sort after :3 - 4 - 5 - 1,2,6,3,4,5
sort before:0 - 2 - 5 - 1,2,6,3,4,5
sort after :0 - 2 - 5 - 1,2,3,4,5,6
Array
(
    [0] => 1
    [1] => 2
    [2] => 3
    [3] => 4
    [4] => 5
    [5] => 6
)
```

## 结论

归并排序比较占用内存，但却是一种效率高且稳定的算法。归并排序的最好，最坏，平均时间复杂度均为`O(nlogn)`。

## 参考文章

- [图解排序算法(四)之归并排序 - 博客园](https://www.cnblogs.com/chengxiao/p/6194356.html)
- [归并排序 - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/%E5%BD%92%E5%B9%B6%E6%8E%92%E5%BA%8F)


