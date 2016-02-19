array 是能够随机访问的数据结构，优点是访问速度快，缺点是如果大小有变化，处理很困难，一般要使用ArrayBuffer

ArrayBuffer不是immutable的，因此可以改用Vector

有些题目划在了array名下，但实际上应该在动态数据结构中更合适，可以参考List的处理
66	Plus One	32.6%	Easy
> refer to #2

26	Remove Duplicates from Sorted Array	32.8%	Easy
> array.distinct
27	Remove Element	32.9%	Easy
> array.filter(_ != value)

88	Merge Sorted Array	29.8%	Easy
189	Rotate Array	20.4%	Easy



217	Contains Duplicate	40.2%	Easy
arr.distinct == arr
219	Contains Duplicate II	29.1%	Easy
相同元素下标之差必须小与k
用Map保存下标

287	Find the Duplicate Number	37.7%	Hard




### 二分

是最重要的算法之一，在一个有序的、可以随机访问的数据结构中，以log(n)的时间查找元素

scala内建了binarysearch
http://stackoverflow.com/questions/4226947/scala-replacement-for-arrays-binarysearch
https://github.com/scala/scala/blob/v2.11.0-M3/src/library/scala/collection/Searching.scala

import scala.collection.Searching._
List(1, 2, 3, 4, 5).search(3)

其中的二分实现非常简洁
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

这里保证了from<=elem，to>elem


对leetcode的问题，简写可以这样 
https://en.wikipedia.org/wiki/Binary_search_algorithm
http://deepscala.blogspot.com/2013/08/binary-search-in-scala.html

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

这里保证了left<=target，而right>target

现在可以应用它了

35	Search Insert Position
binarySearchIterative(arr, target: Int).insertionPoint

然而大部分问题需要将这个算法简单改造一下

34	Search for a Range	28.6%	Medium
> Given [5, 7, 7, 8, 8, 10] and target value 8,
  return [3, 4].
  
原来的算法保证了left<=target，而right>target
这里改造一下，使之始终返回right。很简单，去掉相等时返回Found的逻辑即可

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

recursive方式，以及测试代码

val arr = Array(0, 1, 2, 2, 2, 4, 5, 7)
import scala.collection.Searching._

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
arr.zipWithIndex
binarySearchRecursive(arr, -1, 0, arr.length)
binarySearchRecursive(arr, 2, 0, arr.length)
binarySearchRecursive(arr, 3, 0, arr.length)
binarySearchRecursive(arr, 100, 0, arr.length)

但是要保证left<target，而right>=target，且返回left呢

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

val arr = Array(0, 1, 2, 2, 2, 4, 5, 7)
import scala.collection.Searching._

def binarySearchRecursive(arr: Array[Int], target: Int, left: Int, right: Int): SearchResult = {
  val mid = left + (right - left - 1) / 2
  if (left == right)
    InsertionPoint(left)
  else if (arr(mid) < target)
    binarySearchRecursive(arr, target, mid + 1, right)
  else
    binarySearchRecursive(arr, target, left, mid)
}

// 这里保证了left<=target，而right>target
arr.zipWithIndex
binarySearchRecursive(arr, -1, 0, arr.length)
binarySearchRecursive(arr, 2, 0, arr.length)
binarySearchRecursive(arr, 3, 0, arr.length)
binarySearchRecursive(arr, 100, 0, arr.length)

用改造后的二分就可以找出上下界了，如果下界大于上界 left > right 则表示没有
（ps，还有一个小优化，如果能将上一个计算的下标结果，应用于第二个下标的计算，可以使用recursive的方式）



153	Find Minimum in Rotated Sorted Array
Suppose a sorted array is rotated at some pivot unknown to you beforehand.
(i.e., 0 1 2 4 5 6 7 might become 4 5 6 7 0 1 2).
Find the minimum element.
You may assume no duplicate exists in the array.
注意实际有三种情况
* mid>left mid>right 
* mid<left mid<right 
* mid>left mid<right 重要，此时left在最小点上 

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

// 这里保证了left<=target，而right>target
arr.zipWithIndex
findMinimumInRotatedSortedArray(arr, 0, arr.length)


154	Find Minimum in Rotated Sorted Array II
如果允许重复的话，极端情况下，会退化为O(n)，如(2,2,2,1,2,2)










通查找Minimum类似
33	Search in Rotated Sorted Array
方案1：首先比较一下target两个边值，确定在那个区间，然后找到最小值下标，然后在正确的区间里查找
方案2：类似153，列出所有的可能性，写代码
81	Search in Rotated Sorted Array II
不过如果允许重复的话，极端情况下，会退化为O(n)，如(2,2,2,1,2,2)

