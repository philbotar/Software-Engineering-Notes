# Path Compression Optimisation

- Path compression in disjoint sets flattens the tree by making each visited node during a `find` operation point directly to the root, drastically reducing future lookup times.
- So as you can see below, for each find, we are setting the root to the ultimate parent of that value.
  - In our find function, we are saying that for the value of x, until we get to the root node, we must assign that values parent to the value of find(root[x]).

1. Base Case:
   - if (x == root[x]) -> Where the root of the value equals the value, that means its the root node.
2. Recursive Search:
   - if x is NOT its own root, we recursively call find(root[x]) to walk up the tree toward the root.
3. Path Compression
   - The find(root[x]) call gives the root of the entire set
   - So now when the calls are returned back down the call stack, it assigns root[x] to the ultimate root of orginal x.
   - This means next call, its O(1).

```
class UnionFind {
    private int[] root;

    public UnionFind(int size) {
        root = new int[size];
        for (int i = 0; i < size; i++) {
            root[i] = i;
        }
    }

    public int find(int x) {
        if (x == root[x]) {
            return x; // Base case: this is the root of the set
        }
        return root[x] = find(root[x]); // Recursive step + path compression
    }

    public void union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);
        if (rootX != rootY) {
            root[rootY] = rootX;
        }
    }

    public boolean connected(int x, int y) {
        return find(x) == find(y);
    }
}
```
