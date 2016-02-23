动态规划

### 概念

动态规划类似于数学归纳法，创建递推公式，然后从初始状态寻找规律

什么是动态规划？动态规划的意义是什么？
https://www.zhihu.com/question/23995189

动态规划：从新手到专家
http://www.hawstein.com/posts/dp-novice-to-advanced.html
https://www.topcoder.com/community/data-science/data-science-tutorials/dynamic-programming-from-novice-to-advanced/

背包问题九讲
http://love-oriented.com/pack/



* 53	Maximum Subarray	36.0%	Medium
For example, given the array [-2,1,-3,4,-1,2,1,-5,4], the contiguous subarray [4,-1,2,1] has the
largest sum = 6.
local 与 global 找最优
dp[i + 1] = max(dp[i], dp[i] + a[i + 1])

```
def maxSub(arr: Array[Int]) : Int = {
  var global = arr(0)
  var local = arr(0)
  for(i <- 1 until arr.length) {
    local = max(arr(i), local + arr(i))
    global = max(local, global)
  }
  global
}
```

* 152	Maximum Product Subarray	21.5%	Medium
题目要求是Array[Int]，所以比较简单，只需关注上一个值是否为0即可。
TODO

```
def maxProd(arr: Array[Int]) : Int = {
  var global = arr(0)
  var localabs = arr(0)
  for(i <- 1 until arr.length) {
    if(localabs == 0) {
      localabs = arr(i)
      global = max(global, max(localabs, 0))
    } else {
      localabs = localabs * arr(i)
      global = max(global, max(localabs, arr(i)))
    }
  }
  global
}

maxProd(Array(-2, 1, -3, 4, -1, 2, 1, -5, 4))
maxProd(Array(2,3,-2,4))
Array(-2, 1, -3, 4, -1, 2, 1, -5, 4).product

maxProd(Array(-2, 0, -3, 0, -1, 0))
maxProd(Array(-3))
```

如果是double呢？





























Best Time to Buy and Sell Stock系列
http://liangjiabin.com/blog/2015/04/leetcode-best-time-to-buy-and-sell-stock.html


* 121	Best Time to Buy and Sell Stock	35.2%	Medium
只有一次机会

dp(n)表示一个算法，其结果类型为(i:Int, j:Int)表示买入和卖出点坐标，其中i≤j，使之对任意0≤i1≤j1≤n，有arr(j) - arr(i) ≥ arr(j1) - arr(i1)

保留两个值，local最大值和global最大值，如果local大于global，则使用local的替换global的
这里local是指以arr(n)为卖出点的最大值，只要拿当前值减去当前遇到的最小值即可

curMin 表示当前遇到的最小值坐标 currentMinIndex

```
dp(n) = 
        curMin = if(arr(n) < arr(curMin)) n else curMin 
        if(arr(n) - arr(curMin) < arr(dp(n - 1)._2) - arr(dp(n - 1)._1))
          dp(n - 1)
        else
          (curMin, n)
```

```
def bestTransaction(prices: Array[Int]): (Int, Int, Int) = {
  // state : current Best profit, BuyIndex and SellIndex
  var state = (0, 0, 0)
  var curMin = 0
  for(i <- 1 until prices.length) {
    if(prices(i) < prices(curMin)) curMin = i
    val localProfit = prices(i) - prices(curMin)
    if(localProfit > state._1) state = (localProfit, curMin, i)
  }
  state
}
```

* 122	Best Time to Buy and Sell Stock II	41.3%	Medium
不限次数，就把每一次上涨都包括进来即可

```
def bestTransactions(prices: Array[Int]): (Int, List[(Int, Int)]) = {
  var profit = 0
  var startidx = 0
  var seq = List[(Int, Int)]()
  for (i <- 1 until prices.length) {
    if (prices(i) > prices(i - 1)) {
      profit = profit + prices(i) - prices(i - 1)
    } else {
      if (startidx < i - 1) {
        seq = (startidx, i - 1) :: seq
      }
      startidx = i
    }
  }
  (profit, seq)
}
```

* 123 Best Time to Buy and Sell Stock III 25.6% Hard
只有2次机会

以第i天为分界线，计算第i天之前进行一次交易的最大收益preProfit[i]，和第i天之后进行一次交易的最大收益postProfit[i]，最后遍历一遍，max{preProfit[i] + postProfit[i]} (0≤i≤n-1)就是最大收益。第i天之前和第i天之后进行一次的最大收益求法同Best Time to Buy and Sell Stock I。

todo


* 188	Best Time to Buy and Sell Stock IV	21.3%	Hard

todo









≥



dp(n)表示一个算法，其结果类型为(i1:Int, j1:Int, i2:Int, j2:Int)表示两次买入和卖出点坐标，其中i1<=j1<=i2<=j2，使之对任意0<=a1<=b1<=a2<=b2<=n，有arr(j1) - arr(i1) + arr(j2) - arr(i2) >= arr(b1) - arr(a1) + arr(b2) - arr(a2)

