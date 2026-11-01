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

- [Arrays](Arrays.md)
- [Linked Lists](Linked%20Lists.md)
- [Stacks](Stacks.md)
- [Queues](Queues.md)

## References

- GeeksforGeeks, [Introduction to Linear Data Structures](https://www.geeksforgeeks.org/dsa/introduction-to-linear-data-structures/)
