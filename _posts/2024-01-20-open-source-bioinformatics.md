---
title: "Funding Open Source Bioinformatics Software"
published: true
---

We need sustainable ways to fund quality open source bioinformatics software.

I'm not really sure what's the best solution (funding model), so I'll try to review
some of what's currently happening in the field.
I will mainly focus on genomics and biological sequence analysis software
since I’m most familiar with this area.

First, let's motivate *why* open source bioinformatics is important.

## Why open source software?
* Exists for a long time even if the original developers move on or go bankrupt.
* Benefits both academia and industry: people are not starting from zero for new projects,
which leads to faster iteration speed for the entire field.
* Modifiable to fit new use cases. Every lab or company seems to have their own custom data,
so there's rarely one-size-fits-all solutions.
* Aids the development of new methods by making it easier to build off of, experiment, and
compare against previous tools.
* Easy to use off-the-shelf software for now, then invest in tuning or customizing later.
* For employers: more likely to find people with existing experience.
* For employees: allows work to be "transferable" across employers.

## Why bioinformatics?
Most recent major scientific discoveries are tool-based *(TODO: find the source for this)*.
Bioinformatics software tools are needed to analyze the large amounts of data coming out of
genomic sequencers.
These tools have applications across metagenomics, pandemic monitoring, cancer biology, immunology,
verifying CRISPR edits, etc.
I also don't thinking we are going to get "foundational models" and "AI agents" in biology without foundational
bioinformatics tooling first.

## Status Quo
What is the state of open source bioinformatics now? Who contributes time and money?
Here's what I've seen:
* Scientists and PhD students in their free time, mainly as byproduct of their government grant funded
research projects.
* Chan-Zuckerberg Institute (CZI) gives out grants specifically for developing and maintain open source software.
This seems to go towards well-known academic labs.
* Hardware and instrument companies that want to demonstrate their devices:
    * Intel, which develops mm2-fast and bwa-mem2
    * Nvidia, which develops Clara Parabricks
    * Bioinformatics consulting companies, like Fulcrum Genomics
    * Sequencing companies, like Illumina, Oxford Nanopore, PacBio

Most of these companies seems to have developed more user-facing tools instead of libraries.
Libraries seem to be maintained by people (across industry and academia) on their own time.

*TODO: Seqera Labs, St. Jude's genomics infrastructure team*

I think academic labs do not provide the optimal environment to develop and maintain open source tools:
* PhD students need to publish
* Hard to hire good software engineers (maybe exception: Aaron Quinlan's lab)
* Prominent tool builders do not have enough bandwidth to maintain tools (Heng Li's bwa-mem)

I think simply throwing more money at academic labs may not work.

Which type of funding model could work?
* Grant funding specifically for open source software (government funding or CZI)
    * Possible to provide relatively stable job and career development for one or more software engineers?
* Open core startup
    * VCs need to make their money back
    * Nonzero interest rates is brutal
* Consulting or bootstrapped startup
* Get big biotech companies to pay employees that focus on open source
