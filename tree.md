## Tree

### 简介

树是一种重要的数据结构，最简单的树就属二叉树了，在scala中定义一个二叉树很简单：

```
case class TreeNode(value: Int, left: Option[TreeNode] = None, right: Option[TreeNode] = None)

val tree1 = TreeNode(1,
  Some(TreeNode(21)),
  Some(TreeNode(22)))

val tree2 =
  TreeNode(1,
    Some(TreeNode(21,
      None,
      Some(TreeNode(31,
        Some(TreeNode(41)),
        Some(TreeNode(42,
          Some(TreeNode(51, Some(TreeNode(61)), Some(TreeNode(62)))),
          None)))))),
    Some(TreeNode(22,
      Some(TreeNode(32, Some(TreeNode(43)), Some(TreeNode(44)))),
      Some(TreeNode(33, Some(TreeNode(45)), Some(TreeNode(46)))))))
```

注意这里的子节点使用的是`Option[TreeNode]`，如果值为`None`则表示没有这个子节点；如果两个子节点都是`None`则表示该节点为叶子节点。

### 基本操作

遍历是树最基本的算法，了解树首先要从前序、中序、后序遍历开始：

#### 94 Binary Tree Inorder Traversal 38.6% Medium
#### 144  Binary Tree Preorder Traversal  38.8% Medium
#### 145  Binary Tree Postorder Traversal 34.6% Hard
递归方式很简单：

```
def traverse(root: Option[TreeNode], f:TreeNode => Unit): Unit = root match {
  case None => Unit
  case Some(node) =>
    f(node) // Preorder
    traverse(node.left, f)
    f(node) // Inorder
    traverse(node.right, f)
    f(node) // Postorder
}

val tree1 = TreeNode(1,
  Some(TreeNode(21)),
  Some(TreeNode(22)))

traverse(Some(tree1), {t:TreeNode => println(t.value)})
```

当然作为scala可以传入针对TreeNode或其Value的处理函数即可。

题目还要求实现迭代版本，不过这不是函数式的长项，此处从略

#### 106  Construct Binary Tree from Inorder and Postorder Traversal  28.4% Medium
#### 105  Construct Binary Tree from Preorder and Inorder Traversal 27.9% Medium
略

### 递归分治

树的结构决定了可以自然地使用递归分治方式来解决问题：要求解一个问题，如果能够分别解出左右子树的问题，然后在本节点加以综合即可。这时只需根据题意简单的修改一下遍历的算法即可。

> 在编写树的递归算法时，不用刻意寻找尾递归优化，因为编写的尾递归往往也需要使用类似栈的结构保存树的路径，因此不如使用程序栈来使代码更简洁一些

#### 104	Maximum Depth of Binary Tree	47.1%	Easy
最大深度

  def maxdepth(root: Option[TreeNode]): Int = root match {
    case None => 0
    case Some(node) => max(maxdepth(node.left), maxdepth(node.right)) + 1
  }

  maxdepth(Some(tree1))
  maxdepth(Some(tree2))

#### 111 Minimum Depth of Binary Tree  30.2% Easy
最小深度

* 叶子节点，返回当前深度
* 只有左|右子树，返回子树深度
* 否则，返回较矮的深度

```
def mindepth(root: Option[TreeNode], curdep: Int): Int = root match {
  case None => Int.MaxValue
  case Some(node) =>
    val depth = min(
    mindepth(node.left, curdep + 1),
    mindepth(node.right, curdep + 1))
    if(depth == Int.MaxValue) curdep else depth
}

mindepth(Some(tree1), 1)
mindepth(Some(tree2), 1)
```

#### 101	Symmetric Tree	33.3%	Easy
对称树

```
def isSymmetric(rootLeft: Option[TreeNode], rootRight: Option[TreeNode]): Boolean = (rootLeft, rootRight) match {
  case (None, None) => true
  case (None, _) | (_, None)=> false
  case (Some(left), Some(right)) =>
    if(left.value != right.value) false
    else isSymmetric(left.right, right.left) && isSymmetric(left.left, right.right)
}
```

