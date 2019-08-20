---
title: "The birth of a new subsubfield: adversarial robustness on 3D point sets"
published: false
---

An interesting phenomenon with neural networks is that it is incredibly easy to perturb or change the input by a little amount, and cause the network to make a completely different prediction. In general, this field of adversarial machine learning contains two main unsolved problems:

- How can we attack neural networks with a bounded amount of change/perturbation to the input, under a certain metric?
- How can we (empirically or provably) defend neural networks against adversarial attacks?

Currently, this is a perpetual tug of war between the attacker and the defender. There is a lot of work on both attacking and defending, particularly because coming up with an efficient and provably robust defensive method against all attacks is very difficult.

Even in the rapidly expanding field of machine learning, there are stones left unturned. In the short timespan of one year, I was fortunate enough to contribute to the birth of a new subsubfield examining the adversarial robustness of neural networks that learn from 3D point sets. This was incredibly exciting---I got to witness the development and maturation of ideas in a field with many opportunities by participating _first-hand_!

In this post, I will highlight some interesting ideas born through the intersection of 3D machine learning and adversarial machine learning. This will mostly cover the two papers ([first](https://arxiv.org/abs/1901.03006) and [second](https://arxiv.org/abs/1908.06062) papers) I contributed to the field along with my mentors Ronald Yu and Hao Su, and also some work by groups all over the world. I will also provide evaluations of the plausibility and effectiveness of different ideas, especially for defending against adversarial attacks, which is prone to unintentional errors.

# Name of the game

Before we dive into the ideas for attacking and defending 3D neural networks, let us examine what kind of ideas we want to extract in this field. Since we are examining 3D point clouds and neural networks built especially for them, we want to create attacks and defenses that are native to 3D point clouds. We can create attacks and defenses that are universal to all types of inputs, but then we would have to "compete" with previously proposed ideas. If we merely apply ideas that were used for 2D images, which are studied more thoroughly, to 3D space, then we are not bringing major contributions to the table. Therefore, we want to exploit the special properties of both 3D point clouds and the point cloud neural networks in our journey of attacking and defending.

Now, let us go over a little background on 3D point clouds and their neural networks.

## Point clouds

A 3D point cloud provides an approximation of a 3D object's shape. This shape is only the boundary of a 3D object, so it is hollow. If we represent the true shape as an infinite set of points \(S\), then we have \(x \subset S\) for a point cloud \(x\). For simplicity, we will assume that \(x\) is evenly sampled from the true shape. When point clouds are obtained through scanning objects in the real world (LiDAR and RGB-D scans, or photogrammetry), they are usually uneven and partially occluded.

3D point clouds differ from 2D images in a few ways:
1. The order of points does not matter.
2. Shape, instead of color, is represented. Along with the assumed even sampling of points, this means that there is exploitable structure in 3D point clouds.
3. Randomly moving the points by a small amount, dropping a few points, and adding a few points on the true shape should still represents the same object.

## Learning in 3D space

The challenge of learning in 3D space is to deal with the order and density invariance of point clouds. The general idea of the popular PointNet architecture is to apply the classical multi-layer perceptron (multiplication by weight matrix) for each point _separately_. From the three color channels of each point, we use multiple layers of matrix multiplication to obtain 1024 features. Then, max pooling is applied elementwise across the 1024-dimensional feature vectors of each point. Since the max operation is symmetrical, this is invariant to the order of points in a point set. Many other architectures basically just extend this idea with subsampling and other ideas.

# Basic attacks in 3D

The most straightforward attack is by using gradient descent to directly perturb the position of each point. We are essentially solving the following optimization problem for a point cloud \(x\):

\begin{align}
&\text{maximize}& &J(f_\theta(x + \delta), y)
&\text{subject to}& &||\delta||_p \leq \epsilon
\end{align}

\(J(\cdot, \cdot)\) represents the loss function, \(y\) is the label class, and \(f_\theta\) is a neural network parameterized by \(\theta\). Notice that the perturbation \(\delta\) is bounded by \(\epsilon\) under the \(L_p\) norm, and we do not clip \(x + \delta\) because unlike 2D image color channels, the positions of 3D points are unbounded. \(\epsilon\) also provides a way for us to measure the perceptibility of adversarial perturbations.

Just like with 2D images, this method works very well for generating adversarial attacks. Here are visualizations of adversarial perturbations (bounded with the \(L_2\) norm) on a car and a person from the ModelNet40 dataset:

![]()

![]()

The perturbed points are orange.

# Defense: removing outliers

Right away, we notice that constraining the perturbations using the \(L_2\) norm results in some points being perturbed more than others, and those points become outliers in the point set. Therefore, a simple defense would be to just remove those outlier points. A common method for identifying outliers is based on each point and its \(k\)-nearest neighbors. Finding points that are outliers is done by examining the distribution of distances between each point and its nearest neighbors. Afterwards, the outlier points that are too far away from its \(k\)-nearest neigbors are removed from the point set.

Removing outliers is actually very effective as a defense---it performs much better than adversarial training, a classical defense that involves teaching a neural network the correct labels for adversarial examples. In practice, removing outliers works well even if we constrain the amount of perturbation to each _point_ by an \(\epsilon\) so that large perturbations are not possible.

Interestingly, this method was proposed in parallel in many different papers on defending against adversarial attacks on point clouds. I guess everyone noticed the outliers generated by adversarial perturbations.

# Defense: removing salient points

Since we perturb points by their gradients, it makes sense to remove adversarial points by examining their gradients to hopefully restore the point cloud of an object. The idea is to first calculate the saliency of each point (at index \(i\) in \(x\)) through

\[
s[i] = \max_j ||(\nabla_{x^\ast} f_\theta(x^\ast)[j])[i]||_2
\]

Then, we can sort the points by their saliencies and remove points with high saliencies. In other words, points that have large saliencies, which are the magnitudes of the gradient of each output class with respect to each point, are removed. In practice, this works well as a defense, and it performs better than adversarial training. This defense avoids the issue of being unable to identify adversarial points if there are no outliers, but we are making the assumption that points with large magnitudes of gradients are perturbed.

# Defense: limitations

The reason why removing points performs so well is actually due them relying on gradient masking. The max pooling operation in PointNet ignores a set of points, which causes them to not get any gradient information. These points cannot be perturbed with gradient-based methods, so they can accurately represent the unperturbed shape of a point cloud once the perturbed points are removed. However, relying on masked gradients does not lead to robust models, as we will see in the shape attacks that are proposed later.

# Minimizing the perceptibility of perturbations



# Shape attacks

## Resampling



## Sticks



## Sinks



# Removing points as an attack



# Conclusion

Defending against adversarial attacks in 3D space is quite hard.