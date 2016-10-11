CUDA Path Tracer
================

**University of Pennsylvania, CIS 565: GPU Programming and Architecture, Project 3**

* Ottavio Hartman
* Tested on: Windows 7, Xeon E5-1630 @ 3.70GHz 32GB, GTX 970 4GB (SIG Lab)

![Final render](img/final.png)
## Features
#### Stream compaction
#### First intersection caching
One of the easiest things to cache in a pathtracer is the first-bounce intersection point. While this efficiency can be implemented simply, it relies on some conditions which other features may break: for example, motion blur does not allow for first-bounce caching because the objects in the scene may move.

In `pathtrace.cu`, there is a toggle `CACHE_FIRST_BOUNCE` which changes whether or not the first intersection is cached. The performance data is as follows:

![cache](img/cache.png)

There is a clear improvement with caching the first intersection, but it is also important to see that the performance benefit shrinks as the depth increases. For 2 bounces, there was a __22.3%__ increase in speed, 8 bounces had a __8.46%__ increase, and 16 bounces had a __8.42%__ increase.

If intersection caching were implemented on the CPU, it would be much slower. This feature is really meant for the GPU because it is already being used to calculate all of the intersections; there is no point in moving the code to the CPU just to cache the first intersections.

Further optimization could use the topics learned in class to more efficiently store the cached intersections. I did not pay any attention to the order in which memory was accessed--that is, global memory may be getting retrieved at random, non-contiguos locations. This is a huge performance hit which could be reduced with regard to how global memory is retrieved.

#### Motion blur
I implemented a way to describe the translational and rotational motion of objects in the scene, as well as a motion blur. There is no "shutter speed" or anything like that; it simply blurs the objects over the entirety of their movement. The `T_MOTION` and `R_MOTION` in the `*.txt` scene files describe the __relative__ motion from the starting transformation.

![motion blur](img/motion_blur.png)

![motion blur](img/motion_blur2.png)

![motion blur](img/motion_blur3.png)

In terms of performance hit, this is a super-low cost addition. This is because, the way I have implemented motion blur, the scene's objects are moved randomly along their transformation "line" each iteration. This involves calculating a random `t` between 0.0 and 1.0, calculating a new transformation, and then re-loading the objects into device memory using `cudaMemcpy`. This is all surprisingly very cheap: at resolution 800x800, 12 depth, 1000 samples, and 2 motion blurred objects the render took 71.588s. Without motion blur it took 73.507s. Despite the motion blur actually being quicker, it seems that they are just close enough that is within the margin of error of performance (on this computer).

The actual movement of the objects in the scene is being done on the CPU. This is efficient enough for the simple scenes I have included, however this would be a perfect thing to move to the GPU. The objects can be quickly transformed on the GPU, which would be very efficient when there are 1,000s of objects in the scene.

#### Textures
This renderer includes an implementation of rendering textures from files, bump maps, and procedural texture generation. 
![texture](img/texture1.png)

In order to render textures on spheres, I added a way to pass the `uv` coordinate of intersections to the BRDF shader. The `uv` calculation for a sphere is very simple so the performance hit is minimal. However, the texture lookup operation should contribute a performance hit since each time a ray intersects the texture surface it needs to look up a pixel on the texture. As it turns out the difference is minimal: 34.205s render with textures, 33.938 without. 

texture - 34.205s
no texture - 33.938
##### Procedural
![procedural](img/procedural.png)
##### Bump mapping
![bump](img/bump2.png)
![bump](src/bump2.jpg)
![bump](img/bump.png)
Overview write-up of the feature
Performance impact of the feature
If you did something to accelerate the feature, what did you do and why?
Compare your GPU version of the feature to a HYPOTHETICAL CPU version (you don't have to implement it!) Does it benefit or suffer from being implemented on the GPU?
How might this feature be optimized beyond your current implementation?

