## Validate Binary Search Tree

> Given a binary tree, determine if it is a valid binary search tree (BST).
>
> Assume a BST is defined as follows:
>
> - The left subtree of a node contains only nodes with keys **less than** the node's key.
> - The right subtree of a node contains only nodes with keys **greater than** the node's key.
> - Both the left and right subtrees must also be binary search trees.
>
> Solution signature: `boolean isValidBST(TreeNode root)`
>
> [Try it on LeetCode.](https://leetcode.com/problems/validate-binary-search-tree/)



### Strategy

There are two common approaches to this question, in-order traversal of the tree and node values range checking.

##### Range checking

For a binary tree to be a valid BST each subtree needs to be within a certain range of values dictated by its ancestor nodes. For example, ALL node values in the left subtree of the root must be lesser than the root, the right subtree of the left child must be greater than the left child, but lesser than the left child's parent, and so on. Essentially, this means that each subtree (i.e., node values) must be within a certain range in order to form a valid BST.

1. If `root == null` it's a valid BST
2. Otherwise, leftSubtree values must fall within  `(-∞,root.val)` and rightSubtree values must fall within `(root.val,∞)`
3. As traverse left or right, the range boundaries get updates. Traversing left updates the right boundary (the maximum allowed value), and traversing left updates the left boundary (the minimum allowed value)

##### In-order traversal

The in-order traversal approach uses the fact that traversing a valid binary search tree in an in-order manner produces a sorted list of values, since leftSubtree < root < rightSubtree. As per the BST definition, it is sufficient that we check that an in-order traversal produces an increasing sequence of values which will ensure that leftSubtree < root < rightSubtree. To verify this, we don't have to store all the values, but only the last, not null, traversed node value.



### Code

##### Version 1: recursion

```java
public class Solution {
  public boolean isValidBST(TreeNode root) {
    return isValidBST(root, Long.MIN_VALUE, Long.MAX_VALUE);
  }

  public boolean isValidBST(TreeNode root, long minVal, long maxVal) {
    if (root == null) return true;
    if (root.val >= maxVal || root.val <= minVal) return false;
    return isValidBST(root.left, minVal, root.val) && 
      isValidBST(root.right, root.val, maxVal);
  }
}
```

This version is very concise pretty clear. I would argue however, that the overloaded `isValidBST(..)` could be named better to make it reflect its intent. This overloaded version of the `isValidBST(..)` method checks whether (sub)tree values are all within a specified range, so let's call it something that communicates this, say `treeInRange(..)`.

Personally, I also find the initial statement `isValidBST(root, Long.MIN_VALUE, Long.MAX_VALUE)` not very intuitive and prefer to break it down into separate cases when `root === null` and when it's not. Answering whether a given binary tree is a valid BST boils down to:

```
root == null && 
leftSubtree  values are in the range (-∞, root.val) && 
rightSubtree values are in the range (root.val, ∞)
```

This will be demonstrated in the refactored section.



##### Version 2: in-order traversal

```java
public boolean isValidBST(TreeNode root) {
  if (root == null) return true;
  Stack<TreeNode> stack = new Stack<>();
  TreeNode pre = null;
  while (root != null || !stack.isEmpty()) {
    while (root != null) {
      stack.push(root);
      root = root.left;
    }
    root = stack.pop();
    if(pre != null && root.val <= pre.val) return false;
    pre = root;
    root = root.right;
  }
  return true;
}
```

In this case things get a little cryptic, at least at first glance. The author notes that he uses an in-order traversal to check the validity of the input binary tree, but it is quite challenging to get that from the code at first (and even second glance). There's little separation of concerns here, and it's hard to tell apart the code that traverses the tree and the code that does the BST properties validation, which is not ideal. 



### Refactored

##### Version 1

The original version is already in great shape, so the following changes are quite minor and revolve around making variable / method names better reflect their intent.

```java
public boolean isValidBST(TreeNode root) {
  return root == null
    || (treeInRange(root.right, root.val, Long.MAX_VALUE)
        && treeInRange(root.left, Long.MIN_VALUE, root.val));
}
```

```java
private boolean treeInRange(TreeNode node, long min, long max) {
  return node == null
    || ((node.val > min && node.val < max)
        && treeInRange(node.left, min, node.val)
        && treeInRange(node.right, node.val, max));
}
```



##### Version 2

This version will require more changes to make things clear(er). On of the fundamental things to realize here, is that a tree traversal algorithm can be seen as mechanism that streams values into a black box. This black box is often called a visitor, because it visits the nodes it is being passed. When using in/pre/post order traversal, it will usually involve visitor.

In our case, we wish the visitor mechanism to verify that the node values it receives are in an increasing order (as discussed in the strategy section earlier). The main function will therefore look like so:

```java
public boolean isValidBST(TreeNode root) {
  return traverseInOrder(root, verifyIncreasingValues);
}
```

`traverseInOrder(..)` is a pretty [standard implementation of an in-order binary tree traversal](https://en.wikipedia.org/wiki/Tree_traversal#In-order_(LNR)) where the visitor is of type `Function<TreeNode, Boolean>` since it receives a node and returns whether it violates the BST properties so far. 

```java
private boolean traverseInOrder(TreeNode node, Function<TreeNode, Boolean> visitor) {
  if (node == null) {
    return visitor.apply(null); // let the visitor decide
  }

  return traverseInOrder(node.left, visitor)
    && visitor.apply(node)
    && traverseInOrder(node.right, visitor);
}
```

The visitor performs this validation by comparing the currently traversed node value to the value of the previously traversed, non `null` node:

```java
private final Function<TreeNode, Boolean> verifyIncreasingValues =
  new Function<>() {

  TreeNode previouslyTraversed;

  @Override
  public Boolean apply(TreeNode node) {
    if (node == null) {
      return true;
    } else {
      boolean isValid = previouslyTraversed == null || node.val > previouslyTraversed.val;
      previouslyTraversed = node;
      return isValid;
    }
  }
};
```

When a `null` node is traversed we must not store it in the `previouslyTraversed`. A `null` node never violates the BST properties so the visitor just need to return `true`.

The `apply(..)` method could also be written as:

```java
boolean isValid = node == null || node.val > previouslyTraversed.val;
previouslyTraversed = node == null ? previouslyTraversed : node;
return isValid;
```

This block however, includes two queries asking the same thing: `node == null`. Since I don't like asking twice, I find this a little less elegant.