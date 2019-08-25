## Symmetric Tree

>Given a binary tree, check whether it is a mirror of itself (ie, symmetric around its center).
>
>For example, this binary tree `[1,2,2,3,4,4,3]` is symmetric:
>
>```
>        1
>       / \
>     2   2
>    / \ / \
>   3  4 4  3
>```
>But the following `[1,2,2,null,3,null,3]` is not:
>
>```
>       1
>      / \
>     2   2
>     \   \
>      3    3
>```
>
>**Note:**
>Bonus points if you could solve it both recursively and iteratively.
>
>Solution signature: `boolean isSymmetric(TreeNode root)`
>
>[Try it on LeetCode.](https://leetcode.com/problems/symmetric-tree/)



### Strategy

As the question suggests, there are a number of possible solutions, some recursive while others iterative. 

In the refactored section we will explore two different approaches:

* A BFS (breadth first search) traversal of the binary tree, and
* A pre-order traversal of the binary tree

Each of these approaches provides access to the tree's levels in a way which allows for checking the nodes for symmetry. More details will be provided in the sections below.



### (Leet)Code

The following two solutions were copied from LeetCode's discussion section.

##### Recursive solution

```java
public boolean isSymmetric(TreeNode root) {
  return root==null || isSymmetricHelp(root.left, root.right);
}

private boolean isSymmetricHelp(TreeNode left, TreeNode right){
  if(left==null || right==null) {
    return left==right;
  }    
  if(left.val!=right.val) {
    return false;
  }
  return isSymmetricHelp(left.left, right.right) && isSymmetricHelp(left.right, right.left);
}
```

This is some next level elegancy. Though I have reasons to believe it's not going to be easy to come up with something like this during an interview, and it's more like those either-you-know-it-or-you-don't solutions. That said, this is by far the most elegant solution I'm aware of to this question.

If it was up to me I'd use method overloading and change the name of `isSymmetricHelp` to `isSymmetric` as I'm not a big fan of helper methods whose name actually includes the string "helper", but that's being pedantic.

##### Iterative solution

The iterative solution quite the opposite in terms of elegancy.

```java
public boolean isSymmetric(TreeNode root) {
  if(root==null)  return true;

  Stack<TreeNode> stack = new Stack<TreeNode>();
  TreeNode left, right;
  if(root.left!=null){
    if(root.right==null) return false;
    stack.push(root.left);
    stack.push(root.right);
  }
  else if(root.right!=null){
    return false;
  }

  while(!stack.empty()){
    if(stack.size()%2!=0)   return false;
    right = stack.pop();
    left = stack.pop();
    if(right.val!=left.val) return false;

    if(left.left!=null){
      if(right.right==null)   return false;
      stack.push(left.left);
      stack.push(right.right);
    }
    else if(right.right!=null){
      return false;
    }

    if(left.right!=null){
      if(right.left==null)   return false;
      stack.push(left.right);
      stack.push(right.left);
    }
    else if(right.left!=null){
      return false;
    }
  }

  return true;
}
```

This code is intimidating, in a way it's the exact opposite of the previous, recursive solution. The numerous loops and conditional statements make it extremely hard to understand. There aren't any abstractions, naming variables after their type (e.g., `stack`) conveys little information.



### Refactored

##### Iterative solution

As far as iterative solutions go, a standard [BFS](https://en.wikipedia.org/wiki/Breadth-first_search) traversal would serve us well here. We wish to traverse the binary tree level by level, and check whether each level is symmetric as required. Similarly to in/pre/post traversal, BFS is also a type of traversal, thus we can use a visitor to process each level and determine whether it is symmetric.

The signature of such a visitor will therefore be `Function<List<TreeNode>, Boolean> visitor`, since it receives a list of nodes comprising a tree level, and returns `true` or `false` depending on whether that level is symmetric.

Since we traverse each and every node in the worst case, the time and space complexities are *O(2^h)* where `h` is the height of the tree.

The main method looks like so:

```java
public boolean isSymmetric(TreeNode root) {
  return traverseLevels(root, symmetryValidator);
}
```

Where `traverseLevels(..)` is responsible for traversing tree levels and feed each level to the visitor. This is in fact, a quite standard BFS implementation. After discovering a new level, the BFS feeds it to the visitor.

```java
private boolean traverseLevels(TreeNode root, Function<List<TreeNode>, Boolean> visitor) {
  // root == null will be properly handled
  Queue<TreeNode> workingSet = new LinkedList<>(Arrays.asList(root));
  while (!workingSet.isEmpty()) {
    List<TreeNode> newlyDiscovered = discoverNextLevelNodes(workingSet);
    if (!visitor.apply(newlyDiscovered)) {
      return false;
    }
    workingSet.addAll(newlyDiscovered);
  }
  return true;
}
```

It's important to note even `null` child nodes are added to the list of nodes comprising a given tree level. This is crucial for correctness, since `[v,null]` is NOT a symmetric level, while `[v]` is, so unless we add the `null`s we may get a wrong answer. Since there is no guarantee we'll be dealing with perfectly balanced trees, we must add `null` child nodes.

```java
private List<TreeNode> discoverNextLevelNodes(Queue<TreeNode> work) {
  List<TreeNode> level = new ArrayList<>();
  while (!work.isEmpty()) {
    TreeNode node = work.poll();
    if (node != null) {
      level.add(node.left);
      level.add(node.right);
    }
  }
  return level;
}
```

Finally, we also need the visitor which given a tree level can tell if it is symmetric. Note what tree levels may contain `null` values, so the symmetry check must take them into account. The symmetry check looks a lot like checking whether a string is a palindrome, but we need the equality check to take nulls into consideration, see the `treeNodeEquals(TreeNode n1, TreeNode n2)` method.

```java
private final Function<List<TreeNode>, Boolean> symmetryValidator =
  new Function<>() {
  
  public Boolean apply(List<TreeNode> level) {
    for (int start = 0, end = level.size() - 1; start < end; start++, end--) {
      if (!treeNodeEquals(level.get(start), level.get(end))) {
        return false;
      }
    }
    return true;
  }

  private boolean treeNodeEquals(TreeNode n1, TreeNode n2) {
    return n1 == n2 || (n1 != null && n2 != null && n1.val == n2.val);
  }
};

```

##### Recursive solution

One way to go recursive is to use [pre-order traversal](https://en.wikipedia.org/wiki/Tree_traversal#Pre-order_(NLR)), which can collect each tree level's nodes into a data structure and apply the symmetry check. In a pre-order traversal, the root node is visited first, then the left subtree, and finally the right subtree. The visited nodes are collected into a list which is shared between all the recursion branches. 

The time complexities are *O(2^h)* where `h` is the height of the tree.

Space complexity is `1 + 2 + … + 2^h = 2^(h+1) - 1` since we collect and keep, all the tree levels, however, this is still *O(2^h)*.

```java
public boolean isSymmetric(TreeNode root) {
  List<List<TreeNode>> levels = new ArrayList<>();
  traversePreOrder(root, levels, 0);
  return levels.stream().allMatch(symmetryValidator::apply);
}
```

```java
private void traversePreOrder(TreeNode node, List<List<TreeNode>> levels, int height) {
  if (levels.size() - 1 < height) {
    levels.add(height, new ArrayList<>());
  }
  levels.get(height).add(node);
  if (node != null) {
    int childHeight = height + 1;
    traversePreOrder(node.left, levels, childHeight);
    traversePreOrder(node.right, levels, childHeight);
  }
}
```
