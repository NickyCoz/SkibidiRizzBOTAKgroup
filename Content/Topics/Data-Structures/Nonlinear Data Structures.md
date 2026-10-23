# Nonlinear Data Structures

## What are nonlinear data structures?

A nonlinear data structure does not arrange its elements in one sequence. Its elements connect in a hierarchy or a network. Some common examples are trees and graphs. They are useful when data has relationships that are difficult to represent as a simple linear sequence.

## Characteristics

- Elements are connected through relationships rather than placed in one sequence.
- The structure can have multiple levels or branches.
- Traversal follows the structure's connections to visit elements.
- Trees represent hierarchical relationships, graphs represent networked relationships.
- How elements are connected affects how searching and traversal work.

## Examples of Nonlinear Data Structures

### Trees

A tree is a hierarchical structure made of **nodes** (items) connected by **edges** (links). The top node is the **root**. A node connected below another node is its **child**; the node above it is its **parent**.

Trees can represent a folder system: a folder can contain other folders, and those folders can contain more folders or files. There is one path from the root to each node.

#### Binary trees

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

#### Tree traversal

Traversal means visiting the nodes in a tree in a particular order. For example, an inorder traversal visits the left subtree, then the current node, then the right subtree. In a binary search tree, this visits values in increasing order.

### Graphs

A graph is made of **vertices** (items) and **edges** (connections between items). A graph can represent a network, such as roads between places or friendships between people. Unlike a tree, it does not have to follow a parent-child hierarchy.

An **undirected graph** has connections that can be followed in either direction. A **directed graph** has connections with a direction.

#### Example: a small friendship network

Each person below is a vertex, and each line is an undirected edge. This example is made for these notes.

```text
Nicky --- Darren
  |          |
 Neil ---  Glen
```

Starting at Nicky, we can follow an edge to Darren or Neil. From Neil, we can reach Glen. When traversing a graph, keeping track of visited vertices helps avoid visiting the same person repeatedly.

#### Graph traversal

A graph traversal visits vertices by following edges. **Depth-first search** follows one route as far as it can before returning to earlier vertices. **Breadth-first search** visits nearby vertices first, then moves to vertices farther away. Both methods can use a record of visited vertices to avoid revisiting them.

## Linear and nonlinear structures

| Linear structure | Nonlinear structure |
| --- | --- |
| Elements are arranged sequentially. | Elements connect hierarchically or as a network. |
| Usually has one level. | Can have multiple levels. |
| Examples: arrays, linked lists, stacks, queues. | Examples: trees and graphs. |
| Traversal follows the sequence. | Traversal follows connections and depends on the structure. |

## Try it

```text
Nicky --- Darren
  |          |
 Neil ---  Glen
```
In the friendship network above, which people can be reached directly from Nicky? Which person can be reached by following two edges from Nicky?

**Expected answer:** Darren and Neil can be reached directly from Nicky. Glen can be reached by following the edges from Nicky to Neil and then from Neil to Glen.

## References

- CompilerSutra, [Non-Linear Data Structures](https://www.compilersutra.com/docs/DSA/non-Linear-DSA/)
- GeeksforGeeks, [Difference between Linear and Non-linear Data Structures](https://www.geeksforgeeks.org/dsa/difference-between-linear-and-non-linear-data-structures/)
