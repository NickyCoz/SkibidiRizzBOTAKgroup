# Queue

A queue is a linear data structure that usually follows the First-In, First-Out (FIFO) rule. The first item added is the first one removed. A line of people waiting for a bus is a familiar example.

## Operations

- **Enqueue:** Add an item at the rear.
- **Dequeue:** Remove and return the item at the front. The queue must not be empty.
- **Front or peek:** Read the front item without removing it.
- **Rear:** Read the last item without removing it.
- **Size:** Return the number of items.
- **Is empty:** Check whether there are no items.


## Types

- **Circular queue:** Treats the ends of an array as connected, so positions freed at the front can be reused.
- **Double-ended queue (deque):** Allows items to be added or removed at either end.
- **Input-restricted deque:** Allows insertion at one end, but deletion at either end.
- **Output-restricted deque:** Allows deletion at one end, but insertion at either end.
- **Priority queue:** Removes the item with the highest (or lowest) priority first. Items with equal priority are often handled in arrival order, depending on the implementation. It is not necessarily FIFO overall.

Queues are useful for tasks such as handling print jobs, processing requests in arrival order, and keeping track of work waiting to be done.
