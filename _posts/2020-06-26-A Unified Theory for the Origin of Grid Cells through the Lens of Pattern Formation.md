---
layout: post
subtitle: Ben Sorscher, Gabriel C. Mel, Surya Ganguli, Samuel A. Ocko (2019)
author: Ho Yin Chau
---

I want to talk about this paper which absolutely captured my interest at one of the poster sessions during COSYNE 2020. It presents an elegant theory that explains why grid cells form hexagonal grids by showing that the pattern must emerge if the computational purpose of grid cells is to efficiently represent place cell activity. The idea that grid cells encode place information is not new, and studies have shown that grid-cell like responses arise by training RNNs to encode place information, but no one has been able to explain why grid cells form hexagonal, not, say, rectangular or octagonal patterns. What Sorscher et al. proved was that imposing a non-negative firing rate constraint on grid cells will force the patterns into a hexagonal formation, and the proof itself is very mathematically elegant. I personally think it lends a strong theoretical support for the hypothesis that grid cells efficiently encode place cell activity, and is one of the rare instances where a computationally-motivated theory aligns nicely with experimental data. Unfortunately this blog post will be rather technical in mathematical details, but hopefully even if you don't totally understand every step you can still get the intuition behind it.

The authors start with the premise that the computational objective of grid cells is to maximize the "reconstruction error" of place cell activity. They propose that grid cells try to maximize the following loss function:
\\[L = ||P-GW||\_F^2 \\]
Let me explain what this means. \\(P \in \mathbb{R}^{n_x \times n_p}\\) is a matrix that encodes all place cell activations. Each column of \\(P\\) represents the spatial firing pattern of one place cell. That is, we discretize the 2D spatial environment that the animal is placed in into \\(n_x\\) spatial locations, and each column records the activity of the place cell when the animal is at each of the \\(n_x\\) locations. \\(n_p\\) is the number of place cells. \\(G \in \mathbb{R}^{n_x \times n_g}\\) is a matrix that encodes all grid cell activations, and similarly each column of G represents the activity of one grid cell at the \\(n_x\\) spatial locations. \\(W \in \mathbb{R}^{n_G \times n_p}\\) is a matrix that encodes the connection strength from the grid cells to the place cells. \\(||\cdot||\_F^2\\) is the squared Frobenius norm, which is equal to summing over the square of all the matrix elements. The idea is depicted pictorially in Figure 1a in their paper:

<center>
<img src="/assets/images/grid_cells.png" alt="drawing"/>
</center>

We can think of grid cells as trying to predict or reconstruct place cell activity by linearly combining their spatial activity, and the reconstruction would be \\(\hat{P} = GW\\). The Frobenius norm over \\(P - \hat{P}\\) tells us how far the grid cell reconstructions are from the true place cell activities by summing up the squared error at every spatial location for all place cells.

Now comes the slightly confusing bit. In order to proceed with their derivation, they set W to be the W that maximizes the loss given fixed G, which is given by \\(W = (G^TG)^{-1}G^TP\\). Then their loss function becomes
\\[L = ||P-G(G^TG)^{-1}G^TP||\_F^2 \\]
I say this bit is confusing because my first thought is that this suggests the neuronal connections \\(W\\) change rapidly as grid cell activity changes, and this seems implausible. Thinking more carefully, however, I realize that \\(W\\) does not need to change. If we let \\((W_{opt}, G_{opt})\\) be the optimal weights and the optimal grid cell spatial firing patterns, so that \\(W_{opt} = (G_{opt}^TG_{opt})^{-1}G_{opt}^TP\\), then we can instead write the loss function as
\\[L = ||P-GW_{opt}||\_F^2 \\]
This loss function and the previous one will have the same global minimums. Thus minimizing this more realistic loss function will also minimize the previous loss function.

Now, imposing the constraint that \\(G^TG=I\\) (meaning the spatial activity of grid cells are orthogonal to each other) via Lagrange multipliers, and with some matrix manipulation, we arrive at the Lagragian (which we want to maximize, not minimize)
\\[\mathcal{L} = \mathrm{Tr}[G^T\Sigma G-\lambda(G^TG - I)]\\]
where \\(\Sigma = PP^T\\) is the covariance matrix of place cell spatial firing activity and \\(\lambda\\) (I believe) is actually a matrix. They did not give a derivation of this result in this paper, so I typed up a short proof <a href="/assets/images/short_proof.png" style="text-decoration: none">here</a>.

It turns out that \\(G\\) span the \\(n_G\\) eigenspaces of \\(\Sigma\\) with the largest eigenvalues. They gave a proof in Theorem B2 in the appendix with some fancy linear algebra theorems, but I would like to try to explain it somewhat intuitively. Given a Hermitian matrix \\(M\\) (or symmetric if \\(M\\) is real), we can define the so-called Rayleigh quotient as
\\[R(M,x) = \frac{x^H Mx}{x^Hx}\\]
The Rayleigh quotient is maximized when \\(x = v_1\\), the eigenvector of \\(M\\) with the largest eigenvalue. Intuitively, the Rayleigh quotient tests how well-aligned \\(x\\) is with \\(v_1\\): if we restrict \\(x\\) to be a 3-dimensional unit vector on the unit sphere, then the Rayleigh quotient is at its maximum as \\(x\\) points toward the direction of \\(v_1\\) and decreases as \\(x\\) moves away. This is a useful way of characterizing an eigenvector of a Hermitian matrix, and you probably already know about it if you have learned about the PCA procedure.

