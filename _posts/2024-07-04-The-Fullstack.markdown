---
layout: post
title:  "F9: The Full-Stack Web Application"
date:   2024-07-04 23:00:00 +0200
category: frej
---

This is a milestone, the application is officially up and running, with the frontend accessible at [thiberg.dev/project_frej](https://thiberg.dev/project_frej). I have the goal of building a usable but minimal full-stack web application from start to finish. The backend has been deployed to a Ubuntu VM using nginx, and the React frontend is hosted using GitHub pages in the main repository. Here is the somewhat finished product:

![frontend charts]({{ site.baseurl }}/assets/images/frontend_charts.jpeg)

While my eyes bleed, I managed to stick to the philosophy of going fast and avoiding perfection. There are definitely improvements to be made at every step, but working with incremental improvements of an actually functioning product is a much less tedious experience than being stuck perfecting step one. Now I can at least take a break and gaze at the beautiful language distribution chart:

![language distribution]({{ site.baseurl }}/assets/images/languages_repo.jpeg)

There are critical improvements to be made in three main areas, corresponding to three large next steps (to be divided into smaller issues and goals):

1. **Testing**: The most obvious improvement, especially in the ASP.NET Core backend. This is an oft-repeated weakness among solo and junior developers. Implementing rigorous test coverage will be a good exercise.
2. **Deployment/Infrastructure/CI**: I tried the Azure + GitHub Actions combination before opting to manually configure a VM instead. While the GitHub Pages branch/workflow is relatively smoothly set up, there is currently nothing similar for the backend. Automatic deployment needs to be the goal to streamline updates and get the project up to modern standards.
3. **Improved Data Visualization**: The frontend is pretty basic and needs to be improved. Features like searching based on time ranges, more sophisticated analysis, and comparison tools should be implemented. This also involves creating the necessary API endpoints.

Future goals:
- Dockerizing the application to ensure consistency across different environments and simplify the deployment process.
- WebSockets instead of HTTP REST API, for live updates and better performance.
- Improve the API authentication
- Automated testing in the CI pipeline. 
- Alert system for high temps or particles.
