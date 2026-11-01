# Stack

A stack is a linear data structure that follows the Last-In, First-Out (LIFO) principle. The most recently added element is the first one removed. A stack of plates is a familiar example: the top plate is usually the first one picked up.

## Operations

- **Push:** Add an item to the top.
- **Pop:** Remove and return the top item. The stack must not be empty.
- **Top or peek:** Read the top item without removing it.
- **Size:** Return the number of items.
- **Is empty:** Check whether there are no items.

Push, pop, and peek usually take `O(1)` time. A stack can be implemented with an array or a linked list. Stacks are used for undo history, tracking function calls, and checking matching brackets.
