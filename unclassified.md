* [ ] 89	Gray Code	35.4%	Medium
格雷码

https://zh.wikipedia.org/wiki/%E6%A0%BC%E9%9B%B7%E7%A0%81
提供了一个非常非常精妙的介绍

实现如下：

```
def grey(n :Int) : List[String] = {
  if(n == 1) List("0", "1")
  else {
    val l = grey(n - 1)
    l.map("0" + _) ::: l.reverse.map("1" + _)
  }
}
```

另外wiki中还介绍了公式法：G(N) = (B(n)/2) XOR B(n)

scala binary string http://stackoverflow.com/questions/9442381/formatting-binary-values-in-scala

```
def grey(n :Int) : List[String] = {
  val result = new Array[Int](1 << n)
  for(i <- result.indices)
    result(i) = (i >> 1) ^ i

  result.map(v => String.format(
    "%" + n + "s", v.toBinaryString).replace(' ', '0')).toList
}
```




scala 内建的排列组合方法：
Seq(1,2,3).combinations(2).toList
Seq(1,2,3).permutations.toList


* [ ] 77	Combinations	33.3%	Medium
给定数字 n 和 k, 返回[1, n]所有的k数组合，例如 n = 4， k = 2时，结果是：
[
	[2,4],
	[3,4],
	[2,3],
	[1,2],
	[1,3],
	[1,4],
]

思路，递归，保留递归深度，如果深度为k表示形成一个组合，将所有深度为0的组合连接在一起就可以了


```
def comb(last: Int, k: Int, n: Int, thisList: List[Int], list: List[List[Int]]): List[List[Int]] = {
  if (k == 0) thisList :: list
  else {
    var l = list
    for (i <- last + 1 to n)
      l = comb(i, k - 1, n, i :: thisList, l)
    l
  }
}

comb(0, 2, 4, Nil, Nil)
```

去掉var

```
def comb(last: Int, k: Int, n: Int, thisList: List[Int], list: List[List[Int]]): List[List[Int]] = {
  if (k == 0) thisList :: list
  else (last + 1 to n).foldLeft(list) {(l, v) =>
    comb(v, k - 1, n, v :: thisList, l)
  }
}

comb(0, 3, 4, Nil, Nil)
```

逻辑比较复杂，更重要的是，当n和k很大时，将变成一个NP的数据量的问题，所以考虑是不是能够通过迭代Stream的方式获取
http://stackoverflow.com/questions/127704/algorithm-to-return-all-combinations-of-k-elements-from-n

```
def comb(k:Int, n:Int) :Stream[Seq[Int]] = {
  (0 until 1 << n).toStream
    .map(i => (i >> 1) ^ i)
    .map(v => String.format("%" + n + "s", v.toBinaryString).replace(' ', '0'))
    .filter(_.count(_ == '1') == k)
    .map(_.reverse)
    .map(_.zipWithIndex.filter(_._1 == '1').map(_._2 + 1))
}

//"1100".zipWithIndex.filter(_._1 == '1').map(_._2 + 1)

comb(2, 4).tail
comb(2, 4).toList
```

通过修改gray的实现达到了目的，但是计算量是2^n次方，存在大量无效计算。别的改进可能要具体参考文献了，或阅读scala内建的代码

这个问题的一个scala版本的回答：http://stackoverflow.com/a/2602811/851099



* [ ] 39	Combination Sum	30.1%	Medium
求某集合中所有给定和为目标的组合，数字可以出现多次
思路参见dynamic 279的方法，
获取所有可能就可以通过遍历获取了：

```
def combsum(target: Int, currentIndex:Int, set:Seq[Int], thisList: List[Int], list: List[List[Int]]): List[List[Int]] = {
  if(thisList.sum > target) list
  else if(thisList.sum == target) thisList :: list
  else {
    (currentIndex until set.size).foldLeft(list){(l, v) =>
      combsum(target, v, set, set(v) :: thisList, l)
    }
  }
}

combsum(7, 0, Seq(2,3,4,7), Nil, Nil)
```



* [ ] 40	Combination Sum II	26.9%	Medium
每个数字只能出现一次
参考上一题
只需要在递归调用时保证currentIndex + 1即可

