# Graphs

References: https://leetcode.com/explore/learn/card/graph/

## What are graphs as a data structure?

Graphs are data structures that store data at 'vertices', and relationships between nodes are stored as 'edges'. Nodes are arbitrary and can have any type of information, and edges can either just represent a link or have information against them, and can be directed or undirected.

### How are they Denoted?

Graphs are denoted as G(V, E), where V is the set of vertices, and E is the set of edges connecting pairs of vertices.

### Important Terminology

1. Adjacent Nodes: Nodes that are directly connected.
2. Digraph: Short for directed graph. Edges represent one way relationships between nodes
3. Loop in a graph: An edge that connects a vertex to itself.
4. Degree Of a node: The number of edges connected to that node.
5. In-degree: Number of nodes coming in
6. Out-degree: Number of nodes going out.
7. Weighted Graph: Each edge in a graph has an associated weight. The weight can be any metric.
8. Path: Sequence of vertices to go through from one vertex to another
9. Cycle: a path that starts and ends at the same vertex.
10. Negative Weight Cycle: In a weighted graph, if the sum of the weights of all edges of a cycle is a negative value, it is a negative weight cycle.
11. Connectivity: If there exists at least one path between two vertices, they are connected.