和只有一次机会[121](#121)类似，保留两个值，local最大值和global最大值，如果local大于global，则使用local的替换global的
这里local是指以arr(n)为卖出点的最大值，只要拿当前值减去当前遇到的最小值即可

curMin 表示当前遇到的最小值坐标 currentMinIndex，可以知道必有curMin = i1或curMin = i2或curMin > j2，可以反证法说明。

保存一个curMinLocal表示大于或等于j2的最小坐标

计算当前最大值arr(n) - arr(curMin) vglobal和arr(n) - arr(curMinLocal) vlocal，记前两个是v1 v2
if(vlocal > v1 && curMinLocal > )


if(curMin > i2) {
  if( v > min(v1, v2)) {
    max(v1, v2) + v
  }
} else if(curMin == i2) {
  if(v > v2 || vlocal > v1) {
  val inc1 = v - v2
  val inc2 = vlocal - v1
  if(inc1 > inc2) {
    v1 + v
  } else {
    v2 + vlocal
  }
} else { // curMin == i1
  if(v > v1 + v2) {
    v
  } else if(vlocal > v2) {
    v1 + vlocal
  }
}






70	Climbing Stairs	36.1%	Easy
Climbing Stairs
dp[i] = dp[i - 1] + dp[i - 2]
dp[1] = 1
dp[2] = 2
斐波那契

```
def climb(n:Int) :Int = {
  var state = (1, 2)
  for(i <- 3 to n) {
    val (x, y) = state
    state = (y, x + y)
  }
  state._2
}
```


http://blog.csdn.net/linhuanmars/article/details/22135231
http://bangbingsyb.blogspot.com/2014/11/leetcode-unique-paths-i-ii.html



62	Unique Paths	35.3%	Medium

```
dp[i][j] = dp[i - 1][j] + dp[i][j - 1]
dp[i][j]表示从点(0, 0)到(i, j)唯一路径数量。
```

而当i=0或j=0时，dp[i][j]=1，因此可以使用递推的方式获取所有数量：

```
def path(m:Int, n:Int) :Int = {
  val state = Array.fill[Int](n)(1)
  for(
    i <- 1 until m;
    j <- 1 until n
  ) state(j) = state(j - 1) + state(j)
  state(n - 1)
}
```

63	Unique Paths II	28.9%	Medium
如果有障碍，则dp[i][j]=0，需要传入一个辅助矩阵【大小必须为m*n】表示该点是否有障碍，如果有障碍，则obstacles(i)(j)=1

```
def path(m:Int, n:Int, obstacles: Array[Array[Int]]) : Int = {
  val state = obstacles(0).map(1 - _)
  for(
    i <- 1 until m;
    j <- 0 until n // 从0开始来处理边界情况
  ) {
    if(obstacles(i)(j) == 1) state(j) = 0
    else state(j) = if(j == 0) state(j) else state(j - 1) + state(j)
  }
  state(n - 1)
}

val arr = Array.fill[Array[Int]](3)(Array.fill[Int](10)(1))
arr.deep
path(3, 10, arr)
```

* 64	Minimum Path Sum	34.1%	Medium
和前面类似，只是矩阵带有权值，求权值和最小的路径，和前面类似，比较两种选择中最小的即可。

```
dp[i][j] = min(dp[i - 1][j] , dp[i][j - 1]) + arr[i][j]
dp[i][j]表示从点(0, 0)到(i, j)路径的最小权值。
```

```
def minpath(matrix: Array[Array[Int]]) : Int = {
  val state = matrix(0).clone
  state.foldLeft((state, 0)) {(t, v) =>
    val (s, i) = t
    s(i) = if(i == 0) v else s(i - 1) + v
    (s, i + 1)
  }

  for(
    i <- 1 until matrix.length;
    j <- state.indices
  ) {
    if(j == 0) state(j) = matrix(i)(j) + state(j)
    else state(j) = matrix(i)(j) + min(state(j - 1), state(j))
  }
  state(matrix(0).length - 1)
}
```

* 120	Triangle	29.3%	Medium
给定带权三角，求其从定点到底边权值最小的路径，与上题类似

```
dp[i][j] = min(dp[i - 1][j - 1] + dp[i - 1][j]) + triangle[i][j]
```

```
def minpath(triangle: Array[Array[Int]]) : Int = {
  val state = Array.fill(triangle(triangle.length - 1).length)(0)
  state(0) = triangle(0)(0)

  for(
    i <- 1 until triangle.length;
    j <- i to 0 by -1
  ) {
    if(j == 0) state(j) = triangle(i)(j) + state(j)
    else if(j == triangle(i).length - 1) state(j) = triangle(i)(j) + state(j - 1)
    else state(j) = triangle(i)(j) + min(state(j - 1), state(j))
  }
  state.min
}

val tri = Array(Array(2),Array(3,4),Array(6,5,7),Array(4,1,8,3))

tri.deep
minpath(tri)
```


96	Unique Binary Search Trees	36.8%	Medium
存储1到n的二叉树有多少种。

令dp(n)表示包含n个节点的BST数量
令f(k, n)表示以k为根节点且包含n个节点的BST数量，则

```
dp(n) = f(1, n) + dp(2, n) + ... + f(n)
      = dp(n - 1) * dp(0) + dp(n - 2) * dp(1) + ... + dp(0) * dp(n - 1)
其中dp(0) = 1, dp(1) = 1
```

```
def uniqBST(n : Int) : Int = {
  val state = Array.fill(n + 1)(0)
  state(0) = 1
  state(1) = 1

  for(
    i <- 2 to n;
    j <- 0 until i
  ) state(i) = state(i) + state(j) * state(i - j - 1)
  state(n)
}
```


95	Unique Binary Search Trees II	28.8%	Medium
将树生成出来
todo

279	Perfect Squares	31.2%	Medium

动态规划：从新手到专家
http://www.hawstein.com/posts/dp-novice-to-advanced.html
https://www.topcoder.com/community/data-science/data-science-tutorials/dynamic-programming-from-novice-to-advanced/


dp(n) = for(x <- squares(n)) to 1) min(1 + dp(n - x))

```
def squares(n: Int): List[Int] = {
  var i = 2
  var list = List(1)
  while (i * i <= n) {
    list = i * i :: list
    i = i + 1
  }
  list
}

def perfSquare(n: Int): Int = {
  val sq = squares(n)
  if (sq.head == n) 1
  else sq.foldLeft(n) { (x, v) => min(perfSquare(n - v) + 1, x) }
}

```


