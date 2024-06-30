---
layout: post
title:  "F7: The API, Big Data"
date:   2024-06-30 14:08:00 +0200
category: frej
---

Getting the database with connections up and running was relatively easy, using EF Core and SQLite. Implementing data submission from the RPi also went smoothly. I also set up middleware for authenticating the API key upon POST requests, which seems to be working fine (I might enhance its security later).

Now for an issue that requires some thinking. The current database stores individual sensor readings along with their timestamps. Currently, there is only one GET API endpoint /api/sensordata/{id} which allows retrieval of one sensor reading at a time. This will be a problem when attempting to display larger data sets in the frontend. For example, if we have one reading every 5 seconds, that’s approximately 17 000 data points per 24-hour period. Obviously, sending 17 000 HTTP requests just to display a single day’s worth of data is not practical.

Therefore, some sort of batch processing or aggregation needs to be implemented. We need an endpoint that allows clients to retrieve a larger set of data in a single request, with query parameters for filtering (e.g., date range). Additionally, adding some data aggregation (no need to keep granularity of five seconds for data older than a few days) and calculation in the backend to handle heavy lifting will help reduce storage volume and improve frontend performance.

To this end, I should create one or two new tables (e.g. sensorReadingDaily, sensorReadingDailyHourly) and corresponding API endpoints.

### Next Steps
1. Implement a batch processing API endpoint: Create an endpoint that allows retrieval of larger datasets in a single request with filtering options.
2. Implement backend data aggregation: Add logic to aggregate data, reducing the granularity for older data and performing necessary calculations.
3. Implement a simple frontend to test the API: Develop a frontend to test and visualize the retrieved data from the new API endpoints.
