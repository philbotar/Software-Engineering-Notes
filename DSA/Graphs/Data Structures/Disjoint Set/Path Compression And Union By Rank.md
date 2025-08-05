# Optimized Disjoint Set With Path Compression and Union By Rank

```
class UnionFind {
    private int[] root;
    // Use a rank array to record the height of each vertex, i.e., the "rank" of each vertex.
    private int[] rank;

    public UnionFind(int size) {
        root = new int[size];
        rank = new int[size];
        for (int i = 0; i < size; i++) {
            root[i] = i;
            rank[i] = 1; // The initial "rank" of each vertex is 1, because each of them is
                         // a standalone vertex with no connection to other vertices.
        }
    }

	// The find function here is the same as that in the disjoint set with path compression.
    public int find(int x) {
        if (x == root[x]) {
            return x;
        }
	// Some ranks may become obsolete so they are not updated
        return root[x] = find(root[x]);
    }

	// The union function with union by rank
    public void union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);
        if (rootX != rootY) {
            if (rank[rootX] > rank[rootY]) {
                root[rootY] = rootX;
            } else if (rank[rootX] < rank[rootY]) {
                root[rootX] = rootY;
            } else {
                root[rootY] = rootX;
                rank[rootX] += 1;
            }
        }
    }

    public boolean connected(int x, int y) {
        return find(x) == find(y);
    }
}
```


|                 | Union-Find Constructor | Find                 | Union                | Connected            |
| ----------------- | ------------------------ | :--------------------- | ---------------------- | ---------------------- |
| Time Complexity | O(N)                   | **O**(****α****(N)) | **O**(****α****(N)) | **O**(****α****(N)) |

* **Inverse Ackermann function α(N)** grows so slowly that for any realistic N (even N = billions), α(N) ≤ 5.
* For DSU with **union-by-rank + path compression** :

  * **find:** `O(α(N))` ≈ `O(1)` in practice
  * **union:** `O(α(N))` (because it calls `find` twice)
  * **connected:** `O(α(N))` (because it calls `find` twice)
* Memory: `O(N)` (two arrays: `root[]` and `rank[]`)
