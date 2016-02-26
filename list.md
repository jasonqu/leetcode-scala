## LinkedList

### 简介

List是scala最重要的数据结构之一，学好它是掌握scala的重要基础。这里将复用scala中的List来做题。

由于scala中的List是immutable的，没有环，所以下面的问题将忽略：

* 142	Linked List Cycle II	31.5%	Medium
* 141	Linked List Cycle	36.8%	Medium
* 138	Copy List with Random Pointer	25.9%	Hard
* 160	Intersection of Two Linked Lists	30.0%	Easy

### 基本操作

#### 203	Remove Linked List Elements	27.8%	Easy
删除所有给定值

	List(1, 2, 3, 3, 5).filter(_ != 3)

#### 237	Delete Node in a Linked List	43.8%	Easy
删除单个节点

	val (f, s) = list.span(_ < 3)
	if(s == Nil) f else f ::: s.tail

#### 19	Remove Nth Node From End of List	28.6%	Easy
删除第N个节点

	val (f, s) = list.splitAt(k)
	if(s == Nil) f else f ::: s.tail

#### 61	Rotate List	22.5%	Medium
从指定点翻转列表

	val (l, r) = List(1, 1, 2, 2, 2, 3, 3, 4, 5).splitAt(4)
	r ::: l


#### 83	Remove Duplicates from Sorted List	36.1%	Easy
去重

	list.distinct

#### 82	Remove Duplicates from Sorted List II	26.3%	Medium
去重，把有重复的都滤掉

  list.filter(list.groupBy(t => t).filter(_._2.size == 1).keySet.contains)

由于已经排序，所以也可以一遍搞定，并使用O(1)的空间复杂度。思路是用一个`boolean`表示元素是否已经出现过：

```
list.tail.foldLeft((List[Int](list.head), false)) {(res, v) =>
  val (l, twice) = res
  if(l == Nil || v == l.head) {
    (l, true)
  } else {
    if(twice) (v :: l.tail, false) else (v :: l, false)
  }
}
```

#### 206	Reverse Linked List	38.0%	Easy
反转

  list.reverse

自己也可以通过foldRight一次遍历实现reverse方法

#### 92	Reverse Linked List II	27.3%	Medium
指定`[m,n]`为要反转的部分。注：这里m,n是1开头的，所以写代码时需要减1

  val (f, s) = list.splitAt(m - 1)
  val (r, t) = s.splitAt(n - m + 1)
  f ::: r.reverse ::: t


#### 25	Reverse Nodes in k-Group	26.9%	Hard
将队列按照`k`分组，并反转每一组。

  list.grouped(k).flatMap(_.reverse).toList

时间满足要求，但明显空间复杂度不是`O(1)`，通过类似`No.82`的方式一边遍历也可以达到目的。

#### 24 Swap Nodes in Pairs 34.4% Medium
即`No.25`中`k=2`的情况。

#### 234	Palindrome Linked List	26.8%	Easy
回文队列

  list.reverse == list

空间复杂度O(1)

  list.foldRight(true, list) {(v, r) =>
    val (pal, l)= r
    if (!pal || v != l.head) (false, Nil)
    else (true, l.tail)
  }._1

#### 143	Reorder List	22.3%	Medium
按特殊规则重排序：

```
val list = List(1,2,3,4,5,6)
if(size % 2 == 0) {
  val (l :: r :: _) = list.grouped(size/2).toList
  l.zip(r.reverse).flatMap(t => t._1 :: t._2 :: Nil)
} else {
  val (l :: r :: _) = list.grouped(size/2 + 1).toList
  l.zipAll(r.reverse, 0, 0).flatMap(t => t._1 :: t._2 :: Nil).init
}
```

或

```
val size = list.size
val (l :: r :: _) = list.grouped(if(size % 2 == 0) size/2 else size/2 + 1).toList

if(size % 2 == 0)
  l.zipAll(r.reverse, 0, 0).flatMap(t => t._1 :: t._2 :: Nil)
else
  l.zipAll(r.reverse, 0, 0).flatMap(t => t._1 :: t._2 :: Nil).init
```

### 划分与合并

将List按照一定规则划分为多个List或合并为一个List也是经常出现的题目：

#### 21	Merge Two Sorted Lists	34.6%	Easy
合并排好序的List(参考了[Scala by Example](http://www.scala-lang.org/docu/files/ScalaByExample.pdf))

  def merge(xs1: List[Int], xs2: List[Int]): List[Int] =
    if (xs1.isEmpty) xs2
    else if (xs2.isEmpty) xs1
    else if (xs1.head < xs2.head) xs1.head :: merge(xs1.tail, xs2)
    else xs2.head :: merge(xs1, xs2.tail)

#### 23	Merge k Sorted Lists	22.7%	Hard
合并k个List，简单的做法是一个个处理，时间复杂度是O(k * n)

  lists.foldLeft(List[Int]()) {(res, l) => merge(res, l) }

但这样可能导致大量的重复操作。一次遍历可以通过建堆的方式完成，堆的描述见[Tree](tree.md)

#### 2  Add Two Numbers	22.2%	Medium
单个位数相加，要考虑进位

  def addTwoNumbers(l1: List[Int], l2: List[Int]): List[Int] = {
    var shouldAdd = false
    val result = l1.zipAll(l2, 0, 0).reverse.foldRight(List[Int]()) { case ((a, b), lastList) =>
      val sum = if (shouldAdd) a + b + 1 else a + b
      val result = sum % 10
      shouldAdd = sum >= 10
      result :: lastList
    }

    if (shouldAdd) (1 :: result).reverse else result.reverse
  }

#### 86 Partition List	28.9%	Medium
按值划分

  list.partition(_ < value)

#### 328  Odd Even Linked List	37.8%	Easy
按奇偶划分

  list.partition(_ % 2 == 1)

#### 148  Sort List	24.1%	Medium
排序，对List来说，由于不能随机访问，所以最好的排序方式是MergeSort(参考了[Scala by Example](http://www.scala-lang.org/docu/files/ScalaByExample.pdf))：

  def msort[A](less: (A, A) => Boolean)(xs: List[A]): List[A] = {
    def merge(xs1: List[A], xs2: List[A]): List[A] =
      if (xs1.isEmpty) xs2
      else if (xs2.isEmpty) xs1
      else if (less(xs1.head, xs2.head)) xs1.head :: merge(xs1.tail, xs2)
      else xs2.head :: merge(xs1, xs2.tail)
    val n = xs.length/2
    if (n == 0) xs
    else merge(msort(less)(xs take n), msort(less)(xs drop n))
  }

#### 147  Insertion Sort List	28.6%	Medium
价值不大，略
