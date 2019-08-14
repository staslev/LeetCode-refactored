## Populating Next Right Pointers in Each Node

> You are given a **perfect binary tree** where all leaves are on the same level, and every parent has two children. The binary tree has the following definition:
>
> ```
> struct Node {
>   int val;
>   Node *left;
>   Node *right;
>   Node *next;
> }
> ```
>
> Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to `NULL`.
>
> Initially, all next pointers are set to `NULL`.
>
>  
>
> **Example:**
>
> ![img](https://assets.leetcode.com/uploads/2019/02/14/116_sample.png)
>
>  
>
> **Note:**
>
> - You may only use constant extra space.
> - Recursive approach is fine, implicit stack space does not count as extra space for this problem.
>
> Solution signature: `Node connect(Node root) `
>
> [Try it on LeetCode.](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/)



### Strategy

While it may not seem that way at first, this question is also a about choosing the right traversal method, which in this case is pre-order, meaning we wish to visit the root node first, then its left subtree and finally its right subtree. However, this is not where the story ends, since the tricky part is performing the level-wise connection of the nodes while traversing.

Whenever we visit a node `node` we perform the following:

1. If `node` has any children make the left child's `next` point to the right child
2.  If `node`'s `next` is populated, make the right child's `next` point to `node.next.left`

The second step is the tricky part, and it leverages the traversal method we have chosen (pre-order). Since we first visit the root, by the time we get to its children their `next` reference have been already populated by step (1). This allows step (2) to use the root's `next` pointer to connect the "cousin" nodes.



### Code

Here's a top-voted solution from LeetCode:

```c++
void connect(TreeLinkNode *root) {
  if (root == NULL) return;
  TreeLinkNode *pre = root;
  TreeLinkNode *cur = NULL;
  while(pre->left) {
    cur = pre;
    while(cur) {
      cur->left->next = cur->right;
      if(cur->next) cur->right->next = cur->next->left;
      cur = cur->next;
    }
    pre = pre->left;
  }
}
```

Note that it's in `C++`, but this does not prevent us from realizing it is implementing the strategy described above. 

Or is it? 

It is not very clear at this point, since the traversal method used is not very apparent. By looking at the main `while` loop it seems like we're going left (`pre = pre->left`), thought in the body of that while we're also taking detours via the `next` references of the visited nodes (`cur = cur->next`).

Let's see if we can restructure this code so it's clearer.



### Refactored

As discussed in the strategy section, we'd like to use pre-order traversal, which goes hand in hand with visitors that handle the visited nodes. In our case, the visitor will be responsible for connecting the nodes, i.e., populating their `next` reference.

```java
public Node connect(Node root) {
  traversePreOrder(root, this::connectSameLevelNodes);
  return root;
}
```

The `traversePreOrder(..)` method is a straight forward implementation of a pre order traversal of a binary tree:

```java
private void traversePreOrder(Node root, Consumer<Node> visitor) {
  if (root == null) {
    return;
  }

  visitor.accept(root);
  traversePreOrder(root.left, visitor);
  traversePreOrder(root.right, visitor);
}
```

Now we need to figure out how exactly `connectSameLevelNodes(..)` will do its job. Luckily, we already know the answer, since we figured it out in the strategy part. It should implement what we devised in steps (1) and (2) of the strategy section.

```java
private void connectSameLevelNodes(Node root) {
  connectDirectChildNodes(root); // step 1
  connectCousinNodes(root); // step 2
}

private void connectDirectChildNodes(Node root) {
  if (root.left != null) {
    root.left.next = root.right;
  }
}

private void connectCousinNodes(Node node) {
  if (node.next != null && node.right != null) {
    node.right.next = node.next.left;
  }
}
```



#### Variants

Similar code can be written using lambda functions:

```java
Consumer<Node> sameLevelNodeConnector = node -> {
  connectDirectChildNodes(node); // step 1
  connectCousinNodes(node); // step 2
};

public Node connect(Node root) {
  traversePreOrder(root, sameLevelNodeConnector); // visitor instance vs. method reference
  return root;
}
```

Or even inline the visitor instance:

```java
public Node connect(Node root) {
  traversePreOrder(
    root,
    node -> { // an annonymous visitor instance, too bad though, names are nice
      connectDirectChildNodes(node); // step 1
      connectCousinNodes(node); // step 2
    });
  return root;
}
```

Personally I prefer the very first version where we used a method reference (`traversePreOrder(root, this::connectSameLevelNodes)`). Call me old fashioned but I believe methods are a great tool to communicate meaning and separate concerns.
