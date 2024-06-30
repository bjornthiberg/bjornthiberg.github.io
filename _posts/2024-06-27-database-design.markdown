---
layout: post
title:  "F5: The Database"
date:   2024-06-27 12:40:00 +0200
category: frej
---

The Python interface which will run on the RPi is up and running. The next consideration is data storage. I'll be using SQL for perforamnce, and it makes a lot of sense to simply mirror the database schema on the RPi and in the more permanent storage location in the backend.

Thus, designing the database is my next move. To avoid having to write any SQL queries manually, I'll be using ORMs on both platforms (likely Peewee and EF Core, for the RPi and the backend respectively). 

To begin with, I'll store the data in the most simple and naive way, one entry for each reading. Later, I might implement some sort of batch processing and less granular storage, to help with storage and performance concerns.

I am going to store readings for temperature, humidity, air pressure, light, UV and gas particles from the sensors, along with a timestamp of the reading. Furthermore, I want to define an index on the timestamp column to allow faster queries and retriveal.

Here's the schema. Again done in Lucidchart.

![database schema image]({{ site.baseurl }}/assets/images/database_schema.jpeg)


### Next Steps
1. **Create database**: In Python and SQLite, for use on the RPi.
2. **Implement data insertion and retrieval**
3. **Prepare for data transmission to backend**