```
def combsum(target: Int, currentIndex:Int, set:Seq[Int], thisList: List[Int], list: List[List[Int]]): List[List[Int]] = {
  if(thisList.sum > target) list
  else if(thisList.sum == target) thisList :: list
  else {
    (currentIndex until set.size).foldLeft(list){(l, v) =>
      combsum(target, v + 1, set, set(v) :: thisList, l)
    }
  }
}
```

参考 todo toread
http://stackoverflow.com/questions/10115967/whats-the-most-memory-efficient-way-to-generate-the-combinations-of-a-set-in-py

letter combanition of phone
笛卡尔积

[a,b] x [1,2,3] x [x,y,z]

```
def p(z:List[Seq[Char]]):Seq[String] = {
  z.foldRight(Seq[String]()) {(x, l) =>
    l match {
      case Nil => x.map(_.toString)
      case _ => x.flatMap(c => l.map(c + _))
    }
  }
}

p(Seq('a','b','c') :: Seq('x','y','z') :: Nil)
```

scala无内建的，网上有一些简单的方案，需要关注：
* http://stackoverflow.com/questions/14740199/cross-product-in-scala
* http://stackoverflow.com/questions/8217764/cartesian-product-of-two-lists


permutation
Given a collection of distinct numbers, return all possible permutations.

For example,
[1,2,3] have the following permutations:
[1,2,3], [1,3,2], [2,1,3], [2,3,1], [3,1,2], and [3,2,1].


笛卡尔积之后filter一下就可以了？
n^k - n!/n-k!
效率太低了

```
def perm(set:Set[Int], thisList: List[Int], list:List[List[Int]]):List[List[Int]] = {
  if(set.isEmpty) thisList :: list
  else set.foldLeft(list){(l,x) => perm(set - x, x:: thisList, l)}
}

perm(Set(1,2,3), Nil, Nil)
```



permutation II
如果在集合中有重复的数字，求排列的集合

首先改进perm使之能够支持重复数字，此时需要使用Array来简化操作：

```
def swap(arr:Array[Int], x:Int, y:Int) = {
  val t = arr(x)
  arr(x) = arr(y)
  arr(y) = t
}
def perm(arr:Array[Int], currentIndex:Int, list:List[List[Int]]):List[List[Int]] = {
  println(s"perm ${arr.deep} $currentIndex $list")
  if(currentIndex == arr.length) arr.toList :: list
  else {
    var l = list
    for(i <- currentIndex until arr.length) {
      swap(arr, i, currentIndex)
      l = perm(arr, currentIndex + 1, l)
      swap(arr, i, currentIndex)
    }
    l
  }
}

perm(Array(1,2,3), 0, Nil)
```

求perm之后在distinct一下

	perm(Seq(1,1,2), 0, Nil).distinct

显然这个是需要优化的，优化的关键是剪枝（参考了这里http://leetcode.tanglei.name/）

```
def swap(arr:Array[Int], x:Int, y:Int) = {
  val t = arr(x)
  arr(x) = arr(y)
  arr(y) = t
}
def perm(arr:Array[Int], currentIndex:Int, list:List[List[Int]]):List[List[Int]] = {
  println(s"perm ${arr.deep} $currentIndex $list")
  if(currentIndex == arr.length) arr.toList :: list
  else {
    var l = list
    for(
      i <- currentIndex until arr.length;
      if i == currentIndex || arr(i) != arr(currentIndex) //culling
    ) {
      swap(arr, i, currentIndex)
      l = perm(arr, currentIndex + 1, l)
      swap(arr, i, currentIndex)
    }
    l
  }
}

perm(Array(1,1,2), 0, Nil)
```


Next Permutation
给一个perm，求出它的下一个perm的值，例如
1,2,3 → 1,3,2 3,2,1 → 1,2,3 1,1,5 → 1,5,1

找到从后向前升序的第一个反例，交换这个反例和最后的值，然后reverse反例后面的数据