这种递归的遍历方式，相当于游走了每一条从根到叶子节点的路径，类似于DFS——深度优先搜索。以下题目都可以利用这种方法得出，与上面类似，这里从略

#### 100	Same Tree	42.7%	Easy
判断两树是否相同，和#101类似，略

#### 112	Path Sum	30.8%	Easy
value之和 等于 给定值 的树的路径

#### 113	Path Sum II	27.7%	Medium
所有 value之和 等于 给定值 的树的路径

#### 257	Binary Tree Paths	26.9%	Easy
返回所有路径

#### 129	Sum Root to Leaf Numbers	32.0%	Medium
返回所有路径 所代表的数字之和

对有一些问题，只要查到一个特例就可以返回结果时，可以不需要遍历后面的路径，类似的问题如#101、#100、#112。这时可以通过使用scala的lazy关键字延迟求值，并使用布尔变量来做短路求值（short circuit）。下面将以#110为例介绍一下实现方法：

#### 110	Balanced Binary Tree	33.4%	Easy
平衡二叉树要求子树高度差不能大于1，判断一棵树是否是平衡二叉树

使用字段保存“子树深度”，“是否是平衡树”；然后使用lazy延迟求值，并使用布尔变量来做short circuit

```
def isBalanced(root: Option[TreeNode]): (Int, Boolean) = root match {
  case None => (0, true)
  case Some(node) =>
    lazy val (leftdepth, leftb) = isBalanced(node.left)
    lazy val (rightdepth, rightb) = isBalanced(node.right)
    if(leftb && rightb && Math.abs(leftdepth - rightdepth) < 2)
      (max(leftdepth, rightdepth) + 1, true)
    else
      (0, false)
}
```

### 广度优先

除了深度优先，另一种方式是广度优先，这种方式一般要求使用类似队列的结构来保存子节点

#### 102	Binary Tree Level Order Traversal	31.7%	Easy
BFS广度优先遍历，这里可以利用Option的特性简化代码

```
def levelTraverse(root: TreeNode): List[List[Int]] = {
  levelTraverseRec(List(root), List(root.value) :: Nil)
}

@tailrec
def levelTraverseRec(parents: List[TreeNode], res: List[List[Int]]): List[List[Int]] = {
  val children = parents.foldRight(List[Option[TreeNode]]()) { (node, list) =>
    node.left :: node.right :: list
  }.flatten
  if(children.isEmpty) res
  else levelTraverseRec(children, children.map(_.value) :: res)
}

levelTraverse(tree1)
levelTraverse(tree2)
```

#### 107 Binary Tree Level Order Traversal II  33.1% Easy
只要将#102的结果reverse一下即可，略

#### 103	Binary Tree Zigzag Level Order Traversal	27.9%	Medium
只要增加一个奇偶判断是否需要reverse即可，略


### 树的数组形式和堆
通过树的BFS广度优先遍历来看，可以看出一颗树可以被序列化为一个线性结构——数组。数组的好处是能够随机访问，从而提高读取和修改内容的效率。

这里我们将树的数组形式定义为`Array[Option[Type of TreeNode.value]]`，前面介绍的例子`tree1`将变为：

```
tree1 = Array(1,
              21, 22)

tree2 = Array(1,
              21, 22,
              #, 31, 32, 33,
              #, #, 41, 42, 43, 44, 45, 46,
              #, #, #, #, #, #, 51, #, #, #, #, #, #, #, #, #,
              #, #, #, #, #, #, #, #, #, #, #, #, 61, 62
              )
```

这里使用`#`表示`None`，使用数字表示`Some[Int]`，可见有下面的规律：
* 父节点索引：`parent(i) = (i - 1) / 2`
* 左子节点索引：`left(i) = 2 * i + 1`
* 右子节点索引：`right(i) = 2 * i + 2`

根据`tree2`可以看到，如果是稀疏的树，其空间比链表形式存储浪费的更大。因此数组存储法适合于完全二叉树（Complete Binary Tree）来存储，就像`tree1`那样，这样数组的空间就完全没有浪费。

