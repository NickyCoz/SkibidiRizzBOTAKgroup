# Linked List

A linked list is a sequence of nodes. A node is an item that stores a value and one or more links to other nodes. Nodes do not have to be stored next to each other in memory.

## Features

- **Node:** Holds data and link information.
- **Head:** The first node and the starting point for traversing the list.
- **Tail:** The last node. In a singly linked list, its next link is empty.

## Types

- **Singly linked list:** Each node links to the next node, so traversal moves forward.
- **Doubly linked list:** Each node links to both the next and previous nodes, so traversal can move in either direction. It uses extra memory for the additional link.
- **Circular linked list:** The last node links back to the first node. A circular list can be singly or doubly linked.

## Operations

- **Accessing an element:** Start at the head and follow links until reaching the required position. This takes `O(n)` time in the general case.
- **Searching:** Visit nodes in order until the value is found or the end is reached. This takes up to `O(n)` time.
- **Insertion:** A node can be inserted by changing links. Inserting at the head takes `O(1)` time. Inserting after a node also takes `O(1)` once that node is known, finding the position can still take `O(n)`.
- **Deletion:** Change links to skip the node being removed. Deleting the head takes `O(1)`. Finding another node to delete may take `O(n)`.

Linked lists are useful when items are often added or removed at known positions. They do not provide direct index-based access like arrays, and each node needs memory for its links.
