---
title: "Biological Sequence Retrieval from a Computer Science Perspective"
published: false
---

Why am I writing this?
Want to understand, detect, and cure diseases
Biological sequences (DNA, RNA, proteins) provides some (but not all) valuable information underlying how organisms work and how they may break
Manipulating sequences is also useful for synthetic biology
Many workflows and experiments require storing and retrieving sequences as a building block
Developing bioinformatics tools requires broad knowledge in algorithms/data structures, stats/ML, sequencing technologies, biology, etc. Want more people to participate

Basic biology
DNA -> RNA -> protein
Splicing
Sequencing
Challenges: repeats, centromeres, telomeres, long indels, errors

Questions
How do I compare genetic variations between humans?
How to map out evolution of different species
Given an unknown sequence, how do I figure out if it has some known significance or properties
How can I compare gene expression (# RNA) over time or in different conditions?
Which versions of an alternatively spliced gene are present?
How can I figure out which parts of a chromosome are exposed and reachable?
Given a protein sequence, how can I find other proteins that potentially have the same function?
How can I verify if my gene editing worked?
How to determine which microbes are present in a sample?

Storing and retrieving sequences
Smith-Waterman
Seed and extend
Kmers, minimizers, FM-index
Further directions
Pangenomes, graphs instead of sequences
R-index
Hardware-aware optimizations, embedded systems
Embeddings, vector databases
