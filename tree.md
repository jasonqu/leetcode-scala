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

遍历是树最基本的算法了，了解树首先要从前序、中序、后序遍历开始：

#### 94 Binary Tree Inorder Traversal 38.6% Medium
#### 144  Binary Tree Preorder Traversal  38.8% Medium
#### 145  Binary Tree Postorder Traversal 34.6% Hard
递归方式很简单：

  def traverse(root: Option[TreeNode]): Void = root match {
    case None => Void
    case Some(node) => 
        println(node.value) // Preorder
        traverse(node.left)
        println(node.value) // Inorder
        traverse(node.right)
        println(node.value) // Postorder
  }

当然作为scala可以传入针对TreeNode或其Value的处理函数即可。

题目还要求实现迭代版本，不过这不是函数式的长项，此处从略

#### 106  Construct Binary Tree from Inorder and Postorder Traversal  28.4% Medium
#### 105  Construct Binary Tree from Preorder and Inorder Traversal 27.9% Medium
略

### 递归分治

树的结构决定了可以自然地使用递归分治方式来解决问题：要求解一个问题，如果能够分别解出左右子树的问题，然后在本节点加以综合即可。

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

#### 102	Binary Tree Level Order Traversal	31.7%	Easy
BFS广度优先，这里可以利用Option的特性简化代码

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
上面结果reverse一下
当然如果按照完全二叉树，存到数组的话 Given binary tree {3,9,20,#,#,15,7}，
使用下标的方式也可以获取，完全二叉树特性，
todo

#### 103	Binary Tree Zigzag Level Order Traversal	27.9%	Medium
增加一个奇偶判断是否需要reverse即可，略

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

#### 100	Same Tree	42.7%	Easy
和上题类似，略

#### 110	Balanced Binary Tree	33.4%	Easy
子树高度差不能大于1

使用字段保存“子树深度”，“是否是平衡树”；使用lazy来延迟求值，并使用布尔变量来做short circuit

```
def isBalanced(root: Option[TreeNode]): (Int, Boolean) = root match {
  case None => (Int, true)
  case Some(node) =>
    lazy val (leftdepth, leftb) = isBalanced(node.left)
    lazy val (rightdepth, rightb) = isBalanced(node.right)
    if(leftb && rightb && Math.abs(leftdepth - rightdepth) < 2)
      (max(leftdepth, rightdepth), true)
    else
      (0, false)
}
```

todo dfs 说明
以下题目都可以从dfs得出，与上面类似，这里从略
#### 112	Path Sum	30.8%	Easy
value之和 等于 给定值 的树的路径

#### 113	Path Sum II	27.7%	Medium
所有 value之和 等于 给定值 的树的路径

#### 257	Binary Tree Paths	26.9%	Easy
返回所有路径

#### 129	Sum Root to Leaf Numbers	32.0%	Medium
返回所有路径 所代表的数字之和




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













