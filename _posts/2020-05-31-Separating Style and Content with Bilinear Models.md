---
layout: post
subtitle: Joshua B. Tenenbaum, William T. Freeman (2000)
author: Ho Yin Chau
---

In this well-known paper, Tenenbaum and Freeman developed a bilinear model (I will explain bilinear models later) that can separate the "style" and "content" of images. What is meant by "style" and "content"? Using handwritten letters for example, the content would be the letters being written - "a" to "z" - and the style would be how the letters are being written - cursive, typed, comic sans (yikes). More generally, different styles refer to different factors of variations, or transformations, that do not change object identity, and the problem of how to separate the different factors of variations from the invariant objects is an important machine learning problem.

So what are bilinear models? A bilinear map \\(f\\) is a vector-valued function that takes as its inputs two vectors \\(a, b\\) such that \\(f\\) is linear in both \\(a\\) and \\(b\\), meaning \\(f(\alpha a+\beta b) = \alpha f(a) + \beta f(b)\\) for scalars \\(\alpha, \beta\\). A bilinear model models images as a bilinear map, so that if we let \\(y \in \mathbb{R}^{K}\\) to represent an image with \\(K\\) pixels, then there is some bilinear map \\(f\\) such that for all images \\(y\\) in the dataset, \\(y = f(a,b)\\) for some vectors \\(a,b\\). 

In the case of this paper, \\(a \in \mathbb{R}^I\\) is a vector that encodes the "style" of the image and \\(b \in \mathbb{R}^J\\) is a vector that encodes the "content" of the image. Because of bilinearity, we can write the image \\(y\\) as a linear combination of \\(IJ\\) number of basis images (sometimes also called "templates" or "dictionary elements") \\(w_{ij} \in \mathbb{R}^K\\):
\\[y = \sum_{i=1}^I\sum_{j=1}^J a_ib_j w_{ij}\\]

To demonstrate the idea, Tenenbaum and Freeman used a small dataset of faces to learn these basis images, with \\(I = 4\\) and \\(J = 3\\), shown below:
<center>
<img src="/assets/images/face_basis_images.png" alt="drawing"/>
</center>
The top-left image corresponds to the basis image \\(w_{11}\\), the bottom-left \\(w_{41}\\), the top-right \\(w_{13}\\), and so forth. A 4-dimensional style vector \\(a\\) and a 3-dimensioanl content vector \\(b\\) then assigns the weight \\(a_ib_j\\) to basis image \\(w_{ij}\\) and sums all the basis images up to produce the resulting image.

Below shows what the images look like when we choose different values of \\(a\\) and \\(b\\).
<center>
<img src="/assets/images/face_reconstructions.png" alt="drawing"/>
</center>
Along each row, the same style vector \\(a\\) is chosen, but the content vector \\(b\\) varies. As a result, the face identity changes from left to right but the pose remains the same. Along each column, the style vector varies but the content vector is fixed, and as a result the pose varies but the face identity is fixed. It demonstrates how the pose and identity of the faces are separated in this model.

What I described above is the so-called "symmetric model" in the paper. There is a variant called the "asymmetric model" in the paper in which each style has its own set of basis images, represented by a \\(K \times J\\) matrix \\(A\\), and we create an image by choosing a specific style and then taking a linear combination of the corresponding basis images with a content vector \\(b \in \mathbb{R}^J\\). In other words, we can write the image as
\\[y = Ab = \sum_{j=1}^J A_j b_j\\]
where the set \\(\\{A_j\\}\_{j=1}^J\\) are the basis images corresponding to the chosen style.

In the symmetric model, the goal of the algorithm is to learn the basis images \\(w_{ij}\\), assign a style vector \\(a^s\\) to each style \\(s\\), and assign a content vector \\(b^c\\) to each content \\(c\\). In the asymmetric model, we want to assign a set of basis images \\(A^s\\) for each style \\(s\\) and a content vector \\(b^c\\) to each content \\(c\\).

Training requires a labelled dataset. The style and content of each image in the dataset is already known. This is in contrast to most state-of-the-art research in separating factors of variation, in which the style and content of each image is unknown, which leads to a much harder problem. The algorithm minimizes the L2 loss of the reconstruction of each image with some matrix decomposition techniques.

The authors tested their algorithm with the following experiment. They first trained their asymmetric model on the letters within the gray rectangle, then showed the model the letters "J" through "M" in a previously unseen style, and finally asked the model to produce the letters "A" through "I" using the new style.

<center>
<img src="/assets/images/letters.png" alt="drawing"/>
</center>

During the training phase, the model learns the basis images for each style \\(A^s\\) and the content vectors \\(b^c\\). Then, when given the letters in the new style \\(\tilde{s}\\), the model computes the new basis images \\(A^{\tilde{s}}\\) and multiplies it with the content vectors that it has already learned \\(b^c\\) to get the rest of the letters in the new style. The algorithm works remarkably well: the newly synthesized images look pretty similar to the actual images.