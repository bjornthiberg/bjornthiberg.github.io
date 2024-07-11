---
layout: post
title:  "F11: Pushing past the plateau"
date:   2024-07-11 21:07:00 +0200
category: frej
---

# What I've done lately.
I’ve hit a small plateau. Here’s a summary of my recent commits, shown in the image.

![commit history]({{ site.baseurl }}/assets/images/commit_history.jpeg)

`feat: new GET endpoint to retrieve latest data points (#58)`

Later deleted. Adjusted a current endpoint instead.

`feat: Allow requests from thiberg.dev in prod, any origin in dev (#61)`

Fixed the CORS policy. Allowed requests from the frontend in production and any origin in development.

`feat: implement GitHub Actions workflow for deploying backend (#63)`

Automated deployment to a Linode Ubuntu machine. Very conventient, although the implementation was perhaps not super sleek.

`test: add integration tests for API endpoints in Program.cs (#70)`

Later mostly removed, after architecture changes to the API.

`refactor: add SensorDataController instead of minimal API (#73)`

Switched to an MVC architecture. Significant in concept, minimal in functionality change.

`feat: create SensorDataRepository, use in SensorDataController (#76)`

Instead of having the db context and logic directly in the controller, I introduced a repository for database interaction. No practical difference in functionality.

`feat: add SensorReadingReq model, use in SensorDataController (#80)`
Created a separate request sensor data model for the API controller. No change in functionality.

`feat: add additional CRUD methods to repository (#79)`
`feat: add new CRUD endpoints to controller (#91)`

The only additions of any practical functionality. Some new endpoints to GET in bulk, update, and delete data.

`feat: remove lux, uvs, gas options from charts (#87)`
`feat: interface with new sense HAT, remove old sensors (#85)`

I replaced the environmental sensors with the official sense HAT from Raspberry Pi. This ends up *removing* some options as it lacks some sensors. But it has cool LEDs.

# Where I'm at
Looking back, I’m at risk of getting stuck in a plateau. The project lacks obvious next steps. While focusing on .NET architecture and design patterns, I have predictably realized that my implementations so far have been suboptimal. This is a good thing. The whole point of moving fast is so that one can get something up and running, come back later, and improve.

With that said, I fear I am *coming back* a bit too often and not moving forward enough. I risk burning out on refactoring and redesigning, while not having much to show for it. To that end, I should build something new.

# Next steps
Some specific new functionality I want to implement:
- backend: Proper API authentication using some available library
- backend: A Service Layer between the API controller and the SensorDataRepository\*
- backend: Websocket functionality
- frontend: Filtering data by date
- frontend: Use websockets for live data updates
- frontend: More sophisticated data visualization
- meta: reflect on the project workflow (issues, this blog etc)

\* This sounds like more of the same, no practical difference. But I plan to use it to create a more robust backend, improving testing capabilities, error handling and input validation.