```
def swap(arr:Array[Int], x:Int, y:Int) = {
  val t = arr(x)
  arr(x) = arr(y)
  arr(y) = t
}
def nextperm(arr:Array[Int]):Array[Int] = {
  var x = -1
  var i = arr.length - 1
  while(i > 0 && x == -1) {
    if(arr(i - 1) < arr(i)) x = i - 1
    i = i - 1
  }

  println(x)
  if(x == -1) arr.reverse
  else {
    swap(arr, x, arr.length - 1)
    for(i <- 1 to (arr.length - 1 - x) / 2)
      swap(arr, x + i, arr.length - i)
    arr
  }
}

nextperm(Array(1,1,5,4,3,2))
```

Permutation Sequence
求由n个数组成的第k个排列

康托展开
https://zh.wikipedia.org/wiki/%E5%BA%B7%E6%89%98%E5%B1%95%E5%BC%80
http://leetcode.tanglei.name/content/combination-permutation/Permutation-Sequence.html


Subsets

Given a set of distinct integers, S, return all possible subsets. Note: Elements in a subset must be in
non-descending order. The solution set must not contain duplicate subsets. For example, If S = [1,2,3], a
solution is: [ [3], [1], [2], [1,2,3], [1,3], [2,3], [1,2], [] ]

参考77，通过修改Gray来达到目标，去掉“1”的数量为k的限制即可

```
def subset(n:Int) :Stream[Seq[Int]] = {
  (0 until 1 << n).toStream
    .map(i => (i >> 1) ^ i)
    .map(v => String.format("%" + n + "s", v.toBinaryString).replace(' ', '0'))
    //.filter(_.count(_ == '1') == k)
    .map(_.reverse)
    .map(_.zipWithIndex.filter(_._1 == '1').map(_._2 + 1))
}
```

Subsets II
如果允许重复数字呢？
If S = [1,2,2], a solution is: [ [2], [1], [1,2,2], [2,2], [1,2], [] ]

可以从上题的结果直接distinct

或简化？todo http://www.cnblogs.com/x1957/p/3517989.html












### 应该划到string？

224	Basic Calculator	21.2%	Medium
非负整数 括号的四则运算
略？


43	Multiply Strings	22.9%	Medium
将string代表的数字相乘
数字可能很大，超过Int等
todo







60	Permutation Sequence	24.6%	Medium
求出排好序的第k个排列
todo


293	Flip Game 	49.1%	Easy


#### 5	Longest Palindromic Substring	22.5%	Medium
#### 3	Longest Substring Without Repeating Characters	21.6%	Medium
76	Minimum Window Substring	20.7%	Hard

#### 214	Shortest Palindrome	19.0%	Hard

dynamic ?


157	Read N Characters Given Read4 	29.3%	Easy
43	Multiply Strings	22.9%	Medium
93	Restore IP Addresses	22.8%	Medium
17	Letter Combinations of a Phone Number	27.9%	Medium
161	One Edit Distance 	27.8%	Medium
22	Generate Parentheses	35.8%	Medium
91	Decode Ways	17.2%	Medium
72	Edit Distance	28.1%	Hard
126	Word Ladder II	13.5%	Hard
158	Read N Characters Given Read4 II - Call multiple times 	23.1%	Hard
159	Longest Substring with At Most Two Distinct Characters 	33.7%	Hard
97	Interleaving String	22.1%	Hard
87	Scramble String	26.1%	Hard
115	Distinct Subsequences	28.3%	Hard
30	Substring with Concatenation of All Words	20.5%	Hard





### 开放问题

#### 65	Valid Number	11.9%	Hard

Validate if a given string is numeric.

Some examples:
"0" => true
" 0.1 " => true
"abc" => false
"1 a" => false
"2e10" => true
Note: It is intended for the problem statement to be ambiguous. You should gather all requirements up front before implementing one.



#### 271	Encode and Decode Strings 	26.9%	Medium
Design an algorithm to encode a list of strings to a string. The encoded string is then sent over the network and is decoded back to the original list of strings.

Machine 1 (sender) has the function:

string encode(vector<string> strs) {
  // ... your code
  return encoded_string;
}
Machine 2 (receiver) has the function:

vector<string> decode(string s) {
  //... your code
  return strs;
} 

