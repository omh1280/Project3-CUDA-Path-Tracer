CUDA Path Tracer
================

**University of Pennsylvania, CIS 565: GPU Programming and Architecture, Project 3**

* Ottavio Hartman
* Tested on: Windows 7, Xeon E5-1630 @ 3.70GHz 32GB, GTX 970 4GB (SIG Lab)

![Final render](img/final.png)

#define CACHE_FIRST_BOUNCE 1 // pathtrace.cu
cache enabled - 800x800, 2 bounces, 300 samples, 35.624s
              - 8 bounces, 100 samples, 31.508s
			  - 16 bounces, 38.581s
disabled - 45.851s
         - 34.420s
		 - 42.128s
22.3% faster - 2 bounces
8.46% faster - 8 bounces
8.42% faster - 16 bounces


## Features
#### Stream compaction
#### Motion blur
#### Textures
##### Procedural
##### Bump mapping
Overview write-up of the feature
Performance impact of the feature
If you did something to accelerate the feature, what did you do and why?
Compare your GPU version of the feature to a HYPOTHETICAL CPU version (you don't have to implement it!) Does it benefit or suffer from being implemented on the GPU?
How might this feature be optimized beyond your current implementation?

