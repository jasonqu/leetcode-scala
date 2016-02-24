## Array

### 简介

Array是能够随机访问的数据结构，优点是访问速度快，缺点是如果大小有变化，处理很困难，一般可以选择使用ArrayBuffer

ArrayBuffer不是immutable的，因此可以改用Vector

### 基本操作
todo scala提供了通用操作
有些题目划在了array名下，但实际上应该在动态数据结构中更合适，可以参考List的处理

#### 66	Plus One	32.6%	Easy
见List#2

#### 26	Remove Duplicates from Sorted Array	32.8%	Easy
见List#83

#### 27	Remove Element	32.9%	Easy

  array.filter(_ != value)

#### 88	Merge Sorted Array	29.8%	Easy
见List#21

#### 189	Rotate Array	20.4%	Easy
见List#61

#### 217	Contains Duplicate	40.2%	Easy
是否有重复

  arr.distinct == arr

#### 219	Contains Duplicate II	29.1%	Easy
是否有重复，且相同元素下标之差必须小于k
用Map保存下标 todo

#### 287	Find the Duplicate Number	37.7%	Hard
todo



#### 169 Majority Element  39.8% Easy
超过二分之一的元素

  arr.groupBy(t => t).filter(_._2.size >= arr.size / 2)

#### 229: Majority Element II
超过三分之一的元素

  arr.groupBy(t => t).filter(_._2.size >= arr.size / 3)

