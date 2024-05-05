---
title: "Some Notes on Practical Pairwise Sequence Alignment"
published: false
---

This will cover general techniques and approaches. For a more comprehensive overview, especially
on the historical developments, see Ragnar Groot Koerkamp's blog [post](https://curiouscoding.nl/posts/pairwise-alignment/).

To be more precise the problem is to compute Smith-Waterman-like dynamic programming efficiently in terms of
both memory and runtime.
This is useful for many downstream applications that involve comparing sequences, including variant calling, assembly,
database search, etc.
In practice, different applications will require different tradeoffs for the pairwise alignment algorithms.
The lengths of sequences, the types and frequences of edits, the scoring matrices (DNA, protein, affine, double affine, etc.) all affect
how the algorithm should be designed.

## Computing the DP matrix
Unless otherwise noted, I'll be focusing on global alignment.
There's two main ways to compute and represent the DP matrix:
1. Compute the alignment score up to prefix $$i$$ of sequence $$a$$ and prefix $$j$$ of sequence $$b$$.
This is the most common way I've seen. Examples of algorithms that does this include SSW, Edlib, KSW2, Block Aligner, etc.
2. Iteratively increment a cost threshold and extend diagonals to compute the furthest reaching point for each diagonal reachable
within the threshold. Examples of algorithms that does this include WFA/BiWFA.

It's important to note that you can either use *scores* or *costs*. For scores, matches are positive while mismatches are negative,
so this is a problem of maximizing the score.
For costs, matches are zero while mismatches are positive, so this is a problem of minimizing the cost.
Edit distance is a specific cost model where substitutions, insertions, and deletions all have a cost of 1.
For global alignment, in many cases it is possible to convert an aligner that uses a cost model to use a score model, and vice versa.

It's possible to use Dijkstra's algorithm to explore DP cells in order of distance to the start (under a cost model),
similar to the second way.
However, naively maintaining a priority queue is too much overhead compared to the amount of computation per DP cell.
Therefore, this way is not used in practice. Nevertheless, the connection to shortest path algorithms under a cost model is useful.

## First way
To speed up the first way under a cost model and when the cost is bounded, the computation can be bounded by a
band centered around the main diagonal of the DP matrix. This can be taken further by pruning cells when their
cost value (distance) is larger than the bound/threshold or when the cost of indels to reach the bottom-right corner
of the DP matrix is too large compared the the cost bound.
This idea of looking to the "future" to prune DP cells can be taken to its limits by using A\*.
This is done in A\*PA/A\*PA2, with an appropriate heuristic based on seed matches and good engineering to make
evaluating this heuristic efficient.

If the cost is not bounded, then a small initial cost can be used as the bound, and then this bound can be doubled after
every iteration, until the alignment is able to reach the bottom-right DP cell. In each iteration, the alignment restarts from
the top-left corner of the DP matrix, but it is possible to carefully reuse some overlapping DP cells with incremental doubling.

In practice, these DP cell computations can be parallelized using SIMD, which processes multiple integers in a vector simultaneously.
These SIMD vectors can be tiled antidiagonally, which eliminates all dependencies between DP cells within a vector, as rows or columns,
which requires a prefix scan to resolve dependencies between DP cells within a vector, or with the striped profile, which allows delaying
dependency resolution to once per row or column. When designing alignment algorithms, it is better to compute in blocks consisting of many
SIMD vectors tiled, which reduces control flow and maximizes performance. Move any heuristic evaluation, pruning, etc. outside of block
computation. Also, although antidiagonal tiling minimizes dependencies, it may be more cumbersome to implement or synergize with other
algorithm techniques. In those cases, row/column tiling or very careful striped tiling may be preferable.

SIMD parallelism is maximized when narrow integers are used for each DP cell. 32-bit integers ("lanes") is the simplest that works
for nearly all use cases, however it is possible to store the difference between DP cells using 8-bit integers
(the "difference recurrence" formulation). A simple alternative is to compute in large blocks, store one 32-bit integer per block,
and represent all the DP cells using 16-bit integers relative to that 32-bit value.

## Second way
The second way for computing the DP matrix is very fast for similar sequences.
This is because when computing the furtherest reaching point along each diagonal, consecutive matches can be
efficiently identified using SWAR or SIMD.
On the other hand, as sequences become more divergent, this will become slower due to the lack of consecutive matches
and the cache misses from examining the furthest reaching points for different diagonals.
Additionally, lengths along each diagonal have to be stored as the furthest reaching points, which requires 32-bit integers in
SIMD vectors.

In WFA, the number of extra DP matrices scales with the costs.

## Traceback
