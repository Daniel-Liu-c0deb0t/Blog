---
title: Speeding up the UMI deduplication task with the n-grams BK-trees data structure
published: true
---

Inventing a new algorithm or data structure is often not just one major insight, but a collection tricks that work together to produce better results. With that said, what kind of "better results" are we looking for? For computer scientists, this question often results in three answers: higher accuracy, faster speed, or lower memory footprint.

In a recent [paper](https://www.biorxiv.org/content/10.1101/648683v2) ([code](https://github.com/Daniel-Liu-c0deb0t/UMICollapse)), I explored the problem of efficiently deduplicating, or collapsing, sequenced DNA reads with Unique Molecular Identifiers (UMIs).

# A bit of background
Each UMI is a short, unique random sequence that corresponds to a certain DNA fragment. When PCR amplification is applied DNA fragments before sequencing, each DNA fragment is duplicated multiple times. UMIs allow us to figure out which sequenced reads are duplicates by grouping reads through their UMIs. The reason why we do not group using the DNA fragments themselves is because there may be multiple copies of the same DNA fragment before PCR amplification, and we want to be able to accurately count the duplicate DNA fragments. UMIs are applied in many experiments, including single cell RNA sequencing.

As with many bioinformatics tasks that involve processing sequenced reads, the difficulty lies in handling sequencing and PCR amplification errors. Therefore, the deduplication task is just somehow grouping similar UMIs together, where similarity is defined by counting the number of mismatches between two UMI sequences. Since UMIs are used very often, this task has been thoroughly explored. Most notably, the "directional adjacency" method from [UMI-tools](https://genome.cshlp.org/content/early/2017/01/18/gr.209601.116.abstract) involves first obtaining the frequency of each unique UMI, and then grouping low frequency UMIs with high frequency UMIs. The main intuition behind this is idea is that UMIs that appear frequently have a very high chance of being correctly amplified and sequenced, while UMIs that appear less frequently are most likely wrong. This algorithm is discussed in more detail [here](https://cgatoxford.wordpress.com/2015/08/14/unique-molecular-identifiers-the-problem-the-solution-and-the-proof/). An image comparison of different grouping algorithms, from that blog post:

![UMI-tools grouping algorithms](../assets/UMI_tools_grouping_methods.png)

Consider what happens when we have a high frequency UMI. We need to quickly find UMIs that are similar (connected with an edge in the diagram above) and have a lower frequency. Then, we group those lower frequency UMIs together with the high frequency UMI and we assume that the corresponding sequenced reads for the grouped UMIs all originate from the same DNA fragment.

# Improvements?

Applying the three things that computer scientists care about, we know that we can basically make three improvements to this process: make it more accurate, speed it up, and lower the memory footprint. I considered improving the accuracy with a better algorithm than directional adjacency, but it is difficult without biological insights and actual experience. Therefore, I settled on attempting to improve the other two areas while ensuring that we do not make any compromises on the accuracy of the directional adjacency algorithm. However, lowering the memory footprint is kind of pointless since we have to store all of the sequenced reads in memory no matter what, and most computers have a ton of memory anyways. Thus, the only option is to improve the speed of deduplication process.

The first step is identifying the bottleneck. There are two main steps in the directional adjacency algorithm: building the graph of UMIs and extracting groups of UMIs. Since each UMI can only belong to one group, then it is easy to see that extracting groups of UMIs from the graph only takes time linear to the number of unique UMIs. Building the graph naively requires comparisons between each pair of unique UMIs, which scales _quadratically_ with the number of UMIs. It turns out that many tools made for grouping UMIs use the naive method for building the graph, which leaves a lot of room for improvement.

# n-grams BK-trees: a medley of tricks
## Trick 1: n-grams
## Trick 2: BK-trees
## Trick 3: prune by frequency
## Trick 4: literally extract UMIs
## One last trick: special encoding