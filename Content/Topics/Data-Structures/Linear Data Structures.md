# Linear Data Structure
Reference: https://www.geeksforgeeks.org/dsa/introduction-to-linear-data-structures/

## What are linear data structures?

Linear Data Structures are a type of data structure where elements are arranged sequentially or linearly. Each element has a previous and next except for the first and last elements.

// Insert Allegory

## Characteristics
- Elements are arranged sequentially.
- There is a start element and an end element.
- Every element has a predecessor/previous element (except for the first element) and a sucessor/next element (except for the last element).
- The order of elements is preserved. 
- The size can be fixed or dynamic.
- Accessing elements within a linear data structure is typically very efficient. 

// TODO: Elaborate

## Examples of Linear Data Structures: 
### Arrays
An array is a collection of elements stored sequentially. 
You can also think of arrays as lists (although, in some programming languages a list is typically different from an array). 
#### Characteristics
- All elements must be of the same data type
- In most programming languages, elements of an array are stored in contigous (adjacent) memory locations.
- Arrays are indexed using an integer, typically zero-based (meaning the first element is accessed with the index `0`). 
- Arrays allow constant time access to elements.

// TODO: Elaborate

#### Types
##### One dimensional arrays (1D)
Consists of a single row of elements.
Example: Shopping list
##### Two dimensional arrays (2D)
Is an array of arrays. Consists of rows and columns. It is a grid-like structure. 
Example: Pixels map of an image
##### Multi-dimensional arrays
Arrays can have more than two dimensions. For example, a three-dimensional array is an array of arrays of arrays. 


#### Operations
1. Accessing Elements
2. Insertion
3. Deletion
4. Searching

// TODO: Elaborate

### Linked List

A linked list looks like a chain of nodes. Each node has a value and a reference to the next node in the linked list. 

Difference from arrays: Elements are not stored at a contiguous location in memory. 

#### Features
- Node
    Contains two components:
    - Data
    - Next pointer
- Head
    The first node of a linked list and the starting point for traversing the list.
- Tail
    The last node of a linked list. 

#### Types
- Singly Linked List
    Every node stores the reference to the next node. This allows easy traversal in one direction (towards the tail). The last (tail) element has a reference to NULL because there is no next node after that. 
- Doubly linked list
    Every node stores the reference to the next and previous node. This allows easy traversal in both directions. 
- Circular linked list
    A linked list where the first (head) and last (tail) nodes are linked to each other to form a circle.

#### Operations
- Accessing Elements
- Searching
- Insertion
- Deletion
// TODO: ELABORATE!


### Stack

A stack is a linear data structure that follows the Last-In, First-Out (LIFO) principle. The most recently added element is the first one to be removed.

#### Operations
- push()
- pop()
- top() or peek()
- size()
- isEmpty()
// TODO: Elaborate

A stack can be implemented with an array or a linked list. Stacks are useful when the most recently added item must be handled first, such as in undo history or function calls.

### Queue

A queue is a linear data structure that follows the First-In, First-Out (FIFO) principle. The first element added is the first one to be removed.

#### Operations
- enqueue()
- dequeue()
- front() or peek()
- rear()
- size()
- isEmpty()
// TODO: Elaborate

 A queue can be implemented using an array or a linked list.

#### Types
- Circular queue
- Double-ended queue (deque)
- Priority queue
- Input-restricted deque
- Output-restricted deque
// TODO: Elaborate

