# Arrays

An array stores elements in an ordered sequence. In many programming languages, an array holds values of one data type. Some languages and array-like structures allow different types.

## Characteristics

- Elements are identified by an index, a number that marks a position in the sequence. In many languages, indexes start at `0`.
- Array elements are commonly stored in adjacent memory locations, meaning one after another in memory.
- An element can be accessed directly by its index. This is constant time, written `O(1)`: the access takes about the same time as the array grows.
- Some arrays have a fixed size. Dynamic arrays can grow, although growing them may require allocating a larger block and copying elements.

## Types

### One-dimensional arrays (1D)

A one-dimensional array is a single sequence of elements.

Example: daily temperatures for one week.

### Two-dimensional arrays (2D)

A two-dimensional array arranges elements in rows and columns. In some languages it is represented as an array of arrays.

Example: a grid of pixels in an image.

### Multidimensional arrays

An array can have more than two dimensions. A three-dimensional array, for example, can represent a set of 2D grids.

## Operations

- **Access:** Use an index to get an element. For example, in an array containing 72, 85, and 91, the item at index 1 is 85 when indexing starts at zero.
- **Update:** Replace the value at an index, such as changing the second score to 88.
- **Search:** Linear search checks elements one by one and may inspect all `n` elements, so it takes `O(n)` time in the worst case. Binary search repeatedly halves the sorted search range. It takes `O(log n)` time, meaning the number of checks grows with how many times the range can be halved.
- **Insertion:** Add an element at a position. Elements after that position may need to shift right.
- **Deletion:** Remove an element. Later elements may need to shift left to fill the gap.

Arrays are useful when the number of items is known or when items need frequent index-based access. They are less convenient when many insertions and deletions happen in the middle.

## Example: finding a score

An array stores the scores 72, 85, and 91 in that order. To find 91 with a linear search, first compare 72, then 85, then 91. The third comparison finds the score. If there are `n` scores and the target is last or absent, the search may check all `n` items, so its time grows as `O(n)`.

## Try it

A list stores the numbers 4, 7, and 9 in that order. Use a linear search to find 7. How many values do you check? What changes if the target is 9?

**Expected answer:** Finding 7 takes two checks: 4, then 7. Finding 9 takes three checks: 4, 7, then 9. The search stops as soon as it finds the target.