然而什么样的数据结构需要这样的结构呢？Heap就是这样一种数据结构：它要求树是完全的，并且对每一颗子树，其根节点均小于等于[或大于等于]其子节点。即`Heap(parent(i)) <= Heap(i)`

> 参考资料
> * https://www.youtube.com/watch?v=W81Qzuz4qH0
> * http://stackoverflow.com/questions/1098277/java-implementation-for-min-max-heap
> * https://www.cs.cmu.edu/~adamchik/15-121/lectures/Binary%20Heaps/heaps.html
> * https://www.cs.cmu.edu/~adamchik/15-121/lectures/Binary%20Heaps/code/Heap.java
> * http://stackoverflow.com/questions/1098277/java-implementation-for-min-max-heap


#### 23 Merge k Sorted Lists  22.7% Hard
合并k个List，简单的做法是一个个处理，时间复杂度是O(k * n)

  lists.foldLeft(List[Int]()) {(res, l) => merge(res, l) }

但这样可能导致大量的重复操作。一次遍历可以通过建堆的方式完成，堆的描述见[Tree](tree.md)


根据我们之前的定义
当然如果按照完全二叉树，存到数组的话 Given binary tree {3,9,20,#,#,15,7}，
使用下标的方式也可以获取，完全二叉树特性，
todo




### 修改树

#### 114	Flatten Binary Tree to Linked List	30.4%	Medium
给定一棵树，按照前序遍历转为List

def flatternTree(root: Option[TreeNode]): List[Int] = root match {
  case None => Nil
  case Some(node) =>
    flatternTree(node.right) ::: node.value :: flatternTree(node.left)
}

但是这里不满足in-place的要求。由于TreeNode定义为一个case class，此时树是immutable的。要实现修改树需要改为class

具体方式就是找左子树最右节点，把右子树挂上，然后继续处理左子树 todo





#### Binary Search Tree

构建 todo


* 98	Validate Binary Search Tree	20.7%	Medium
验证特性使用dfs即可，其中左子树返回max，右子树返回min，然后看value是否满足 l-max < value < r-max 即可


* 109	Convert Sorted List to Binary Search Tree	29.7%	Medium
* 108	Convert Sorted Array to Binary Search Tree	36.3%	Medium
要想利用BST特性，需要从中间开始构建


* 99	Recover Binary Search Tree	25.8%	Hard
> constant space solution, without changing its structure
> todo




* 116	Populating Next Right Pointers in Each Node	36.4%	Medium
constant extra space. perfect binary tree
> todo
* 117	Populating Next Right Pointers in Each Node II	32.6%	Hard
constant extra space. not perfect binary tree
> todo




333	Largest BST Subtree 	26.9%	Medium
298	Binary Tree Longest Consecutive Sequence 	35.2%	Medium
297	Serialize and Deserialize Binary Tree	26.5%	Medium
285	Inorder Successor in BST 	35.0%	Medium
272	Closest Binary Search Tree Value II 	31.7%	Hard
270	Closest Binary Search Tree Value 	33.2%	Easy
255	Verify Preorder Sequence in Binary Search Tree 	36.1%	Medium
250	Count Univalue Subtrees 	35.4%	Medium
236	Lowest Common Ancestor of a Binary Tree	28.5%	Medium
235	Lowest Common Ancestor of a Binary Search Tree	38.0%	Easy
230	Kth Smallest Element in a BST	35.9%	Medium
226	Invert Binary Tree	43.7%	Easy
222	Count Complete Tree Nodes	24.1%	Medium
199	Binary Tree Right Side View	32.9%	Medium
173	Binary Search Tree Iterator	33.3%	Medium
156	Binary Tree Upside Down 	37.2%	Medium



124	Binary Tree Maximum Path Sum	22.8%	Hard




96	Unique Binary Search Trees	36.8%	Medium
95	Unique Binary Search Trees II	28.8%	Medium













