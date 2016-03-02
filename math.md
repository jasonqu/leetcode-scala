## Math

leetcode中有一些数学类算法题，它们技巧性很高，难点再找到规律，这里重点介绍的是规律，实现没有放在重点

### 基本操作

#### 8	String to Integer (atoi)	13.4%	Easy
大多数语言都有内置函数

	"123".toInt

#### 7	Reverse Integer	23.6%	Easy
可以计算它的reverse值，也可以转成string，更为直观

	123.toString.reverse.toInt

#### 9	Palindrome Number	30.9%	Easy
可以计算它的reverse值，在与之比较

	123.toString.reverse.toInt == 123


#### 67	Add Binary	26.7%	Easy
两个代表binary的 string相加，如："11" + "1" = "100"

	Integer.toBinaryString(Integer.parseInt(a, 2) + Integer.parseInt(b, 2))

自己实现的方式类似#2 add two numbers，这里不再赘述


#### 13	Roman to Integer	38.2%	Easy
#### 12	Integer to Roman	37.6%	Medium
略

#### 168	Excel Sheet Column Title	20.9%	Easy
#### 171	Excel Sheet Column Number	40.4%	Easy
26进制 与 10进制 互转

略

#### 273	Integer to English Words	18.1%	Medium
数字转英文

略

#### 204	Count Primes	23.5%	Easy
给定一个数，计算小于它的质数个数

埃拉托斯特尼筛法，创建一个大小为n的数组，从2到`sqrt(n)`一遍遍地筛，最后统计结果。

	val n = 101
	var r = 1 to n toList
	val x = sqrt(n).toInt
	for(i <- 2 to x) r = r.filterNot(_ % i == 0)
	r


#### 268	Missing Number	39.2%	Medium
`[0, n]` 少一个数，可以只用`O(n)`时间，`O(1)`空间实现吗？

加、异或，异或的版本

	var res = 0
	for(i <- arr.indices) res = res ^ i ^ arr(i)
	res ^ n


#### 69	Sqrt(x)	24.9%	Medium
返回值为Int类型，可以使用二分查找的方法，略

