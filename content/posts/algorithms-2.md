---
title: Algorithm II
date: 2020-07-07
categories:
- Study
tags:
- CS
- Algorithm
- Open Courses
---


Review of Princeton [Algorithms II](https://www.coursera.org/learn/algorithms-part2) course on Coursera.

[Algorithms I Review]({{< ref "algorithms-1" >}})

My [solution](https://github.com/silencial/Algorithms) to the homework

<!--more-->

---

# Course Overview

|   topic    |           data structures and algorithms           |
| :--------: | :------------------------------------------------: |
| data types |   stack, queue, bag, union-find, priority queue    |
|  sorting   |           quicksort, mergesort, heapsort           |
| searching  |           BST, red-black BST, hash table           |
|   graphs   |         BFS, DFS, Prim, Kruskal, Dijkstra          |
|  strings   | radix sorts, tries, KMP, regexps, data compression |
|  advanced  |           B-tree, suffix array, maxflow            |

# Undirected Graphs

Terminology:

- **Path**: sequence of vertices connected by edges
- **Cycle**: path whose first and last vertices are the same

![Undirected Graphs](https://i.imgur.com/QaMFLaK.png)

Some graph problems:

- **Path**: Is there a path between $s$ and $t$
- **Shortest path**: What is the shortest path between $s$ and $t$
- **Cycle**: Is there a cycle in the graph
- **Euler tour**: Is there a cycle that uses each edge exactly once
- **Hamilton tour**: Is there a cycle that uses each vertex exactly once.
- **Connectivity**: Is there a way to connect all of the vertices?
- **MST**: What is the best way to connect all of the vertices?
- **Biconnectivity**: Is there a vertex whose removal disconnects the graph?
- **Planarity**: Can you draw the graph in the plane with no crossing edges
- **Graph isomorphism**: Do two adjacency lists represent the same graph?

## API

```java
public class Graph {
    Graph(int V);  // create an emtpy graph with V vertices
    Graph(In in);  // create a graph from input stream
    void addEdge(int v, int w);  // add an edge v-w
    Iterable<Integer> adj(int v);  // vertices adjacent to v
    int V();  // number of vertices
    int E();  //number of edges
    String toString();  // string representation
}
```

For vertices, convert them between names and integers with symbol table

For edges, we have 3 choices:

1. Maintain a list of the edges
2. Maintain a 2-D $V$-by-$V$ boolean matrix
3. Maintain a vertex-indexed array of lists

Comparison:

|  Representation  | Space | Add edge | Edge between v and w | Iterate over vertices adjacent to v |
| :--------------: | :---: | :------: | :------------------: | :---------------------------------: |
|  list of edges   |   E   |    1     |          E           |                  E                  |
| adjacency matrix | V^2^  |    1     |          1           |                  V                  |
| adjacency lists  | E + V |    1     |      degree(v)       |              degree(v)              |

In practice, use adjacency lists because

- Algorithms based on iterating over vertices adjacent to $v$
- Real-world graphs tend to be **sparse**

```java
public class Graph {
    private final int V;
    private Bag<Integer>[] adj;  // order doesn't matter

    public Graph(int V) {
        this.V = V;
        adj = (Bag<Integer>[]) new Bag[V];
        for (int v = 0; v < V; v++)
            adj[v] = new Bag<Integer>();
    }

    public void addEdge(int v, int w) {
        adj[v].add(w);
        adj[w].add(v);
    }

    public Iterable<Integer> adj(int v)
    { return adj[v]; }
}
```

## Depth-First Search

1. Mark vertex $v$ as visited
2. **Recursively** visit all unmarked vertices adjacent to $v$

```java
public class DepthFirstPaths {
    private boolean[] marked;
    private int[] edgeTo;
    private int s;

    public DepthFirstPaths(Graph G, int s) {
        ...
        dfs(G, s);
    }

    private void dfs(Graph G, int v) {
        marked[v] = true;
        for (int w : G.adj(v)) {
            if (!marked[w]) {
                dfs(G, w);
                edgeTo[w] = v;
            }
        }
    }
}
```

After DFS, can find vertices connected to $s$ in constant time (all true vertices in `marked`) and can find a path to $s$ in time proportional to its length (`edgeTo` is a parent-link representation of a tree rooted at $s$).

## Breadth-First Search

1. Remove vertex $v$ from queue
2. Add to queue all unmarked vertices adjacent to $v$ and mark them

```java
public class BreadthFirstPaths {
    private boolean[] marked;
    private int[] edgeTo;

    private void bfs(Graph G, int s) {
        Queue<Integer> q = new Queue<Integer>();
        q.enqueue(s);
        marked[s] = true;
        while (!q.isEmpty()) {
            int v = q.dequeue();
            for (int w : G.adj(v)) {
                if (!marked[w]) {
                    q.enqueue(w);
                    marked[w] = true;
                    edgeTo[w] = v;
                }
            }
        }
    }
}
```

BFS examines vertices in increasing distance from $s$. It can be used to find the shortest path.

## Connected Components

**Definition**:

- Vertices $v$ and $w$ are connected if there is a path between them
- A connected component is a maximal set of connected vertices

Use DFS to partition vertices into connected components, then can answer whether $v$ is connected to $w$ in **constant** time

```java
public class CC {
    private boolean[] marked;
    private int[] id;  // id of component containing v
    private int count;  // number of components

    public CC(Graph G) {
        marked = new boolean[G.V()];
        id = new int[G.V()];
        for (int v = 0; v < G.V(); v++) {
            if (!marked[v]) {
                dfs(G, v);
                count++;  // next component
            }
        }
    }

    boolean connected(int v, int w)
    { return id[v] == id[w]; }

    public int count()
    { return count; }

    public int id(int v)
    { return id[v]; }

    private void dfs(Graph G, int v) {
        marked[v] = true;
        id[v] = count;  // all vertices discovered in same call of dfs have same id
        for (int w : G.adj(v))
            if (!marked[w]) {
                dfs(G, w);
                edgeTo[w] = v;
            }
    }
}
```

# Directed Graphs

![Directed Graphs](https://i.imgur.com/1z0ur3o.png)

## API

Almost same as `Graph`:

```java
public class DiGraph {
    DiGraph(int V);  // create an emtpy digraph with V vertices
    DiGraph(In in);  // create a digraph from input stream
    void addEdge(int v, int w);  // add an edge v->w
    Iterable<Integer> adj(int v);  // vertices pointing from v
    int V();  // number of vertices
    int E();  //number of edges
    Digraph reverse();  // reverse of this digraph
    String toString();  // string representation
}
```

## Digraph Search

Both DFS and BFS are digraph algorithms, code is identical to undirected graphs.

**DFS search applications**:

- Every program is a digraph. Vertex=basic block of instructions; edge=jump
  - Dead-code elimination: find and move unreachable code
  - Infinite-loop detection: whether exit is unreachable
- Every data structure is a digraph. Vertex=object; edge=reference
  - Mark-sweep algorithm: collect unreachable objects as garbage

**BFS search applications**:

- Multiple-source shortest paths: initialize by enqueuing all source vertices
- Web crawler

## Topological Sort

**Precedence scheduling**: Given a set of tasks to be completed with precedence constraints, in which order should we schedule the tasks?

1. Represent data as digraph: vertex=task; edge=precedence constraint
2. Represent the problem as topological sort: redraw directed acyclic graph (**DAG**) so all edges point upwards

Run DFS and return vertices in reverse postorder.

## Strong Components

**Definition**:

- Vertices $v$ and $w$ are **strongly connected** if there is both a directed path from $v$ to $w$ and a directed path from $w$ to $v$
- A Strong component is a maximal subset of strongly-connected vertices

**Applications**:

- Food Web. Vertex=species; edge=from producer to consumer; strong component=subset of species with common energy flow
- Software module dependency graph. Vertex=software module; edge=from module to dependency; strong component=subset of mutually interacting modules

**Kosaraju-Sharir algorithm**:

1. Compute topological order (reverse postorder) in **Reverse** Graph $G^R$
2. Run DFS in $G$, visiting unmarked vertices in the order computed above

```java
public class KosarajuSharirSCC {
    private boolean marked[];
    private int[] id;
    private int count;

    public KosarajuSharirSCC(Digraph G) {
        marked = new boolean[G.V()];
        id = new int[G.V()];
        DepthFirstOrder dfs = new DepthFirstOrder(G.reverse());
        for (int v : dfs.reversePost()) {
            if (!marked[v]) {
                dfs(G, v);
                count++;
            }
        }
    }

    private void dfs(Digraph G, int v) {
        marked[v] = true;
        id[v] = count;
        for (int w : G.adj(v))
            if (!marked[w])
                dfs(G, w);
    }

    public boolean stronglyConnected(int v, int w)
    { return id[v] == id[w]; }
}
```

## HW6 Wordnet

[Specification](https://coursera.cs.princeton.edu/algs4/assignments/wordnet/specification.php)

- **WordNet digraph**: build digraph from input files. [WordNet.java](https://github.com/silencial/Algorithms/blob/master/06_WordNet/WordNet.java)
  - `synsets.txt` saves all the id and corresponding words <mark>(one id can have one or more words, and one word can have one or more ids)</mark>
  - `hypernyms.txt` contains hypernym relationships
- **Shortest ancestral path**: given two vertices, find the directed paths to their common ancestor with the max total length. [SAP.java](https://github.com/silencial/Algorithms/blob/master/06_WordNet/SAP.java)
- **Outcast detection**: given a list of WordNet nouns, find the noun least related to the others. This can be measured by the sum of SAP distances to all the other vertices. [Outcast.java](https://github.com/silencial/Algorithms/blob/master/06_WordNet/Outcast.java)

# Minimum Spanning Trees

**Definition**: A spanning tree of $G$ is a subgraph $T$ that is both a tree (connected and acyclic) and spanning (includes all of the vertices)

**Problem**: Given undirected graph $G$ with positive edge weights, find the min weight spanning tree.

## Edge-Weighted Graph API

Edge abstraction needed for weighted edges.

```java
public class Edge implements Comparable<Edge> {
    private final int v, w;
    private final double weight;

    Edge(int v, int w, double weight) {
        this.v = v;
        this.w = w;
        this.weight = weight;
    }

    int either()
    { return v; }

    int other(int vertex) {
        if (vertex == v) return w;
        else return v;
    }

    int compareTo(Edge that) {
        if (this.weight < that.weight) return -1;
        else if (this.weight > that.weight) return 1;
        else return 0;
    }

    double weight()
    { return weight; }
}
```

Edge-weighted graph representation:

```java
public class EdgeWeightedGraph {
    private final int V;
    private final Bag<Edge>[] adj;

    public EdgeWeightedGraph(int V) {
        this.V = V;
        adj = (Bag<Edge>[]) new Bag[V];
        for (int v = 0; v < V; v++)
            adj[v] = new Bag<Edge>();
    }

    public void addEdge(Edge e) {
        int v = e.either(), w = e.other(v);
        adj[v].add(e);
        adj[w].add(e);
    }

    public Iterable<Edge> adj(int v)
    { return adj[v]; }
}
```

## Greedy Algorithm

**Simplifications**:

- Edge weights are distinct
- Graph is connected

Then MST exists and is unique.

**Definitions**:

- A **cut** in a graph is a partition of its vertices into two (nonempty) sets
- A **crossing edge** connects a vertex in one set with a vertex in the other.

Given any cut, the crossing edge of min weight is in the MST.

**Algorithm**:

1. Start with all edges colored white
2. Find cut with no black crossing edges; color its min-weight edge black
3. Repeat until $V-1$ edges are colored black

**Remove simplifying assumptions**:

- Greedy MST still correct if equal weights are present
- Compute MST of each component if graph is not connected

**Efficient implementations**: How to choose cut? How to find min-weight edge?

## Kruskal's Algorithm

1. Consider edges in ascending order of weight
2. Add next edge to tree $T$ unless doing so would create a cycle

The challenge is how to examine adding edge $v$-$w$ to tree $T$ create a cycle. Use **union-find**:

- Maintain a set for each connected component in $T$
- If $v$ and $w$ are in same set, then adding $v$-$w$ would create a cycle
- To add $v$-$w$ to $T$, merge sets containing $v$ and $w$

```java
public class KruskalMST {
    private Queue<Edge> mst = new Queue<Edge>();

    public KruskalMST(EdgeWeightedGraph G) {
        MinPQ<Edge> pq = new MinPQ<Edge>();

        for (Edge e : G.edges())
            pq.insert(e);

        UF uf = new UF(G.V());
        while (!pq.isEmpty() && mst.size() < G.V()-1) {
            Edge e = pq.delMin();
            int v = e.either(), w = e.other(v);
            if (!uf.connected(v, w)) {
                uf.union(v, w);
                mst.enqueue(e);
            }
        }
    }

    public Iterable<Edge> edges()
    { return mst; }
}
```

## Prim's Algorithm

1. Start with vertex $0$ and greedily grow tree $T$
2. Add to $T$ the min weight edge with exactly one endpoint in $T$
3. Repeat until $V-1$ edges

### Lazy Implementation

Maintain a PQ of **edges** with (at least) one endpoint in $T$, where the priority is the weight of edge

1. Delete-min to determine next edge $e=v\text{-}w$ to add to $T$
2. Disregard if both endpoints $v$ and $w$ are marked (both in $T$)
3. Otherwise, let $w$ be the unmarked vertex (not in $T$)
   - add to PQ any edge incident to $w$
   - add $e$ to $T$ and mark $w$

```java
public class LazyPrimMST {
    private boolean[] marked; // MST vertices
    private Queue<Edge> mst; // MST edges
    private MinPQ<Edge> pq; // PQ of edges

    public LazyPrimMST(WeightedGraph G) {
        pq = new MinPQ<Edge>();
        mst = new Queue<Edge>();
        marked = new boolean[G.V()];
        visit(G, 0);

        while (!pq.isEmpty() && mst.size() < G.V() - 1) {
            Edge e = pq.delMin();
            int v = e.either(), w = e.other(v);
            if (marked[v] && marked[w]) continue;
            mst.enqueue(e);
            if (!marked[v]) visit(G, v);
            if (!marked[w]) visit(G, w);
        }
    }

    private void visit(WeightedGraph G, int v) {
        marked[v] = true;
        for (Edge e : G.adj(v))
            if (!marked[e.other(v)])
                pq.insert(e);
    }

        public Iterable<Edge> mst()
        { return mst; }
}

```

### Eager Implementation

Maintain a PQ of **vertices** connected by an edge to $T$, where priority of vertex $v$ = the weight of shortest edge connecting to $T$

1. Delete min vertex $v$ and add its associated edge $v$-$w$ to $T$
2. Update PQ by considering all edges $v$-$x$ incident to $v$
   - ignore if $x$ is already in $T$
   - add $x$ to PQ if not already in it
   - update priority of $x$ if $v$-$x$ becomes shortest edge connecting $x$ to $T$

```java
public class PrimMST {
    private Edge[] edgeTo;  // edgeTo[v] = shortest edge from tree vertex to non-tree vertex
    private double[] distTo;  // distTo[v] = weight of shortest such edge
    private boolean[] marked;  // marked[v] = true if v on tree, false otherwise
    private IndexMinPQ<Double> pq;

    public PrimMST(WeightedGraph G) {
        edgeTo = new Edge[G.V()];
        distTo = new double[G.V()];
        marked = new boolean[G.V()];
        pq = new IndexMinPQ<Double>(G.V());
        for (int v = 0; v < G.V(); v++)
            distTo[v] = Double.POSITIVE_INFINITY;

        for (int v = 0; v < G.V(); v++)  // run from each vertex to find
            if (!marked[v]) prim(G, v);  // minimum spanning forest
    }

    private void prim(EdgeWeightedGraph G, int s) {
        distTo[s] = 0.0;
        pq.insert(s, distTo[s]);
        while (!pq.isEmpty()) {
            int v = pq.delMin();
            scan(G, v);
        }
    }

    private void scan(EdgeWeightedGraph G, int v) {
        marked[v] = true;
        for (Edge e : G.adj(v)) {
            int w = e.other(v);
            if (marked[w]) continue;
            if (e.weight() < distTo[w]) {
                distTo[w] = e.weight();
                edgeTo[w] = e;
                if (pq.contains(w)) pq.decreaseKey(w, distTo[w]);
                else                pq.insert(w, distTo[w]);
            }
        }
    }

    public Iterable<Edge> edges() {
        Queue<Edge> mst = new Queue<Edge>();
        for (int v = 0; v < edgeTo.length; v++) {
            Edge e = edgeTo[v];
            if (e != null)
                mst.enqueue(e);
        }
        return mst;
    }
}
```

And we need a `IndexMinPQ` data structure

```java
public class IndexMinPQ<Key extends Comparable<Key>> {
    private int maxN;  // maximum number of elements on PQ
    private int n;  // number of elements on PQ
    private int[] pq;  // binary heap using 1-based indexing
    private int[] qp;  // inverse of pq - qp[pq[i]] = pq[qp[i]] = i
    private Key[] keys;  // keys[i] = priority of i

    IndexMinPQ(int N);  // create indexed priority queue with indices 0, 1, …, N-1
    void insert(int i, Key key);  // associate key with index i
    void decreaseKey(int i, Key key);  // decrease the key associated with index i, use swim(qp[i])
    boolean contains(int i);  // is i an index on the priority queue?
    int delMin();  // remove a minimal key and return its associated index
    boolean isEmpty();  // is the priority queue empty?
    int size();  // number of entries in the priority queu
}
```

# Shortest Paths

Given an edge-weighted digraph, find the shortest path from $s$ to $t$.

|         Algorithm          |     Restriction     | Typical case | Worst case | Extra space |
| :------------------------: | :-----------------: | :----------: | :--------: | :---------: |
|      Topological sort      | no directed cycles  |    E + V     |   E + V    |      V      |
|   Dijkstra (binary heap)   | no negative weights |   E log V    |  E log V   |      V      |
|        Bellman-Ford        | no negative cycles  |     E V      |    E V     |      V      |
| Bellman-Ford (queue-based) | no negative cycles  |    E + V     |    E V     |      V      |

## API

**Weighted directed edge** API:

```java
public class DirectedEdge {
    private final int v, w;
    private final double weight;

    // weighted edge v→w
    public DirectedEdge(int v, int w, double weight) {
        this.v = v;
        this.w = w;
        this.weight = weight;
    }

    int from()
    { return v; }

    int to()
    { return w; }

    double weight()
    { return weight; }

    String toString();  // string representation
}
```

 The **Edge-weighted digraph** API is the same as `EdgeWeightedGraph`.

**Single-source shortest paths** API:

```java
public class SP {
    SP(EdgeWeightedDigraph G, int s);  // shortest paths from s in graph G
    double distTo(int v);  // length of shortest path from s to v
    Iterable<DirectedEdge> pathTo(int v);  // shortest path from s to v
}
```

## Edge Relaxation

Relax edge $e=v \to w$

- `distTo[v]` is length of shortest known path from $s$ to $v$
- `distTo[w]` is length of shortest known path from $s$ to $w$
- `edgeTo[w]` is last edge on shortest known path from $s$ to $w$
- If $e = v \to w$ gives shorter path to $w$ through $v$, update both `distTo[w]` and `edgeTo[w]`

Different ways to choose which edge to relax:

- Dijkstra's algorithm (nonnegative weights)
- Topological sort algorithm (no directed cycles)
- Bellman-Ford algorithm (no negative cycles)

## Dijkstra's Algorithm

1. Consider vertices in increasing order of distance from $s$
2. Add vertex to tree and relax all edges pointing from that vertex

```java
public class DijkstraSP {
    private DirectedEdge[] edgeTo;
    private double[] distTo;
    private IndexMinPQ<Double> pq;

    public DijkstraSP(EdgeWeightedDigraph G, int s) {
        edgeTo = new DirectedEdge[G.V()];
        distTo = new double[G.V()];
        pq = new IndexMinPQ<Double>(G.V());

        for (int v = 0; v < G.V(); v++)
            distTo[v] = Double.POSITIVE_INFINITY;
        distTo[s] = 0.0;

        pq.insert(s, 0.0);
        while (!pq.isEmpty()) {
            int v = pq.delMin();
            for (DirectedEdge e : G.adj(v))
                relax(e);
        }
    }

    private void relax(DirectedEdge e) {
        int v = e.from(), w = e.to();
        if (distTo[w] > distTo[v] + e.weight()) {
            distTo[w] = distTo[v] + e.weight();
            edgeTo[w] = e;
            if (pq.contains(w)) pq.decreaseKey(w, distTo[w]);
            else pq.insert(w, distTo[w]);
        }
    }
}
```

Prim's algorithm is essentially the same algorithm, distinct in the way to choose next vertex for the tree:

- Prim's algorithm choose the closest vertex to the **tree** (via undirected edge)
- Dijkstra's algorithm choose the closest vertex to the **source** (via a directed path)

## Topological Sort

Suppose the edge-weighted digraph has no directed cycles

1. Consider vertices in topological order
2. Relax all edges pointing from that vertex

It is more efficient to get the SPT. Time is proportional to $E + V$

```java
public class AcyclicSP {
    private DirectedEdge[] edgeTo;
    private double[] distTo;

    public AcyclicSP(EdgeWeightedDigraph G, int s) {
        edgeTo = new DirectedEdge[G.V()];
        distTo = new double[G.V()];

        for (int v = 0; v < G.V(); v++)
            distTo[v] = Double.POSITIVE_INFINITY;
        distTo[s] = 0.0;

        Topological topological = new Topological(G);
        for (int v : topological.order())
            for (DirectedEdge e : G.adj(v))
                relax(e);
        }
    }
```

**Applications**:

- Seam carving: Resize an image without distortion
  - Grid DAG: vertex = pixel; edge = from pixel to 3 downward neighbors
  - Weight of pixel = energy function of 8 neighboring pixels
  - Seam = shortest path (sum of vertex weights) from top to bottom
- Find longest paths in edge-weighted DAGs
  - Topological sort algorithm works with negative weights.
  - Negate all the weights, find shortest path, negate weights in result.
- Parallel job scheduling: Given a set of jobs with durations and precedence constraints, schedule the jobs (by finding a start time for each) so as to achieve the minimum completion time.
  - Source and sink vertices. Two vertices for each job (begin and end).
  - Three edges for each job. Begin to end (weighted by duration); source to begin (0 weight); end to sink (0 weight)
  - One edge for each precedence constraint (0 weight)
  - Use **longest path** from the source to schedule each job

## Bellman-Ford

Dijkstra doesn't work with **negative** edge weights.

A SPT exists iff no negative cycles, A **negative cycle** is a directed cycle whose sum of edge weights is negative.

**Bellman-Ford algorithm**:

```java
for (int i = 0; i < G.V(); i++)
    for (int v = 0; v < G.V(); v++)
        for (DirectedEdge e : G.adj(v))
            relax(e);
```

**Dynamic programming** algorithm can be used to compute SPT in any edge-weighted graph with no negative cycles in time proportional to $E \times V$:

- If `distTo[v]` does not change during pass $i$, no need to relax any edge pointing from $v$ in pass $i+1$
- Maintain a queue of vertices whose `distTo[]` changed

Negative cycle can be found by Bellman-Ford algorithm too: If any vertex $v$ is updated in phase $V$, there exists a negative cycle.

**Negative cycle application**: Arbitrage detection. Given table of exchange rates, is there an arbitrage opportunity?

- Vertex = currency; edge = transaction; weight = exchange rate
- Find a directed cycle whose product of edge weights > 1
- Take $-\ln$ for the weights so that multiplication > 1 turns to addition < 0
- Equivalent to find a negative cycle

## HW7 Seam Carving

[Specification](https://coursera.cs.princeton.edu/algs4/assignments/seam/specification.php)

Seam Carving is a content-aware image resizing technique. Remove one row/column every time.

1. Design energy function for pixels
2. Find seam with minimal energy with one pixel every row/column, and every two adjacent seam points differ at most 1 column/row
3. Delete seam

For this problem, dynamic programming is equivalent to treat the image as a graph and find the shortest path on it.

1. Treat every pixel as a vertex (column, row) and there are edges connecting from it to vertexes (column-1, row+1), (column, row+1) and (column+1, row+1), unless the vertex is invalid.
2. This graph is a DAG so the shortest path problem can be solved by topological sort. Moreover, since edges are connected layer by layer, we can sort it layer by layer too.
3. To optimize the algorithm, energy can be precomputed and stored. When removing seams, only points near the removed location should be updated.
4. Find/remove horizontal and vertical seam are equivalent, except for a transpose.

**Solution**: [SeamCarver.java](https://github.com/silencial/Algorithms/blob/master/07_Seam_Carving/SeamCarver.java)

# Maximum Flow

**Definition**:

- A **$st$-cut** is a partition of the vertices into two disjoint sets, with $s$ in one set $A$ and $t$ is the other set $B$
- Its **capacity** is the sum of the capacities of the edges from $A$ to $B$
- A **$st$-flow** is a an assignment of values to the edges such that:
  - Capacity constraints: $0 \le$ edge's flow $\le$ edge's capacity
  - Local equilibrium: inflow = outflow at every vertex (except $s$ and $t$)
- The value of a flow is the inflow at $t$

**Mincut problem**: Find a cut of minimum capacity

**Maxflow problem**: Find a flow of maximum value

## Ford-Fulkerson Algorithm

For digraph with $V$ vertices, $E$ edges and **integer** capacities between $1$ and $U$:

1. Start with $0$ flow
2. Fina an undirected path from $s$ to $t$ such that (**Augmenting Path**)
   - Can increase flow on forward edges (not full)
   - Can decrease flow on backward edge (not empty)
3. Increase flow on that path by bottleneck capacity

FF performance depends on choice of augmenting paths:

| Augmenting path |  Number of paths  |  Implementation  |
| :-------------: | :---------------: | :--------------: |
|  shortest path  |   $\le 1/2 E V$   |   queue (BFS)    |
|  fattest path   | $\le E \ln (E U)$ |  priority queue  |
|   random path   |     $\le E U$     | randomized queue |
|    DFS path     |     $\le E U$     |   stack (DFS)    |

## Maxflow-Mincut Theorem

**Definition**: The **net flow across** a cut $(A, B)$ is the sum of the flows on its edges from $A$ to $B$ minus the sum of the flows on its edges from $B$ to $A$

**Flow-value lemma**: Let $f$ be any flow and let $(A, B)$ be any cut. The net flow across $(A, B)$ equals the value of $f$

**Maxflow-mincut theorem**: Value of the maxflow = capacity of mincut

To compute mincut $(A, B)$ from maxflow $f$: Compute $A$ = set of vertices connected to $s$ by an undirected path with no full forward or empty backward edges.

## Implementation

**Residual network**:

- Use forward edge to represent residual capacity
- Use backward edge to represent flow

Augmenting path in original network is equivalent to directed path in residual network

![Residual Network](https://i.imgur.com/6qlMKiN.png)

**Flow Edge API**:

```java
public class FlowEdge {
    private final int v, w;
    private final double capacity;
    private double flow;

    public FlowEdge(int v, int w, double capacity) {
        this.v = v;
        this.w = w;
        this.capacity = capacity;
    }

    public int from() { return v; }
    public int to() { return w; }
    public double capacity() { return capacity; }
    public double flow() { return flow; }

    public int other(int vertex) {
        if (vertex == v) return w;
        else if (vertex == w) return v;
        else throw new RuntimeException("Illegal endpoint");
    }

    public double residualCapacityTo(int vertex) {
        if (vertex == v) return flow;
        else if (vertex == w) return capacity - flow;
        else throw new IllegalArgumentException();
    }

    public void addResidualFlowTo(int vertex, double delta) {
        if (vertex == v) flow -= delta;
        else if (vertex == w) flow += delta;
        else throw new IllegalArgumentException();
    }
}
```

**Flow Network API**:

```java
public class FlowNetwork {
    private final int V;
    private Bag<FlowEdge>[] adj;

    public FlowNetwork(int V) {
        this.V = V;
        adj = (Bag<FlowEdge>[]) new Bag[V];
        for (int v = 0; v < V; v++)
            adj[v] = new Bag<FlowEdge>();
    }

    public void addEdge(FlowEdge e) {
        int v = e.from();
        int w = e.to();
        adj[v].add(e);
        adj[w].add(e);
    }

    public Iterable<FlowEdge> adj(int v)
    { return adj[v]; }
}
```

**Ford-Fulkerson algorithm**:

```java
public class FordFulkerson {
    private boolean[] marked; // true if s->v path in residual network
    private FlowEdge[] edgeTo; // last edge on s->v path
    private double value; // value of flow

    public FordFulkerson(FlowNetwork G, int s, int t) {
        value = 0.0;
        while (hasAugmentingPath(G, s, t)) {
            double bottle = Double.POSITIVE_INFINITY;
            for (int v = t; v != s; v = edgeTo[v].other(v))
                bottle = Math.min(bottle, edgeTo[v].residualCapacityTo(v));
            for (int v = t; v != s; v = edgeTo[v].other(v))
                edgeTo[v].addResidualFlowTo(v, bottle);
            value += bottle;
        }
    }

    private boolean hasAugmentingPath(FlowNetwork G, int s, int t) {
        edgeTo = new FlowEdge[G.V()];
        marked = new boolean[G.V()];
        Queue<Integer> queue = new Queue<Integer>();
        queue.enqueue(s);
        marked[s] = true;
        while (!queue.isEmpty()) {
            int v = queue.dequeue();
            for (FlowEdge e : G.adj(v)) {
                int w = e.other(v);
                if (e.residualCapacityTo(w) > 0 && !marked[w]) {
                    edgeTo[w] = e;
                    marked[w] = true;
                    queue.enqueue(w);
                }
            }
        }
        return marked[t];
    }

    public double value()
    { return value; }

    public boolean inCut(int v)
    { return marked[v]; }
}
```

## Applications

### Bipartite Matching Problem

$N$ students apply for $N$ jobs, each gets several offers. Is there a way to match all students to jobs?

Equivalent to: Given a bipartite graph, find a perfect matching.

1. Create $s$, $t$, one vertex for each student, and one vertex for each job
2. Add edge from $s$ to each student (capacity $1$)
3. Add edge from each job to $t$ (capacity $1$)
4. Add edge from student to each job offered (capacity infinity)

Every perfect matching in bipartite graph corresponds to a maxflow of value $N$

### Baseball Elimination Problem

Given each team's current wins and losses, and their games left to play, determine whether team $i$ can be eliminated (cannot win).

Construct the graph:

1. Create $s$, $t$, one vertex for each pair of teams other than $i: j \leftrightarrow k$, and one vertex for each team other than $i$
2. Add edge from $s$ to each pair of teams (capacity = number of games left between this pair)
3. Add edge from each team $j$ to $t$ (capacity = $w_i + r_i - w_j$ ) **upper-bound** on the games that $j$ can win
4. Add edge from each pair of teams to two corresponding teams (capacity infinity)

Team $i$ will not be eliminated iff all edges pointing from $s$ are full in maxflow

## HW8 Baseball Elimination

[Specification](https://coursera.cs.princeton.edu/algs4/assignments/baseball/specification.php)

To check whether team $x$ is eliminated, we consider two cases:

- Trivial elimination: The maximum number of games team $x$ can win is less than the number of wins of some other team $i$
- Nontrivial elimination: Solve a maxflow problem as mentioned above

**Solution**: [BaseballElimination.java](https://github.com/silencial/Algorithms/blob/master/08_Baseball_Elimination/BaseballElimination.java)

# String Sorts

|                        |      worst       |    average     | extra space | stable? |
| :--------------------: | :--------------: | :------------: | :---------: | :-----: |
|          LSD           |      $2NW$       |     $2NW$      |   $N + R$   |    ✔    |
|          MSD           |      $2NW$       |  $N \log_R N$  |  $N + DR$   |    ✔    |
| 3-way string quicksort | $1.39 W N \lg R$ | $1.39 N \lg R$ | $\lg N + W$ |         |

## String vs. StringBuilder

Operations:

- Length: Number of characters
- Indexing: Get the $i$-th character
- Substring extraction: Get a contiguous subsequence of characters
- String concatenation: Append one character to end of another string

|               | length() | charAt() | substring() | concat() |
| :-----------: | :------: | :------: | :---------: | :------: |
|    String     |    1     |    1     |      1      |    N     |
| StringBuilder |    1     |    1     |      N      |    1     |

String:

- sequence of characters (immutable)
- Immutable `char[]` array, offset, and length

StringBuilder:

- Sequence of characters (mutable)
- Resizing `char[]` array and length

## Key-Indexed Counting

**Compare-based** algorithms require $\sim N\lg N$ compares.

We can do better if we don't depend on key compares.

**Key-indexed counting**:

- Assumption: keys are integers between $0$ and $R-1$
- Implication: can use key as an array index

Sort an array `a[]` of $N$ integers between $0$ and $R-1$:

- Count frequencies of each letter using key as index
- Compute frequency cumulates which specify destinations
- Access cumulates using key as index to move items
- Copy back into original array

```java
int N = a.length;
int[] count = new int[R+1];

for (int i = 0; i < N; i++)
    count[a[i]+1]++;
for (int r = 0; r < R; r++)
    count[r+1] += count[r];
for (int i = 0; i < N; i++)
    aux[count[a[i]]++] = a[i];
for (int i = 0; i < N; i++)
    a[i] = aux[i];
```

## LSD Radix Sort

LSD (Least-Significant-Digit-first) string sort:

- Consider characters from right to left
- Stably sort using $d$-th character as the key (key-indexed counting)

```java
public class LSD {
    public static void sort(String[] a, int W) {
        int R = 256;
        int N = a.length;
        String[] aux = new String[N];

        for (int d = W-1; d >= 0; d--) {
            int[] count = new int[R+1];
            for (int i = 0; i < N; i++)
                count[a[i].charAt(d) + 1]++;
            for (int r = 0; r < R; r++)
                count[r+1] += count[r];
            for (int i = 0; i < N; i++)
                aux[count[a[i].charAt(d)]++] = a[i];
            for (int i = 0; i < N; i++)
                a[i] = aux[i];
        }
    }
}
```

## MSD Radix Sort

MSD (Most-Significant-Digit-First) string sort:

- Partition array into $R$ pieces according to first character
- Recursively sort all strings that start with each character

Treat **variable-length strings** as if they had an extra char at end (smaller than any char)

```java
private static int charAt(String s, int d) {
    if (d < s.length()) return s.charAt(d);
    else return -1;
}
```

```java
public static void sort(String[] a) {
    aux = new String[a.length];
    sort(a, aux, 0, a.length-1, 0);
}

private static void sort(String[] a, String[] aux, int lo, int hi, int d) {
    if (hi <= lo) return;
    int[] count = new int[R+2];
    for (int i = lo; i <= hi; i++)
        count[charAt(a[i], d) + 2]++;
    for (int r = 0; r < R+1; r++)
        count[r+1] +=count[r];
    for (int i = lo; i <=hi; i++)
        aux[count[charAt(a[i], d) + 1]++] = a[i];
    for (int i = lo; i <= hi; i++)
        a[i] = aux[i - lo];

    for (int r = 0; r < R; r++)
        sort(a, aux, lo + count[r], lo + count[r+1] - 1, d+1);
}
```

### MSD String Sort vs. Quicksort

Disadvantages of MSD string sort:

- Extra space for `aux[]`
- Extra space for `count[]`
- Inner loop has a lot of instructions
- Accesses memory "randomly" (cache inefficient)

Disadvantage of quicksort:

- Linearithmic number of string compares (not linear)
- Has to rescan many characters in keys with long prefix matches

## 3-Way Radix Quicksort

Do 3-way partitioning on the $d$-th character

- Less overhead than $R$-way partitioning in MSD string sort
- Does not re-examine characters equal to the partitioning char

```java
private static void sort(String[] a)
{ sort(a, 0, a.length - 1, 0); }

private static void sort(String[] a, int lo, int hi, int d) {
    if (hi <= lo) return;
    int lt = lo, gt = hi;
    int v = charAt(a[lo], d);
    int i = lo + 1;
    while (i <= gt) {
        int t = charAt(a[i], d);
        if      (t < v) exch(a, lt++, i++);
        else if (t > v) exch(a, i, gt--);
        else            i++;
    }

    sort(a, lo, lt-1, d);
    if (v >= 0) sort(a, lt, gt, d+1);
    sort(a, gt+1, hi, d);
}
```

### 3-Way String Quicksort vs. Standard Quicksort

Standard quicksort:

- Uses $\sim 2 N \ln N$ **string compares** on average
- Costly for keys with long common prefixes (and this is a common case)

3-way string quicksort:

- Uses $\sim 2 N \ln N$ **character compares** on average
- Avoids re-comparing long common prefixes

## Suffix Arrays

**Problem:** Given a text of $N$ characters, preprocess it to enable fast substring search (find all occurrences of query string context)

1. suffix sort the text
2. Binary search for query; scan until mismatch

![Suffix Array](https://i.imgur.com/mEKKQCA.png)

# Tries

**String symbol table API**:

```java
public class StringST<Value> {
    StringST();
    void put(String key, Value val);
    Value get(String key);
    void delete(String key);
}
```

Goal: Faster than hashing, more flexible than BSTs.

![String Search Algorithms](https://i.imgur.com/WcRYIWV.png)

## R-Way Tries

- Store characters in nodes (not keys)
- Each node has $R$ children, one for each possible character

**Search**: Follow links corresponding to each character in the key

- Search hit: node where search ends has a non-null value
- Search miss: reach null link or node where search ends has null value

**Insertion**: Follow links corresponding to each character in the key

- Encounter a null link: create new node
- Encounter the last character of the key: set value in that node

**Deletion**:

- Find the node corresponding to the key and set value to null
- If node has null value and all null links, remove that node (and recur)

```java
public class TriesST<Value> {
    private static final int R = 256;
    private Node root = new Node();

    private static class Node {
        private Object value;
        private Node[] next = new Node[R];
    }

    public void put(String key, Value val)
    { root = put(root, key, val, 0); }

    private Node put(Node x, String key, Value val, int d) {
        if (x == null) x = new Node();
        if (d == key.length()) {
            x.val = val;
            return x;
        }
        char c = key.charAt(d);
        x.next[c] = put(x.next[c], key, val, d+1);
        return x;
    }

    public boolean contains(String key)
    { return get(key) != null; }

    public Value get(String key) {
        Node x = get(root, key, 0);
        if (x == null) return null;
        return (Value) x.val;
    }

    private Node get(Node x, String key, int d) {
        if (x == null) return null;
        if (d == key.length()) return x;
        char c = key.charAt(d);
        return get(x.next[c], key, d+1);
    }
}
```

**Performance**:

- Search hit: Need to examine all $L$ characters for quality
- Search miss: Examine only a few characters for typical case
- Space: $R$ null links at each leaf (sublinear if many short strings share common **prefixes**)

## Ternary Search Tries

- Store characters and values in nodes (not keys)
- Each node has **3** children: smaller (left), equal (middle), larger (right)

**Search**: Follow links corresponding to each character in the key

- If less, take left link; if greater, take right link
- If equal, take the middle link and move to the next key character

**Insertion**: Follow links corresponding to each character in the key

- Encounter a null link: create new node
- Encounter the last character of the key: set value in that node

**Deletion**:

- Find the node corresponding to the key and set value to null
- If node has null value and all null links, remove that node (and recur)

```java
public class TST<Value> {
    private Node root = new Node();

    private static class Node {
        private Value val;
        private char c;
        private Node left, mid, right;
    }

    public void put(String key, Value val)
    { root = put(root, key, val, 0); }

    private Node put(Node x, String key, Value val, int d) {
        char c = key.charAt(d);
        if (x == null) {
            x = new Node();
            x.c = c;
        }
        if      (c < x.c)            x.left = put(x.left, key, val, d);
        else if (c > x.c)            x.right = put(x.right, key, val, d);
        else if (d < key.length()-1) x.mid = put(x.mid, key, val, d+1);
        else                         x.val = val;
        return x;
    }

    public boolean contains(String key)
    { return get(key) != null; }

    public Value get(String key) {
        Node x = get(root, key, 0);
        if (x == null) return null;
        return x.val;
    }

    private Node get(Node x, String key, int d) {
        if (x == null) return null;
        char c = key.charAt(d);
        if      (c < x.c)            return get(x.left, key, d);
        else if (c > x.c)            return get(x.right, key, d);
        else if (d < key.length()-1) return get(x.mid, key, d+1);
        else                         return x;
        return x;
    }
}
```

### TST vs. Hashing

Hashing:

- Need to examine entire key
- Search hits and misses cost about the same
- Performance relies on hash function
- Does not support ordered symbol table operations

TST:

- Works only for strings (or digital keys)
- Only examines just enough key characters
- Search miss may involve only a few characters
- Support ordered symbol table operations (plus others)

# Substring Search

Find pattern of length $M$ in a text of length $N$. Typically $N \gg M$

Brute force: Check for pattern starting at each text position.

- We want linear-time guarantee
- We want to avoid **backup** problem: treat input as stream of data

![Substring Search Algorithms](https://i.imgur.com/CNYnA46.png)

## Knuth-Morris-Pratt

### DFA

DFA (Deterministic finite state automaton):

- Finite number of states (including start and halt)
- Exactly one transition for each char in alphabet
- Accept if sequence of transitions leads to halt state

The DFA state after reading in `text[i]` is the length of longest prefix of `pattern[]` that is a suffix of `text[0:i]`

Constructing the DFA:

- Copy of `dfa[][X]` to `dfa[][j]` for mismatch case
- Set `dfa[pat.charAt(j)][j]` to `j+1` for match case
- Update `X`

```java
public KMP(String pat) {
    this.pat = pat;
    M = pat.length();
    dfa = new int[R][M];
    dfa[pat.charAt(0)][0] = 1;
    for (int X = 0, j = 1; j < M; j++) {
        for (int c = 0; c < R; c++)
            dfa[c][j] = dfa[c][X];  // copy mismatch case
        dfa[pat.charAt(j)][j] = j + 1;  // set match case
        X = dfa[pat.charAt(j)][X];  // update restart state
    }
}
```

### KMP

Once we have the `dfa` matrix, we can use this make transition without backup

```java
public int search(In in) {
    int i, j;
    for (i = 0, j = 0; !in.isEmpty() && j < M; i++)
        j = dfa[in.readChar()][j];
    if (j == M) return i - M;
    else return NOT_FOUND;
}
```

## Boyer-Moore

- Scan characters in pattern from right to left
- Can skip as many as $M$ text chars when finding one not in the pattern

Precompute index of rightmost occurrence of character in pattern to decide how much to skip:

```java
right = new int[R];
for (int c = 0; c < R; c++)
    right[c] = -1;
for (int j = 0; j < M; j++)
    right[pat.charAt(j)] = j;
```

Boyer-Moore:

```java
public int search(String txt) {
    int N = txt.length();
    int M = pat.length();
    int skip;
    for (int i = 0; i <= N-M; i += skip) {
        skip = 0;
        for (int j = M-1; j >= 0; j--) {
            if (pat.charAt(j) != txt.charAt(i+j)) {
                skip = Math.max(1, j - right[txt.charAt(i+j)]);
                break;
            }
        }
        if (skip == 0) return i;
    }
    return N;
}
```

## Rabin-Karp

Use modular hashing:

- Compute a hash of pattern characters $0$ to $M-1$
- For each $i$, compute a hash of text characters $i$ to $M+i-1$
- if pattern hash = text substring hash, check for a match

Modular hash function: use the notation $t_i$ for `txt.charAt(i)`, compute
$$
x_{i}=(t_{i} R^{M-1}+t_{i+1} R^{M-2}+\dots+t_{i+M-1} R^{0}) \bmod Q
$$
**Horner's method**: linear-time method to evaluate degree-$M$ polynomial

```java
private long hash(String key, int M) {
    long h = 0;
    for (int j = 0; j < M; j++)
        h = (R * h + key.charAt(j)) % Q;
    return h;
}
```

Rabin-Karp:

```java
public class RabinKarp {
    private long patHash;
    private int M;
    private long Q;
    private int R;
    private long RM;  // R^(M-1) % Q

    public RabinKarp(String pat) {
        M = pat.length();
        R = 256;
        Q = longRandomPrime();

        RM = 1;
        for (int i = 1; i <= M-1; i++)
            RM = (R * RM) % Q;
        patHash = hash(pat, M);
    }

    private long hash(String key, int M)
    { /* as before */ }

    public int search(String txt) {
        int N = txt.length();
        int txtHash = hash(txt, M);
        if (patHash == txtHash) return 0;
        for (int i = M; i < N; i++) {
            txtHash = (txtHash + Q - RM*txt.charAt(i-M) % Q) % Q;
            txtHash = (txtHash*R + txt.charAt(i)) % Q;
            if (patHash == txtHash) return i - M + 1;
        }
        return N;
    }
}
```

## HW9 Boggle

[Specification](https://coursera.cs.princeton.edu/algs4/assignments/boggle/specification.php)

Find valid words in a board by connecting characters to its eight neighbors.

1. Add dictionary to 26-way Tries
2. Use dfs to search the board. Early termination can be made when no prefix match
3. dfs can be run with node expansion together to avoid starting from the root every time

**Solution**: [BoggleSolver.java](https://github.com/silencial/Algorithms/blob/master/09_Boggle/BoggleSolver.java)

# Regular Expressions

- **Substring search**: Find a single string in text
- **Pattern matching**: Find one of a specified set of strings in text

Regular Expression Pattern:

|    operation    | order |    example RE     |          matches           |      dose not match       |
| :-------------: | :---: | :---------------: | :------------------------: | :-----------------------: |
|  concatenation  |   3   |      AABAAB       |           AABAAB           |    every other string     |
|       or        |   4   |    AA \| BAAB     |         AA<br>BAAB         |    every other string     |
|     closure     |   2   |       AB*A        |      AA<br>ABBBBBA       |       AB<br>ABABA       |
|   parentheses   |   1   |   A(A \| B)AAB    |      AAAAB<br>ABAAB      |    every other string     |
|   parentheses   |   1   |      (AB)*A       |      A<br>ABABABABA      |       AA<br>ABBA        |
|    wildcard     |       |      .U.U.U.      |    CUMULUS<br>JUGULUM    | SUCCUBUS<br>TUMULTUOUS  |
| character class |       |  [A-Za-z]\[a-z]*  |   word<br>Captitalized   |  camelCase<br>4illegal  |
|   at least 1    |       |     A(BC)+DE      |     ABCDE<br>ABCBCDE     |       ADE<br>BCDE       |
|    exactly k    |       | [0-9]{5}-[0-9]{4} | 08540-1321<br>19072-5541 | 111111111<br>166-54-111 |

## NFA

**Kleene's theorem**:

- For any DFA, there exists a RE that describes the same set of strings
- For any RE, there exists a DFA that recognized the same set of strings

<mark>The DFA built from RE may have exponential number of states, so instead we use NFA (Nondeterministic finite state automata).</mark>

Regular-expression-matching NFA:

- RE enclosed in parentheses
- One state per RE character (start=$0$, accept=$M$)
- Red **$\epsilon$-transition** (change state, but don't scan text)
- Black match transition (change state and scan to next text char)
- Accept if **any** sequence of transitions end in accept state

![NFA Example](https://i.imgur.com/kw2bfcV.png)

The **Nondeterminism** comes from different transitions. The program has to consider all possible transition sequences.

Basic plan:

- Build NFA from RE
- Simulate NFA with text as input

## NFA Simulation

NFA representation:

- State names: Integers from $0$ to $M$
- Match-transitions: Keep regular expression in array `re[]`
- $\epsilon$-transitions: Store in a digraph $G$

Simulation Steps:

1. Run DFS from each source, without unmarking vertices
2. Maintain set of all possible states that NFA could be in after reading in the first $i$ characters
3. When no more input characters, accept if any state in the set is an accept state

```java
public class NFA {
    private char[] re; // match transitions
    private Digraph G; // epsilon transition digraph
    private int M; // number of states

    public NFA(String regexp) {
        M = regexp.length();
        re = regexp.toCharArray();
        G = buildEpsilonTransitionDigraph();
    }

    public boolean recognizes(String txt) {
        Bag<Integer> pc = new Bag<Integer>();
        DirectedDFS dfs = new DirectedDFS(G, 0);
        for (int v = 0; v < G.V(); v++)
            if (dfs.marked(v)) pc.add(v);

        for (int i = 0; i < txt.length(); i++) {
            Bag<Integer> match = new Bag<Integer>();
            for (int v : pc) {
                if (v == M) continue;
                if ((re[v] == txt.charAt(i)) || re[v] == '.')
                    match.add(v+1);
            }

            dfs = new DirectedDFS(G, match);
            pc = new Bag<Integer>();
            for (int v = 0; v < G.V(); v++)
                if (dfs.marked(v)) pc.add(v);
        }

        for (int v : pc)
            if (v == M) return true;

        return false;
    }

    public Digraph buildEpsilonTransitionDigraph()
    { /* next section */ }
}

```

**Time complexity**: Determining whether an $N$-character text is recognized by the NFA corresponding to an $M$-character pattern takes time proportional to $MN$ in the worst case.

## NFA Construction

Construction process:

- States: Include a state for each symbol in the RE, plus an accept state.
- Concatenation: Add match-transition edge from state corresponding to characters in the alphabet to next state.
- Parentheses: Add $\epsilon$-transition edge from parentheses to next state.
- Closure: Add three $\epsilon$-transition edges for each `*` operator.
- Or: Add two $\epsilon$-transition edges for each `|` operator

![DFA Construction](https://i.imgur.com/hJ20ydx.png)

```java
private Digraph buildEpsilonTransitionDigraph() {
   Digraph G = new Digraph(M+1);
   Stack<Integer> ops = new Stack<Integer>();

   for (int i = 0; i < M; i++) {
       int lp = i; // left parentheses

       if (re[i] == '(' || re[i] == '|') ops.push(i);

       else if (re[i] == ')') {
           int or = ops.pop();
           // 2-way or
           if (re[or] == '|') {
               lp = ops.pop();
               G.addEdge(lp, or+1);
               G.addEdge(or, i);
           }
           else lp = or;
       }
       // Closure needs 1-character lookahead
       if (i < M-1 && re[i+1] == '*') {
           G.addEdge(lp, i+1);
           G.addEdge(i+1, lp);
       }
       // Metasymbols
       if (re[i] == '(' || re[i] == '*' || re[i] == ')')
           G.addEdge(i, i+1);
   }
   return G;
}
```

**Time complexity**: Building the NFA corresponding to an $M$-character RE takes time and space proportional to $M$

# Data Compression

Lossless compression and expansion:

- **Message**: Binary data $B$ we want to compress
- **Compress**: Generates a "compressed" representation $C(B)$
- **Expand**: Reconstructs original bitstream $B$

**Static model**: Same model for all texts

- Fast
- Not optimal: different texts have different statistical properties
- Ex: ASCII, Morse code

**Dynamic model**: Generate model bases on text

- Preliminary pass needed to generate model
- Must transmit the model
- Ex: Huffman code

**Adaptive model**: Progressively learn and update model as you read text

- More accurate modeling produces better compression
- Decoding must start from beginning
- Ex: LZW

## Run-Length Encoding

Simple type of redundancy in a bitstream: Long runs of repeated bits

```text
0000000000000001111111000000011111111111
```

Representation: 4-bit counts to represent alternating runs of 0s and 1s (15 0s, 7 1s, 7 0s, 11 s)

```text
1111 0111 0111 1011
```

## Huffman Compression

Variable-length codes: Use different number of bits to encode different chars.

To avoid ambiguity, ensure that no codeword is a **prefix** of another.

Use **binary trie** to represent the prefix-free code.

Huffman trie node data type:

```java
private static class Node implements Comparable<Node> {
    private final char ch; // used only for leaf nodes
    private final int freq; // used only for compress
    private final Node left, right;

    public Node(char ch, int freq, Node left, Node right) {
        this.ch = ch;
        this.freq = freq;
        this.left = left;
        this.right = right;
    }

    public boolean isLeaf()
    { return left == null && right == null; }

    public int compareTo(Node that)
    { return this.freq - that.freq; }
}
```

Expansion:

```java
public void expand() {
    Node root = readTrie();
    int N = BinaryStdIn.readInt();

    for (int i = 0; i < N; i++) {
        Node x = root;
        while (!x.isLeaf()) {
            if (!BinaryStdIn.readBoolean())
                x = x.left;
            else
                x = x.right;
         }
        BinaryStdOut.write(x.ch, 8);
     }
    BinaryStdOut.close();
}
```

Read and write:

```java
private static void writeTrie(Node x) {
    if (x.isLeaf()) {
        BinaryStdOut.write(true);
        BinaryStdOut.write(x.ch, 8);
        return;
    }
    BinaryStdOut.write(false);
    writeTrie(x.left);
    writeTrie(x.right);
}

private static Node readTrie()  {
    if (BinaryStdIn.readBoolean()) {
        char c = BinaryStdIn.readChar(8);
        return new Node(c, 0, null, null);
    }
    Node x = readTrie();
    Node y = readTrie();
    return new Node('\0', 0, x, y);
}
```

![Encode Trie as Bitstream](https://i.imgur.com/XSeU85p.png)

To find the best prefix-free code, use **Huffman algorithm**:

- Count freq for each char in input
- Start with one node for each char with weight equal to freq
- Select two tries with min weight and merge into single trie with cumulative weight

```java
private static Node buildTrie(int[] freq) {
    MinPQ<Node> pq = new MinPQ<Node>();
    for (char i = 0; i < R; i++)
        if (freq[i] > 0)
            pq.insert(new Node(i, freq[i], null, null));

      while (pq.size() > 1) {
         Node x = pq.delMin();
         Node y = pq.delMin();
         Node parent = new Node('\0', x.freq + y.freq, x, y);
         pq.insert(parent);
      }

         return pq.delMin();
    }
```

## LZW Compression

- Create ST associating $W$-bit codewords with string keys
- Initialize ST with codewords for single-char keys
- Find longest string $s$ in ST that is a prefix of unscanned part of input
- Write the $W$-bit codeword associated with $s$
- Add $s+c$ to ST, where $c$ is next char in the input

Use a trie that supports longest prefix match to represent LZW compression code table:

```java
public static void compress() {
    String input = BinaryStdIn.readString();

    TST<Integer> st = new TST<Integer>();
    for (int i = 0; i < R; i++)
        st.put("" + (char) i, i);
    int code = R+1;

    while (input.length() > 0) {
        String s = st.longestPrefixOf(input);
        BinaryStdOut.write(st.get(s), W);
        int t = s.length();
        if (t < input.length() && code < L)
            st.put(input.substring(0, t+1), code++);
        input = input.substring(t);
    }

    BinaryStdOut.write(R, W);
    BinaryStdOut.close();
}
```

## HW10 Burrows–Wheeler

[Specification](https://coursera.cs.princeton.edu/algs4/assignments/burrows/specification.php)

Implement the *Burrows-Wheeler data compression algorithm*

1. *Burrows–Wheeler transform.* Given a typical English text file, transform it into a text file in which sequences of the same character occur near each other many times. [BurrowsWheeler.java](https://github.com/silencial/Algorithms/blob/master/10_Burrows_Wheeler/BurrowsWheeler.java), [CircularSuffixArray.java](https://github.com/silencial/Algorithms/blob/master/10_Burrows_Wheeler/CircularSuffixArray.java)
2. *Move-to-front encoding.* Given a text file in which sequences of the same character occur near each other many times, convert it into a text file in which certain characters appear much more frequently than others. [MoveToFront.java](https://github.com/silencial/Algorithms/blob/master/10_Burrows_Wheeler/MoveToFront.java)
3. *Huffman compression.* Given a text file in which certain characters appear much more frequently than others, compress it by encoding frequently occurring characters with short codewords and infrequently occurring characters with long codewords.
