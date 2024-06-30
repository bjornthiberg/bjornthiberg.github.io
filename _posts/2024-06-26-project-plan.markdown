---
layout: post
title:  "F4: The Plan"
date:   2024-06-26 16:15:24 +0200
category: frej
---

For this project, I am going to use "sprints". Well, not really, as agile development does not really make sense for a solo developer. However, it does make sense to divide the project into chunks in which the first couple of chunks are more fine-grained and the later stages are more TBD.

Furthermore, I will not plan the exact number of dates that each part should take, that seems unreasonable at this moment. With this said, this is the project and its tasks from basically start to finish, that can more or less be converted to GitHub issues:

**Chunk 1:** Initial setup and planning
1. Create this blog
2. Detail the project purpose
3. Create a project plan
4. Set up runtime environment on Raspberry Pi

**Chunk 2:** Getting to know the HAT
1. Implement interfacing with the HAT code
2. Set up intermediate storage

**Chunk 3:** RPi-facing backend
1. Set up .NET Core project and backend skeleton 
2. Create API endpoints for data submission
3. Design and set up database and ORM (SQLite + EF Core)
4. Implement Python data transmission from the RPI

**Chunk 4:**: Front-facing backend
1. Set up a basic frontend to API requirements
2. Set up API Endpoint for data retrieval

**Chunk 5:**: Data visualization
1. Create a more sophsiticated front-end for data visualization
2. Conduct user testing and gather feedback.

**Chunk 6:** Final touches
1. Move to cloud-hosting platform
2. Create/improve documentation

**Continou** 
1. Update this blog at appropriate intervals
2. Evaluate the project plan and structure


### Next Steps
1. **Create GitHub issues** Corresponding to the above tasks.
2. **Dev environment**: Set up the necessary tools and environments for development, both on my machine and on the RPi.
3. **Python RPi handler**: Start on chunk 2. Develop and test the Python code that will run on the RPi.
