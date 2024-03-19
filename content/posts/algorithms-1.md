---
title: Algorithm I
date: 2019-02-10
lastmod: 2020-07-10
categories:
- Study
tags:
- CS
- Algorithm
- Open Courses
---

Review of Princeton [Algorithms I](https://www.coursera.org/learn/algorithms-part1) course on Coursera.

[Algorithms II Review](https://silencial.github.io/algorithms-2/)

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

# Union-Find

Given a set of N objects, we want to implement the following two function.

- `Union`: connect two objects.
- `Find`: is there a path connecting the two objects?

![Union-Find](https://i.imgur.com/VVopEKs.png)

|             | initialize |  union  | connected |
| :---------: | :--------: | :-----: | :-------: |
| Quick-find  |    $N$     |   $N$   |    $1$    |
| Quick-union |    $N$     |   $N$   |    $N$    |
| Weighted QU |    $N$     | $\lg N$ |  $\lg N$  |

## Quick-Find

1. `Initialization`: integer array `id[]` of length N
2. `Union(p, q)`: change all entries whose id equal `id[p]` to `id[q]`
3. `Find`: check if `p` and `q` have the same id.

```java
public class QuickFindUF {
    private int[] id;

    public QuickFindUF(int N) {
        id = new int[N];
        // Set id of each object to itself
        for (int i = 0; i < N; i++) id[i] = i;
    }

    // Check whether p and q are in the same component
    public boolean connected(int p, int q)
    { return id[p] == id[q]; }

    public void union(int p, int q) {
        int pid = id[p];
        int qid = id[q];
        // Change all entries with id[p] to id[q]
        for (int i = 0; i < id.length; i++)
            if (id[i] == pid) id[i] = qid;
    }
}
```

- Union too expensive ($N$ array accesses).
- Trees are flat, but too expensive to keep them flat.

## Quick-Union

1. `Initialization`: integer array `id[]` of length N. `id[i]` is parent of `i`
2. `Union(p, q)`: change the root id of `p` to the root id of `q`
3. `Find`: check if `p` and `q` have the same root.

```java
public class QuickUnionUF {
    private int[] id;

    public QuickUnionUF(int N)
    { /* same as in quick-find */ }

    private int root(int i) {
        // Chase parent pointers until reach root
        while (i != id[i]) i = id[i];
        return i;
    }

    // Check if p and q have same root
    public boolean connected(int p, int q)
    { return root(p) == root(q); }

    // Change root of p to point to root of q
    public void union(int p, int q) {
        int i = root(p);
        int j = root(q);
        id[i] = j;
    }
}
```

- Find too expensive (could be $N$ array accesses).
- Trees can get tall.

## Weighted Quick-Union

1. `Initialization`: addition array `sz[i]` to count number of objects in the tree rooted at `i`
2. `Union(p, q)`: change the root of smaller tree to root of larger tree and update `sz[]`
3. `Find`: same as quick-union
4. <mark>Path compression: we can add extra line of code to make the tree more flat when computing the root of a node</mark>

```java
public class WeightedQuickUnionUF {
    private int[] id;
    private int[] sz;

    public WeightedQuickUnionUF(int N) {
        id = new int[N];
        sz = new int[N];
        // Set id of each object to itself
        for (int i = 0; i < N; i++) {
            id[i] = i;
            sz[i] = 1;
        }
    }

    private int root(int i) {
        while (i != id[i]) {
            // Only one extra line of code to halve path length
            id[i] = id[id[i]];
            i = id[i];
        }
        return i;
    }

    public boolean connected(int p, int q)
    { /* same as in quick-union */ }

    public void union(int p, int q) {
        int i = root(p);
        int j = root(q);
        if (i == j) return;
        // Link root of smaller tree to root of larger tree and update the sz[] array
        if (sz[i] < sz[j]) { id[i] = j; sz[j] += sz[i]; }
        else { id[j] = i; sz[i] += sz[j]; }
    }
}
```

- Find takes time proportional to depth of $p$ and $q$.
- Union takes constant time, given roots.
- Depth of any node is at most $\lg N$.

## Applications

- Pixels in a digital photo.
- Computers in a network.
- Friends in a social network.
- Transistors in a computer chip.
- Elements in a mathematical set.
- Variable names in Fortran program.
- Metallic sites in a composite system.

## HW1 Percolation

[Specification](http://coursera.cs.princeton.edu/algs4/assignments/percolation.html)

- $N$-by-$N$ grid of sites.
- Each site is open with probability $p$.
- System percolates iff top and bottom are connected by open sites.

![Percolation](https://i.imgur.com/3QlWJ8r.png)

When $N$ is large, theory guarantees a sharp threshold $p^*$. What is the value of $p^*$?

Use Monte Carlo simulation:

- Initialize $N$-by-$N$ whole grid to be blocked.
- Declare random sites open until top connected bottom.
- Vacancy percentage estimates $p^*$.

How to check whether an $N$-by-$N$ system percolates:

- Create an object for each site and name them $0$ to $N^2 - 1$.
- Sites are in same component if connected by open sites.
- Percolates iff any site on bottom row is connected to site on top row (brute-force algorithm: $N^2$ calls to `connected()`).
- <mark>Clever trick: introduce $2$ virtual sites connected to top and bottom separately and check if virtual top site is connected to virtual bottom site.</mark> (only $1$ call to `connected()`)

![Virtual Node](https://i.imgur.com/i7S0poS.png)

**Virtual top/bottom backwash problem:**

- When the system percolates (virtual top is connected to virtual bottom), every node that is connected to the bottom is also considered to be connected to the top (even though it is not). This is the backwash problem.
- A simple way to solve this problem is to use **two** `WeightedQuickUnionUF` object, one with the virtual bottom and the other not. Use the first to decide whether the system percolates and the second to see whether a node is full (connected to the top).
- A more memory-efficient way to solve this is to manually maintain two boolean arrays, `contop[]` and `conbot[]` to keep track whether the root of a node is connected to the top/bottom. When a node is connected to the top and the bottom at the same time, the system percolates.

Solution [Percolation.java](https://github.com/silencial/Algorithms/blob/master/01_Percolation/Percolation.java)

# Bags, Queues, and Stacks

- Operations: insert, remove, iterate.
- Stack: LIFO (last in first out).
- Queue: FIFO (first in first out).
- Bag: Adding items to a collection and iterating (order doesn't matter)

## Stack

### Linked-List Implementation

```java
public class LinkedStackOfStrings {
    private Node first = null;

    private class Node {
        String item;
        Node next;
    }

    public boolean isEmpty()
    { return first == null; }

    public void push(String item) {
        Node oldfirst = first;
        first = new Node();
        first.item = item;
        first.next = oldfirst;
    }

    public String pop() {
        String item = first.item;
        first = first.next;
        return item;
    }
}
```

### Resizing-Array Implementation

```java
public class ArrayStackOfStrings {
    private String[] s;
    private int N = 0;

    public ArrayStackOfStrings()
    { s = new String[2]; }

    public boolean isEmpty()
    { return N == 0; }

    private void resize(int capacity) {
        String[] copy = new String[capacity];
        for (int i = 0; i < N; i++)
            copy[i] = s[i];
        s = copy;
    }
    public void push(String item) {
        // Double size the array when it is full
        if (N == s.length) resize(2*s.length);
        s[N++] = item;
    }

    public String pop() {
        String item = s[--N];
        s[N] = null;
        // Halve size the array when it is one-quarter full
        if (N > 0 && N == s.length/4) resize(s.length/2);
        return item;
    }
}
```

### Resizing-Array vs. Linked-List

**Linked-list implementation:**

- Every operation takes constant time in the worst case.
- Use extra time and space to deal with the links.

**Resizing-array implementation:**

- Every operation takes constant amortized time.
- Less wasted space.

## Queues

### Linked-List Implementation

```java
public class LinkedQueueOfStrings {
    private Node first, last;

    private class Node
    { /* same as in StackOfStrings */ }

    public boolean isEmpty()
    { return first == null; }

    public void enqueue(String item) {
        Node oldlast = last;
        last = new Node();
        last.item = item;
        last.next = null;
        // Special cases for empty queue
        if (isEmpty()) first = last;
        else           oldlast.next = last;
    }

    public String dequeue() {
        String item = first.item;
        first = first.next;
        // Speicial cases for empty queue
        if (isEmpty()) last = null;
        return item;
    }
}
```

### Resizing-Array Implementation

```java
public class ArrayQueueOfStrings {
    private String[] s;
    private int N, first, last;

    public ArrayQueueOfStrings() {
        s = new String[2];
        N = 0;
        first = 0;
        last = 0;
    }

    public boolean isEmpty()
    { return N == 0; }

    private void resize(int capacity) {
        String[] copy = new String[capacity];
        for (int i = 0; i < N; i++)
            // Wrap-around
            copy[i] = s[(first + i) % s.length];
        s = copy;
        // Reset first and last index
        first = 0;
        last = N;
    }

    public void enqueue(String item) {
        // Double size the array when it is full
        if (N == s.length) resize(2*s.length);
        s[last++] = item;
        // Wrap-around
        if (last == s.length) last = 0;
        N++;
    }

    public String dequeue() {
        String item = s[first];
        s[first] = null;
        N--;
        first++;
        // Wrap-around
        if (first == q.length) first = 0;
        // Halve size the array when it is one-quarter full
        if (N > 0 && N == s.length/4) resize(s.length/2);
        return item;
    }
}
```

## Generics

- Avoid casting in client.
- Discover type mismatch errors at compile-time instead of run-time.
- Client code can use generic stack for any type of data.

Take Stack as an example

```java
public class Stack<Item> {
    private Item[] s;
    private int N = 0;

    // Generic array creation is not allowed in Java
    // s = new Item[capacity];
    public Stack(int capacity)
    { s = (Item[]) new Object[capacity]; }

    public void push(Item item)
    { s[N++] = item; }

    public Item pop()
    { return s[--N]; }
}
```

## Iterators

- Support iteration over stack items by client, without revealing the internal representation of the stack.
- Implement the `java.lang.Iterable` interface supported by Java.

### Linked-List Implementation

```java
import java.util.Iterator;

public class Stack<Item> implements Iterable<Item> {
    // ...

    // Iterable must have a method that returns an Iterator
    public Iterator<Item> iterator()
    { return new ListIterator(); }

    // Iterator must have methods hasNext() and next()
    private class ListIterator implements Iterator<Item> {
        private Node current = first;

        public boolean hasNext() { return current != null; }
        public void remove() { /* not supported */ }
        public Item next() {
            Item item = current.item;
            current = current.next;
            return item;
        }
    }
}
```

### Array Implementation

```java
import java.util.Iterator;

public class Stack<Item> implements Iterable<Item> {
    // ...

    public Iterator<Item> iterator()
    { return new ReverseArrayIterator(); }

    private class ReverseArrayIterator implements Iterator<Item> {
        private int i = N;
        public boolean hasNext() { return i > 0; }
        public void remove() { /* not supported */ }
        public Item next() { return s[--i]; }
    }
}
```

### Java Support

Java supports elegant client code if `Iterable` interface is implemented.

```java
// Shorthand code
for (String s : stack)
    StdOut.prinln(s);

// Longhand code
Iterator<Stirng> i = stack.iterator();
while (i.hasNext()) {
    String s = i.next();
    StdOut.println(s);
}
```

## Applications

- Parsing in a compiler.
- Java virtual machine.
- Undo in a word processor.
- Back button in a Web browser.
- PostScript language for printers.
- Implementing function calls in a compiler

### Two-Stack Algorithm

To evaluate infix expressions.
$$
(\ 1\ +\ (\ (\ 2\ +\ 3\ )\ *\ (\ 4\ *\ 5\ )\ )\ )
$$

- Value: push onto the value stack.
- Operator: push onto the operator stack.
- Left parenthesis: ignore.
- Right parenthesis: pop operator and two values; push the result of applying that operator to those values onto the operand stack.

```java
public class Evaluate {
    public static void main(String[] args) {
        Stack<String> ops = new Stack<String>();
        Stack<Double> vals = new Stack<Double>();

        while (!StdIn.isEmpty()) {
            String s = StdIn.readString();
            if (s.equals("(")) ;
            else if (s.equals("+")) ops.push(s);
            else if (s.equals("*")) ops.push(s);
            else if (s.equals(")")) {
                String op = ops.pop();
                if (op.equals("+")) vals.push(vals.pop() + vals.pop());
                else if (op.equals("*")) vals.push(vals.pop() * vals.pop());
            }
            else vals.push(Double.parseDouble(s));
        }

        StdOut.println(vals.pop());
    }
}
```

- Dijkstra's two-stack algorithm computes the same value if the operator occurs after the two values.

$$
(\ 1\ (\ (\ 2\ 3\ +\ )\ (\ 4\ 5\ *\ )\ *\ )\ +\ )
$$

- All of the parentheses are redundant.

$$
1\ 2\ 3\ +\ 4\ 5\ *\ *\ +
$$

## HW2 Deques and Randomized Queues

[Specification](http://coursera.cs.princeton.edu/algs4/assignments/queues.html)

- **Dequeue:** A double-ended queue or deque is a generalization of a stack and a queue that support adding and removing items from **either the front or the back** of the data structure. [Deque.java](https://github.com/silencial/Algorithms/blob/master/02_Deques_and_Randomized_Queues/Deque.java)
- **Randomized queue:** A randomized queue is similar to a stack or queue, except that the item removed is chosen **uniformly at random** from items in the data structure. [RandomizedQueue.java](https://github.com/silencial/Algorithms/blob/master/02_Deques_and_Randomized_Queues/RandomizedQueue.java)
- **Client:** Write a client program that takes an integer $k$ as a command-line argument; reads in a sequence of strings from standard input using `StdIn.readString()`; and prints exactly $k$ of them, uniformly at random. [Permutation.java](https://github.com/silencial/Algorithms/blob/master/02_Deques_and_Randomized_Queues/Permutation.java)

> Use [reservoir sampling](https://en.wikipedia.org/wiki/Reservoir_sampling) to randomly choose $k$ samples from a list of $n$ elements in a single pass over the items:
>
> - Keep the first $k$ items
> - When the $i$-th item arrives
>   - with probability $k/i$, discard an old item at random and keep the new one
>   - with probability $1 - k/i$, discard the new item

# Elementary Sorts

|             | inplace? | stable? |   worst   |  average  |   best   |                       remarks                        |
| :---------: | :------: | :-----: | :-------: | :-------: | :------: | :--------------------------------------------------: |
|  selection  |    ✔     |         |  $N^2/2$  |  $N^2/2$  | $N^2/2$  |                    $N$ exchanges                     |
|  insertion  |    ✔     |    ✔    |  $N^2/2$  |  $N^2/4$  |   $N$    |        use for small $N$ or partially ordered        |
|    shell    |    ✔     |         |           |           |   $N$    |               tight code, subquadratic               |
|    merge    |          |    ✔    | $N\lg N$  | $N\lg N$  | $N\lg N$ |              $N\lg N$ guarantee, stable              |
|    quick    |    ✔     |         |  $N^2/2$  | $2N\ln N$ | $N\lg N$ | $N\lg N$ probabilistic guarantee fastest in practice |
| 3-way quick |    ✔     |         |  $N^2/2$  | $2N\ln N$ |   $N$    |   improves quicksort in presence of duplicate keys   |
|    heap     |    ✔     |         | $2N\lg N$ | $2N\lg N$ | $N\lg N$ |             $N\lg N$ guarantee, in-place             |

## Comparable

**Interface:**

- Built-in comparable types: Integer, Double, String, Date, File, ...
- User-defined comparable types. Implement the `Comparable` interface supported by Java.

```java
// Only compare dates to other dates <Date>
public class Date implements Comparable<Date> {
    private final int month, day, year;

    public Date(int m, int d, int y) {
        month = m;
        day = d;
        year = y;
    }

    public int compareTo(Date that) {
        if (this.year < that.year ) return -1;
        if (this.year > that.year ) return +1;
        if (this.month < that.month) return -1;
        if (this.month > that.month) return +1;
        if (this.day < that.day ) return -1;
        if (this.day > that.day ) return +1;
        return 0;
    }
}
```

**Two useful sorting helper functions:**

- Less: is item $v$ less than $w$?
- Exchange: swap item in array at index $i$ with the one at index $j$.

```java
private static boolean less(Comparable v, Comparable w)
{ return v.compareTo(w) < 0; }

private static void exch(Comparable[] a, int i, int j) {
    Comparable swap = a[i];
    a[i] = a[j];
    a[j] = swap;
}
```

## Selection Sort

- Scan from left to right $[i, N]$
- Find the minimum entry and exchange with $i$'s entry
- Continue next scan from $[i+1, N]$

```java
public class Selection {
    public static void sort(Comparable[] a) {
        int n = a.length;
        for (int i = 0; i < n; i++) {
            int min = i;
            for (int j = i + 1; j < n; j++)
                if (less(a[j], a[min]))
                    min = j;
            exch(a, i, min);
        }
    }

    private static boolean less(Comparable v, Comparable w)
    { /* as before */ }

    private static void exch(Comparable[] a, int i, int j)
    { /* as before */ }
}
```

## Insertion Sort

- Scan $i$ from left to right $[0, N]$
- Scan $j$ from right to left $[i, 0]$, exchange `a[j]` with each larger entry to its left
- Start next scan $[1, N]$

```java
public class Insertion {
    public static void sort(Comparable[] a) {
        int N = a.length;
        for (int i = 0; i < N; i++)
            for (int j = i; j > 0; j--)
                if (less(a[j], a[j-1]))
                    exch(a, j, j-1);
                else break;
    }

    private static boolean less(Comparable v, Comparable w)
    { /* as before */ }

    private static void exch(Comparable[] a, int i, int j)
    { /* as before */ }
}
```

## Shellsort

- Insertion sort with stride length $h$.
- <mark>A $g$-sorted array remains $g$-sorted after $h$-sorting it.</mark>
- Use a sequence of increment steps to shellsort the array.

Which increment sequence to use?

- Power of two: $1, 2, 4, 8, 16, 32, \dots$ **No**
- Power of two minus one: $1, 3, 7, 15, 31, 63, \dots$ **Maybe**
- $3x + 1$: $1, 4, 13, 40, 121, 364, \dots$ **OK. Easy to compute**
- **Sedgewick**: $1, 5, 19, 41, 109, 209, 505, 929, 2161, 3905, \dots$ **Good. Tough to beat in empirical studies**

```java
public class Shell {
    public static void sort(Comparable[] a) {
        int N = a.length;
        int h = 1;
        while (h < N/3) h = 3*h + 1;
        while (h >= 1) {
            for (int i = h; i < N; i++)
                for (int j = i; j >= h && less(a[j], a[j-h]); j -= h)
                    exch(a, j, j-h);
            h = h/3;
        }
    }

    private static boolean less(Comparable v, Comparable w)
    { /* as before */ }

    private static void exch(Comparable[] a, int i, int j)
    { /* as before */ }
}
```

## Shuffle

Rearrange array so that result is a uniform random permutation.

### Shuffle sort

- Generate a random real number for each array entry.
- Sort the array.

### Knuth shuffle

- In iteration $i$, pick integer $r$ between $0$ and $i$ uniformly at random.
- Swap $a[i]$ and $a[r]$.

```java
public class StdRandom {
    // ...

    public static void shuffle(Object[] a) {
        int N = a.length;
        for (int i = 0; i < N; i++) {
            // Between 0 and i
            int r = StdRandom.uniform(i + 1);
            exch(a, i, r);
        }
    }
}
```

## Convex Hull

The convex hull of a set of $N$ points is the smallest perimeter fence enclosing the points.

### Convex hull applications

- **Robot motion planning**: Find shortest path in the plane from $s$ to $t$ to avoids a polygonal obstacle. Shortest path is either a straight line from $s$ to $t$ or it is one of two polygonal chains of convex hull.

![Motion Planning](https://i.imgur.com/ndQDjOc.png)

- **Farthest pair problem**: Given $N$ points in the plane, find a pair of points with the largest Euclidean distance between them. Farthest pair of points are extreme points on convex hull.

![Farthest Pair](https://i.imgur.com/xGBazRK.png)

**Graham scan:**

- Choose point $p$ with smallest $y$-coordinate.
- Sort points by polar angle with $p$.
- Consider points in order; discard unless it create a counterclockwise turn.

![Graham Scan](https://i.imgur.com/QFK0akK.png)

# Mergesort

- Divide array into two halves.
- Recursively sort each half.
- Merge two halves.

```java
public class Merge {
    private static void merge(Comparable[] a, Comparable[] aux, int lo, int mid, int hi) {
        // Precondition: a[lo..mid], a[mid+1..hi] sorted
        assert isSorted(a, lo, mid);
        assert isSorted(a, mid+1, hi);

        for (int k = lo; k <= hi; k++)
            aux[k] = a[k];
        int i = lo, j = mid+1;
        for (int k = lo; k <= hi; k++) {
            if      (i > mid)              a[k] = aux[j++];
            else if (j > hi)               a[k] = aux[i++];
            else if (less(aux[j], aux[i])) a[k] = aux[j++];
            else                           a[k] = aux[i++];
        }

        // Postcondition: a[lo..hi] sorted
        assert isSorted(a, lo, hi);
    }

    private static void sort(Comparable[] a, Comparable[] aux, int lo, int hi) {
        if (hi <= lo) return;
        int mid = (hi + lo) / 2;
        sort(a, aux, lo, mid);
        sort(a, aux, mid+1, hi);
        merge(a, aux, lo, mid, hi);
    }

    public static void sort(Comparable[] a) {
        aux = new Comparable[a.length];
        sort(a, aux, 0, a.length - 1);
    }
}
```

Java assert statement can be enable or disable at runtime

```java
java -ea Myprogram  // enable assertions
java -da Myprogram  // disable assertions (default)
```

## Practical Improvements

- Use insertion sort for small subarrays since mergesort has too much overhead for tiny subarrays.
- Stop if the biggest item in first half $\le$ smallest item in second half.
- **Eliminate** the copy to the auxiliary array by switching the role of the input and auxiliary array in each recursive call.

```java
public class Merge {
    private static final CUTOFF = 7;

    private static void merge(Comparable[] a, Comparable[] aux, int lo, int mid, int hi) {
        // Assertion
        int i = lo, j = mid + 1;
        for (int k = lo; k <= hi; k++) {
            if      (i > mid)          aux[k] = a[j++];
            else if (j > hi)           aux[k] = a[i++];
            else if (less(a[j], a[i])) aux[k] = a[j++];
            else                       aux[k] = a[i++];
        }
        // ...
    }

    private static void sort(Comparable[] a, Comparable[] aux, int lo, int hi) {
        // Cutoff to insertion sort
        if (hi <= lo + CUTOFF - 1) {
            Insertion.sort(a, lo, hi);
            return;
        }

        int mid = lo + (hi - lo) / 2;
        // Switch the role
        sort(aux, a, lo, mid);
        sort(aux, a, mid+1, hi);
        // Stop if already sorted
        if (!less(a[mid+1], a[mid])) return;
        merge(a, aux, lo, mid, hi);
    }

    public static void sort(Comparable[] a) {
        Comparable[] aux = a.clone();
        sort(a, aux, 0, a.length - 1);
    }
}
```

## Bottom-Up Mergesort

- Pass through array, merging subarrays of size $1$.
- Repeat for subarrays of size $2, 4, 8, 16, \dots$

```java
public class MergeBU {
    private static void merge(...)
    { /* as before */ }

    public static void sort(Comparable[] a) {
        int N = a.length;
        Comparable[] aux = new Comparable[N];
        for (int sz = 1; sz < N; sz = sz+sz)
            for (int lo = 0; lo < N-sz; lo += sz+sz)
                merge(a, aux, lo, lo+sz-1, Math.min(lo+sz+sz-1, N-1));
    }
}
```

## Comparator

**Interface:**

- Sorting using natural order by implementing the `Comparable` interface.
- Sorting using an alternate order by implementing the `Comparator` interface.
- Implement the `compare()` method.

```java
public class Student {
    public static final Comparator<Student> BY_NAME = new ByName();
    private final String name;
    // ...

    private static class ByName implements Comparator<Student> {
        public int compare(Student v, Student w)
        { return v.name.compareTo(w.name); }
    }
}
```

**To use with Java system sort:**

- Create Comparator object.
- Pass as second argument to `Arrays.sort()`

```java
String[] a;
Arrays.sort(a); // uses natrual order
Arrays.sort(a, String.CASE_INSENSITIVE_ORDER); // uses alternate order
```

**To support comparators in our sort implementations:**

- Use `Object` instead of `Comparable`.
- Pass `Comparator` to `sort()` and `less()` and use it in `less()`.

```java
public static void sort(Object[] a, Comparator comparator) {
    int N = a.length;
    for (int i = 0; i < N; i++)
        for (int j = i; j > 0 && less(comparator, a[j], a[j-1]); j--)
            exch(a, j, j-1);
}

private static boolean less(Comparator c, Object v, Object w)
{ return c.compare(v, w) < 0; }

private static void exch(Object[] a, int i, int j)
{ Object swap = a[i]; a[i] = a[j]; a[j] = swap; }
```

## Stability

A stable sort preserves the relative order of items with equal keys. The following is a counter example.

![Selection Sort is Unstable](https://i.imgur.com/x4Kstpl.png)

- **Stable:** Insertion sort, Mergesort
- **Unstable:** Selection sort, Shellsort

## HW3 Collinear Points

[Specification](http://coursera.cs.princeton.edu/algs4/assignments/collinear.html)

Given a set of $n$ distinct points in the plane, find every (maximal) line segment that connects a subset of $4$ or more of the points.

**Point data type:** Create an immutable data type `Point` that represents a point in the plane. [Point.java](https://github.com/silencial/Algorithms/blob/master/03_Collinear_Points/Point.java)

**Brute force:** Examines $4$ points at a time and checks whether they all lie on the same line segment (check the three slope between them are equal), returning all such line segments. [BruteCollinearPoints.java](https://github.com/silencial/Algorithms/blob/master/03_Collinear_Points/BruteCollinearPoints.java)

**Fast, sorting-based solution:** For each point $p$, calculate the slope it makes with every other point and sort. The 3 other collinear points must have equal slopes w.r.t. $p$. [FastCollinearPoints.java](https://github.com/silencial/Algorithms/blob/master/03_Collinear_Points/FastCollinearPoints.java)

# Quicksort

- Shuffle the array
- Partition so that, for some $j$
  - entry $a[j]$ is in place
  - no larger entry to the left of $j$
  - no smaller entry to the right of $j$
- Sort each piece recursively.

```java
public class Quick {
    private static int partition(Comparable[] a, int lo, int hi) {
        int i = lo, j = hi+1;
        while (true) {
            // Scan i from left to right so long as a[i] < a[lo]
            while (less(a[++i], a[lo]))
                if (i == hi) break;
            // Scan j from right to left so long as a[j] > a[lo]
            while (less(a[lo], a[--j]))
                if (j == lo) break;

            if (i >= j) break;
            // Exchange a[i] with a[j]
            exch(a, i, j);
        }
        // Exchange a[lo] with a[j]
        exch(a, lo, j);
        return j;
    }

    public static void sort(Comparable[] a) {
        // Shuffle needed for performance guarantee
        StdRandom.shuffle(a);
        sort(a, 0, a.length - 1);
    }

    private static void sort(Comparable[] a, int lo, int hi) {
        if (hi <= lo) return;
        int j = partition(a, lo, hi);
        sort(a, lo, j-1);
        sort(a, j+1, hi);
    }
}
```

Quicksort is an **in-place** sorting algorithm but is **not stable**.

## Practical Improvements

- Use insertion sort for small subarrays.
- Choose the median item as the pivot

```java
public class Quick {
    private static final int CUTOFF = 10;

    private static int partition(Comparable[] a, int lo, int hi)
    { /* as before */ }

    public static void sort(Comparable[] a)
    { /* as before */ }

    private static void sort(Comparable[] a, int lo, int hi) {
        // Cutoff to insertion sort
        if (hi <= lo + CUTOFF - 1) {
            Insertion.sort(a, lo, hi);
            return;
        }

        // Choose the median item to be the pivot
        int m = medianOf3(a, lo, (hi+lo)/2, hi);
        swap(a, lo, m);

        int j = partition(a, lo, hi);
        sort(a, lo, j-1);
        sort(a, j+1, hi);
    }
}
```

## Quick Select

Use `partition` to find a $k$-th smallest item in a given array.

```java
public static Comparable select(Comparable[] a, int k) {
    StdRandom.shuffle(a);
    int lo = 0, hi = a.length - 1;
    while (hi > lo) {
        int j = partition(a, lo, hi);
        if      (j < k) lo = j + 1;
        else if (j > k) hi = j - 1;
        else            return a[k];
    }
    return a[k];
}
```

Quick select takes **linear** time on average with the random shuffle provides a probabilistic guarantee.

## 3-Way Partitioning

When all keys equal, the quicksort needs $\sim 1/2 N^2$ compares. We need to modify quicksort to deal with duplicate elements.

Partition array into $3$ parts so that:

- Entries between $lt$ and $gt$ equal to partition item $v$.
- No larger entries to left of $lt$.
- No smaller entries to right of $gt$.

```java
private static void sort(Comparable[] a, int lo, int hi) {
    if (hi <= lo) return;
    int lt = lo, gt = hi;
    Comparable v = a[lo];
    int i = lo;
    while (i <= gt) {
        int cmp = a[i].compareTo(v);
        // a[i] < v: exchange a[lt] with a[i]; increment both lt and i
        if      (cmp < 0) exch(a, lt++, i++);
        // a[i] < v: exchange a[gt] with a[i]; decrement gt
        else if (cmp > 0) exch(a, i, gt--);
        // a[i] == v: increment i
        else              i++;
    }
    sort(a, lo, lt - 1);
    sort(a, gt + 1, hi);
}
```

# Priority Queues

Remove the largest (or smallest) item in deletion.

| implementation  | insert  | del max | max  |
| :-------------: | :-----: | :-----: | :--: |
| unordered array |   $1$   |   $N$   | $N$  |
|  ordered array  |   $N$   |   $1$   | $1$  |
|   binary heap   | $\lg N$ | $\lg N$ | $1$  |

## Array Implementation

### Unordered Array Implementation

```java
public class UnorderedMaxPQ<Key extends Comparable<Key>> {
    private Key[] pq;
    private int N;

    public UnorderedMaxPQ(int capacity)
    { pq = (Key[]) new Comparable[capacity]; }

    public boolean isEmpty()
    { return N == 0; }

    public void insert(Key x)
    { pq[N++] = x; }

    public Key delMax() {
        int max = 0;
        for (int i = 1; i < N; i++)
            if (less(max, i)) max = i;
        exch(max, N-1);
        return pq[--N];
    }
}
```

### Ordered Array Implementation

```java
public class OrderedArrayMaxPQ<Key extends Comparable<Key>> {
    private Key[] pq;
    private int N;

    public OrderedArrayMaxPQ(int capacity) {
        pq = (Key[]) (new Comparable[capacity]);
        N = 0;
    }

    public boolean isEmpty()
    { return N == 0; }

    public int size()
    { return N; }

    public Key delMax()
    { return pq[--N]; }

    public void insert(Key key) {
        int i = N-1;
        while (i >= 0 && less(key, pq[i])) {
            pq[i+1] = pq[i];
            i--;
        }
        pq[i+1] = key;
        N++;
    }

    private boolean less(Key v, Key w) {
        return v.compareTo(w) < 0;
    }
}
```

## Binary Heap

- Parent's key no smaller than children's keys.
- If child's key becomes larger than its parent's key, then repeatedly exchange child's key with parent's key.
- If parent's key becomes smaller than one (or both) of its children's, then repeatedly exchange parent's key with larger child's key.
- When **insert** a key, add node at end, then swim it up.
- When **delete** the maximum, exchange root with node at end, then sink it down.
- <mark>Index starts from $1$.</mark>

```java
public class MaxPQ<Key extends Comparable<Key>> {
    private Key[] pq;
    private int N;

    public MaxPQ(int capacity)
    { pq = (Key[]) new Comparable[capacity+1]; }

    public boolean isEmpty()
    { return N == 0; }

    public void insert(Key key) {
        // Index starts from 1
        pq[++N] = x;
        swim(N);
    }

    public Key delMax() {
        Key max = pq[1];
        exch(1, N--);
        sink(1);
        // Prevent loitering
        pq[N+1] = null;
        return max;
    }

    private void swim(int k) {
        while (k > 1 && less(k/2, k)) {
            exch(k, k/2);
            k = k/2;
        }
    }

    private void sink(int k) {
        while (2*k <= N) {
            int j = 2*k;
            // Children of node at k are 2k and 2k+1
            if (j < N && less(j, j+1)) j++;
            if (!less(k,j))            break;
            exch(k, j);
            k = j;
        }
    }

    private boolean less(int i, int j)
    { return pq[i].compareTo(pq[j]) < 0; }

    private void exch(int i, int j)
    { Key t = pq[i]; pq[i] = pq[j]; pq[j] = t; }
}
```

## Heapsort

- Create max-heap with all $N$ keys and repeatedly remove the maximum key

```java
public class Heap {
    public static void sort(Comparable[] a) {
        int N = a.length;
        // Heap-ordered
        for (int k = N/2; k >= 1; k--)
            sink(a, k, N);
        // Exchange the maximum with the bottom and sink it down
        while (N > 1) {
            exch(a, 1, N);
            sink(a, 1, --N);
        }
    }

    private static void sink(Comparable[] a, int k, int N)
    { /* as before */ }
    private static boolean less(Comparable[] a, int i, int j)
    { /* as before */ }
    private static void exch(Comparable[] a, int i, int j)
    { /* as before */ }
}
```

## HW4 8 Puzzle

[Specification](http://coursera.cs.princeton.edu/algs4/assignments/8puzzle.html)

A $3$-by-$3$ grid with $8$ square blocks labeled $1$ through $8$ and a blank square. The goal is to rearrange the blocks so that they are in order, using as few moves as possible. You are permitted to slide blocks horizontally or vertically into the blank square. Here is an example:
$$
\begin{matrix}
  & 1 & 3 \\
4 & 2 & 5 \\
7 & 8 & 6
\end{matrix}
\quad\to\quad
\begin{matrix}
1 &   & 3 \\
4 & 2 & 5 \\
7 & 8 & 6
\end{matrix}
\quad\to\quad
\begin{matrix}
1 & 2 & 3 \\
4 &   & 5 \\
7 & 8 & 6
\end{matrix}
\quad\to\quad
\begin{matrix}
1 & 2 & 3 \\
4 & 5 &   \\
7 & 8 & 6
\end{matrix}
\quad\to\quad
\begin{matrix}
1 & 2 & 3 \\
4 & 5 & 6 \\
7 & 8 &
\end{matrix}
$$
We use the **A* search algorithm** to solve this problem. We require:

- A search node of the game to be a board.
- The number of moves made to reach the board.
- The predecessor search node.

The search process:

- Insert the initial search node (the initial board, $0$ moves, and a null predecessor search node) into a priority queue.
- Delete min priority search node from the priority queue, and insert all neighboring search nodes back.
- Repeat until the search node dequeued corresponds to a goal board.

Choices of priority function:

- **Hamming priority function**: the number of blocks in the wrong position, plus the number of moves made so far to get to the search node.
- **Manhattan priority function**: the sum of the Manhattan distances (sum of the vertical and horizontal distance) from the blocks to their goal position, plus the number of moves so far to get to the search node.

Optimization:

- To avoid search node corresponding to the same board enqueued on the priority queue many times, don't enqueue a neighbor if its board is the same as the board of the predecessor search node.
- Pre-compute the Manhattan priority when constructing the search node and save it in an instance variable.

**Board:** Creat an immutable data type `Board` that models an $n$-by-$n$ board with sliding tiles. [Board.java](https://github.com/silencial/Algorithms/blob/master/04_8puzzle/Board.java)

**Solver:**  [Solver.java](https://github.com/silencial/Algorithms/blob/master/04_8puzzle/Solver.java)

# Symbol Tables

**Key-value pair abstraction:**

- Insert a value with specified key.
- Search for the corresponding value with a given key.

## API

```java
public class ST<Key, Value> {
    ST()                         // create a symbol table
    void put(Key key, Value val) // put key-value pair into the table
                                 // (remove key from table if value is null)
    Value get(Key key)           // value paired with key
                                 // (null if key is absent)
    void delete(Key key)         // remove key (and its value) from table
    boolean contains(Key key)    // is there a value paired with key?
    boolean isEmpty()            // is the table empty?
    int size()                   // number of key-value pairs in the table
    Iterable<Key> keys()         // all the keys in the table
}
```

## Implementing Equals for User-Defined Types

```java
// Use final since it is typically unsafe to use equals() with inheritance
public final class Date implements Comparable<Date> {
    private final int month;
    private final int day;
    private final int year;
    // ...

    public boolean equals(Object y) {
        // Optimize for true object equality
        if (y == this) return true;
        // Check for null
        if (y == null) return false;
        // Objects must be in the same class
        if (y.getClass() != this.getClass()) return false;

        // Cast is guaranteed to succeed
        Date that = (Date) y;
        if (this.day != that.day ) return false;
        if (this.month != that.month) return false;
        if (this.year != that.year ) return false;
        return true;
    }
}
```

# Binary Search Trees

- A BST is a binary tree in symmetric order.
- A binary tree is either empty or two disjoint binary trees (left and right).
- Each node has a key and every node's key is
  - larger than all keys in its left subtree.
  - smaller than all keys in its right subtree.

## Basic Operations

- **Search:** If less, go left; if greater, go right; if equal, search hit.
- **Insert:** If less, go left; if greater, go right; if null, insert.

### Node

A Node is comprised of four fields:

- A key and value
- A reference to the left and right subtree

```java
private class Node {
    private Key key;
    private Value val;
    private Node left, right;

    public Node(Key key, Value val) {
        this.key = key;
        this.val = val;
    }
}
```

### Get

Return value corresponding to given key, or `null` if no such key.

```java
public Value get(Key key) {
    Node x = root;
    while (x != null) {
        int cmp = key.compareTo(x.key);
        if (cmp < 0)      x = x.left;
        else if (cmp > 0) x = x.right;
        // cmp == 0
        else              return x.val;
    }
    return null;
}
```

### Put

Search for key, then

- If key in tree, reset value.
- If not in tree, add new node.

```java
public void put(Key key, Value val)
{ root = put(root, key, val); }

private Node put(Node x, Key key, Value val) {
    if (x == null) return new Node(key, val);
    int cmp = key.compareTo(x.key);
    if (cmp < 0)      x.left = put(x.left, key, val);
    else if (cmp > 0) x.right = put(x.right, key, val);
    else              x.val = val;
    return x;
}
```

## Ordered Operations

### Max, Min, Floor, Ceiling

- Minimum: smallest key in table. Keep search `x.left()` until it is null, then return `x`.
- Maximum: Largest key in table. Keep search `x.right()` until it is null, then return `x`.
- Floor: Largest key $\le$ a given key.
- Ceiling: Smallest key $\ge$ a given key.

```java
public Key floor(Key key) {
    Node x = floor(root, key);
    if (x == null) return null;
    return x.key;
}
private Node floor(Node x, Key key) {
    if (x == null) return null;
    int cmp = key.compareTo(x.key);
    if (cmp == 0) return x;
    if (cmp < 0) return floor(x.left, key);
    // Could be on the right
    Node t = floor(x.right, key);
    if (t != null) return t;
    else return x;
}
```

### Subtree Counts

In each node, we store the number of nodes in the subtree rooted at that node.

```java
private class Node {
    /* as before */
    private int count;
}

public int size()
{ return size(root); }

private int size(Node x) {
    if (x == null) return 0;
    return x.count;
}

private Node put(Node x, Key key, Value val) {
    if (x == null) return new Node(key, val, 1);
    int cmp = key.compareTo(x.key);
    if (cmp < 0)      x.left = put(x.left, key, val);
    else if (cmp > 0) x.right = put(x.right, key, val);
    else              x.val = val;

    x.count = 1 + size(x.left) + size(x.right);
    return x;
}
```

### Rank

The number of keys $<$ k.

```java
public int rank(Key key)
{ return rank(key, root); }

private int rank(Key key, Node x) {
    if (x == null) return 0;
    int cmp = key.compareTo(x.key);
    if      (cmp < 0) return rank(key, x.left);
    else if (cmp > 0) return 1 + size(x.left) + rank(key, x.right);
    else              return size(x.left);
}
```

### Inorder Traversal

- Traverse left subtree.
- Enqueue key.
- Traverse right subtree.

```java
public Iterable<Key> keys() {
    Queue<Key> q = new Queue<Key>();
    inorder(root, q);
    return q;
}

private void inorder(Node x, Queue<Key> q) {
    if (x == null) return;
    inorder(x.left, q);
    q.enqueue(x.key);
    inorder(x.right, q);
}
```

## Deletion

### Deleting The Minimum

- Go left until finding a node with a null left link.
- Replace that node by its right link.
- Update subtree count.

```java
public void deleteMin()
{ root = deleteMin(root); }

private Node deleteMin(Node x) {
    if (x.left == null) return x.right;
    x.left = deleteMin(x.left);
    x.count = 1 + size(x.left) + size(x.right);
    return x;
}
```

### Hibbard Deletion

Search for node $t$ containing key $k$.

- If no children: Delete $t$ by setting parent link to null
- If one child: Delete $t$ by replacing parent link
- If two children:
  - Find the minimum in $t$'s right subtree $x$.
  - Delete $x$ in $t$'s right subtree.
  - Put $x$ in $t$'s spot.

Update subtree counts.

```java
public void delete(Key key)
{ root = delete(root, key); }

private Node delete(Node x, Key key) {
    if (x == null) return null;
    int cmp = key.compareTo(x.key);
    if      (cmp < 0) x.left = delete(x.left, key);
    else if (cmp > 0) x.right = delete(x.right, key);
    else {
        if (x.right == null) return x.left;
        if (x.left == null) return x.right;

        Node t = x;
        x = min(t.right);
        x.right = deleteMin(t.right);
        x.left = t.left;
    }
    x.count = size(x.left) + size(x.right) + 1;
    return x;
}
```

# Balanced Search Trees

![Search Trees](https://i.imgur.com/ye8sT2a.png)

## 2-3 Tree

Allow 1 or 2 keys per node

- 2-node: one key, two children.
- 3-node: two key, three children.

Symmetric order. Inorder traversal yields keys in ascending order.

Perfect balance. Every path from root to null link has same length.

![2-3 Tree](https://i.imgur.com/7bxUFVO.png)

### Search

Search is similar to binary search trees. Recursively compare search key against keys in node. The only difference is for 3-node, when a search key needs to be compared with these two keys.

### Insertion

Insertion into a 2-node at bottom will change it to a 3-node.

Insertion into a 3-node at bottom:

- Add new key to 3-node to create temporary 4-node.
- Move middle key in 4-node into parent and split the other two keys.
- Repeat up the tree, as necessary.
- If you reach the root and it's a 4-node, split it into three 2-nodes.

This insertion maintains symmetric order and perfect balance.

### Implementation

Direct implementation is complicated, because:

- Maintaining multiple node types is cumbersome.
- Need multiple compares to move down tree.
- Need to move back up the tree to split 4-nodes.
- Large number of cases for splitting.

Instead, we choose a better way to do it, the red-black BST.

## Red-Black BST

Represent 2-3 tree as a BST by splitting 3-node to two 2-node with a red link connecting them.

- No node has two red links connected to it.
- Every path from root to null link has the same number of black links.
- Red links lean left.

![Red-Black Tree](https://i.imgur.com/vRH33DN.png)

### Representation

Each node is pointed to by precisely one link from its parent and can encode color of the link.

```java
private static final boolean RED = true;
private static final boolean BLACK = false;

private class Node {
    Key key;
    Value val;
    Node left, right;
    boolean color; // color of parent link
}

private boolean isRed(Node x) {
    if (x == null) return false;
    return x.color == RED;
}
```

### Elementary Operations

**Left rotation**. Orient a right-leaning red link to lean left.

```java
private Node rotateLeft(Node h) {
    assert isRed(h.right);
    Node x = h.right;
    h.right = x.left;
    x.left = h;
    x.color = h.color;
    h.color = RED;
    return x;
}
```

**Right rotation**. Orient a left-leaning red link to (temporarily) lean right (To solve two red links in a row).

```java
private Node rotateRight(Node h) {
    assert isRed(h.left);
    Node x = h.left;
    h.left = x.right;
    x.right = h;
    x.color = h.color;
    h.color = RED;
    return x;
}
```

**Color flip**. Recolor to split a (temporary) 4-node.

```java
private void flipColors(Node h) {
    assert !isRed(h);
    assert isRed(h.left);
    assert isRed(h.right);
    h.color = RED;
    h.left.color = BLACK;
    h.right.color = BLACK;
}
```

### Search

Search is the same as for elementary BST (ignore color) but runs faster because of better balance.

### Insertion

- Right child red, left child black: rotate left.
- Left child, left-left grandchild red: rotate right.
- Both children red: flip colors.

```java
private Node put(Node h, Key key, Value val) {
    if (h == null) return new Node(key, val, RED);
    int cmp = key.compareTo(h.key);

    if (cmp < 0)       h.left = put(h.left, key, val);
    else if (cmp > 0)  h.right = put(h.right, key, val);
    else if (cmp == 0) h.val = val;

    if (isRed(h.right) && !isRed(h.left))    h = rotateLeft(h);
    if (isRed(h.left) && isRed(h.left.left)) h = rotateRight(h);
    if (isRed(h.left) && isRed(h.right))     flipColors(h);
    return h;
}
```

## B-Tree

B-tree and its variants are widely used for file systems and databases to reduce the time for probing.

Generalize 2-3 trees by allowing up to $M-1$ key-link pairs per node. Choose $M$ as large as possible so that $M$ links fit in a page.

- At least 2 key-link pairs at root.
- At least $M/2$ key-link pairs in other nodes.
- External nodes contain client keys.
- Internal nodes contain copies of keys to guide search (each key is a copy of min key in subtree).

A search or an insertion in a B-tree of order $M$ with $N$ keys requires between $\log_{M-1} N$ and $\log_{M/2} N$ probes. In practice, the number of probes is at most 4. e.g. $M=1024; N=62 \text{ billion}$, $\log_{M/2} N \le 4$.

# Geometric Applications of BSTs

![Geometric Application](https://i.imgur.com/6tXtiX7.png)

## 1d Range Search

Extension of ordered symbol table. Besides insertion, search and deletion, we require **Range count** and **Range search**.

Range count: number of keys between $k_1$ and $k_2$.

```java
public int size(Key lo, Key hi) {
    if (contains(hi)) return rank(hi) - rank(lo) + 1;
    else              return rank(hi) - rank(lo);
}
```

Range search: find all keys between $k_1$ and $k_2$.

- Recursively find all keys in left subtree (if any could fall in range).
- Check key in current node.
- Recursively find all keys in right subtree (if any could fall in range).

## Line Segment Intersection

Given $N$ horizontal and vertical line segments, find all intersections.

Sweep-line algorithm: reduces 2d line intersection to 1d range search.

- x-coordinates define events.
- h-segment (left endpoint): insert y-coordinate into BST.
- h-segment (right endpoint): remove y-coordinate from BST.
- v-segment: range search for interval of y-endpoints.

## Kd Trees

Start from 2d trees. It is the extension of ordered symbol-table to 2d keys.

### Space-Partitioning Trees

- Grid: Divide space uniformly into squares.
- 2d tree: Recursively divide space into two halfplanes.
- Quadtree: Recursively divide space into four quadrants.
- BSP tree: Recursively divide space into two regions.

![Kd Tree](https://i.imgur.com/JfVLqJ2.png)

### Grid Implementation

- Divide space into $M\times M$ grid of squares.
- Create list of points contained in each square.
- Use 2d array to directly index relevant square.
- Insert: add $(x,y)$ to list for corresponding square.
- Range search: examine only squares that intersect 2d range query.

Grid size is the key to performance. If too small, it will waste space; if too large, there will be too many points per square. A reasonable choice is $\sqrt{N}$-by-$\sqrt{N}$. Grid implementation is fast and simple solution for evenly-distributed points but not for clustering.

### 2d Tree

BST but alternate using x and y coordinates as key.

**Range search:** Find all points in a query axis-aligned rectangle.

- Check if point in node lies in given rectangle.
- Recursively search trees (if any could fall in rectangle).
- If query rectangle does not intersect the rectangle corresponding to a subtree, prune this subtree.

**Nearest neighbor search:** Find closest point to query point.

- Recursively search trees.
- If closest point discovered so far is closer than the distance between the query point and the rectangle corresponding to a subtree, prune this subtree.

### Applications

- Ray tracing
- **2d range search**
- Flight simulators
- N-body simulation
- Collision detection
- Astronomical databases
- **Nearest neighbor search**
- Adaptive mesh generation
- Accelerate rendering in Doom
- Hidden surface removal and shadow casting

## Interval Search Trees

1d interval search:

- Insert an interval $(lo, hi)$.
- Search for an interval.
- Delete an interval.
- Given an interval, find all intervals in data structure that intersects it.

<mark>Non-degeneracy assumption: No two intervals have the same left endpoint.</mark>

Create BST, where each node stores an interval. Use left endpoint as BST key and store max endpoint in subtree rooted at node.

To insert an interval $(lo, hi)$, first insert into BST using $lo$ as the key, then update max in each node on search path.

To find intersections for query interval $(lo, hi)$:

- If interval in node intersects query interval, return it.
- Else if left subtree is null, go right.
- Else if max endpoint in left subtree is less than $lo$, go right.
- Else go left

## Rectangle Intersection

Find all intersections among a set of $N$ orthogonal rectangles.

<mark>Non-degeneracy assumption: All x and y coordinates are distinct.</mark>

Sweep-line algorithm: reduces 2d orthogonal rectangle intersection search to 1d interval search.

- x-coordinates of left and right end points define events.
- Maintain set of rectangles that intersect the sweep line in an interval search tree (using y-intervals of rectangle).
- Left endpoint: interval search for y-interval of rectangle; insert y-interval.
- Right endpoint: remove y-interval

## HW5 Kd-Trees

[specification](<http://coursera.cs.princeton.edu/algs4/assignments/kdtree.html>)

Write a data type to represent a set of points in the unit square using a 2d-tree to support efficient range search and nearest-neighbor search.

Two immutable data types are provided, the `Point2D` represents points in the plane and `RectHV` represents axis-aligned rectangles.

**Brute-force implementation:** use red-black BST. [PointSET.java](https://github.com/silencial/Algorithms/blob/master/05_KdTree/PointSET.java)

**2d-tree implementation**: [KdTree.java](https://github.com/silencial/Algorithms/blob/master/05_KdTree/KdTree.java)

# Hash Table

Save items in a **key-indexed table** (index is a function of the key)

- Computing the hash function
- Equality test: Method for checking whether two keys are equal
- Collision resolution: Algorithm and data structure to handle two keys that hash to the same array index

## Hash Function

Idealist goal:

- Efficiently computable
- Each table index equally likely for each key

Java library implementations for primitive types: `Integer`, `Boolean`, `Double`, `String`

```java
public final class Integer {
    private final int value;
    ...
    public int hashCode()
    { return value; }
}

public final class Boolean {
    private final boolean value;
    ...
    public int hashCode() {
        if (value) return 1231;
        else       return 1237;
    }
}

public final class Double {
    private final double value;
    ...
    public int hashCode() {
        long bits = doubleToLongBits(value);
        return (int) (bits & (bits >>> 32));
    }
}

public final class String {
    // Cache of hash code
    private int hash = 0;
    private final char[] s;
    ...
    public int hashCode() {
        int h = hash;
        if (h != 0) return h;
        for (int i = 0; i < length(); i++)
            h = s[i] + 31 * h;
        hash = h;
        return h;
    }
}
```

- **Hash code**: an `int` between $-2^{31}$ and 2^{31} - 1$
- **Hash function output**: an `int` between $0$ and $M - 1$

```java
private int hash (Key key)
{ return (key.hashCode() & 0x7fffffff) % M;}
```

## Separate Chaining

Use an array of $M < N$ linked lists to deal with collisions:

- **Hash**: map key to integer $i$ between $0$ and $M-1$
- **Insert**: put at front of $i$-th chain (if not already there)
- **Search**: need to search only $i$-th chain

```java
public class SeparateChainingHashST<Key, Value> {
    private int M = 97; // number of chains
    private Node[] st = new Node[M]; // array of chains
    ...
    public void put(Key key, Value val) {
        int i = hash(key);
        for (Node x = st[i]; x != null; x = x.next)
            if (key.equals(x.key)) {
                x.val = val;
                return;
            }
        st[i] = new Node(key, val, st[i]);
    }

    public Value get(Key key) {
        int i = hash(key);
        for (Node x = st[i]; x != null; x = x.next)
            if (key.equals(x.key))
                return (Value) x.val;
        return null;
    }
}
```

Typical choice: $M \sim N / 5$

## Linear Probing

When a new key collides, find next empty slog, and put it there ($M > N$)

- **Hash**: map key to integer $i$ between $0$ and $M-1$
- **Insert**: put at table index $i$ if free, if not try $i+1$, $i+2$, etc
- **Search**: search table index $i$, if occupied but no match, try $i+1$, $i+2$, etc

```java
public class SeparateChainingHashST<Key, Value> {
    private int M = 30001;
    private Value[] vals = (Value[]) new Object[M];
    private Key[] keys = (Key[]) new Object[M];
    ...
    public void put(Key key, Value val) {
        int i;
        for (i = hash(key); keys[i] != null; i = (i+1) % M)
            if (keys[i].equals(key))
                break;
        keys[i] = key;
        vals[i] = val;
    }

    public Value get(Key key) {
        for (int i = hash(key); keys[i] != null; i = (i+1) % M)
            if (keys[i].equals(key))
                return vals[i];
        return null;
    }
}
```

Typical choice: $M \sim 2N$, mean displacement will be $\sim 3/2$
