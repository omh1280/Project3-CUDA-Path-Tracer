CUDA Path Tracer
================

**University of Pennsylvania, CIS 565: GPU Programming and Architecture, Project 3**

* (TODO) YOUR NAME HERE
* Tested on: (TODO) Windows 22, i7-2222 @ 2.22GHz 22GB, GTX 222 222MB (Moore 2222 Lab)

### (TODO: Your README)

*DO NOT* leave the README to the last minute! It is a crucial part of the
project, and we will not be able to grade you without a good README.

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

