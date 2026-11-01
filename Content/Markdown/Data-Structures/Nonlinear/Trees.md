# Trees

A tree is a hierarchical structure made of **nodes** (items) connected by **edges** (links). The top node is the **root**. A node connected below another node is its **child**; the node above it is its **parent**.

Trees can represent a folder system: a folder can contain other folders, and those folders can contain more folders or files. There is one path from the root to each node.

## Binary trees

A **binary tree** is a tree in which each node has at most two children, commonly called the left child and right child.

```text
      8
     / \
    3   10
   /
  1
```

A binary tree does not have to follow an ordering rule. A **binary search tree** (BST) is a binary tree where values in the left subtree are smaller than the node value, and values in the right subtree are larger. This rule can help with searching. In the example, the values follow the BST rule.

An **AVL tree** is a self-balancing binary search tree. It keeps the heights of a node's left and right subtrees close, helping the tree stay balanced as values are added or removed.

## Tree traversal

Traversal means visiting the nodes in a tree in a particular order. For example, an inorder traversal visits the left subtree, then the current node, then the right subtree. In a binary search tree, this visits values in increasing order.
