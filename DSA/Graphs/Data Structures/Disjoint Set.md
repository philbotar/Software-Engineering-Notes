# Disjoint Set

References: [Google](https://www.google.com/search?q=what+is+a+disjoint+set+in+cs&sca_esv=bd8b7c99eeaeb415&sxsrf=AE3TifNxtBb74tuUfOa3fd4pB8waOEMTyQ%3A1753534981695&ei=BdKEaKWcKo-JqfkP_oj4wAk&ved=0ahUKEwjlsr3tytqOAxWPRCoJHX4EHpgQ4dUDCBA&uact=5&oq=what+is+a+disjoint+set+in+cs&gs_lp=Egxnd3Mtd2l6LXNlcnAiHHdoYXQgaXMgYSBkaXNqb2ludCBzZXQgaW4gY3MyBRAAGIAEMgYQABgWGB4yBhAAGBYYHjIGEAAYFhgeMgYQABgWGB4yBhAAGBYYHjIGEAAYFhgeMgYQABgWGB4yBhAAGBYYHjIGEAAYFhgeSKYKUJgEWNIJcAJ4AZABAJgBmQKgAZYHqgEFMC4xLjO4AQPIAQD4AQGYAgagAqcHwgIKEAAYsAMY1gQYR8ICDRAAGIAEGLADGEMYigWYAwCIBgGQBgqSBwUyLjEuM6AH1BqyBwUwLjEuM7gHogfCBwUwLjUuMcgHDg&sclient=gws-wiz-serphttps:/) , [Leetcode](https://leetcode.com/explore/learn/card/graph/618/disjoint-set/3881/https:/)

## What is a Disjoint Set?

A disjoint set is a data structure (Also known as as the union find data structure), that is used to store a collection of sets where each element belongs to exactly one set, and not two sets overlap.

## What are the 3 main operations

1. makeSet(x): Makes a new set with one element x.
2. find(x): Returns the representative (root) of the set that contains x
3. union(x,y): Merges the sets that contain x and y into a single set

## How do disjoint sets work?

#### 1. Initialization: Make Set(x)

- For every element x, create a set where x is its own parent.
- Think of the dictionary like: { x: x } for each.
- Each Element points to itself initially, forming n seperate singleton sets.

#### 2. Find with Path Compression: find(x)

- Recursively follow parent pointers until you reach the root (i.e. parent[x] == x).
- Then, Update all nodes along the path to directly point to the root
- This flattens the structure, making subsequent find calls faster.

```
def find(x):
     if parent[x] != x:
          parent[x] = find(parent[x])
     return parent[x]

```

#### 3. Union by Rank: union(x, y)

- Combines the sets that contain x and y
- To keep the tree shallow, we always attach the smaller tree to the larger one.

How it works

- Get the roots of both sets (find(x))
- Compare their ranks (approx. height of the tree)

  - Attach the smaller tree under the larger one.
  - If equal, pick one and increment its rank.

```
def union(x, y):
    rootX = find(x)
    rootY = find(y)

    if rootX == rootY:
        return  # already in the same set

    if rank[rootX] < rank[rootY]:
        parent[rootX] = rootY
    elif rank[rootX] > rank[rootY]:
        parent[rootY] = rootX
    else:
        parent[rootY] = rootX
        rank[rootX] += 1

```

## Terminologies

- Parent Node: The direct parent of a vertex
- Root Node: A node without a parent node. It can be viewed as a parent node of itself.
