## String

String在scala中可以被看做是一个Char的Array，因此也可以方便的使用集合操作.它甚至可以被看做是Array[Int]的特例，因为Char的范围比Int要小的多。

#### 58	Length of Last Word	28.8%	Easy
最后一个词的长度

	s.length - s.lastIndexOf(' ') - 1


#### 14	Longest Common Prefix	27.5%	Easy
给定一组String，确定其最长前缀

只需要一个个确定其头部是否相同即可

	def longestPrefix(strs: List[String]) : String = {
	  var i = 1
	  var found = false
	  while(i <= strs.head.length && !found) {
	    val prefix = strs.head.substring(0, i)
	    if(!strs.forall(_.startsWith(prefix))) found = true
	    i = i + 1
	  }
	  strs.head.substring(0, i - 1)
	}



#### 125	Valid Palindrome	23.4%	Easy
回文，见#234



#### 165	Compare Version Numbers	17.0%	Easy
比较版本号，如"1.2.33" > "1.1.35"，返回1、0、-1

	compare("1.2.3", "1.1.4")
	def compare(v1:String, v2:String): Int =
	  compareList(v1.split("\\.").toList.map(_.toInt), v2.split("\\.").toList.map(_.toInt))

	def compareList(v1:List[Int], v2:List[Int]): Int = {
	  (v1,v2) match {
	    case (Nil,Nil) => 0
	    case (Nil, _) => -1
	    case (_, Nil) => 1
	    case (x :: xl, y :: yl) =>
	      if(x < y) -1
	      else if(x > y) 1
	      else compareList(xl, yl)
	  }
	}

#### 38	Count and Say	28.1%	Easy
打印序列 `1, 11, 21, 1211, 111221, ...`的第k个

尾递归处理，更具上一个字符串导出下一个字符串，没什么难的，略


#### 6	ZigZag Conversion	23.3%	Easy
将String按照ZigZag的形式重新输出

关键是找到规律，实际上每2 * nRows - 1 个字符循环一回。

```
令n = 2 * nRows - 1
则第1行 n * k 
第2行 1 * k, (n - 1) * k
第3行 2 * k, (n - 2) * k
...
第nRows行（nRows - 1） * k 
```



#### 49	Group Anagrams	26.7%	Medium
For example, given: ["eat", "tea", "tan", "ate", "nat", "bat"], 
Return:

	[
	  ["ate", "eat","tea"],
	  ["nat","tan"],
	  ["bat"]
	]

	List("eat", "tea", "tan", "ate", "nat", "bat").groupBy(_.sorted).values


#### 249	Group Shifted Strings 	31.0%	Easy

Given a string, we can "shift" each of its letter to its successive letter, for example: "abc" -> "bcd". We can keep "shifting" which forms the sequence:

"abc" -> "bcd" -> ... -> "xyz"

For example, given: ["abc", "bcd", "acef", "xyz", "az", "ba", "a", "z"], 
Return:

	[
	  ["abc","bcd","xyz"],
	  ["az","ba"],
	  ["acef"],
	  ["a","z"]
	]

一个字符串就是一个26进制的数字

	List("abc", "bcd", "acef", "xyz", "az", "ba", "a", "z").groupBy { str =>
	  val x = str.map(_.toInt - 97)
	  x.map(_ - x.head).map(i => if (i >= 0) i else i + 26)
	}.values










### 文法

#### 20	Valid Parentheses	28.7%	Easy
确定一串括号是否合乎文法，如"()[]{}"合法，但是"([)]"不合法

使用一个List来模拟栈即可：

	def validParentheses(str: String) : Boolean = {
	  var list = List[Char]()
	  var valid = true
	  var i = 0
	  val map = Map(')' -> '(', ']' -> '[', '}' -> '{')
	  val set = Set('(', '[', '{')
	  while(i < str.length && valid) {
	    val c = str(i)
	    if(set.contains(c)) list = c :: list
	    else list match {
	      case Nil => valid = false
	      case head :: tail =>
	        if(head != map.get(c).get) valid = false
	        else list = tail
	      case _ =>
	    }
	    i = i + 1
	  }
	  valid
	}

#### 32	Longest Valid Parentheses	22.2%	Hard
Given a string containing just the characters '(' and ')',

参考dynamic 的 #53
怎样算出local longest，只要知道)的个数就行了

	def longestValidParentheses(str: String) : Boolean = {
	  var list = List[Char]()
	  var (global, local) = (0, 0)
	  var i = 0
	  while(i < str.length) {
	    val c = str(i)
	    if(c == '(') list = c :: list
	    else list match {
	      case Nil =>
	        local = 0
	      case head :: tail =>
	        local = local + 2
	        if(local > global) global = local
	        list = tail
	      case _ =>
	    }
	    i = i + 1
	  }
	  global
	}



