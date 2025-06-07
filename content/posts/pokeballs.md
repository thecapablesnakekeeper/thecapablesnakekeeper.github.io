---
title: SDF-based PBR Pokeballs
date: 2023-04-21T23:03:50-05:00
lastmod: 2024-10-21T23:03:50-05:00
addReadingTime: false
cover: /img/pokeballs/main.gif
categories:
  - Rendering
  - Procedural
  - Themed
tags:
  - C++
  - Qt
  - OpenGL
---

Pokeballs made using Signed Distance Fields, implementing realtime PBR, Environment Mapping, Raymarching & Subsurface Scattering.

<!--more-->

In this C++/OpenGL project, I created Pokeballs using Signed Distance Functions (SDFs) and also implemented the realtime PBR pipeline based on the paper [Real Shading in Unreal Engine 4](https://cdn2.unrealengine.com/Resources/files/2013SiggraphPresentationsNotes-26915738.pdf). Some basic Qt tooling allows an interactive program like the following:
![](/img/pokeballs/pbr_demo.mp4?f=mp4)

### The PBR Pipeline consists of:

1. Cook-Torrance Microfacet Specular BRDF:
   - GGX/Trowbridge-Reitz Distribution Function
   - Schlick Model for Geometric Attenuation
   - Schlick’s Approximation for Fresnel
2. Image-based Lighting
   - Environment Map Sampling
   - Precomputed Diffuse & Specular Reflections
   - Mipmapping to avoid artifacts

### The SDF implementation comprises of:
1. Raymarching
2. 3 different SDFs for Normal Ball, Great Ball, Ultra Ball
3. Normal computation for SDFs
4. Domain Repetition based on [this](https://iquilezles.org/articles/distfunctions/) article from Inigo Quilez
5. Perlin Noise for adding rotational variation


Here are some fun renders with different types of Pokeballs, environment maps, and roughness/metallic settings, along with some grid-baed repetition:
![](/img/pokeballs/model1.jpg)
![](/img/pokeballs/model2.jpg)
![](/img/pokeballs/model3.jpg)
![](/img/pokeballs/oprep1.jpg)
![](/img/pokeballs/oprep2.jpg)