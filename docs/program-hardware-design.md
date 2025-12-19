---
title: Program/hardware design
nav_order: 2
---

# Software Design

The recurring theme of our software design boils down to information management. The key challenge in an Agent-Based Model is keeping track of each agent’s parameters, updating them as necessary, all within the limited hardware of the RP2040. 

We immediately ran into issues at the start with this approach (we did not have grids at the time). Because we could not localize a given ball to any specific part of the screen, we had to search through every pair of balls when checking for infections. This proved to fail with only a meager 100 balls, in which infections would not occur because by the time the sweep has reached the specific pairing of ball IDs, they are no longer in proximity. To resolve this, we implemented two features: the grids system. This way, for a given ball, we only need to sweep through the balls that’s in its grid, greatly reducing the runtime.

## Grids & Dynamic Arrays through Memory Allocation
The grids system assigns each ball to one of twelve grids, to which they are spawned in at the grid centers with a small random velocity to create stochastic modeling. The assigned grid number then determines the x and y coordinates at which the ball is repelled by the grid walls, which are calculated from the grid number. 
Despite seeming simplicity, the implementation of the grids and related features (travel, wall detection, color) proved to be complicated when achieved with C on the RP2040 due to the lack of native dynamic arrays. That is, C arrays have a set size that is decided when an array is initialized. For small features, we can simply create a larger-than-needed array. 

The grid system proved particularly problematic. For example, in order to sweep through the other balls in a ball’s grid when checking for infections, we need to be able to determine both which grid the ball is in, along with which other balls are in said array. This requires both an array keeping track of each ball’s grid number, but also an array per grid keeping track of the ball IDs in it. All of this, of course, had to be updated live whenever a ball changes grids. 
To implement this within limited memory, we created pseudo-dynamic arrays using malloc() and realloc(), two native methods in C that allows us to allocate a designated memory block that functions similar to an array. It can then be resized by reallocating a new memory block of the new size, before moving the data over. To achieve this, we wrote custom functions appendGrid() and popGrid(), the former which resizes up, then adds a specific ball ID to a specific grid array, and the latter removes a specific index in the grid array, before resizing down. 

This proved particularly difficult to implement for many reasons. Firstly, memory allocation issues are hard to debug, as they tend to manifest as the program freezing due to some key memory being overwritten. This symptom is incredibly hard to diagnose, and so often debugging boils down to trial and error, as well as deploying new features in small chunks. It is unhelpful that this issue can be caused by a variety of issues. Any error in the size of the allocation, which array is allocated, the order of variable assignments can all cause this issue. 

The sizes of the arrays were a key obstacle, as they cannot be determined from an array alone, but must be kept track of separately so that appendGrid() and popGrid() knows what size to reallocate to. In early development, we decided to keep the size of a grid array in the 0th index of itself. This design remains in parts of our code, but ultimately caused lots of errors because any for loop indexing error could overwrite the size, thus breaking the appendGrid() and popGrid(), thus causing stalling issues. (We thus transitioned to structs later on for other parts that required dynamic arrays) A significant portion of our development was thus spent on implementing dynamic arrays correctly. 