#### 150	Evaluate Reverse Polish Notation	22.9%	Medium
例子：

  ["2", "1", "+", "3", "*"] -> ((2 + 1) * 3) -> 9
  ["4", "13", "5", "/", "+"] -> (4 + (13 / 5)) -> 6

```
evalPolish(List("2", "1", "+", "3", "*"))
evalPolish(List("4", "13", "5", "/", "+"))

def evalPolish(exps:List[String]):Int ={
  val funcs = Map("+" -> ((a:Int, b:Int) => a+b),
    "-" -> ((a:Int, b:Int) => a-b),
    "*" -> ((a:Int, b:Int) => a*b),
    "/" -> ((a:Int, b:Int) => a/b))
  exps.foldLeft(List[Int]()) {(stack, ex) =>
    ex match {
      case "+" | "-" | "*" | "/" =>
        val x :: y :: rest = stack
        funcs(ex)(y, x) :: rest
      case num => num.toInt :: stack
    }
  }.head
}
```

#### 224	Basic Calculator
#### 227	Basic Calculator II
non-negative integers, +, -, *, and / operators, and (, )

* parboiled https://github.com/sirthias/parboiled/wiki/Simple-Calculator
* scala parser combinator 效率比parboiled慢
https://gist.github.com/emiliolg/1095917
http://www.artima.com/pins1ed/combinator-parsing.html


@tailrec
parse(ex:String, tokens:List[String]) = ex match {
case "" => tokens
case a if Set('+', '-', '*', '/', '(', ')').contains(ex(0)) => parse(ex.subString(1), ex(0).toString :: tokens)
case a if ex(0) == ' ' => parse(ex.subString(1), tokens)
case _ => val (digit, rest) = ex.span(_.isdigit)
 parse(rest, digit :: tokens)
}

order(tokensWithOutParentheses:List[String]) {
}


44	Wildcard Matching	17.0%	Hard

#### 71	Simplify Path	21.5%	Medium
"/a/./b/../../c/", => "/c"

```
def simplify(p:String) :String = {
  p.substring(1).split("/").toList
    .foldLeft(List[String]()) { (path, ex) =>
    ex match {
      case "." => path
      case ".." => if (path.isEmpty) path else path.tail
      case _ => ex :: path
    }
  }.mkString("/", "/", "")
}
simplify("/home/")
simplify("/a/./b/../../c/")
simplify("/../")
```

10	Regular Expression Matching	21.7%	Hard




293	Flip Game 	49.1%	Easy


#### 5	Longest Palindromic Substring	22.5%	Medium
#### 3	Longest Substring Without Repeating Characters	21.6%	Medium
76	Minimum Window Substring	20.7%	Hard

#### 214	Shortest Palindrome	19.0%	Hard

dynamic ?

#### 28	Implement strStr()	24.3%	Easy

http://www.programcreek.com/2012/12/leetcode-implement-strstr-java/

157	Read N Characters Given Read4 	29.3%	Easy
43	Multiply Strings	22.9%	Medium
93	Restore IP Addresses	22.8%	Medium
17	Letter Combinations of a Phone Number	27.9%	Medium
161	One Edit Distance 	27.8%	Medium
22	Generate Parentheses	35.8%	Medium
271	Encode and Decode Strings 	26.9%	Medium
151	Reverse Words in a String	15.6%	Medium
186	Reverse Words in a String II 	29.5%	Medium

91	Decode Ways	17.2%	Medium
72	Edit Distance	28.1%	Hard
126	Word Ladder II	13.5%	Hard
158	Read N Characters Given Read4 II - Call multiple times 	23.1%	Hard
159	Longest Substring with At Most Two Distinct Characters 	33.7%	Hard
97	Interleaving String	22.1%	Hard
87	Scramble String	26.1%	Hard
115	Distinct Subsequences	28.3%	Hard
30	Substring with Concatenation of All Words	20.5%	Hard


#### 68	Text Justification	15.8%	Hard

words: ["This", "is", "an", "example", "of", "text", "justification."] L:16.
Return the formatted lines as: [ "This is an", "example of text", "justification." ]

原题中最后一行如果是单个词且<L，则需要补空格，感觉没有必要

foldLeft((List[String], currentLength:Int)) {(res, v) =>
  val (l :: rest, c) = res
  if(v.length + c + 1> limit) {
  (v :: l :: rest, c)
  } else {
  (s"@l @v" :: rest, v.length + c + 1)
  }
}





65	Valid Number	11.9%	Hard



