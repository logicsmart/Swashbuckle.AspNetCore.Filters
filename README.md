# Swashbuckle.AspNetCore.Filters.Multipart
Adds multipart mime functionality to swashbuckle


## Usage


### Filter Configuration

#### .Net Core 3.1 sample
Startup.cs
```C#
/// <summary>
/// 
/// </summary>
/// <remarks>
/// This method gets called by the runtime. Use this method to add services to the container.
/// </remarks>
/// <param name="services"></param>
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers()
        .AddXmlSerializerFormatters();

    services.AddSwaggerGen((Swashbuckle.AspNetCore.SwaggerGen.SwaggerGenOptions o) =>
    {
        o.OperationFilter<Swashbuckle.AspNetCore.Filters.MultipartOperationFilter>();
    });
}
```

#### .Net 5 sample
Startup.cs
```C#
public void ConfigureServices(IServiceCollection services)
{

    services.AddControllers();
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo { Title = "WebApplication1", Version = "v1" });
        c.OperationFilter<Swashbuckle.AspNetCore.Filters.MultipartOperationFilter>();
    });
}
```

#### .Net 6 sample
Program.cs
```C#
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.

builder.Services.AddControllers();
// Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo { Title = "WebApplication1", Version = "v1" });
    c.OperationFilter<Swashbuckle.AspNetCore.Filters.MultipartOperationFilter>();
});

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();

app.UseAuthorization();

app.MapControllers();

app.Run();
```

### Controller
MyController.cs

```C#
[ApiController]
[Route("[controller]")]
public class MultipartFilterTestController : ControllerBase
{
    public MultipartFilterTestController()
    {
    }

    [HttpPost("MultiPartFormExample")]
    [MultipartRequest(Type = MultipartRequestAttribute.MultiPartType.Mixed | MultipartRequestAttribute.MultiPartType.FormData | MultipartRequestAttribute.MultiPartType.Related)]
    [MultipartSection(Name = "section1", Type = typeof(Guid), Title = "This is guid")]
    [MultipartSection(Name = "section2", Type = typeof(string), Title = "This is xml string", ContentType = "application/xml")]
    [MultipartSection(Name = "Section33", Type = typeof(object), Title = "This is xml string", ContentType = "application/xml")]
    public string MultiPartFormExample(int id)
    {
        return $"{Request.ContentType}: {Request.ContentLength}";
    }
}
```

