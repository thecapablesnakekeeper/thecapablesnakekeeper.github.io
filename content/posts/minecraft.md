---
title: WandaVision themed Minecraft
date: 2022-12-17T22:09:40-05:00
lastmod: 2024-10-17T22:09:40-05:00
#author: Author Name
# avatar: /img/author.jpg
# authorlink: https://author.site
addReadingTime: false
cover: /img/minecraft/main_screen.png
categories:
  - Graphics/Rendering
  - Procedural
  - Themed
tags:
  - C++
  - OpenGL
# nolastmod: true
---

Procedurally generated Minecrat terrain with post-process effects matching the theme of my favourite Marvel TV show, WandaVision.
<!--more-->

In the first semester of my Masters, I worked on building a miniature version of Minecraft in a group of 3 for my Interactive Compute Graphics course’s final project. WandaVision is one of my favorite TV shows, and I suggested to my team that we incorporate some elements from the same to make our project fun. I was responsible for the following implementations:

- [1. Efficient Terrain Rendering](#1-efficient-terrain-rendering)
- [2. Multiple Render Passes](#2-multiple-render-passes)
- [3. Timeline Shaders](#3-timeline-shaders)
  - [60’s Timeline](#60s-timeline)
  - [80’s Timeline](#80s-timeline)
  - [Dystopian (Current) Timeline](#dystopian-current-timeline)
- [4. SDF Hex](#4-sdf-hex)
- [5. Noise-based Overlays](#5-noise-based-overlays)
- [6. Procedural Cave System](#6-procedural-cave-system)

As a quick summary, pressing H casts the ‘Hex’ – a hexagonal structure that grows around the player up till a certain radius. There are 3 different timelines – the 60’s greyscale timeline, the 80’s toon-shaded timeline, and the current unstable timeline. Pressing H repeatedly cycles through these 3 timelines. The timeline effects are contained within the hex boundaries.
Here is our final submission video:
[![](/img/minecraft/yt_link.png?w=600)](https://www.youtube.com/watch?v=Ma94eS7i2eY)

## 1. Efficient Terrain Rendering
As we know, all assets in Minecraft are rendered as cubes. The initial implementation of our project used instanced rendering to place each cube at its respective coordinates independently. But this results in an overdraw because the faces getting drawn between two consecutive cubes are not visible to the player. Hence, I implemented a better rendering process that checks for solid cube neighbors for each cube and decides which faces to draw. This makes the solid terrain look hollow if viewed from the inside, as shown in the pictures.

|![](/img/minecraft/chunking_outside.png?w=400&h=200) | ![](/img/minecraft/chunking_inside.png?w=400&h=200)|
|:-:|:-:|
| *Terrain as visible to the player* | *Same 'hollowed' terrain when viewed from the inside* |

|![](/img/minecraft/chunking.gif?w=600) |
|:-:|
| *Small choppy demo of what the terrain looks like from the inside* |

## 2. Multiple Render Passes
In order to be able to apply different timeline-based shaders inside the Hex as well as different post-processing overlays like when under water or lava, I needed to use multiple frame buffers to be able to store the result from a render pass somewhere and to use it for compositing or as-is in the subsequent passes. A brief summary of my approach is as follows:

1. First, I draw the entire terrain as-is and store it in a frame buffer.
2. Next, I apply the timeline-based effect on this terrain and store the result in a second frame buffer.
3. I use a **Signed Distance Field (SDF)** to calculate the Hex boundaries when the player hits the key H, and store it as a black-and-white image in a frame buffer. Black areas represent the terrain that lies inside the hex, while the white areas are the parts lying outside.
4. I then use the black-and-white hex bounds frame buffer as a **stencil** to composite the normally drawn terrain with the timeline-based post-process overlay.
5. Any other post-processing like under-water or under-lava effects are applied on top of the final composition and rendered to the screen in addition to drawing the actual TV-static-like Hex.

Here is a flow diagram that summarizes the approach described above:
![](/img/minecraft/render_passes.png?w=900)

## 3. Timeline Shaders

### 60’s Timeline
|![](/img/minecraft/timeline_greyscale.mp4?f=mp4&w=700) |
|:-:|
| A simple fragment shader that applies a greyscale overlay. |

### 80’s Timeline
|![](/img/minecraft/timeline_toon.mp4?f=mp4&w=700) |
|:-:|
| A fragment shader that applies Toon Shading. |

### Dystopian (Current) Timeline
|![](/img/minecraft/timeline_dyst.mp4?f=mp4&w=700) |
|:-:|
| Vertex shader for the terrain jittering effect, fragment shader for Chromatic Aberration. |

## 4. SDF Hex
The Hex is a signed distance function that spans a certain radius around the player when the H key is pressed. It starts growing around the player and continues to gradually grow till a configured threshold is reached. A TV-static-like red tinted noise function is used to render the vertical terrain-spanning hex walls. All timeline-based effects are restricted to the Hex bounds, even if they cut across cubes.

Casting Hex by pressing the H key: ![](/img/minecraft/hex_2.gif)

Vertical Terrain-Spanning TV Static-like Hex: ![](/img/minecraft/hex_1.gif)

Timeline effects restricted to Hex bounds: ![](/img/minecraft/hex_3.gif)

## 5. Noise-based Overlays

I was also responsible for writing post-processing overlayes when the player is underwater or under lava.
Perlin-interbed time varying FBM for underwater effect: ![](/img/minecraft/water.gif)
Perlin-based time varying under-lava effect: ![](/img/minecraft/lava.gif)

## 6. Procedural Cave System
 used 3D Perlin Noise to place the blocks for caves under the terrain. I scale it appropriately so that Perlin gives nice wide openings to allow player movement.
![](/img/minecraft/caves.gif)