What about the eigenvectors corresponding to the second and third largest eigenvalues, \\(v_2, v_3\\), and so forth? Well, if we constrain \\(x\\) to be orthogonal to \\(v_1\\), then \\(R(M,x)\\) is maximized when \\(x = v_2\\), and if \\(x\\) is orthogonal to both \\(v_1\\) and \\(v_2\\), then \\(R(M,x)\\) is maximized when \\(x = v_3\\), and so forth. Hence, if \\(\\{x_i\\}\\) is a set of \\(n\\) orthogonal vectors, then \\(\sum_i R(M,x_i)\\) is maximized when \\(\\{x_i\\} = \\{v_1, v_2, \cdots, v_n\\}\\). All this, however, assumes that the eigenvalues are non-degenerate; in the case that there are degenerate eigenvalues, all we can say is that \\(\\{x_i\\}\\) spans the top \\(n\\) eigenvectors.

Now, back to our original problem, we note that the first term of the objective function can be written in the following way:
\\[\mathrm{Tr}[G^T\Sigma G] = \sum_{i} g_i^T \Sigma g_i = \sum_{i} \frac{g_i^T \Sigma g_i}{g_i^Tg_i} = \sum_i R(\Sigma,g_i)\\]
where we use the fact that we constrain \\(G\\) to be orthogonal, so that \\(g_i^Tg_i = 1\\), and the fact that \\(\Sigma = PP^T\\), so it is Hermitian (symmetric). As we have pointed out in previous paragraph, if the \\(g_i\\)'s are all orthogonal to each other, the set of \\(g_i\\)'s that maximize \\(\sum_i R(\Sigma,g_i)\\) would span the top \\(n_G\\) eigenvectors of \\(\Sigma\\), so we are done.

The top \\(n_G\\) eigenvectors of \\(\Sigma\\) are approximately a bunch of 2D plane waves. I don't think they gave a very satisfying explanation of why this is the case, and I don't think I can satisfactorily explain it here either without going on a very long tangent. However, this will be explained as part of a much more general phenomenon pertaining to locally compact group symmetries in the data in some future blog post. For now, just know that there are two conditions for this phenomenon to hold: first, that the spatial firing pattern of two place cells are approximately equivalent up to a 2D translation if we ignore boundary effects; and second, that the distribution of place cells is near uniform on the 2D plane. Under these two conditions, the eigenvectors of the covariance matrix \\(\Sigma\\) will be the eigenvectors that joint diagonalize the infinitesimal horizontal and vertical translation operators, which are just a bunch of 2D plane waves. 

Moreover, because of the rotational symmetry in the data, different 2D plane waves with the same frequency but pointing in different directions have the same eigenvalues. In the 2D Fourier domain, we can represent the plane wave with the point \\(\vec{k} = (k_1, k_2)\\), where \\(\vec{k}\\) is the wavevector of the plane wave. A circle in the 2D Fourier domain, which consists of all \\(\vec{k}\\) with the same length \\(\|\vec{k}\|\\), will then represent all those eigenvectors that have the same eigenvalue. These beautiful pictures from Figures 2A and 2B in their paper plot the eigenvalues as a function of \\(\vec{k}\\):

<center>
<img src="/assets/images/grid_cell_fourier_domain_eigenvalues.png" alt="drawing"/>
</center>

The left picture shows that when place cells have a Gaussian-shaped tuning, then the subspace with the largest eigenvalue is a small circle, which consists of plane waves with low-frequency. The right picture shows that when place cells have a difference of Gaussian tuning, then the top subpsace is spanned by mid-frequency plane waves.

We're getting close to the mystery of hexagonal grids. As mentioned, we want the columns of \\(G\\) to span the top \\(n_G\\) eigenvectors of \\(\Sigma\\). Let's think about how to span the top subspace first. Because of discretization, the top subspace will consist of a finite number of eigenvectors \\(\\{v_1, \cdots, v_n\\}\\) (in the limit of inifinite number of neurons we will have infinite number of eigenvectors).The easiest way to span this space is to assign \\(n\\) grid cells such that \\(g_i = v_i\\). But any linear combination of \\(\\{v_1, \cdots, v_n\\}\\) also works, as long as we respect the orthogonality constraint on \\(G\\). More formally, if we let \\(V \in \mathbb{R}^{n_x \times n}\\) such that the i-th column of \\(V\\) is \\(v_i\\), then \\(G = VU\\) is also a valid choice of grid cells, where \\(U\\) is a unitary matrix. Notably, if we let a grid cell be a linear combination of three plane waves \\(v_1, v_2, v_3\\) that lie on the ring such that the corresponding wavevectors \\(k_1, k_2, k_3\\) form an equilateral triangle, then that grid cell will have a hexagonal grid spatial firing pattern. Figure 2F from the paper demonstrates this idea:

