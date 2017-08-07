---
title: Implementation of Hopcroft-Karp Algorithm
date: 2017-04-11 09:24:17
tags: R
---

<script type="text/javascript">
  MathJax.Hub.Config({
    extensions: ["tex2jax.js"],
    "HTML-CSS": { scale: 10}
  });
</script>

*See [GitHub](https://github.com/GaoFangshu/Hopcroft-Karp-algorithm) for my R code.*
_ _ _

Several days ago, I made a bet with my roommates that I can find out who are anonymous reviewers of each student's graduate thesis. Because 55 professors participate anonymous peer review as advisors and reviewers, names of advisors and ID of anonymous reviewers are known. For each graduate thesis, its advisor and anonymous reviewer cannot be the same person. To sum up all these constraints, maybe we can speculate on the identity of reviewers.

<!--more-->

For example, there are 5 professors (named `A, B, C, D, E` with ID `a, b, c, d, e`) in the anonymous peer review. This problem can be described with a matrix.
$$
\begin{matrix}
\quad \\\\
\end{matrix}\begin{matrix}
 a & b & c & d & e \\\\
\end{matrix}\\\\\begin{matrix}
 A \\\\
 B \\\\
 C \\\\
 D \\\\
 E \\\\
\end{matrix}\begin{bmatrix}
 0 & 1 & 0 & 1 & 0 \\\\
 1 & 1 & 1 & 0 & 0 \\\\
 1 & 0 & 0 & 1 & 1 \\\\
 1 & 1 & 0 & 0 & 0 \\\\
 0 & 1 & 0 & 0 & 1 \\\\
\end{bmatrix}
$$

As the matrix above, if professor named `A` is the anonymous reviewer whose ID is `b`, then the element with address `(A, b)` is equal to `1`. Our goal is to find a possible matrix, based on original matrix, that in each row and column there is only one element equal to `1`:
$$
\begin{bmatrix}
 0 & 1 & 0 & 1 & 0 \\\\
 1 & 1 & 1 & 0 & 0 \\\\
 1 & 0 & 0 & 1 & 1 \\\\
 1 & 1 & 0 & 0 & 0 \\\\
 0 & 1 & 0 & 0 & 1 \\\\
\end{bmatrix}\Longrightarrow\begin{bmatrix}
 0 & 0 & 0 & 1 & 0 \\\\
 0 & 0 & 1 & 0 & 0 \\\\
 0 & 0 & 0 & 0 & 1 \\\\
 1 & 0 & 0 & 0 & 0 \\\\
 0 & 1 & 0 & 0 & 0 \\\\
\end{bmatrix}
$$

First I come up with an enumeration method consisting of two steps:

**Step 1. **find all possible matrices that each row and column only has one non-zero element.
$$
\begin{bmatrix}
 1 & 0 & 0 & 0 & 0 \\\\
 0 & 1 & 0 & 0 & 0 \\\\
 0 & 0 & 1 & 0 & 0 \\\\
 0 & 0 & 0 & 1 & 0 \\\\
 0 & 0 & 0 & 0 & 1 \\\\
\end{bmatrix},\begin{bmatrix}
 0 & 1 & 0 & 0 & 0 \\\\
 1 & 0 & 0 & 0 & 0 \\\\
 0 & 0 & 1 & 0 & 0 \\\\
 0 & 0 & 0 & 1 & 0 \\\\
 0 & 0 & 0 & 0 & 1 \\\\
\end{bmatrix},\begin{bmatrix}
 0 & 0 & 1 & 0 & 0 \\\\
 1 & 0 & 0 & 0 & 0 \\\\
 0 & 1 & 0 & 0 & 0 \\\\
 0 & 0 & 0 & 1 & 0 \\\\
 0 & 0 & 0 & 0 & 1 \\\\
\end{bmatrix}\ldots\begin{bmatrix}
 0 & 0 & 0 & 0 & 1 \\\\
 0 & 0 & 0 & 1 & 0 \\\\
 0 & 0 & 1 & 0 & 0 \\\\
 0 & 1 & 0 & 0 & 0 \\\\
 1 & 0 & 0 & 0 & 0 \\\\
\end{bmatrix}
$$

**Step 2. **original matrix minus each possible matrix above, and check whether all elements in result are positive. If so, this matrix is the one we look for.
$$
\begin{align}
&\begin{bmatrix}
 0 & 1 & 0 & 1 & 0 \\\\
 1 & 1 & 1 & 0 & 0 \\\\
 1 & 0 & 0 & 1 & 1 \\\\
 1 & 1 & 0 & 0 & 0 \\\\
 0 & 1 & 0 & 0 & 1 \\\\
\end{bmatrix}-\begin{bmatrix}
 1 & 0 & 0 & 0 & 0 \\\\
 0 & 1 & 0 & 0 & 0 \\\\
 0 & 0 & 1 & 0 & 0 \\\\
 0 & 0 & 0 & 1 & 0 \\\\
 0 & 0 & 0 & 0 & 1 \\\\
\end{bmatrix}=\begin{bmatrix}
 -1 & 1 & 0 & 1 & 0 \\\\
 1 & 0 & 1 & 0 & 0 \\\\
 1 & 0 & -1 & 1 & 1 \\\\
 1 & 1 & 0 & -1 & 0 \\\\
 0 & 1 & 0 & 0 & 0 \\\\
\end{bmatrix}\quad \text{Check next possible matrix.} \\\\
\\\\
&\begin{bmatrix}
 0 & 1 & 0 & 1 & 0 \\\\
 1 & 1 & 1 & 0 & 0 \\\\
 1 & 0 & 0 & 1 & 1 \\\\
 1 & 1 & 0 & 0 & 0 \\\\
 0 & 1 & 0 & 0 & 1 \\\\
\end{bmatrix}-\begin{bmatrix}
 0 & 1 & 0 & 0 & 0 \\\\
 0 & 0 & 1 & 0 & 0 \\\\
 0 & 0 & 0 & 1 & 0 \\\\
 1 & 0 & 0 & 0 & 0 \\\\
 0 & 0 & 0 & 0 & 1 \\\\
\end{bmatrix}=\begin{bmatrix}
 0 & 0 & 0 & 1 & 0 \\\\
 1 & 1 & 0 & 0 & 0 \\\\
 1 & 0 & 0 & 0 & 1 \\\\
 0 & 1 & 0 & 0 & 0 \\\\
 0 & 1 & 0 & 0 & 0 \\\\
\end{bmatrix}\quad \text{A solution is found !}
\end{align}
$$

This method seems to work, but actually there are \\(55!\\) possible matrices in the enumeration, which brings a terrible storage burden.

Thus, we need a faster algorithm but not just enumeration. Let's reconsider our goal, we want to find every advisor's ID in anonymous review. If we put advisors' names into a set, anonymous reviews' ID into another set, our goal is to find a bijection from one set to another.

It is worth noting that, in graph theory, our problem can be regarded as finding a perfect matching in bipartite graphs (to find *all* perfect matching, refer this [paper](http://www.sciencedirect.com/science/article/pii/0893965994900450) after we can find *one* perfect matching). Fortunately, this problem is solved by [Hopcroft–Karp algorithm](https://en.wikipedia.org/wiki/Hopcroft%E2%80%93Karp_algorithm). (Hopcroft–Karp algorithm is used for searching *maximum cardinality matching*, however, we can check number of edges to find out perfect matchings)

The intuition of Hopcroft–Karp algorithm is that, after having a set of edges we matched, we try to increase the size ([cardinality](https://en.wikipedia.org/wiki/Cardinality)) of the set by 1, if success we update the set, until reaching the maximum cardinality. In each iteration, we need to look for an [augmenting path](http://mathworld.wolfram.com/AugmentingPath.html), which can be 'flipped' to increase the matching size by 1. To visualize Hopcroft–Karp algorithm, this [YouTube tutorial](https://www.youtube.com/watch?v=GPg8qqYWYCY) is helpful and easy to understand.

My implementation of Hopcroft–Karp algorithm in R is pushed to [GitHub](). However, it may not be implemented in an elegant way, I think there still exist some problems:
* I use adjacency matrix to represent graph, which has too much space cost. I realize (unfortunately when I nearly finish the program) that array of adjacency lists is a better data structure for this task (see chapter 4.1 in *[Algorithm](http://algs4.cs.princeton.edu/41graph/)*).
* It contains to many `for` and `if` statements, which is not precise and efficient enough. This problem may be partly caused by adjacency matrix I used.
* I try to implement the algorithm by creating and calling lots of functions, but I am not sure whether it is the best way.

Here is an example of my implementation:
```
source('C:/Users/Fangshu Gao/Desktop/Advisor/Hopcroft_Karp.R', encoding = 'UTF-8')
test1 <- matrix(c(1,1,0,0,1,0,1,
                  1,0,0,0,0,1,0,
                  0,1,0,0,0,0,0,
                  0,1,0,1,0,0,0,
                  0,1,0,1,0,0,0,
                  0,0,1,1,1,0,0,
                  0,0,0,0,0,1,0), nrow = 7, ncol = 7, byrow = TRUE)
test2 <- matrix(c(1,0,1,
                  0,1,0,
                  1,0,0), nrow = 3, ncol = 3, byrow = TRUE)
MMHK(test1)
> [1] 3 2 4 4 6 3 7 6 2 1 1 5
MMHK(test2)
> [1] 2 2 3 1 1 3
```

The result shows that matrix `test1` has a maximum cardinality matching \\( \\{(3,2),(4,4),(6,3),(7,6),(2,1),(1,5)\\} \\) and matrix `test2` has a perfect matching \\( \\{(2,2),(3,1),(1,3)\\} \\).

**Further reading:**
* An algorithm summary at Quora: [What are some known algorithms for finding a perfect match in a bipartite graph?](https://www.quora.com/What-are-some-known-algorithms-for-finding-a-perfect-match-in-a-bipartite-graph)
* A good [tutorial](http://www.csie.ntnu.edu.tw/~u91029/Matching.html) on matching.
* If you want to count the number of perfect matchings in a planar graph, this [paper](http://aarongorenstein.com/wp-content/uploads/2014/02/fkt.pdf) may help.
* Original [paper](https://pdfs.semanticscholar.org/7e8b/7a7cf3e4954fdacc36db60829ee620e77c96.pdf) of Hopcroft–Karp algorithm by [John Edward Hopcroft](https://en.wikipedia.org/wiki/John_Hopcroft) and [Richard Manning Karp](https://en.wikipedia.org/wiki/Richard_M._Karp).