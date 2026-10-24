# Data Structures

## Introduction to Data Structures

### Definition of Data Structures

A data structure is a way of organizing and storing data so a program can use it. The choice of structure affects how the program accesses (gets), adds, removes, and changes data.

### Importance of Data Structures in Programming

Programs often need to work with many related pieces of information. A suitable data structure keeps that information organized and makes common operations easier to perform.

For example, a program that stores student scores can use an ordered collection to keep the scores together and find a score by its position.

The best choice depends on what the program needs to do most often. A structure that makes one operation quick may need more memory or make another operation slower.

### Classification of Data Structures

Data structures are commonly grouped as **linear** or **nonlinear**:

- **Linear data structures** arrange elements in a sequence. Arrays, linked lists, stacks, and queues are examples.
- **Nonlinear data structures** connect elements as a hierarchy or network. Trees and graphs are examples.

These groups describe how elements relate to one another. Different structures within either group support different operations and use cases.

### Overview of Data Structures

- **Array:** Stores elements in an ordered sequence. Each item has an index, a number identifying its position. Arrays are useful when items need to be accessed by position.
- **Linked list:** Stores elements in nodes (items containing data and links to other nodes). It is useful when the program needs to add or remove nodes at known positions.
- **Stack:** Adds and removes items at the same end. Last-in, first-out means the item added most recently is removed first.
- **Queue:** Adds items at one end and removes them from the other. First-in, first-out means the item added earliest is removed first.
- **Tree:** Connects nodes (items) in a hierarchy, where some nodes are above others.
- **Graph:** Connects vertices (items in a graph) with edges (connections) to represent a network of relationships.

## Complexity Analysis in Data Structures

Complexity analysis describes how the resources needed by an operation change as the amount of data grows. **Time complexity** describes how the amount of work grows. **Space complexity** describes how the memory use grows.

Big-O notation describes how the work or memory use grows as the number of items grows. Here, `n` means the number of items. `O(1)` means the work stays about the same as `n` grows, while `O(n)` means it can grow in proportion to `n`.

Example: suppose a collection stores the scores 72, 85, and 91.

```text
Array:       [72] [85] [91]
Index:         0   1   2

Linked list: (72) -> (85) -> (91)
```

1. To get the score at index 2 in the array, use the index to access 91 directly. This takes `O(1)` time.
2. To get the third score in the linked list, start at 72, follow a link to 85, then follow a link to 91. In a longer list, this can take `O(n)` time.
3. The linked list stores a link with each node, so it uses extra memory for those links. The array does not need those per-item links.

This example shows a time and space trade-off. The better choice depends on which operations the program needs most often and how much memory it can use.

### Check your understanding

A program often reads items by their position and rarely inserts items between existing ones. Which structure, an array or a linked list, is likely to fit better? Give one reason.

**Expected answer:** An array is likely to fit better because it gives direct access by position, usually in `O(1)` time. This assumes the program does not need many insertions in the middle.


## References

- IBM, [What is a data structure?](https://www.ibm.com/think/topics/data-structure)
- GeeksforGeeks, [Introduction to Linear Data Structures](https://www.geeksforgeeks.org/dsa/introduction-to-linear-data-structures/)