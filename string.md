## String

String在scala中可以被看做是一个Char的Array，因此也可以方便的使用集合操作.它甚至可以被看做是Array[Int]的特例，因为Char的范围比Int要小的多。

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


#### 125	Valid Palindrome	23.4%	Easy
见#234




165	Compare Version Numbers	17.0%	Easy
38	Count and Say	28.1%	Easy
58	Length of Last Word	28.8%	Easy
6	ZigZag Conversion	23.3%	Easy
293	Flip Game 	49.1%	Easy
249	Group Shifted Strings 	31.0%	Easy
28	Implement strStr()	24.3%	Easy
157	Read N Characters Given Read4 	29.3%	Easy
43	Multiply Strings	22.9%	Medium
3	Longest Substring Without Repeating Characters	21.6%	Medium
93	Restore IP Addresses	22.8%	Medium
5	Longest Palindromic Substring	22.5%	Medium
17	Letter Combinations of a Phone Number	27.9%	Medium
186	Reverse Words in a String II 	29.5%	Medium
49	Group Anagrams	26.7%	Medium
161	One Edit Distance 	27.8%	Medium
22	Generate Parentheses	35.8%	Medium
271	Encode and Decode Strings 	26.9%	Medium
151	Reverse Words in a String	15.6%	Medium
71	Simplify Path	21.5%	Medium
91	Decode Ways	17.2%	Medium
227	Basic Calculator II	23.9%	Medium
72	Edit Distance	28.1%	Hard
32	Longest Valid Parentheses	22.2%	Hard
126	Word Ladder II	13.5%	Hard
158	Read N Characters Given Read4 II - Call multiple times 	23.1%	Hard
10	Regular Expression Matching	21.7%	Hard
159	Longest Substring with At Most Two Distinct Characters 	33.7%	Hard
97	Interleaving String	22.1%	Hard
44	Wildcard Matching	17.0%	Hard
87	Scramble String	26.1%	Hard
214	Shortest Palindrome	19.0%	Hard
115	Distinct Subsequences	28.3%	Hard
30	Substring with Concatenation of All Words	20.5%	Hard
68	Text Justification	15.8%	Hard
65	Valid Number	11.9%	Hard
76	Minimum Window Substring	20.7%	Hard



