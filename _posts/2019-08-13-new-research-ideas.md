---
title: "Coming up with new research ideas"
published: false
---

This post contains some notes on methods for obtaining research ideas, so I can remember to apply them when I am stuck.

I believe that working efficiently is more important than blindly throwing time at a project. For my research work, I spend a lot more time _thinking_ instead of _implementing_ (at least for projects that are not focused on reimplementing existing ideas). Also, I believe it is important to have solid programming and problem solving (competitive math/programming) fundamentals, so the bottleneck in the research process is in coming up with new ideas, not implementing the ideas. In terms of actually coming up with ideas, here are a few methods:

## 1. Working forwards

Credit for this idea goes to [Avi Srivastava](https://twitter.com/k3yavi) for pointing this out to me.

The idea is to first read existing research papers to closely examine previous work. Then, attempt to find details that they miss, or "future work" that they have not done yet. There should always be _something_ that the previous work misses, because researchers have limited time to put into their work.

The UMICollapse tool ([paper](https://www.biorxiv.org/content/10.1101/648683v2)) I created was based off of this idea. The insight was to speed up the slow pairwise UMI comparison step that was used in previous tools. This step is a bottleneck in the speed many UMI collapsing algorithms for grouping UMIs, but many previous works only focused on how to accurately group UMIs, not how it can be done efficiently. To ensure that the algorithms I introduce are novel compared to previous algorithms, I adapted the previous well-known algorithms to the UMI deduplication task specifically, by using different tricks based on the UMI grouping algorithms. This is another example of the idea of working forwards from previous ideas by adapting them for a specific domain. For more information on UMI collapsing tricks, read my [blog post](https://blog.liudaniel.com/n-grams-BK-trees) on it.

## 2. Working backwards

The idea is to examine the main research problem from a completely different angle that is not yet examined in a previous work. Usually, this involves defining a new metric. For example, if the previous work claims to achieve a high score under a certain metric, then come up with a different meaningful metric and attempt to get better results under this new metric.

My work on adversarial attacks in 3D space ([paper]()) is an example of defining new metrics. In addition to creating effective attacks against 3D neural networks, I examined alternative goals like the perceptibility of the adversarial perturbations to humans, the ease of construction of the adversarial examples in real-life, and the effectiveness of the attacks against defensive techniques. In this case, I came up with new ideas by asking myself, "what other desirable qualities of adversarial examples do we want?"

## 3. Diversity

When working on a research problem and its subproblems, especially through the working forwards method, try to find diverse, marginally related ideas and attempt to connect them to the research problem. Sometimes, this connection may be novel. Even if this connection proves fruitless, it may reveal an alternative approach to the problem. The marginally related ideas can be found through learning. In fact, when learning about new topics, I often think about connecting those new topics to each of the research problems I have tackled before.

To learn different ideas, I do the following in my spare time:
- Read relevant research subreddits on Reddit.
- Read Hacker News.
- Read tweets about blog posts and threads about new papers on Twitter.
- Watch videos that summarize recent research, like [Two Minute Papers](https://www.youtube.com/channel/UCbfYPyITQ-7l4upoX8nvctg) on YouTube.
- Skim a paper and then google things in the paper that I do not know, but seem promising. This usually involves reading Wikipedia to learn more.

Sometimes I come up with a random idea not exactly related to what I am working on, and then I google it to find if it has been done. Often times, someone else has already examined this idea. In this case, I can still learn a new concept that I can use in future work. Other times, if I am lucky, then I get a new connection between two different ideas that are related to my current research, and I can get a new algorithm or analysis technique out of it.

An example of this diversity method in action is how I came up with the adversarial sinks attack for 3D points ([paper]()). The general premise of the attack is to pull points on a 3D object towards sink points. This idea was inspired by black holes and how they attract other objects, but it was ultimately named after the source/sinks in network flow problems. I also came up with the idea of actually attempting to approximate the true shape of a 3D point set through alpha shapes, which stems from Delaunay triangulations that I considered for nearest neighbor searches in my bioinformatics work on finding similar DNA sequences.

Another example of this method is how I came up with using a domain-specific language for describing patterns while preprocessing DNA sequences ([paper](https://peerj.com/articles/7170/)). I knew that the string matching ideas for finding patterns were related to matching regular expressions, so I thought, "why not go one step further and make a full _language_?" Sometimes, generalizing further works well.