# Linear Data Structures

## What are linear data structures?

A linear data structure stores its elements in a sequence. Visiting the elements one by one in their order is called traversal. Each element (apart from the first and last) has a neighbour before it and a neighbour after it. In complexity notation, `n` means the number of elements. Big-O notation describes how the amount of work grows as `n` grows.

A simple example is a queue of people waiting to buy tickets. Each person has someone in front and behind, except for the people at the two ends.

## Characteristics

Elements are arranged sequentially.
- There is a start element and an end element, unless the structure is empty.
- Every element has a predecessor/previous element (except for the first element) and a sucessor/next element (except for the last element).
- Elements can usually be traversed from one end to the other.
- The way elements are stored in memory depends on the type of structure. Array elements are commonly stored next to each other, while linked-list nodes can be in different locations.
- Some structures have a fixed capacity, others can grow or shrink as elements are added or removed.
- The time needed to access, search, insert, or delete an element depends on the structure and the operation.

## Examples of Linear Data Structures

### Arrays

An array stores elements in an ordered sequence. In many programming languages, an array holds values of one data type. Some languages and array-like structures allow different types.

#### Characteristics

- Elements are identified by an index, a number that marks a position in the sequence. In many languages, indexes start at `0`.
- Array elements are commonly stored in adjacent memory locations, meaning one after another in memory.
- An element can be accessed directly by its index. This is constant time, written `O(1)`: the access takes about the same time as the array grows.
- Some arrays have a fixed size. Dynamic arrays can grow, although growing them may require allocating a larger block and copying elements.

#### Types

##### One-dimensional arrays (1D)

A one-dimensional array is a single sequence of elements.

Example: daily temperatures for one week.

##### Two-dimensional arrays (2D)

A two-dimensional array arranges elements in rows and columns. In some languages it is represented as an array of arrays.

Example: a grid of pixels in an image.

##### Multidimensional arrays

An array can have more than two dimensions. A three-dimensional array, for example, can represent a set of 2D grids.

#### Operations

- **Access:** Use an index to get an element. For example, in an array containing 72, 85, and 91, the item at index 1 is 85 when indexing starts at zero.
- **Update:** Replace the value at an index, such as changing the second score to 88.
- **Search:** Linear search checks elements one by one and may inspect all `n` elements, so it takes `O(n)` time in the worst case. Binary search repeatedly halves the sorted search range. It takes `O(log n)` time, meaning the number of checks grows with how many times the range can be halved.
- **Insertion:** Add an element at a position. Elements after that position may need to shift right.
- **Deletion:** Remove an element. Later elements may need to shift left to fill the gap.

Arrays are useful when the number of items is known or when items need frequent index-based access. They are less convenient when many insertions and deletions happen in the middle.

#### Example: finding a score

An array stores the scores 72, 85, and 91 in that order. To find 91 with a linear search, first compare 72, then 85, then 91. The third comparison finds the score. If there are `n` scores and the target is last or absent, the search may check all `n` items, so its time grows as `O(n)`.

#### Try it

A list stores the numbers 4, 7, and 9 in that order. Use a linear search to find 7. How many values do you check? What changes if the target is 9?

**Expected answer:** Finding 7 takes two checks: 4, then 7. Finding 9 takes three checks: 4, 7, then 9. The search stops as soon as it finds the target.

### Linked List

A linked list is a sequence of nodes. A node is an item that stores a value and one or more links to other nodes. Nodes do not have to be stored next to each other in memory.

#### Features

- **Node:** Holds data and link information.
- **Head:** The first node and the starting point for traversing the list.
- **Tail:** The last node. In a singly linked list, its next link is empty.

#### Types

- **Singly linked list:** Each node links to the next node, so traversal moves forward.
- **Doubly linked list:** Each node links to both the next and previous nodes, so traversal can move in either direction. It uses extra memory for the additional link.
- **Circular linked list:** The last node links back to the first node. A circular list can be singly or doubly linked.

#### Operations

- **Accessing an element:** Start at the head and follow links until reaching the required position. This takes `O(n)` time in the general case.
- **Searching:** Visit nodes in order until the value is found or the end is reached. This takes up to `O(n)` time.
- **Insertion:** A node can be inserted by changing links. Inserting at the head takes `O(1)` time. Inserting after a node also takes `O(1)` once that node is known, finding the position can still take `O(n)`.
- **Deletion:** Change links to skip the node being removed. Deleting the head takes `O(1)`. Finding another node to delete may take `O(n)`.

Linked lists are useful when items are often added or removed at known positions. They do not provide direct index-based access like arrays, and each node needs memory for its links.

### Stack

A stack is a linear data structure that follows the Last-In, First-Out (LIFO) principle. The most recently added element is the first one removed. A stack of plates is a familiar example: the top plate is usually the first one picked up.

#### Operations

- **Push:** Add an item to the top.
- **Pop:** Remove and return the top item. The stack must not be empty.
- **Top or peek:** Read the top item without removing it.
- **Size:** Return the number of items.
- **Is empty:** Check whether there are no items.

Push, pop, and peek usually take `O(1)` time. A stack can be implemented with an array or a linked list. Stacks are used for undo history, tracking function calls, and checking matching brackets.

### Queue

A queue is a linear data structure that usually follows the First-In, First-Out (FIFO) rule. The first item added is the first one removed. A line of people waiting for a bus is a familiar example.

#### Operations

- **Enqueue:** Add an item at the rear.
- **Dequeue:** Remove and return the item at the front. The queue must not be empty.
- **Front or peek:** Read the front item without removing it.
- **Rear:** Read the last item without removing it.
- **Size:** Return the number of items.
- **Is empty:** Check whether there are no items.


#### Types

- **Circular queue:** Treats the ends of an array as connected, so positions freed at the front can be reused.
- **Double-ended queue (deque):** Allows items to be added or removed at either end.
- **Input-restricted deque:** Allows insertion at one end, but deletion at either end.
- **Output-restricted deque:** Allows deletion at one end, but insertion at either end.
- **Priority queue:** Removes the item with the highest (or lowest) priority first. Items with equal priority are often handled in arrival order, depending on the implementation. It is not necessarily FIFO overall.

Queues are useful for tasks such as handling print jobs, processing requests in arrival order, and keeping track of work waiting to be done.

## References

- GeeksforGeeks, [Introduction to Linear Data Structures](https://www.geeksforgeeks.org/dsa/introduction-to-linear-data-structures/)