---
layout: post
title:  "frej2: high-level architecture"
date:   2024-06-25 18:05:00 +0200
category: frej
---

Defining a specific architecture before actually starting development is difficult due to my inexperience with the tools in question. However, I need to have something to work with, so I will simply let everything be subject to change. Thinking about the high-level structure causes the project itself to become clearer and creates a natural order in which to proceed.

I have used [Lucidchart](https://lucid.app) to create the flow chart shown below.

![high level architecture image]({{ site.baseurl }}/assets/images/high_level_architecture.jpeg)

### High-Level Architecture

#### **Raspberry Pi + HAT**
- **Existing HAT Interface**: Retrieves environmental data by interfacing with the HAT.
- **Handler + Intermediate Storage**: Python handler stores the data temporarily before sending it to the server.
- **Python Data Transmission**: Transmits data to the backend API for permanent storage.

#### **.NET Backend**
- **Data Storage API**: Protected API for the Raspberry Pi to access.
- **Data Processing**: Processes the data to store it.
- **Data Storage**: Interfaces with EF Core ORM and SQLite.
- **Data Retrieval API**: API for the frontend to access the data.

#### **Blazor/React Frontend (TBD)**
- **Data Retrieval**: Accesses the .NET API to retrieve data.
- **Data Visualization**: Visualizes data and allows for user interaction.

### Next Steps

1. **Identify Project Objectives**: More specifically define the specific objectives and goals of the project.
2. **Purpose and Motivation**: Explain the purpose of the project and the motivation behind choosing the tools and technologies above.
3. **Project Plan**: Outline the order in which the project will be undertaken, detailing the steps and milestones.