#### 50	Pow(x, n)	27.9%	Medium
n可正可负，返回值为Double类型，可以[递归使用二分法](http://fisherlei.blogspot.com/2012/12/leetcode-powx-n.html)

略

### 代数
#### 172	Factorial Trailing Zeroes	31.9%	Easy
求`Factorial(n)`结尾的0的个数，很容易知道这等价于质数因子中5的个数

	n!后缀0的个数 = n!质因子中5的个数
	              = floor(n/5) + floor(n/25) + floor(n/125) + ....

#### 233	Number of Digit One	23.8%	Medium
给出小于等于n的出现的1的数量,例如n = 13时返回6，因为1出现在如下数字中： 1, 10, 11, 12, 13

参考[leetcode discuss](https://leetcode.com/discuss/44281/8-lines-o-log-n-c-java-python)

	public int countDigitOne(int n) {
	    int ones = 0;
	    for (long m = 1; m <= n; m *= 10)
	        ones += (n/m + 8) / 10 * m + (n/m % 10 == 1 ? n%m + 1 : 0);
	    return ones;
	}

#### 231	Power of Two	35.3%	Easy
如果一个整数是2的幂，那么它的二进制形式最高位为1，其余各位为0
等价于：`n & (n - 1) = 0`，且`n > 0`

#### 326	Power of Three	35.6%	Easy
除了迭代求余之外，似乎没有更好的方法。

#### 202	Happy Number	35.8%	Easy
例子: 19 is a happy number

	1^2 + 9^2 = 82
	8^2 + 2^2 = 68
	6^2 + 8^2 = 100
	1^2 + 0^2 + 0^2 = 1

可以使用Map保存历史，循环操作即可，略

	val x = 19
	var map = Map[Int, Int]()
	var sum = x
	while(sum != 1 && !map.contains(sum)) {
	  val tmp = sum
	  sum = tmp.toString.map(_.toString.toInt).map(x => x*x).sum
	  map = map + (tmp -> sum)
	}
	println("sum " + sum + "  " + map.get(sum))
	sum


#### 166	Fraction to Recurring Decimal	14.7%	Medium
写出分数的数字形式：

> Given numerator = 1, denominator = 2, return "0.5".
> Given numerator = 2, denominator = 1, return "2".
> Given numerator = 2, denominator = 3, return "0.(6)".

想法是当除数出现重复的时候，就可以确定重复的小数部分是什么了

```
import Math._

def fraction(numer: Int, denom: Int): String = {
  var (x, y) = (numer / denom, numer % denom)
  if (y == 0) x.toString
  else {
    val deno = abs(denom)
    val prefix = (if(numer * denom < 0) "-" else "") + x + "."
    var map = Map[Int, Int]()
    var list = List[(Int, Int)]() // numer -> numer / denom
    var n = 0
    while (y != 0 && !map.contains(y * 10)) {
      val n = y * 10
      x = n / deno
      y = n % deno
      map = map + (n -> x)
      list = (n -> x) :: list
    }
    if (y == 0) prefix + list.map(_._2).reverse.mkString("")
    else {
      val (nonrepeat, repeat) = list.reverse.span(_._1 != y * 10)
      prefix + nonrepeat.map(_._2).mkString("") + repeat.map(_._2).mkString("(", "", ")")
    }
  }
}

fraction(-4, 2)
fraction(3, 2)
fraction(3, 7)
fraction(23, -130)
```

#### 319	Bulb Switcher	39.5%	Medium
有n个灯，初始都灭，第一遍都开，然后toggle 2的倍数，然后toggle 3的倍数。。。直到n轮之后，还有多少开的？

有规律可循，可以发现只有平方数是打开的，参见[这里](https://www.hrwhisper.me/leetcode-bulb-switcher/)

所以只要找出小与等于n的所有平方数即可。略

#### 258	Add Digits	48.1%	Easy
将个个位数相加，直到结果为个位数时返回

先循环，找规律：`out = (in - 1) % 9 + 1`，见 [书影博客 观察法](http://bookshadow.com/weblog/2015/08/16/leetcode-add-digits/)

#### 246	Strobogrammatic Number 	35.5%	Easy
转180度，与之相同，相关的数字是0，1，6，8，9，所以判断起来就很简单了，略

#### 247	Strobogrammatic Number II 	33.2%	Medium
给定位数，找到所有的Strobogrammatic Number 

* 如果只有1位，只能选0、1、8
* 如果位数是偶数2k，则可选的数量是5^(k-1) * 4，因为0不能在第一位
* 如果位数是奇数2k+1，则数量是5^(k-1) * 4 * 3

最后需要递归打印出所有数字

	val map = Map(0 -> 0, 1 -> 1, 8 -> 8, 6 -> 9, 9 -> 6)
	def strobogrammaticNumbers(k: Int) : List[String] = k match {
	  case x if x <= 0 => List("")
	  case 1 => List("0","1","8")
	  case _ =>
	    var res: List[String] = Nil
	    for((key, value) <- map) {
	      //println(k + " " + (key, value))
	      res = res ::: (strobogrammaticNumbers(k - 2) map(key + _ + value))
	    }
	    res
	}
	strobogrammaticNumbers(0)
	strobogrammaticNumbers(1)
	strobogrammaticNumbers(2).filterNot(_.startsWith("0"))
	strobogrammaticNumbers(3).filterNot(_.startsWith("0"))



#### 248	Strobogrammatic Number III 	25.5%	Hard
求在给定范围`low <= num <= high`内的Strobogrammatic Number 数

其实就是在#247的基础上增加约束，略


#### 263	Ugly Number	35.8%	Easy
只有2、3、5三个因子的数。while循环求余即可

#### 264	Ugly Number II	26.7%	Medium
找到第n个ugly number

归并排序3个数列，可以参考[书影博客的解答](http://bookshadow.com/weblog/2015/08/19/leetcode-ugly-number-ii/)

#### 313	Super Ugly Number	32.2%	Medium
给定质数作为丑陋数的给定因子，是#264的普遍情况

见[segmentfault上的一个解答](https://segmentfault.com/a/1190000004187449)

#### 29	Divide Two Integers	15.5%	Medium
计算两数相除，不能用乘法、除法和mod

自虐型，方法有：
* 用减法 复杂度O(n)
* 用二的幂次表示 见[http://blog.csdn.net/linhuanmars/article/details/20024907](http://blog.csdn.net/linhuanmars/article/details/20024907)

但感觉都不完美


### 几何

#### 223	Rectangle Area	29.2%	Easy
给定两个矩形的左下、右上坐标，求其相交面积

给定坐标：`(lx1, ly1), (rx1, ry1)`、`(lx2, ly2), (rx2, ry2)`，求相交面积的算法：

	val width = min(rx1, rx2) - max(lx1, lx2)
	val height = min(ry2, ry1) - max(ly2, ly1)
	if(width > 0 && height > 0) width * height
	else 0


#### 335	Self Crossing	18.0%	Medium
都是正整数，有没有相交



#### 296	Best Meeting Point 	45.6%	Hard
todo

#### 149	Max Points on a Line	14.0%	Hard
todo 找出在一个线上的点





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




#### 65	Valid Number	11.9%	Hard

Validate if a given string is numeric.

Some examples:
"0" => true
" 0.1 " => true
"abc" => false
"1 a" => false
"2e10" => true
Note: It is intended for the problem statement to be ambiguous. You should gather all requirements up front before implementing one.


