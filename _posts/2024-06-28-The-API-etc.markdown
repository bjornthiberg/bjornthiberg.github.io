---
layout: post
title:  "F6: The API, etc."
date:   2024-06-28 11:20:00 +0200
category: update
---

The ASP.NET Core application and the first minimal API endpoint is up and running. I had some issues with certificates, but decided to just disable HTTPS when developing locally. I'll use ngnix or some other reverse proxy when hosting later on, and handle encryption there.

Currently working mostly with boilerplate, it will be fun to think about any middleware, which will hopefully require some more technical problem solving. For authentication I'm going to use an API key and create some middleware in the backend to handle it all.

Perhaps I should extend the RPi-facing API to an entire CRUD API, I'll let it be for now.

![backend architecture image]({{ site.baseurl }}/assets/images/backend_architecture.jpeg)

### Next Steps
1. **Create SQLite db and set up EF Core**: 
2. **Implement data submission from RPi**
3. **Implement API key auth middleware**
