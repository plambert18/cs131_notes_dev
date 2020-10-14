---
title: Image models and priors
keywords: (insert comma-separated keywords here)
order: 7 # Lecture number for 2020
---

You can start your notes here before you start diving into specific topics under each heading. This is a useful place to define the topic of the day and lay out the structure of your lecture notes. You'll edit the Markdown file, in this case template.md, which will automatically convert to HTML and serve this web page you're reading! 

The table of contents can link to each section so long as you match the names right (see comments in template.md for more elaboration on this!). This Markdown to HTML mapping doesn't like periods in the section titles and won't link them from the table of contents, so use dashes instead if you need to.

- [Statistical Image Modeling](#statistical-image-modeling)
	- [Subtopic 1-1](#subtopic-1-1)
	- [Subtopic 1-2](#subtopic-1-2)
	- [Subtopic 1-3](#subtopic-1-3)
- [Pixel Level Models](#pixel-level-models)
	- [Introduction](#introduction)
	- [Bayesian Approach](#bayesian-approach)
	- [Isolated Pixel Intensities: An Introductory Model](#isolated-pixel-intensities:-an-introductory-model)
- [Patch Level Models](#patch-level-models)
- [Non Parametric Models](#non-parametric-models)
	- [Pattern Matching](#introduction)
	- [Non Parametric Sampling Approach](#non-parametric-sampling-approach)

[//]: # (This is how you can make a comment that won't appear in the web page! It might be visible on some machines/browsers so use this only for development.)

[//]: # (Notice in the table of contents that [First Big Topic] matches #first-big-topic, except for all lowercase and spaces are replaced with dashes. This is important so that the table of contents links properly to the sections)

[//]: # (Leave this line here, but you can replace the name field with anything! It's used in the HTML structure of the page but isn't visible to users)

<a name='Statistical Image Modeling'></a>
## Statistical Image Modeling
	
Here you can start to talk about the first topic of your notes. You can bold text like **this**, or italicize text like *this*. If you want to make a numbered list it's as easy as
1.  
2. 
3. 

- Bullet
- points
- are
- similar 

<a name='Pixel Level Models'></a>
## Pixel Level Models

<a name='Introduction'></a>
### Introduction
When we attempt to **statistically model** an image, we must first note that what we are seeing (the **luminance** of the image) comes from the product of two variables: **reflectance** and **illumination**. In this section, we will assume that these variables are pixel-specific, so that each pixel (x,y) has reflectance R and illumination I. From here on, we call these variables **L**, **R**, and **I** respectively. One way that we can visualize this is through imagining that $L$ is equal to a fixed constant, like 1. Then, we have that $R * I = 1$. Our puzzle is to figure out the optimal values of $R$ and $I$ so that we can model an image, without knowing either of their true values. 

<a name='Bayesian Approach'></a>
### Bayesian Approach
One way that we can look at the seemingly-unsolvable problem $R * I = 1$, where we have two unknowns in one equation, is through a Bayesian lens. Our ultimate goal is to calculate $R$ and $I$ to maximize the equation $P(R,I | L = 1)$. 
Through a Bayesian lens, we can think of this equation as a posterior probability, where we observe that the Luminance is equal to 1 and now must find the Reflectance and Illumination values that maximize the probability of this Luminance occurring. Then, by **Bayes' Formula**, we can transform this posterior probability into a product of a likelihood function and a prior probability. 
Formally, $$P(R, I | R * I = L = 1) = k * P(R * I = L = 1 | R, I) * P(R, I)$$ where $k$ is some constant. 
As an example likelihood function, we can take: \\[ P(L = 1 | R, I) = k * e^{\frac{-(1 - R * I)^2}{2 * \sigma ^2}} \\] This example likelihood function is basically measuring how close the product of $R$ and $I$ is to $L = 1$; as it gets closer, the likelihood gets higher, and vice versa.
As an example prior function, we can take: \\[ P(R, I) = k * e^{\frac{-(R - I)^2}{2 * \sigma ^2}} \\] This example prior is basically measuring how close our values of Reflectance and Luminance are to each other.
Overall, the Bayesian Approach that we introduced here is one important method to **statistically model** an image.

<a name='Isolated Pixel Intensities: An Introductory Model'></a>
### Isolated Pixel Intensities: An Introductory Model
The **Isolated Pixel Intensity** model focuses on building an Image Prior based on individual pixels.
We perform the following steps to build this Image Prior for a given image $I$:
1. Mask the entire image except for one pixel (call it I(x,y)).
2. Calculate our prior function of this pixel based on its values (e.g. calculate $p(I(x,y))$, where $p$ is our prior function and I(x,y) is a given image pixel).
3. Repeat this process for every pixel in the image.
4. Calculate the product of all these values; the resultant product will be our Image Prior, or $p(I)$.
Formally, \\[ p(I) = \product_{x,y} p(I(x,y))\\]
This is a very simplified image prior, and makes the following assumptions:
- All pixels are independent of one another
- Pixel distributions are equal across all image positions; e.g. pixel intensities do not depend on image position

These assumptions are often incorrect in practice, and thus can cause our prior to err.
- For example, if pixels are very close to each other, then their intensities are likely to be very close or even the same; as pixels move farther and farther apart, though, they're much less likely to have similar intensities.



<a name='Patch Level Models'></a>
## Patch Level Models

<a name='Non Parametric Models'></a>
## Non Parametric Models

<a name='Pattern Matching'></a>
### Patern Matching

Pixel and patch level models perform well at certain tasks, but they have limitations. Consider the challenge of texture synthesis, where the goal is to generate new samples of a given texture. Texture synthesis has many applications including virtual environments, hole-filling and texturing surfaces. Pixel or patch level models are not enough, as we must model the entire spectrum from larger repeated structures to stochastic textures. These other models don’t perform well at capturing repeated structures, as they are focused too locally to understand global patterns. 

<a name='Non Parametric Sampling Approach'></a>
### Non Parametric Sampling Approach

Non-parametric models address this by assuming the Markov property and finding the probability $P(p|N(p))$.  First, we look at the input image and find spots that have neighbors similar to spots we have already synthesized. We then compute the likelihood of the RGB value of the current pixel conditioned on its neighborhood. The probability density function is just all similar neighborhoods in the input image. We can then sample from this PDF by picking one match at random and using the value of that pixel for the RGB value of the pixel to be synthesized. This allows us to iteratively expand the texture by working off of our previous results to find similar areas in the original image to sample from. 

One of the most important challenges is picking the size of a neighborhood window. If your window is too small, the model does not have enough context to locate the pixel of interest within the pattern. Larger windows can synthesize more regular patterns (e.g. bricks), and thus capture more world information. For instance, a good window size for the example below is a window that can cover at least two objects in the pattern so it can discern the shape of the objects as well as how they are dispersed.  

Using non-parametric models for texture synthesis has several applications beyond creating larger examples of patterns. This same approach can be used for patching holes in images and extrapolating pixels beyond the frame of an image.

