---
title: Techniques for livecoding complex 3D printing patterns
author: 
- name: Evan Raskob
  affiliation: The CCI at UAL
  email: evan.raskob@arts.ac.uk
abstract: |
  This paper introduces an experimental library called **gridlib** for building complex 3D paths for continous 3D printing that is part of the LivePrinter system of interactive 3D printing. It discussed the problem of speed and continous material handling during 3D printing plus the cognitive complexity of creating 3D printable forms as rationales for such a library. Some of the goals of **gridlib** are briefly discussed along with its main features, such as the primitive types of paths it can generate and how geometric modifiers interact with them to form more complex shapes, plus how paths are iterable, able to be added to "timelines" so they can be stacked on top of each other and cross-faded between both in time and physical space. 
  
---

## Introduction

When 3D printing is interactively controlled via code, or "livecoded", speed is of the essence to keep the experience interesting for the audience and performer and for the integrity and accuracy of the printed forms. This is because 3D printing, specifically Fused Deposition Modeling (FDM), uses melted plastic extruded from a hot end that can unpredictably drip out when the print head is moving too slowly or is stopped for even a few moments. The process is also often slowed down by "retraction",  whereby the plastic filament is pulled back into the hot end to avoid it spilling out during pauses in printing and then "unretracted" back out when printing should resume. 

This usually isn't an issue with most 3D object creation and "slicing" software that uses shape composition functions operating with or on solid geometric objects like cubes, spheres, and their edges and then rendering optimal tool paths for printing them at each height (e.g. "slicing" them). Retraction is used, but strategically, in a limited fashion when moving to print different layers. This allows for printing complex, filled-in shapes.

Another more efficient solution to this is to continuously move the print head whilst extruding material and never retract until the shape is finished. This is commonly called "vase mode" in 3D slicing software where each layer of a 3D object is rendered as an outline of the shape at that height, spiralling slowly upwards. This method works best for vase-like forms with single, well-defined outlines and in a limited number of other shapes that can easily be found on popular 3D model websites.  

Vase-mode has the benefit of being quicker and less material-intensive because it doesn't take time to fill in layers inside their outlines. This saves time, money, materials, and energy costs.

The **gridlib** library of iterative, continuous 3D paths was created during the development of the author's LivePrinter system to support the quick creation of forms in a vase-mode-like manner using only paths and not slicing solid forms. It is designed for quickly describing shapes that aren't sliced but instead are developed live using interactive programming tools during an interactive programming performance or making session. The main consideration is to create an expanding library of possible forms for efficient livecoding, and to provide an interface for quickly describing and manipulating these complex, multi-layered and mostly continuous 3D paths in space. This paper briefly introduces the library, which is still in a development state, along with its current goals and main design features.


## gridlib goals

### Helpful presets

The main goals of **gridlib** were to provide "preset" forms with options that could be somewhat easily modified during interactive 3D printing (I3DP). These presets would be composed of discrete points generated in distinct 2D topologies, and modified by internal functions for offsetting each point in space over time. As of this writing, three main presets form topologies are included: zig-zag curves, hollow vase-like polygons, and lines across the printer's x (left-to-right) or y (front-to-back) axes. The current point modifications functions are standard sine, cosine, sawtooth and triangle functions that change over time according to the index of the point in the curve.

For example, a simple line preset oriented in the x direction could have a number of points (3) and a set distance between those points (10mm) which means that a full period of this shape is 3 points or 30mm before it needs to raise a layer and print the next 3 points in the reverse direction. Thus, there are are actually 3+1+3+1=8 points in this full 3D shape because each section is 3 points in a direction and then up a layer. If this was strictly using vase mode, which is optional but not default, it would only be 3+3=6 points because of the continous upwards movement across the shape. 

Using just lines and modifiers, it is also possible to make interesting rectangular shapes as cas be seen in the top-most part of the form in Figure 1. The bottom-most forms of Figure 1 are two types of hexagonal (6-point) polygon paths modified by a complex sinusoidal function across the x axis and another simplarly complex function across the shape's radius.

More complex operations are possible, like rotating lines, shapes and points around an arbitrary point to change its orientation on the print bed, but are limited by the cognitive ergonomics of describing such operations quickly during making sessions.

### Stepping through paths using iterators

Moving through the points in each shape involved typing too much code to support a more intuitive, unbroken interactive coding session, in the author's experience. Thus, path "iterators" were added to **gridlib** to support more automatic traversal of the points in a shape after the preset was created, still allowing for some modification of the printing speed and preset properties during the printing process. As of this writing, this feature is still new and under development but it does point towards ways of experimenting with just how much these forms can be manipulated live whilst still producing something intentional and printable. 

### Scores: timelines and crossfades

![Figure 1: A visualisation of a gridlib timeline printed in the LivePrinter system.](https://doc.patternclub.org/uploads/822aa914-d79e-4847-9b33-eef3c9157427.png)


During performances, each of the preset shapes can be thought of as musical or physical "movements" (in the musical sense) or "scores". That leads to the concept of a timeline of either full or partial shapes printed one after another, with some kind of transition between them over time and space. In **gridlib**, currently there is a *timeline()* function that takes an array of *events* which are presets with an optional number of layers to print of them and another optional number of layers to crossfade into/out of. The results can be seen in Figure 1. In the library, the cross-fading and layer progress is followed through both the index of the point being iterated (versus the total number of indicies in the shape) and the current layer being printed out of all the layers (if applicable, which is vase-mode may not exactly be the case). Again, this feature is experimental and under development but hs produced some interesting forms so far.

There are questions about how to cross-fade between shapes with different points-per-layer and vastly different geometries, and how much coersion between disparate shapes is necessary or desired.

### Audio-visual paths

A unique feature of **gridlib** is the way it blends musical notation and standard 3D printer controls. As part of the LivePrinter ecosystem, it uses the core LivePrinter library to allow developers to specify object dimensions in terms of beats and notes by taking advantage of the musical nature of the 3D printer motors when running at set speeds. Using a translation table, a user can describe a line with a length specifed by a duration (say, a quarter note) and a speed (for example middle C) in a composion with a set beats-per-minute ("bpm"). This is discussed in detail in the paper "Space as Time" [Raskob 2023].

## Conclusion 

This paper introduced a new system for creating, manipulating, and traversing 3D paths in an interactive 3D printing environment. This is an early work that demonstrates a proof of concept in music, space, and physical form that is meant to provoke discussion and further research about what new tools, techniques, and goals should become part of the new field of I3DP. 

Raskob, Evan S. “Space as Time.” Trópos Vol. 15 No. 1 (November 15, 2023): 177-198 Pages. https://doi.org/10.13135/2036-542X/9040.
