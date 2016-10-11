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

22.3% faster - 2 bounces
8.46% faster - 8 bounces
8.42% faster - 16 bounces
#### Motion blur
#### Textures
##### Procedural
##### Bump mapping
Overview write-up of the feature
Performance impact of the feature
If you did something to accelerate the feature, what did you do and why?
Compare your GPU version of the feature to a HYPOTHETICAL CPU version (you don't have to implement it!) Does it benefit or suffer from being implemented on the GPU?
How might this feature be optimized beyond your current implementation?

