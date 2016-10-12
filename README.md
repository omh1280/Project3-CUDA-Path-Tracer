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

In order to render textures on spheres, I added a way to pass the `uv` coordinate of intersections to the BRDF shader. The `uv` calculation for a sphere is very simple so the performance hit is small. However, the texture lookup operation should contribute a performance hit since each time a ray intersects the texture surface it needs to look up a pixel on the texture. As it turns out the difference is minimal: 34.205s render with textures, 33.938 without. I think one of the reasons the texture lookup does not have a huge performance hit is because it is looking up only one texture. In a full scene with multiple textures for each material (bump, specular, albedo, etc.), this would clearly increase the render time.

In order to improve the efficiency of texture lookups, one thing I could do is reduce the texture sizes using mipmap, so that as a material's distance from the camera increases the render will use a smaller size texture to represent it. This works because the high resolution of textures is unneeded when an object is far away.

Another optimization which would increase render time but would make the visual quality improve is texture filtering. As it stands, the render simply picks a single pixel in the texture as the color for the ray. Because of this, there is some minor artifacting and aliasing. I could add a texture filter to sample surrounding pixels and effectively "blur" the values, but this would increase computation.

##### Procedural
There is a mechanism in place to render a procedural texture. A function populates an array with R, G, and B values according to the pixel location. Due to time constraints, this is being done on the CPU, but it would be much more efficient on the GPU. The GPU would be able to run this in parallel for on-the-fly procedural texture generation (as it stands, the texture is generated once at the beginning of the render).

The most obvious optimization would be to implement this procedural texture generation on the GPU. Since the texture is never changing throughout the render, however, it does not make a whole lot of difference because it is just overhead at the beginning of the render. This is because I have implemented the procedural texture as an array, as opposed to a texture "look-up" where each query to the material would run a function to decide its color.

![procedural](img/procedural.png)

##### Bump mapping
![bump](img/bump2.png)
![bump](src/bump2.jpg)
![bump](img/bump.png)

Bump mapping is implemented using a normal map. Instead of simply applying the color of each pixel to the object, the pixel color is used as the basis for changing the normal of the surface so that all bounced rays react as if the surface is bumpy. In order to calculate the new normal, the binormal and tangent vectors of the surface at the intersection are used. This allows the normal to move with regard to the normal map.

The performance impact of bump mapping is small because it has the same overhead as the texture lookup with some simple mathematical operations included. The time for a 800x800 render without bump mapping is 34.411s, whereas with bump mapping is 34.928s. Of course, as with the other textures, the more bump maps are added to the scene the higher the performance hit.

Similar efficiencies can be applied to bump mapping as texturing.