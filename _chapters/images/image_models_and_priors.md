---
title: Image models and priors
keywords: Image Modeling, Bayesian Modeling, Pixel Distributions
order: 7 # Lecture number for 2020
---


This lecture discusses statistical image models (i.e. models that rely on statistics). The table of contents for this lecture includes:


- [Statistical Image Modeling](#statistical-image-modeling)
- [Pixel Level Models](#pixel-level-models)
	- [Introduction](#introduction)
	- [Bayesian Approach to Modeling](#bayesian-approach-to-modeling)
	- [Isolated Pixel Intensities Model](#isolated-pixel-intensities-model)
	- [Pixel Intensity Covariance Model](#pixel-intensity-covariance-model)
- [Patch Level Models](#patch-level-models)
- [Non Parametric Models](#non-parametric-models)
	- [Pattern Matching](#introduction)
	- [Non Parametric Sampling Approach](#non-parametric-sampling-approach)


<a name='Statistical Image Modeling'></a>
## Statistical Image Modeling
	
This first section introduces the idea of statistical image models by considering how they relate to human perception. When we look at images, we heavily rely on our prior knowledge of the world in order to understand them. For instance, using our prior knowledge, we are able to (1) separate images into components, such as the real-world object and noise, as shown in Figure 1, (2) differentiate between texture and noise in an image, and (3) decide which of several possible scenarios the image is most likely representing, as shown in Figure 2. Therefore, we can see that much of our perception of images relies on the prior knowledge and assumptions that we make. In the rest of this lecture, we will talk about how we can build models that capture those assumptions.

<div class="fig figcenter fighighlight">
  <img src="{{ site.baseurl }}/assets/images/fig1.png">
  <div class="figcaption">Separating image into components</div>
</div>

<div class="fig figcenter fighighlight">
  <img src="{{ site.baseurl }}/assets/images/fig2.png">
  <div class="figcaption">Multiple possible scenarios</div>
</div>

<a name='Pixel Level Models'></a>
## Pixel Level Models

<a name='Introduction'></a>
### Introduction
When we attempt to **statistically model** an image, we must first note that what we are seeing (the **luminance** of the image) comes from the product of two variables: **reflectance** and **illumination**. In this section, we will assume that these variables are pixel-specific, so that each pixel (x,y) has reflectance R and illumination I. From here on, we call these variables **L**, **R**, and **I** respectively. One way that we can visualize this is through imagining that $L$ is equal to a fixed constant, like 1. Then, we have that $R * I = 1$. Our puzzle is to figure out the optimal values of $R$ and $I$ so that we can model an image, without knowing either of their true values. 

<a name='Bayesian Approach To Modeling'></a>
### Bayesian Approach To Modeling
One way that we can look at the seemingly-unsolvable problem $R * I = 1$, where we have two unknowns in one equation, is through a Bayesian lens. Our ultimate goal is to calculate $R$ and $I$ to maximize the equation $P(R,I | L = 1)$. 
Through a Bayesian lens, we can think of this equation as a posterior probability, where we observe that the Luminance is equal to 1 and now must find the Reflectance and Illumination values that maximize the probability of this Luminance occurring. Then, by **Bayes' Formula**, we can transform this posterior probability into a product of a likelihood function and a prior probability. 
Formally, $$P(R, I | R * I = L = 1) = k * P(R * I = L = 1 | R, I) * P(R, I)$$ where $k$ is some constant. 
As an example likelihood function, we can take: \\[ P(L = 1 | R, I) = k * e^{\frac{-(1 - R * I)^2}{2 * \sigma ^2}} \\] This example likelihood function is basically measuring how close the product of $R$ and $I$ is to $L = 1$; as it gets closer, the likelihood gets higher, and vice versa.
As an example prior function, we can take: \\[ P(R, I) = k * e^{\frac{-(R - I)^2}{2 * \sigma ^2}} \\] This example prior is basically measuring how close our values of Reflectance and Luminance are to each other.
Overall, the Bayesian Approach that we introduced here is one important method to **statistically model** an image.

<a name='Isolated Pixel Intensities Model'></a>
### Isolated Pixel Intensities Model
The **Isolated Pixel Intensity** model focuses on building an Image Prior based on individual pixels.
We perform the following steps to build this Image Prior for a given image $I$:
1. Mask the entire image except for one pixel (call it I(x,y)).
2. Calculate our prior function of this pixel based on its values (e.g. calculate $p(I(x,y))$, where $p$ is our prior function and I(x,y) is a given image pixel).
3. Repeat this process for every pixel in the image.
4. Calculate the product of all these values; the resultant product will be our Image Prior, or $p(I)$.
Formally, \\[ p(I) = \prod_{x,y} p(I(x,y))\\]
This is a very simplified image prior, and makes the following assumptions:
- All pixels are independent of one another
- Pixel distributions are equal across all image positions; e.g. pixel intensities do not depend on image position

These assumptions are often incorrect in practice, and thus can cause our prior to err.
- For example, if pixels are very close to each other, then their intensities are likely to be very close or even the same; as pixels move farther and farther apart, though, they're much less likely to have similar intensities.

<a name='Pixel Intensity Covariance Model'></a>
### A Second Model: Pixel Intensity Covariance Model
The **Pixel Intensity Covariance** model takes our previous work a step further, and models the covariance between two different pixels.
Covariance is a measure of how pixel intensities vary together; we find that if pixels are close together, their intensity values are highly correlated (and in many cases likely to be the same), but if they aren't close together, their intensity values really aren't correlated at all. This is exactly one of the problems that we identified with our last model!
- One important note is that there is often a difference between correlation with vertical pixel difference and horizontal pixel difference. We normally see that pixels with a large horizontal difference, but little vertical difference, retain some correlation in intensity values, while pixels with a large vertical difference and little horizontal difference display little to no correlation in intensity values. This is often attributed to the horizontal layer structure of images in the natural world, and indeed our perception of what we see; we display a rudimentary prior that images at the same height tend to have similar properties.


<a name='Patch Level Models'></a>
## Patch Level Models
We want to be able to build statistical models for small neighborhoods, not just one or two pixels. 
To do this, we look at patches in an image. 
When we model the frequencies of pixel intensities for raw images, it is  difficult to extract useful information. However, if we convolve a raw image with a filter like [-1 1], we get a sparse response centered at 0. This is because neighboring pixel often have similar colors, so they cancel each other out when convolved with such a filter.
This property applies regardless of the original intensity graph.
Thus, we can use a Generalized Gaussian Model to model the distribution of possible filter output:
Here, the parameter r determines the kind of distribution it is--e.g. Laplacian, Gaussian, Uniform. 

<div class="fig figcenter fighighlight">
  <img src="{{ site.baseurl }}/assets/images/GenGauss.png">
  <div class="figcaption">A Generalized Gaussian Distribution</div>
</div>

We use this idea in Model 2: the Wavelet Marginal Model. 
Here, we want to build a model of the prior of the entire image.
We still treat each pixel as independent, so we multiply over all of the pixels.
We also assume we have k filters, and we apply each filter to the patch centered at each point (x,y).
Thus, for each filter at each patch, we get a Generalized Gaussian Distribution (represented by p) with its own value for r.
Since we assume each filter response distribution is independent, we can multiply together all of the Generalized Gaussian Distributions to get the prior of the image.

<div class="fig figcenter fighighlight">
  <img src="{{ site.baseurl }}/assets/images/Model2.png">
  <div class="figcaption">Wavelet Marginal Model</div>
</div>

We can use the Wavelet Marginal Model to solve the problem of Denoising.
Denoising is the process of breaking a noisy image (y) into its components of noise (n) and a non-noisy image (x).
That is, y = x+n, where we assume that n is Gaussian.

<div class="fig figcenter fighighlight">
  <img src="{{ site.baseurl }}/assets/images/denoising.png">
  <div class="figcaption">Denoising Images</div>
</div>

Here, y is your observation and P(x) is your prior, which comes from the fact that the intensities of a filter output function should follow a Generalized Gaussian Distribution. P(y|x) is the Gaussian likelihood function.
By Bayes Theorem, P(x|y) ~ P(y|x)P(x)
Thus, if y is small (i.e. not too far away from the center of P(x)), multiplying it with P(x) will cause P(x|y) to still be near the prior; that slight deviation in y from 0 is likely due to noise.
However, if y is very large, it is likely an edge, and P(x|y) will be pulled much closer to y.
Thus, when denoising, we should set small y values to 0 (as they are likely noise) and leave large y values alone (as they are likely edges).

<a name='Non Parametric Models'></a>
## Non Parametric Models

<a name='Pattern Matching'></a>	
### Pattern Matching

Pixel and patch level models perform well at certain tasks, but they have limitations. Consider the challenge of texture synthesis, where the goal is to generate new samples of a given texture. Texture synthesis has many applications including virtual environments, hole-filling and texturing surfaces. Pixel or patch level models are not enough, as we must model the entire spectrum from larger repeated structures to stochastic textures. These other models don’t perform well at capturing repeated structures, as they are focused too locally to understand global patterns. 

<a name='Non Parametric Sampling Approach'></a>
### Non Parametric Sampling Approach

Non-parametric models address this by assuming the Markov property and finding the probability $$P(p | N(p))$$.  
First, we look at the input image and find spots that have neighbors similar to spots we have already synthesized. We then compute the likelihood of the RGB value of the current pixel conditioned on its neighborhood. The probability density function is just all similar neighborhoods in the input image. 
We can then sample from this PDF by picking one match at random and using the value of that pixel for the RGB value of the pixel to be synthesized. This allows us to iteratively expand the texture by working off of our previous results to find similar areas in the original image to sample from. 

<div class="fig figcenter fighighlight">
  <img src="{{ site.baseurl }}/assets/images/Screen Shot 2020-10-13 at 12.27.20 PM.png">
  <div class="figcaption">Sampling from similar neighborhood windows in the input image</div>
</div>

One of the most important challenges is picking the size of a neighborhood window. If your window is too small, the model does not have enough context to locate the pixel of interest within the pattern. Larger windows can synthesize more regular patterns (e.g. bricks), and thus capture more world information. For instance, a good window size for the example below is a window that can cover at least two objects in the pattern so it can discern the shape of the objects as well as how they are dispersed.  

<div class="fig figcenter fighighlight">
  <img src="{{ site.baseurl }}/assets/images/Screen Shot 2020-10-13 at 12.51.35 PM.png">
  <div class="figcaption">Picking the right window size is essential for capturing repeating patterns</div>
</div>

Using non-parametric models for texture synthesis has several applications beyond creating larger examples of patterns. This same approach can be used for patching holes in images and extrapolating pixels beyond the frame of an image.

