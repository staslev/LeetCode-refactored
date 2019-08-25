## Binary Tree Level Order Traversal

> Given a binary tree, return the *level order* traversal of its nodes' values. (ie, from left to right, level by level).
>
> For example:
> Given binary tree `[3,9,20,null,null,15,7]`,
>
> ```
>  3
> / \
> 9  20
>  /  \
> 15   7
> ```
>
> 
>
> return its level order traversal as:
>
> ```
> [
> [3],
> [9,20],
> [15,7]
> ]
> ```
>
> Solution signature: `List<List<Integer>> levelOrder(TreeNode root)`
>
> [Try it on LeetCode.](https://leetcode.com/problems/binary-tree-level-order-traversal/)



### Strategy

Various tree traversal techniques are very common in interview questions. Luckily, it tends to boil down to a variation of one of the following [key traversal methods](https://en.wikipedia.org/wiki/Tree_traversal):

* BFS (breadth first search/traversal)
* DFS (depth first search/traversal)
* In-order traversal: `left`, `root`, `right`
* Pre-order traversal: `root`, `left`, `right`
* Post-order traversal: `left`, `right`, `root`

Note that the pre/in/post refers to the order in which the `root` node is traversed relatively to the `left` and `right` subtrees. The "regular" order is considered to be: `left`, `root`, `right` (i.e., "in-order").

Back to the given questions, which asks for a level order traversal. This can be viewed as a variation on the BFS traversal, since it goes breadth first, that is, if first traverses all the nodes of a given level before going to the next level, which is exactly what we want.

Another way would be to use pre-order traversal which may at first glance seems to be very different from what we need, but using some trickery we can get it to work.

You can also take a look at the [Symmetric Tree](SymmetricTree.md) questions which where we use level order traversal as a building block. Since this time level order traversal is the main thing we're dealing with, we will make our best to simplify it as much as possible.



### (Leet)Code

##### BFS

Here's a very up-voted solution from LeetCode:

```java
public List<List<Integer>> levelOrder(TreeNode root) {
  Queue<TreeNode> queue = new LinkedList<TreeNode>();
  List<List<Integer>> wrapList = new LinkedList<List<Integer>>();

  if(root == null) return wrapList;

  queue.offer(root);
  while(!queue.isEmpty()){
    int levelNum = queue.size();
    List<Integer> subList = new LinkedList<Integer>();
    for(int i=0; i<levelNum; i++) {
      if(queue.peek().left != null) queue.offer(queue.peek().left);
      if(queue.peek().right != null) queue.offer(queue.peek().right);
      subList.add(queue.poll().val);
    }
    wrapList.add(subList);
  }
  return wrapList;
}
```

This is an implementation of the BFS algorithm. However, figuring the details is not very straight forward.

* `subList` and `wrapList` are not a very informative variable names
* `levelNum` is misleading, while one may be tempted to think it is the (index) number of the tree level, but a deeper inspection of the code reveals it's in fact the initial size of the (work) queue for a given iteration, so the intent was probably to store the number of nodes in a given level. Other names could have served this purpose better, e.g., `nodeCountInLevel`, `numberOfNodesInLevel`, `levelSize`, etc.
* The control flow is not very straight forward, and it would be nice to introduce some helper methods to make things clearer

Another interesting thing is to node how this code tells apart the different levels while using a single queue that is never depleted unless all work is done. This is done by recording the initial queue size, so that when new nodes are added to the queue the algorithm is able to tell apart which nodes were already present and which were added as part of the current discovery iteration. Cool trick!

##### In-order

Here's a very up-voted solution from LeetCode:

```java
public List<List<Integer>> levelOrder(TreeNode root) {
  List<List<Integer>> res = new ArrayList<List<Integer>>();
  levelHelper(res, root, 0);
  return res;
}

public void levelHelper(List<List<Integer>> res, TreeNode root, int height) {
  if (root == null) return;
  if (height >= res.size()) {
    res.add(new LinkedList<Integer>());
  }
  res.get(height).add(root.val);
  levelHelper(res, root.left, height+1);
  levelHelper(res, root.right, height+1);
}
```

That's a neat version of a recursive pre-order traversal of a tree. 

I'd change the name of `levelHelper(..)` to `traversePreOrder(..)` and reorder the argument list a bit (and I did, when discussing [Symmetric Tree](SymmetricTree.md)), but other than that this is a clear and concise solution right out of the box.



### Refactored

##### BFS

The common BFS algorithm traverses a tree in level order, but without tweaking it, it will produce a single list of all traversed nodes. Therefore we wish to take the original BFS, and tweak it to produce a list of lists of traversed nodes, from which we can then extract the node values and output a `List<List<Integer>>`. While we can extract node values while traversing nodes (instead of having two separate stages), I believe that separating these concerns makes for a better design.

This is what we want to do copy-paste from mind to code:

```java
public List<List<Integer>> levelOrder(TreeNode root) {
  List<List<TreeNode>> levels = bfs(root);
  return extractNodeValues(levels);
}
```

And now for the BFS part, which start from the root, and traverses breadth first, level by level. We need to make sure we collect each level's nodes separately and put them the result list. It's important that we keep thinking in building blocks, otherwise it is extremely easy to get lost in all the details, mix up the current and next levels, try to traverse `null` nodes and the likes.

We will therefore stick to a few guiding principles:

* Keep a `levels` variable to which we will be adding levels
* Keep a `workingSet` variable, which is a list of nodes we are about to process, but have not yet added to the result
* The `workingSet` may contain `null` nodes, which we need to skip when processing 
* In each iteration we will be processing nodes *from* the `workingSet`, and possibly adding newly discovered nodes *to* the `workingSet`. 
  * In order to be able to tell levels apart we first collect all the newly discovered nodes, as opposed to adding them one by one
  * A solution presented on LeetCode and discussed below uses an alternative method, where the work queue's size is queried before processing, and is used to determine where a new level of nodes starts
* When the `workingSet` is empty it means we have traversed the entire tree and things should end

The `bfs(..)` method would therefore look like so:

```java
private List<List<TreeNode>> bfs(TreeNode root) {
  if (root == null) {
    return new ArrayList<>();
  }
  
  List<List<TreeNode>> levels = new ArrayList<>();
  List<TreeNode> workingSet = new ArrayList<>();
  
  workingSet.add(root);
  while (!workingSet.isEmpty()) {
    List<TreeNode> level = workingSet;
    levels.add(level);
    workingSet = discoverNew(level);
  }

  return levels;
}
```

The next algorithmic step is to discover new nodes which need to be traversed. This is what `discoverNew(..)` does. 

```java
private List<TreeNode> discoverNew(List<TreeNode> nodes) {
  List<TreeNode> discovered = new ArrayList<>();
  for (TreeNode node : nodes) {
    List<TreeNode> childNodes = Arrays.asList(node.left, node.right);
    discovered.addAll(nonNullNodes(childNodes));
  }
  return discovered;
}

```

If you're a Java streams fan then you'd like this:

```java
private List<TreeNode> discoverNew(List<TreeNode> nodes) {
  Predicate<TreeNode> nonNull = Predicate.not(Predicate.isEqual(null));
  return nodes.stream()
    .filter(nonNull)
    .flatMap(node -> Stream.of(node.left, node.right))
    .filter(nonNull)
    .collect(Collectors.toList());
}
```

This is not very pretty, mostly on account of Java, well, being Java. But that is a nice exercise in doing things the stream way.

Another point worth noting is how we deal with `null` nodes in the context of the `bfs(..)` and the `discoverNew(..)` methods. For traversal only, `null` nodes are of little interest since they will not lead to the discovery of new nodes. However, they do carry certain information which can be important, like the absence of child nodes for example. Some algorithms require this information, such as the one employed to solve the [Symmetric Tree](SymmetricTree.md) question. In this algorithm `null` nodes must be considered to check for level symmetry. The bottom line is that it's important to understand whether `null` nodes are of interest or not, since it will determine whether they should be filtered out.

Next we implement a couple of helper methods, namely `nonNullNodes(..)` and `extractNodeValues(..)`:

```java
private List<TreeNode> nonNullNodes(List<TreeNode> nodes) {
  return nodes.stream()
    .filter(Predicate.not(Predicate.isEqual(null)))
    .collect(Collectors.toList());
}
```

```java
private List<List<Integer>> extractNodeValues(List<List<TreeNode>> levels) {
  // Java, oh Java
  // scala: levels.map(_.map(_.val))
  return levels.stream()
    .map(level -> level.stream().map(node -> node.val).collect(Collectors.toList()))
    .collect(Collectors.toList());
}
```

##### BFS, the visitor version

BFS can be viewed as a traversal method, and can thus be used with a visitor that consumes the visited nodes / levels. When a node/level is traversed by the BFS it can be sent to the visitor object that is passed as an argument to the BFS.

```java
public List<List<Integer>> levelOrder(TreeNode root) {
  final List<List<TreeNode>> levels = new ArrayList<>();
  bfs(root, levels::add); // the 2nd arg is a Consumer<List<TreeNode>>
  return extractNodeValues(levels);
}
```

where `bfs(..)` is similar to what we had before, except that it now interacts with a visitor instead of returning a value to the caller:

```java
private void bfs(TreeNode root, Consumer<List<TreeNode>> levelVisitor) {
  if (root == null) {
    return new ArrayList<>();
  }
  
  List<TreeNode> workingSet = new ArrayList<>();
  
  workingSet.add(root); 
  while (!workingSet.isEmpty()) {
    List<TreeNode> level = workingSet;
    levelVisitor.accept(level); // hey visitor, look what I've got for you
    workingSet = discoverNew(level);
  }
}
```