<center>
<img src="/assets/images/hexagonal_grid.png" alt="drawing"/>
</center>

Now here comes the key insight. If we add a new term \\(\sigma(G)\\) to the loss function, and if the cubic term in the Taylor expansion of \\(\sigma(G)\\) around \\(0\\) is non-zero, then instead of using an arbitrary combination of \\(\\{v_i\\}\\) to span the subspace, the system will be biased towards using linear combinations of three plane waves \\(v_1, v_2, v_3\\) such that \\(k_1, k_2, k_3\\) form an equilateral triangle, so that we have hexagonal grid patterns. The \\(\sigma(G)\\) term is easily justified: since firing rate is non-negative, we set a penalty on negative \\(G\\) by letting, for example, \\(\sigma\\) be the ReLU function (I know it's not differentiable at 0, so let's say we smooth it out a little at 0).

Let's look at the case where there is only one grid cell, so we can replace \\(G\\) by a vector \\(g\\). With our new term \\(\sigma(g)\\), the loss function becomes
\\[\mathcal{L} = g^T\Sigma g + \lambda (1-g^Tg) + \sigma(g)\\]
Let us also turn vectors and matrices into continuous functions, replacing \\(g_\vec{x}\\) by \\(g(\vec{x})\\) and \\(\Sigma_{\vec{x},\vec{x}'}\\) by \\(\Sigma(\vec{x} - \vec{x}')\\). Then the loss becomes a linear functional:
\\[\mathcal{L}[g(\vec{x})] = \iint_{\vec{x}, \vec{x}'} g(\vec{x})\Sigma(\vec{x} - \vec{x}')g(\vec{x}') + \lambda(1 - \int_{\vec{x}} g^2(\vec{x})) + \int_{\vec{x}} \sigma(g(\vec{x}))\\]
We can Taylor expand \\(\sigma(g)\\) around \\(0\\) to get \\(\sigma(g) = \sigma_0 + \sigma_1g + \sigma_2g^2 + \sigma_3g^3\\) and then perform Fourier transform:
\\[
\tilde{\mathcal{L}}[\tilde{g}(\vec{x})] = \int_{\vec{k}} |\tilde{g}(\vec{k})|^2\tilde{\Sigma}(\vec{k}) + \lambda(1 - \int_{\vec{k}} |\tilde{g}(\vec{k})|^2) +
[\sigma_0 + \sigma_1\tilde{g}(\vec{0}) + \sigma_2 \int_{\vec{k}} |\tilde{g}(\vec{k})|^2 + \sigma_3 \iiint_{\vec{k},\vec{k}',\vec{k}^{\prime \prime}} \tilde{g}(\vec{k})\tilde{g}(\vec{k}')\tilde{g}(\vec{k}^{\prime \prime})\delta(\vec{k}+\vec{k}'+\vec{k}^{\prime \prime}))
\\]
They didn't provide a proof, so I typed it up <a href="/assets/pdf/fourier_proof.pdf" style="text-decoration: none">here</a>.
The triple interaction term is the key. The integrand is nonzero when \\(\vec{k}+\vec{k}'+\vec{k}^{\prime \prime} = 0\\). The only such combination when each \\(\vec{k}\\) is constrained to lie on the ring is that the three wavevectors form an equilateral triangle. We clearly want \\(\tilde{g}(\vec{k})\tilde{g}(\vec{k}')\tilde{g}(\vec{k}^{\prime \prime})\\) to be as large as possible, and along with the orthogonality constraint on \\(g\\), the \\(\tilde{g}\\) that maximizes the triple interaction term is
\\[\tilde{g}(\vec{k}) = \frac{1}{\sqrt{6}}\sum_{i=1}^3 (\delta(\vec{k} - \vec{k}\_i) + \delta(\vec{k} + \vec{k}\_i))\\]
Transforming back to the spatial domain, we have 
\\[g(\vec{x}) = \frac{1}{\sqrt{6}}\sum_{i=1}^3 (e^{i\vec{k}\_i \cdot \vec{x}} + e^{-i\vec{k}\_i \cdot \vec{x}}) = \frac{2}{\sqrt{6}}\sum_{i=1}^3 \cos(\vec{k}\_i \cdot \vec{x})\\]
which is, as claimed, a linear combination of three plane waves with the three wavevectors forming an equilateral triangle, and hence is a hexagonal grid.

As an epilogue, the authors also show that the gradient of the loss function with respect to \\(G\\) yields the dynamics of typical continuous attractor model of grid cells. I think this is neat, but not particularly surprising, since it has long been known that minimizing L2 loss with non-negativity cost yields these types of dynamics (for example, see the <a href="https://www.ece.rice.edu/~eld1/papers/Rozell08.pdf" style="text-decoration: none">LCA</a> algorithm). 