162	Find Peak Element
如果中间元素大于其相邻后续元素，则中间元素左侧(包含该中间元素）必包含一个局部最大值。如果中间元素小于其相邻后续元素，则中间元素右侧必包含一个局部最大值。
http://blog.csdn.net/u010367506/article/details/41943309

278	First Bad Version
略







74	Search a 2D Matrix
Integers in each row are sorted from left to right. The first integer of each row is greater than the last
integer of the previous row. For example,
[
  [1, 3, 5, 7],
  [10, 11, 16, 20],
  [23, 30, 34, 50]
]
先col，后row即可

240 Search a 2D Matrix II
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
需要环绕着，col->row->col->row->...
todo







4	Median of Two Sorted Arrays




209	Minimum Size Subarray Sum
167	Two Sum II - Input array is sorted 


287	Find the Duplicate Number
Array Two Pointers Binary Search
37.7%	Hard



















1	Two Sum	21.1%	Medium
167	Two Sum II - Input array is sorted 	47.1%	Medium





	85	Maximal Rectangle	23.0%	Hard
154	Find Minimum in Rotated Sorted Array II	33.8%	Hard
33	Search in Rotated Sorted Array	29.8%	Hard
123	Best Time to Buy and Sell Stock III	25.7%	Hard
126	Word Ladder II	13.5%	Hard
56	Merge Intervals	24.5%	Hard
4	Median of Two Sorted Arrays	18.1%	Hard
42	Trapping Rain Water	31.6%	Hard
128	Longest Consecutive Sequence	31.4%	Hard
84	Largest Rectangle in Histogram	23.7%	Hard
45	Jump Game II	24.9%	Hard
57	Insert Interval	23.2%	Hard
41	First Missing Positive	23.5%	Hard
209	Minimum Size Subarray Sum	26.1%	Medium
15	3Sum	18.2%	Medium
245	Shortest Word Distance III 	45.7%	Medium
73	Set Matrix Zeroes	32.9%	Medium
35	Search Insert Position	36.8%	Medium
280	Wiggle Sort 	49.2%	Medium
81	Search in Rotated Sorted Array II	31.7%	Medium
74	Search a 2D Matrix	33.2%	Medium
48	Rotate Image	33.9%	Medium
18	4Sum	23.1%	Medium
122	Best Time to Buy and Sell Stock II	41.3%	Medium
62	Unique Paths	35.3%	Medium
80	Remove Duplicates from Sorted Array II	32.0%	Medium
238	Product of Array Except Self	41.3%	Medium
121	Best Time to Buy and Sell Stock	35.2%	Medium
40	Combination Sum II	26.9%	Medium
216	Combination Sum III	34.2%	Medium
31	Next Permutation	26.0%	Medium
39	Combination Sum	30.1%	Medium
163	Missing Ranges 	28.2%	Medium
268	Missing Number	39.1%	Medium
63	Unique Paths II	29.0%	Medium
64	Minimum Path Sum	34.1%	Medium
106	Construct Binary Tree from Inorder and Postorder Traversal	28.4%	Medium
53	Maximum Subarray	36.0%	Medium
152	Maximum Product Subarray	21.5%	Medium
120	Triangle	29.4%	Medium
105	Construct Binary Tree from Preorder and Inorder Traversal	27.9%	Medium
229	Majority Element II	25.1%	Medium
79	Word Search	22.3%	Medium
259	3Sum Smaller 	37.7%	Medium
55	Jump Game	27.9%	Medium
78	Subsets	30.5%	Medium
90	Subsets II	29.8%	Medium
289	Game of Life	33.4%	Medium
54	Spiral Matrix	22.0%	Medium
59	Spiral Matrix II	34.0%	Medium
277	Find the Celebrity 	34.9%	Medium
162	Find Peak Element	32.7%	Medium
153	Find Minimum in Rotated Sorted Array	35.5%	Medium
75	Sort Colors	34.1%	Medium
11	Container With Most Water	33.8%	Medium
16	3Sum Closest	28.4%	Medium




169	Majority Element	39.8%	Easy
arr.groupBy(t => t).filter(_._2.size >= arr.size / 2)
229: Majority Element II
arr.groupBy(t => t).filter(_._2.size >= arr.size / 3)

其他方法
http://www.cnblogs.com/EdwardLiu/p/4179345.html


283	Move Zeroes	43.4%	Easy
0 移到最后
arr.filter(_ != 0) ++ arr.filter(_ == 0)

两个指针，这样空间复杂度就是O(1)了

243	Shortest Word Distance 	45.7%	Easy


228	Summary Ranges	23.0%	Easy

Given a sorted integer array without duplicates, return the summary of its ranges.

For example, given [0,1,2,4,5,7], return ["0->2","4->5","7"].

使用范围代替排序好的数组【无重复】
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














dyn
119	Pascal's Triangle II	31.4%	Easy
118	Pascal's Triangle	32.4%	Easy
