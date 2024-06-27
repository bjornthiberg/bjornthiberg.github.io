---
layout: post
title:  "F4: The Bigger Picture"
date:   2024-06-26 14:45:24 +0200
category: update
---

### Purpose
Before getting into the nit and grit, I want to contemplate what I am doing and why.

The main purpose of this project is to provide a practical learning experience of a realistic software development project. This means that every part of the project should be done with care, and with realism in mind. I want to learn useful technical skills and get practice in project experience, documentation and system design.

### Objectives
To that end, the objectives are to create a working product that provides both the core functionality of data collection and visualization, as well as the secondary goals, including documentation, scalability and security. 

Furthermore, I want development, documentation and storage to avoid bloat. When possible, I am to use the simplest tool for the job, preferably as few as possible in total.

### Motivation of tools
Moving on to the motivation of the frameworks and tools chosen in the [previous post](https://bjornthiberg.github.io/project_frej/update/2024/06/25/high-level-architecture.html).

The RPi is chosen because of its low power consumtion, small frame and because I already had one. Using Python for interfacing with the sensors and data transmission makes sense, as the manufactures provides some already existing Python code which will then be easy to integrate. The same functionality is provided in C as well, but that's going too far.

The choice of the .NET framework for the backend follows from the fact that I simply want to learn it, it has extensive documentation and a large community, and its common usage in industry. 

The same goes for the choice of frontend framework/language. That is a decision I will leave for later, with Blazor or React seeming like natural choices.

### Philosophy
Do not let perfection be the enemy of good. While I want to learn best practices, I do not want to get stuck in the details and getting everything just right for what is ATEOTD a small project. If something works, it most likely works well enough.

### Next Steps
1. **Project Plan**: Outline the order in which the project will be undertaken, detailing the steps and milestones. Define the development strategy.
2. **Dev environment**: Set up the necessary tools and environments for development, both on my machine and on the RPi.
3. **Python RPi handler**: Develop and test the Python code that will run on the RPi to interface with the HAT, temporarily store and that transmit data.
