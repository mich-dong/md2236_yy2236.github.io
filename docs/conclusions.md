---
title: Conclusions
nav_order: 4
---

# Conclusions

Overall, our project fitted our original vision: a user-friendly, interactive agent-based disease simulator that was intuitive to use and understand. However, there were many ambitions during the design process that we were unable to include in the final project due to time and budget constraints. 

One of the main challenges in our design was balancing model complexity with real-time performance, memory usage, and visual clarity. Although the simulation could computationally support several hundred agents at 30 FPS, practical limits were reached much earlier due to both visualization and memory constraints. In particular, the infection transmission tree became visually crowded at around 200 agents, making it difficult to interpret individual transmission paths even though the simulation itself continued to run correctly.

Memory usage was also an important consideration. Each agent stores position, velocity, infection state, timing information, and relationship data for the transmission tree, and additional memory is required for history buffers used to plot the SIR curves over time. As the number of agents and recorded time steps increased, memory consumption grew accordingly. These constraints influenced design decisions such as limiting the number of stored history samples and capping certain behaviors to prevent unbounded data growth. These limitations highlight an important tradeoff: increasing realism and scale does not always improve usability or interpretability. In future versions, memory usage could be reduced through more compact data structures, dynamic pruning of old history data, or simplifying the transmission tree representation. 

Some software features we’d have liked to implement are: resizeable grids, pre-programmed epidemic events with set parameters, vaccination rates, vaccination effectiveness, and more. We’d also have liked to implement a more unique quarantine system, as currently, the closest approximation we can achieve is to increase social distancing radius when the disease has spread far. It would be interesting if balls cannot leave a grid after a certain percentage of the grid is infected. 

In terms of hardware, besides the designs that were ultimately left out mentioned prior, we’d have also liked to design our own enclosure in the future. Due to both members having no CAD experiences, we ultimately chose to open to use CAD design someone else made after attempting to learn the software ourselves. Furthermore, we’d like to secure the boards through screws in the future, so that the design is more robust. 

Our UI/UX design is inspired by, and must be credited to Youtuber 3Blue1Brown. We chose to reverse engineer it, however, and not examine his software implementation but entirely devise our own instead. All other code was written by the lab members, and referenced Hunter and Bruce’s demos and libraries. The enclosure was sourced from an open-source Creative Commons Attribution 4.0 design, credited at [https://www.printables.com/model/409654-raspberry-pi-4-ssdhdd-case](https://www.printables.com/model/409654-raspberry-pi-4-ssdhdd-case).
