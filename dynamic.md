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


* 121	Best Time to Buy and Sell Stock	35.2%	Medium
只有一次机会

dp(n)表示一个算法，其结果类型为(i:Int, j:Int)表示买入和卖出点坐标，使之对任意0<=i1<=j1<=n，有arr(j) - arr(i) >= arr(j1) - arr(i1)

保留两个值，local最大值和global最大值，如果local大于global，则使用local的替换global的
这里local是指以arr(n)为卖出点的最大值，只要拿当前值减去当前遇到的最小值即可

curMin 表示当前遇到的最小值坐标 currentMinIndex

dp(n) = 
        curMin = if(arr(n) < arr(curMin)) n else curMin 
        if(arr(n) - arr(curMin) < arr(dp(n - 1)._2) - arr(dp(n - 1)._1))
          dp(n - 1)
        else
          (curMin, n)


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

* 122	Best Time to Buy and Sell Stock II	41.3%	Medium
不限次数







* 123	Best Time to Buy and Sell Stock III	25.6%	Hard
* 122	Best Time to Buy and Sell Stock II	41.3%	Medium
* 121	Best Time to Buy and Sell Stock	35.2%	Medium

Best Time to Buy and Sell Stock


def best(prices: Array[Int]): (Int, Int) = {
  // state : startIndex and value
  val state = Array.fill[(Int, Int)](prices.length)((0, 0))
  for (i <- 1 until prices.length) {
    if (prices(i) > prices(state(i - 1)._1)) {
      state(i) = (state(i - 1)._1, state(i - 1)._2 - prices(i - 1) + prices(i))
    } else {
      state(i) = (i, 0)
    }
  }
  state.maxBy(_._2)
}

best(Array(1, 0, 3, 4, 8, 6, 7, 4, 9, 3, 2))

Best Time to Buy and Sell Stock II
不限机会
def best2(prices: Array[Int]): (Int, List[(Int, Int)]) = {
  // state : startIndex and value
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

best2(Array(1, 0, 3, 4, 8, 6, 7, 4, 9, 3, 2))



Best Time to Buy and Sell Stock II
2次机会，下面的解法是错的
def best(prices: Array[Int]): List[(Int, Int)] = {
  // state : startIndex and value, included
  val state = Array.fill[(Int, Int, Boolean)](prices.length)((0, 0, false))
  for (i <- 1 until prices.length) {
    if (prices(i) > prices(i - 1)) {
      state(i) = (state(i - 1)._1, state(i - 1)._2 - prices(i - 1) + prices(i), false)
      state(i - 1) = state(i - 1).copy(_3 = true)
    } else {
      state(i) = (i, 0, false)
    }
  }
  println(state.toList)
  state.filter(!_._3).sortWith((a, b) => a._2 > b._2).take(2).toList
  .map(t => (t._1, t._2))
}

best(Array(1, 0, 3, 4, 8, 6, 7, 4, 9, 3, 2, 5))



Unique Paths

def path(m:Int, n:Int) :Int = {
  val state = Array.fill[Int](n)(1)
  for(
    i <- 1 until m;
    j <- 1 until n
  ) state(j) = state(j - 1) + state(j)
  state(n - 1)
}

Unique Paths II
http://blog.csdn.net/linhuanmars/article/details/22135231
http://bangbingsyb.blogspot.com/2014/11/leetcode-unique-paths-i-ii.html

Minimum Path Sum
lue



Maximum Subarray
local 与 global 找最优
dp[i + 1] = max(dp[i], dp[i] + a[i + 1])

Maxmimum Product Subarray


Climbing Stairs
斐波那契

Triangle
类似Maximum Subarray

Unique Binary Search Trees
Unique Binary Search Trees II
todo

Perfect Squares
背包问题


