其他更优化的算法todo参见[这里](http://www.cnblogs.com/EdwardLiu/p/4179345.html)

#### 283 Move Zeroes 43.4% Easy
0 移到最后

  arr.filter(_ != 0) ++ arr.filter(_ == 0)

也可以使用两个指针来使空间复杂度达到O(1)


### 二分查找

[二分查找](https://en.wikipedia.org/wiki/Binary_search_algorithm)是最重要的算法之一，它能在一个有序的、可以随机访问的数据结构中，以log(n)的时间查找元素

scala内建了[binarysearch](https://github.com/scala/scala/blob/v2.11.0-M3/src/library/scala/collection/Searching.scala)：

todo
http://stackoverflow.com/questions/4226947/scala-replacement-for-arrays-binarysearch


  import scala.collection.Searching._
  List(1, 2, 3, 4, 5).search(3)

其中的二分实现非常简洁：

  sealed abstract class SearchResult {
    def insertionPoint: Int
  }

  case class Found(foundIndex: Int) extends SearchResult {
    override def insertionPoint = foundIndex
  }
  case class InsertionPoint(insertionPoint: Int) extends SearchResult

    @tailrec
    private def binarySearch[B >: A](elem: B, from: Int, to: Int)
    (implicit ord: Ordering[B]): SearchResult = {
      if (to == from) InsertionPoint(from) else {
        val idx = from+(to-from-1)/2
        math.signum(ord.compare(elem, coll(idx))) match {
          case -1 => binarySearch(elem, from, idx)(ord)
          case  1 => binarySearch(elem, idx + 1, to)(ord)
          case  _ => Found(idx)
        }
      }
    }

这里保证了`from <= elem`且`to > elem`

[自己实现](http://deepscala.blogspot.com/2013/08/binary-search-in-scala.html)可以这样：

* 迭代版本

```
import scala.collection.Searching._

def binarySearchIterative(arr: Array[Int], target: Int): SearchResult = {
  var left = 0
  var right = arr.length
  while (left < right) {
    val mid = left + (right - left - 1) / 2
    if (arr(mid) == target)
      return Found(mid)
    else if (arr(mid) > target)
      right = mid
    else
      left = mid + 1
  }
  InsertionPoint(right)
}
```

* 递归版本

```
def binarySearchRecursive(arr: Array[Int], target: Int, left: Int, right: Int): SearchResult = {
  val mid = left + (right - left - 1) / 2
  if (left == right)
    InsertionPoint(right)
  else if (arr(mid) == target)
    Found(mid)
  else if (arr(mid) > target)
    binarySearchRecursive(arr, target, left, mid)
  else
    binarySearchRecursive(arr, target, mid + 1, right)
}
```

这里保证了`left <= target`，而`right > target`

现在可以应用它了

#### 35	Search Insert Position

  binarySearchIterative(arr, target: Int).insertionPoint

不过对大部分问题不能直接应用，还需要将这个算法改造一下

#### 34	Search for a Range	28.6%	Medium
Given [5, 7, 7, 8, 8, 10] and target value 8, return [3, 4].
  
原来的算法保证了`left <= target 且 right > target`。
这里需要使之始终返回right。很简单，去掉相等时返回Found的逻辑即可：

```
import scala.collection.Searching._
def binarySearchIterative(arr: Array[Int], target: Int): SearchResult = {
  var left = 0
  var right = arr.length
  while (left < right) {
    val mid = left + (right - left - 1) / 2
    if (arr(mid) > target)
      right = mid
    else
      left = mid + 1
  }
  InsertionPoint(right - 1)
}

def binarySearchRecursive(arr: Array[Int], target: Int, left: Int, right: Int): SearchResult = {
  val mid = left + (right - left - 1) / 2
  if (left == right)
    InsertionPoint(right - 1)
  else if (arr(mid) > target)
    binarySearchRecursive(arr, target, left, mid)
  else
    binarySearchRecursive(arr, target, mid + 1, right)
}

// 这里保证了left<=target，而right>target
val arr = Array(0, 1, 2, 2, 2, 4, 5, 7)
arr.zipWithIndex
binarySearchRecursive(arr, -1, 0, arr.length)
binarySearchRecursive(arr, 2, 0, arr.length)
binarySearchRecursive(arr, 3, 0, arr.length)
binarySearchRecursive(arr, 100, 0, arr.length)
```

但是要保证`left<target 且 right>=target`，并返回left呢？需要这样修改：

```
def binarySearchIterative(arr: Array[Int], target: Int): SearchResult = {
  var left = 0
  var right = arr.length
  while (left < right) {
    val mid = left + (right - left - 1) / 2
    if (arr(mid) >= target)
      right = mid
    else
      left = mid + 1
  }
  InsertionPoint(left - 1)
}

def binarySearchRecursive(arr: Array[Int], target: Int, left: Int, right: Int): SearchResult = {
  val mid = left + (right - left - 1) / 2
  if (left == right)
    InsertionPoint(left)
  else if (arr(mid) < target)
    binarySearchRecursive(arr, target, mid + 1, right)
  else
    binarySearchRecursive(arr, target, left, mid)
}

val arr = Array(0, 1, 2, 2, 2, 4, 5, 7)
arr.zipWithIndex
binarySearchRecursive(arr, -1, 0, arr.length)
binarySearchRecursive(arr, 2, 0, arr.length)
binarySearchRecursive(arr, 3, 0, arr.length)
binarySearchRecursive(arr, 100, 0, arr.length)
```

用改造后的二分就可以找出上下界了，如果下界大于上界，即`left > right`则表示没有。
（ps，还有一个小优化，就是将上一个计算的下标结果，应用于第二个下标的计算，可以使用recursive的方式）


#### 153	Find Minimum in Rotated Sorted Array
Suppose a sorted array is rotated at some pivot unknown to you beforehand.
(i.e., 0 1 2 4 5 6 7 might become 4 5 6 7 0 1 2).
Find the minimum element.
You may assume no duplicate exists in the array.

注意实际有三种情况
* `mid>left mid>right`
* `mid<left mid<right`
* `mid>left mid<right` 重要，此时left在最小点上 

```
val arr = Array(4, 5, 6, 7, 0, 1, 2)
import scala.collection.Searching._

def findMinimumInRotatedSortedArray(arr: Array[Int], left: Int, right: Int): SearchResult = {
  val mid = left + (right - left - 1) / 2
  if (left == right)
    InsertionPoint(left)
  else if (arr(mid) < arr(left))
    findMinimumInRotatedSortedArray(arr, left, mid)
  else if (arr(mid) > arr(right - 1))
    findMinimumInRotatedSortedArray(arr, mid + 1, right)
  else
    InsertionPoint(left)
}

arr.zipWithIndex
findMinimumInRotatedSortedArray(arr, 0, arr.length)
```

#### 154	Find Minimum in Rotated Sorted Array II
如果允许重复的话，极端情况下，会退化为O(n)，如(2,2,2,1,2,2)
todo









同查找Minimum类似的题目：

#### 33	Search in Rotated Sorted Array

* 方案1：首先比较一下target两个边值，确定在那个区间，然后找到最小值下标，然后在正确的区间里查找
* 方案2：类似153，列出所有的可能性，写代码

#### 81	Search in Rotated Sorted Array II
不过如果允许重复的话，极端情况下，会退化为O(n)，如(2,2,2,1,2,2)

todo

#### 162	Find Peak Element
如果中间元素大于其相邻后续元素，则中间元素左侧(包含该中间元素）必包含一个局部最大值。如果中间元素小于其相邻后续元素，则中间元素右侧必包含一个局部最大值。
参见[这里](http://blog.csdn.net/u010367506/article/details/41943309)
todo

#### 278	First Bad Version
简，略


### Matrix
二维数组，就是`Array[Array[Int]]`

#### 74	Search a 2D Matrix
Integers in each row are sorted from left to right. The first integer of each row is greater than the last
integer of the previous row. For example,

  [
    [1, 3, 5, 7],
    [10, 11, 16, 20],
    [23, 30, 34, 50]
  ]

使用二分法，先纵col，后横row即可
todo

240 Search a 2D Matrix II

  [
    [1,   4,  7, 11, 15],
    [2,   5,  8, 12, 19],
    [3,   6,  9, 16, 22],
    [10, 13, 14, 17, 24],
    [18, 21, 23, 26, 30]
  ]

需要环绕着查找来缩小范围，col->row->col->row->...
todo


### 结合List和Array

#### 228	Summary Ranges	23.0%	Easy
Given a sorted integer array without duplicates, return the summary of its ranges.

For example, given [0,1,2,4,5,7], return ["0->2","4->5","7"].

使用范围代替排序好的数组【无重复】

```
var list = List[String]()
var lastValue = arr(0)
var initIndex = 0
for (i <- 1 until arr.length) {
  if (arr(i) != lastValue + 1) {
    list = arr(initIndex) + "->" + lastValue :: list
    initIndex = i
  }
  lastValue = arr(i)
}
arr(initIndex) + "->" + lastValue :: list reverse
```

#### 118	Pascal's Triangle	32.4%	Easy
帕斯卡三角：可以从上一个Array的状态推出下一个Array的状态

```
  @tailrec
  def getTriRecur(n: Int, res: List[Array[Int]]): List[Array[Int]] = {
    if (n == 0) res
    else getTriRecur(n - 1, genNext(res.head) :: res)
  }

  def genNext(last: Array[Int]): Array[Int] = {
    val arr = Array.fill(last.length + 1)(1)
    for (i <- 1 until last.length) {
      arr(i) = last(i - 1) + last(i)
    }
    arr
  }

  def getTri(n: Int): List[Array[Int]] = {
    getTriRecur(n - 1, Array(1) :: Nil)
  }

  getTri(10).foreach(a => println(a.toList))
```

#### 119	Pascal's Triangle II	31.4%	Easy
只用一个数组，更加节省空间，这时每个小循环只需要从后往前计算即可

```
  def getSingleTri(n: Int): Array[Int] = {
    val arr = Array.fill(n)(0)
    arr(n - 1) = 1
    for (
      i <- n - 2 to 0 by -1;
      j <- i to n - 2
    ) arr(j) = arr(j) + arr(j + 1)
    arr
  }
```
