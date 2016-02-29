math.md


8	String to Integer (atoi)	13.4%	Easy
a.toInt

7	Reverse Integer	23.6%	Easy
> 123.toString.reverse.toInt
> -(-123.abs.toString.reverse.toInt)

9	Palindrome Number	30.9%	Easy
> calculate the Palindrome Number
> 123.toString.reverse.toInt == 123

223	Rectangle Area	29.2%	Easy
给定两个矩形的左下、右上坐标，求其相交面积

(lx1, ly1), (rx1, ry1)
(lx2, ly2), (rx2, ry2)

val width = min(rx1, rx2) - max(lx1, lx2)
val height = min(ry2, ry1) - max(ly2, ly1)
if(width > 0 && height > 0) width * height
else 0


231	Power of Two	35.3%	Easy
如果一个整数是2的幂，那么它的二进制形式最高位为1，其余各位为0
等价于：n & (n - 1) = 0，且n > 0


326	Power of Three	35.6%	Easy
每位数之和是3的倍数









13	Roman to Integer	38.2%	Easy
12	Integer to Roman	37.6%	Medium
略



	202	Happy Number	35.8%	Easy


258	Add Digits	48.1%	Easy
先循环，找规律：
out = (in - 1) % 9 + 1

172	Factorial Trailing Zeroes	31.9%	Easy
求5的个数
n!后缀0的个数 = n!质因子中5的个数
              = floor(n/5) + floor(n/25) + floor(n/125) + ....

168	Excel Sheet Column Title	20.9%	Easy
171	Excel Sheet Column Number	40.4%	Easy
26进制 与 10进制 互转

67	Add Binary	26.7%	Easy
两个binary string相加，"11" + "1" = "100"
类似与#2 add two numbers

204	Count Primes	23.5%	Easy
给定一个数，计算质数个数
埃拉托斯特尼筛法
http://bookshadow.com/weblog/2015/04/27/leetcode-count-primes/

319	Bulb Switcher	39.5%	Medium
有n个灯，初始都灭，第一遍都开，然后灭2的倍数。。。知道n轮之后，还有多少开的？

有规律可循
https://www.hrwhisper.me/leetcode-bulb-switcher/



246	Strobogrammatic Number 	35.5%	Easy
转180度，与之相同，相关的数字是0，1，6，8，9

247	Strobogrammatic Number II 	33.2%	Medium
给定位数，找到所有的Strobogrammatic Number 
todo 动态规划？

248	Strobogrammatic Number III 	25.5%	Hard
给定范围 low <= num <= high
求在范围内的Strobogrammatic Number 数
todo


263	Ugly Number	35.8%	Easy
只有2、3、5三个因子的数
while循环求余即可

264	Ugly Number II	26.7%	Medium
找到第n个ugly number
todo

313	Super Ugly Number	32.2%	Medium
https://segmentfault.com/a/1190000004187449

29	Divide Two Integers	15.5%	Medium
计算两数相除，不能用乘法、除法和mod

用减法 复杂度O(n)
用二的幂次表示
http://blog.csdn.net/linhuanmars/article/details/20024907



224	Basic Calculator	21.2%	Medium
非负整数 括号的四则运算
略？


43	Multiply Strings	22.9%	Medium
将string代表的数字相乘
数字可能很大，超过Int等
todo


69	Sqrt(x)	24.9%	Medium
Implement int sqrt(int x).

Compute and return the square root of x.

二分



335	Self Crossing	18.0%	Medium
都是正整数，有没有



50	Pow(x, n)	27.9%	Medium
public double myPow(double x, int n)
n可正可负

60	Permutation Sequence	24.6%	Medium
求出排好序的第k个排列
todo

233	Number of Digit One	23.8%	Medium
给出小于等于n的出现的1的数量：
Given n = 13,
Return 6, because digit 1 occurred in the following numbers: 1, 10, 11, 12, 13.

todo

268	Missing Number	39.2%	Medium
0-n 少一个数，可以只用O(n)时间，O(1)空间实现吗？
加？异或？



273	Integer to English Words	18.1%	Medium
数字转英文，关键是找出规律

166	Fraction to Recurring Decimal	14.7%	Medium
写出分数的数字形式：
Given numerator = 1, denominator = 2, return "0.5".
Given numerator = 2, denominator = 1, return "2".
Given numerator = 2, denominator = 3, return "0.(6)".

todo


296	Best Meeting Point 	45.6%	Hard
149	Max Points on a Line	14.0%	Hard
65	Valid Number	11.9%	Hard




