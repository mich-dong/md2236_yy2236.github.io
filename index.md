---
title: Home
layout: home
nav_order: 0
---
# Pandora's Box

## Project Introduction

Our final project is a stochastic, agent-based epidemic simulator with adjustable parameters through physical sliders and displayed via VGA on a 640 x 480 screen alongside statistical graphs demonstrating the spread of the disease. 

<div style="display: flex; justify-content: center;">
  <div style="position: relative; width: 80%; padding-bottom: 45%; height: 0;">
    <iframe
      src="https://www.youtube.com/embed/46gfggSQE6U"
      frameborder="0"
      allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
      allowfullscreen
      style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
    </iframe>
  </div>
</div>

---

## Project Summary

Epidemic simulations can be divided into two broad philosophies: deterministic SIR (Susceptible-Infected-Recovered) models and stochastic agent-based models. In SIR models, differential equations are matched to the desired modeled scenario, which are then solved to show the result of the epidemic. It is deterministic because the result does not change per run. An agent-based simulation prescribes behavior parameters to each agent (in our case, balls representing people), which then are simulated to interact. Some randomness is then incorporated to make each run unique, determined by each step of the simulation, thus stochastic. However, in the long run, total runs will converge to the average expected behavior. 

We chose to tackle simulating an agent-based model on the RP2040’s limited hardware. We chose this over the SIR based model because we wanted to create a simulation that is both interactive and educational, allowing the user to play with the relevant parameters and observe how their combined effects can affect how an epidemic spreads, and its results. We also implement spatial (tree) and temporal (auto-scaling population distribution graph) data displays in order to better represent the larger changes in the system.
