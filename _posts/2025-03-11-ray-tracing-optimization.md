---
layout: post
title: "Master's Thesis - Real Time Software Ray Tracing"
date: 2025-03-11
categories: [Portfolio, VT]
order: 3
description: A survey of my master's thesis work.
---


## About
I obtained my Master's degree in Computer Engineering at Virginia Tech by researching ways to optimize software raytracing for real time use without relying on hardware accelerators. My system uses a hybrid of rasterization and ray tracing techniques to produce images that are more visually appealing than rasterization can achieve while at the same time running in real time. The consistent theme is to perform ray tracing algorithms, but fallback on approximations where the convergence rate of an algorithm is too slow. By doing this we can model decent approxmiations to light transport while remaining above 60 frames per second. 

## Motivation
Many real time applications such as games or animation engines wish to simulate realistic light transport to present the user with streams of high fidelity images. For decades rasterization was the rendering technique of choice because it is fast and produces high quality images. However, in recent years graphics hardware has begun including specialized cores to accelerate ray tracing, an alternative rendering solution to rasterization and a type of light transport simulation. Ray tracing is generally the more desirable choice due to its ability to present images with higher fidelity than rasterization, and due to advances in graphics hardware it can now be simulated in real time. However, with the price increases of graphics processing hardware outpacing their performance increases, and with many other devices still not including this specialized hardware due to power constraints, we present an optimized model which uses ray tracing to produce a higher quality output than that of a raster engine while at the same time not requiring the use of specialized hardware accelerators to achieve real time frame rates. In this thesis we present two experiments we performed in pursuit of this goal, one which simulated global illumination but could not maintain reasonable levels of accuracy, and another which simulated lighting, shadows, and reflections and produced images which closely resembled our reference model while achieving consistent real time frame rates.

## Technical
As a reference, I created a path tracer which rendered scenes in a physically accurate way over long periods of time using a techinique called temporal accumulation. The hallmark of raytracing algorithms is something called Monte Carlo Integration which provides a substitution for recursive integration which cannot be computed by averaging many samples. The more samples we take, the closer our approximation is to ground truth, but the longer it takes to converge. As a general rule, we cannot obtain enough random samples to accurately render an image in real time, which is why our path tracer accumulates data over time, effectively increasing the sample rate. 

This is what the path tracer looks like after a single frame. It's incredibly noisey because we have not obtained enough samples yet, but this is the baseline that we need to improve upon.
![alt text](<../assets/images/raytracing/path_frame_1.png>)

This path tracer assumes no level of lighting which is more accurate but too slow for us. Instead, we will assume that the scene is fully and consistently lit by storing each position's direct albedo as an estimate of its radiance. To find this we cast a ray from the camera through every pixel on the screen and query the color of the surface hit.

![alt text](<../assets/images/raytracing/direct_color.png>)

From this lit basis, we will gather lighting data to augment our approximation and pull it back towards reality. We use importance sampling, which is to send lighting rays from the initial hit points towards areas that we know lights are at. This drastically reduces the amount of computation we need since rays are no longer as likely to be wasted by bouncing around a scene and not hitting any light source. A ray which does this cannot contribute to the overall final lighting and is a waste of computational power. With importance sampling, we lose some physical accuracy because we are not longer modeling the true behavior of light which of course does not target anything when bouncing off a surface. It is also worth noting that while light normally emits from a light source and registers when it hits a camera lens, we reverse that and send rays from the camera and register when they hit light sources to drastically reduce the amount of irrelevant rays.

With importance sampling, recursive reflection, and our lit basis, we are able to produce a much better image as shown here.

![alt text](<../assets/images/raytracing/no_denoise_frame_1.png>)

However, we are still suffering some noise, albeit much less than the initial path tracer and at a faster frame rate. To get rid of this noise, we use the second step of our pipeline which is a custom denoising algorithm. Our denoiser runs at the end of a frame and is context aware meaning, it only does as much work as it can without dropping below a real time frame rate. We estimate how much work can be done to clean up the image by using a PID loop where the input is the current frame rate and the output is a threshold value used to generate a work mask. The work mask tries to find the noisiest areas by inverting a common image processing edge detection technique. Instead of blurring to reduce noise, then performing edge detection, we first perform edge detection to find the high frequency noise, then run a blur to fill in areas of our mask that are noisy. Finally, we use the output threshold from out PID loop to binarize the mask and use it to determine where to send addtional rays to attempt to converge the worst areas faster.

Comparing to the noisy image above, a generated mask is show below and we can see that it correctly finds the noisiest areas of the image.

![alt text](<../assets/images/raytracing/mask.png>)

Putting it all together, after running the denoiser, we get the following output.

![alt text](<../assets/images/raytracing/denoised.png>)

After 10 frames (43ms) temporal accumulation brings us to this.
![alt text](<../assets/images/raytracing/frame10.png>)

Which is in comparison to our ground truth path tracer after 2 minutes:

![alt text](<../assets/images/raytracing/path.png>)


Some other test cases are shown here:

![alt text](<../assets/images/raytracing/scene2.png>)
![alt text](<../assets/images/raytracing/scene3.png>)
![alt text](<../assets/images/raytracing/scene4.png>)


At the end of this project, there are several things I would do to take it further. First, I would want to generate an bounding volume hierarchy, which would allow for far more complex geometry in real time. Next, a failed experiment that I did not present here could be used to cache global illumination and add that to our render which is by far the largest source of error between our approximation and the ground truth model. Also, by using different thresholds, arbitrary work can be done on the image to denoise it so it would be interesting to look into a spatial denoiser which works by blurring together pathes of an image to reduce noise. We could try augmenting it with scene depth and normal information to keep hard edges where they should be, however since the guassian kernel is normally a square we would likely get artifacts at the boundaries of curved surfacees. Each of these could go a long way towards converging the image in more realistic ways.