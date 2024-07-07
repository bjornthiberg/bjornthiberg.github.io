---
layout: post
title:  "F10: Unit Tests for ASP.NET Core Backend"
date:   2024-07-06 17:00:00 +0200
category: frej
---

As previously stated, adding tests is the most obvious improvement to this project, especially in the backend. It is also a useful exercise since writing tests is a common weakness of junior devs.

With that, I'm going to show some unit tests as I create them, for the ASP.NET Core backend. xUnit seems to be the standard, so that is what I'm going to use.

## Testing the API key Authentication Middleware
To test the API key middleware, I used the [official guide](https://learn.microsoft.com/en-us/aspnet/core/test/middleware?view=aspnetcore-8.0) on testing middleware using TestServer. This approach allows the middleware to be tested in a virtual, isolated pipeline.

Here is a static method that returns an `IHostBuilder` instance with the necessary configuration for emulating the backend in its current state. It returns with the preconfigured defaults, along with some custom config: adding the API key, disabling standard logging, adding the middleware and an API which can receive requests.

```csharp
private static IHostBuilder CreateHostBuilder(string endpoint, Action<IEndpointRouteBuilder> configure)
{
    return Host.CreateDefaultBuilder()
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseTestServer()
            .ConfigureAppConfiguration((context, config) =>
            {
                config.AddInMemoryCollection(new Dictionary<string, string?>
                {
                    { "ApiKey", "supersecretkey" }
                });
            })
            .ConfigureServices(services =>
            {
                services.AddLogging(builder =>
                {
                    builder.ClearProviders(); // remove all logging providers
                    builder.AddFilter("Microsoft", LogLevel.None); // disable Microsoft logs
                });
            })
            .Configure(app =>
            {
                app.UseMiddleware<ApiKeyPostMiddleware>();
                app.UseRouting();
                app.UseEndpoints(configure);
            });
        });
}
```

I then implement four unit tests, corresponding to the 4 different scenarios for HTTP requests to the API where the middleware triggers:
1.	`ContinuesForNonPostRequest`: Ensures non-POST requests pass through and the pipeline continues.
2.	`ReturnsUnauthorizedForMissingApiKey`: Checks for a 401 response for a missing API key.
3.	`ReturnsUnauthorizedForInvalidApiKey`: Checks for a 401 response for an invalid API key.
4.	`ReturnsAuthorizedForValidApiKey`: Confirms a 200 response for valid API keys.

This gives 100% (line) test coverage for the middleware code.

## Testing the Aggregation Service
Next is the aggregation service, which periodically calculates average/max/min for the different data sets and stores them in the SQLite database. It is not used in its current state and likely won't be, but translating any unit test to a slightly different service will be easy, so it still makes sense to create the test(s).

One can use mocking or setting up a real database to test a service like this, and I thought about using the [in-memory database provider supplied by EF Core](https://learn.microsoft.com/en-us/ef/core/providers/in-memory). However, that seems to be heavily discouraged. Since I am using SQLite for the database, I am simply going to use an in-memory SQLite database for testing, using the same databse context as in production. 

Here’s the setup using `IDisposable` and `DataSource=:memory:` for the SQLite connection:

```csharp
public class AggregationServiceTests : IDisposable
    {
        private SqliteConnection _connection;
        private SensorDataContext _context;
        private AggregationService _service;

        public AggregationServiceTests()
        {
            _connection = new SqliteConnection("DataSource=:memory:");
            _connection.Open();

            var options = new DbContextOptionsBuilder<SensorDataContext>()
                .UseSqlite(_connection)
                .Options;

            _context = new SensorDataContext(options);
            _context.Database.EnsureCreated();

            _service = new AggregationService(_context);
        }

        // ...
```

(And writing some [Fact] tests to ensure the aggregation is actually correct for different sensor readings).

## Testing the Rest
That's it, since this is the only code that actually requires unit testing (or rather, where it makes sense). This alone makes the test coverage much more acceptable:

![test coverage report]({{ site.baseurl }}/assets/images/linecoverage_unittests.jpeg)



Integration testing is a natural next step. Both for the database context and models, and the API itself.

## Next Steps
1. **Achieve full* backend test coverage with integration tests.**
2. **Test the RPi Python scripts**
3. **Do some refactoring of the backend**: Developing these tests, I have discovered several small issues that should be remedied.
