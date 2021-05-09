---
layout: page
title: Research
permalink: /research/
---


<div class="container">
<div class="leftpane">
	<img src="/assets/images/operator_visualization.png" alt="drawing"/>
</div>
<div class="rightpane" markdown="1">
<p class="title">Disentangling images with Lie group transformations and sparse coding</p>
<p class="body" markdown="1" style="font-size: 80%">How do humans learn to recognize transformations in the visual scene, such as rotation and scaling, that are independent of object identities? The question of how to form representation of images that disentangle visual transformations from objects is of interest both to neuroscientists and computer scientists. Rather than using neural networks like VAEs with some additional loss, we developed a Bayesian generative model that is capable of learning the continuous transformations as well as discrete patterns in images in an unsupervised manner. The model, which is a direct extension of Cohen and Welling's <a href="http://proceedings.mlr.press/v32/cohen14.html" style="text-decoration:none">work</a>, uses the representation theory of Lie groups (specifically, the n-dimensional torus \\(\mathbb{T}^n)\\) to parameterize the transformations in a computationally tractable form.
</p>
<a href="https://github.com/hchau630/LSC" style="text-decoration: none">Code</a> &nbsp;&nbsp;
<a href="https://arxiv.org/abs/2012.12071" style="text-decoration: none">Preprint</a>
</div>
</div>

<div class="container">
  <div class="leftpane">
  	<img src="/assets/images/model_decentralized.png" alt="drawing"/>
  	<img src="/assets/images/wc1_reorder.png" alt="drawing"/>
  	<img src="/assets/images/population_response_s2.png" alt="drawing"/>
  </div>
  <div class="rightpane">
  <p class="title">Emergence of opposite neurons in a firing-rate model of multisensory processing</p>
  <p class="body" style="font-size: 80%">Multisensory integration areas such as dorsal medial superior temporal (MSTd) and ventral intraparietal (VIP) areas in macaques combine visual and vestibular cues to produce better estimates of self-motion. Congruent and opposite neurons, two types of neurons found in these areas, prefer congruent inputs and opposite inputs from the two modalities respectively. We created a firing-rate model of multisensory processing to explain how the unique tuning properties of opposite neurons may arise from biologically realistic synaptic learning mechanisms. This research may have broader implications about how the brain learns to perform causal inference - the ability to decide which of the competing explanations for sensory inputs is the most likely one - as opposite neurons have been hypothesized to be involved in causal inference in multisensory processing.
  </p>
  <a href="https://github.com/hchau630/opposite-neurons" style="text-decoration: none">Code</a> &nbsp;&nbsp;
  <a href="https://www.biorxiv.org/content/10.1101/845743v3.full" style="text-decoration: none">Preprint</a> &nbsp;&nbsp;
  <a href="/assets/pdf/operator_visualization.pdf" style="text-decoration: none">Poster</a>
  </div>
</div>
