# Graphs

A graph is made of **vertices** (items) and **edges** (connections between items). A graph can represent a network, such as roads between places or friendships between people. Unlike a tree, it does not have to follow a parent-child hierarchy.

An **undirected graph** has connections that can be followed in either direction. A **directed graph** has connections with a direction.

## Example: a small friendship network

Each person below is a vertex, and each line is an undirected edge. This example is made for these notes.

```text
Nicky --- Darren
  |          |
 Neil ---  Glen
```

Starting at Nicky, we can follow an edge to Darren or Neil. From Neil, we can reach Glen. When traversing a graph, keeping track of visited vertices helps avoid visiting the same person repeatedly.

## Graph traversal

A graph traversal visits vertices by following edges. **Depth-first search** follows one route as far as it can before returning to earlier vertices. **Breadth-first search** visits nearby vertices first, then moves to vertices farther away. Both methods can use a record of visited vertices to avoid revisiting them.
