# [\.NET 9 Web API & Entity Framework 🚀 Full Course: CRUD, Code-First Migrations & SQL Server](https://www.youtube.com/watch?v=AKjG2tjI07U&list=PLF1jhYUTnHo7Jvmf-xzefwLaT10Xp1U8C) by Patrick God

## Table of Contents
- [.NET 9 Web API \& Entity Framework 🚀 Full Course: CRUD, Code-First Migrations \& SQL Server by Patrick God](#net-9-web-api--entity-framework--full-course-crud-code-first-migrations--sql-server-by-patrick-god)
  - [Table of Contents](#table-of-contents)
  - [Prepare \& Create the Project](#prepare--create-the-project)
  - [Project Overview](#project-overview)
  - [Scalar](#scalar)
  - [Add Models and Controllers](#add-models-and-controllers)
  - [HttpGet/ActionResult vs IEnumerable and other Practices](#httpgetactionresult-vs-ienumerable-and-other-practices)
  - [HttpPost](#httppost)
  - [HttpPatch and HttpPut](#httppatch-and-httpput)
  - [HttpDelete](#httpdelete)
  - [Implement DbContext](#implement-dbcontext)
  - [Adding Connection to Database](#adding-connection-to-database)
  - [Code-First Migrations](#code-first-migrations)
  - [Seeding Data](#seeding-data)
  - [CRUD with EF Core](#crud-with-ef-core)
    - [HttpGet](#httpget)
    - [HttpPost](#httppost-1)
    - [HttpPut](#httpput)

## Prepare & Create the Project

```markdown
1. ✅ Configure for HTTPS
2. ⬛ Enable container support (for Docker)

1. ✅ Enable OpenAPI support
2. ⬛ Do not use top-level statements
3. ✅ Use controllers
4. ⬛ Enlist in .NET Aspire orchestration
```
## Project Overview

```
sln
|_ project
    |_ connected services
    |_ dependencies
    |_ properties
    |_ controllers
    |   |_ controller.cs
    |_ appsettings.json
    |_ program.cs
    |_ *.http
    |_ razor.cs
```
## Scalar
* You can install Scalar.AspNetCore package to  generate API documentation/PostMan-like interface. `Program.cs > if (app.Engironment .IsDevelopment())` add:
``` csharp
app.MapScalarApiReference();
```
when accessing through the browser, enter
```URL
localhost:PORT/scalar/v[versioon] e.g. v1
```
## Add Models and Controllers 
In Models Folder
1. Add Item
2. Add Model Entities

``` csharp
// SYNTAX
public int Id {get; set;}
```

In Controllers Folder
1. Add API Controller

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;

namespace VideoGameApi.Controllers
{
    [Route("api/[controller]"])]
    [ApiController]

    public class VideoGameController : ControllerBase
    {
        // CRUD Operations Here
    }
}
```

> *MVC controller can be used as view, for frontend.

## HttpGet/ActionResult vs IEnumerable and other Practices

With ActionResult (better practice), you can also return a status code. 
``` csharp
// [HttpGet(Name = "GetWeatherForecast")]
// public IEnumerable<WeatherForecast> Get() {}

[HttpGet]
public ActionResult<List<VideoGame>> GetVideoGames()
{
    Ok(videoGames);
}

// here we are able to define our status code

// we defined the route here in the same line unlike traditionally wherein a route is added below 
[HttpGet("{id}")]
// this variable is referenced by the parameter in the function below
public ActionResult<VideoGame> GetVideoGameById(int id)
{
    var game = videoGames.FirstOrDefault(g => g.Id == id);
    // return the game g where its id (g.Id) is equal to the id in the parameter
    if (game is null) return NotFound();
    return Ok(game);
}
```

## HttpPost
```csharp
public ActionResult<VideoGame> AddVideoGame(VideoGame newGame)
{
    if(newGame is nulle) return BadRequest();

    newGame.Id = videoGames.Max(g => g.Id) + 1;
    // this finds the maximum integer of all games and increments 1

    VideoGames.Add(newGame)
    return CreatedAtAction(nameof(GetVideoGameById), new { if = newGame.Id}, newGame)
}
```

## HttpPatch and HttpPut
* with PUT, we update with complete options
* with PATCH, endpoint to partially update an object (patch it)

```csharp
[HttpPut("{id}")]
public IActionResult UpdateVideoGame(int id, VideoGame updatedGame)
{
    var game = videoGames.FirstOrDefault(g => g.Id == id);
    if (game is null) return NotFound;

    game.Title = updatedGame.Title;
    // so on
}
```

## HttpDelete
```csharp
[HttpDelete("{id}")]
public IActionResult DeleteVideoGame(int id)
{
    var game = videoGames.FirstOrDefault(g => g.Id == id);
    if (game is null) return NotFound;

    VideoGames.Remove(game);
    return NoContent();
}
```
## Implement DbContext
```csharp
using Microsoft.EntityFrameworkCore;

namespace VideoGameApi.Data
{
    public class VideoGameDbContext(DbContextOptions<VideoGameDbContext> options) : DbContext(options) 
    {
        // add database set here
    }
}
```
> CTRL + . for suggestions
>> When doing this from scratch, you have to manually type that the VideoGameDbContext constructor will take on the parameter DbContextOptions\<VideoGameDbContext\> options, which will then get passed to its base class which its DbContext. If the EF Core is already installed then you can just use the suggest feature to tell the IDE that this uses the EF Core.

The database set will then result into a table in the database.

```csharp
// database set
// prop [tab] -> property declaration shortcut

public DbSet<VideoGame> VideoGames { get; set; }
```

> This will bear a warning that VideoGames is a none-nullable property and a better practice is to set it already to the type of its entity.

```csharp
// set VideoGames
public DbSet<VideoGame> videoGames => Set<VideoGame>();
```

## Adding Connection to Database
in `appsettings.json`
```json
{
    "ConnectionStrins": {
        "DefaultConnection": "Server=localhost\\SQLExpress;Database=VideoGameDb;Trusted_Connection=true;TrustServerCertificate=true"
    },
    Logging: {
        ...
    }
}
```

in `Program.cs`, before building
```csharp
builder.Services.AddDbContext<VideoGameDbContext>(options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));
```
> Install Microsoft.EntitiFrameWorkCore.SqlServer

## Code-First Migrations
In `Package Manager Console`,
```
PM > Add-Migration [Migration Name]
PM > Update-Database
```
e.g. `Add-Migration Initial` for the first Migration

## Seeding Data
Back in the DbContext, if we have hardcoded data then we can seed those into the database using

```csharp
protected override OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<VideoGame>().HasData(
        new VideoGame
        {
            // props here
        }
    )
}
```
> TIP: view IDE suggestions then TAB to select such as OnModelCreeating

in the PMC,
```
PM > Add-Migration Seeding
PM > Update-Database
```

## CRUD with EF Core
We now have to inject the data context

```csharp
private readonly VideoGameDbContext _context = context;
```
> CTRL + . to generate constructor OR from the constructor itself, inherit the context
``` csharp
public class VideoGameController(VideoGameDbContext context) : ControllerBase
```
Then we turn all function **asyncrhonous**. Such that
``` csharp
[HttpGet]
public ActionResult<List<VideoGame>> GetVideoGames()
{
    Ok(videoGames);
}
```
should be,
```csharp
public async Task<ActionResult<List<VideoGame>>> GetVideoGames()
{
    return Ok(await _context.VideoGames.ToListAsync())
}
```
 In `Program.cs`,
 add 
 ``` csharp
 builder.Services.AddOpenApi();
```
and for the IsDevelopment condition, `if (app.Environment.IsDevelopment())`,
add
```csharp
app.MapScalarApiReference();
app.MapOpenApi();
```
### HttpGet
```csharp
public ActionResult<VideoGame> AddVideoGame(VideoGame newGame)
{
    if(newGame is nulle) return BadRequest();

    newGame.Id = videoGames.Max(g => g.Id) + 1;
    // this finds the maximum integer of all games and increments 1

    VideoGames.Add(newGame)
    return CreatedAtAction(nameof(GetVideoGameById), new { if = newGame.Id}, newGame)
}
```csharp
[HttpGet("{id}")]
public async Task<ActionResult<VideoGame>> GetVideoGameById(int id)
{
    // var game = videoGames.FirstOrDefault(g => g.Id == id);

    var game = await _context.VideoGames.FindAsync(id)
    
    if (game is null) return NotFound();
    return Ok(game);
}
```
### HttpPost
```csharp
public async Task<ActionResult<VideoGame>> AddVideoGame(VideoGame newGame)
{
    if(newGame is nulle) return BadRequest();

    // newGame.Id = videoGames.Max(g => g.Id) + 1;

    _context.VideoGames.Add(newGame)

    // this is crucial because we did not just query, tracking is only done before this line saves the progress into the database

    await _context.SaveChangesASync();

    return CreatedAtAction
    (nameof(GetVideoGameById), new { if = newGame.Id}, newGame)
}
```
### HttpPut