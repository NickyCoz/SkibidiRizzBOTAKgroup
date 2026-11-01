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

- [Trees](Trees.md)
- [Graphs](Graphs.md)

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
