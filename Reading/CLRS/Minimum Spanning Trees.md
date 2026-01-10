- Minimum Spanning Trees are "trees" that have the shortest path between all points on the tree. We join all points in a graph so they are connected at the shortest possible cost. 
- Minimum Spanning Trees were renamed from Minimum Weight Spanning Trees. 
- Prims algorithm and Kruskals algorithm are for solving the Minimum Spanning Trees problem. 
	- Both run in O(E log V) time. Prims uses a binary heap as a Priority Queue.
	- Both Algorithms are greedy.
- The input to a MST problem is a connected, undirected graph, G = (V,E) with a weight function w : E -> R (Real Numbers).
- The cut property of Minimum Spanning Trees (MSTs) states that for any way you divide a graph's vertices into two separate groups (a "cut"), the lightest-weight edge that crosses from one group to the other (a "crossing edge") must be part of any MST

### Prims Algorithm
