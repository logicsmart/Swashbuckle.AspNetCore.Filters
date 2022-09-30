# Swashbuckle.AspNetCore.Filters.Multipart
Adds multipart mime functionality to swashbuckle

## Background
In the case of multiple part messages, in which one or more different sets of data are combined in a single body, a "multipart" Content-Type field must appear in the request header. The body must then contain one or more "body parts," each preceded by an encapsulation boundary, and the last one followed by a closing boundary. Each part starts with an encapsulation boundary, and then contains a body part consisting of header area, a blank line, and a body area. Thus a body part is similar to an RFC 822 message in syntax, but different in meaning.

A multipart http request can look like this:
```
POST https://localhost:44313/PostMultipartContentSample HTTP/1.1
Accept: application/xml
Content-Type: multipart/related;Boundary=myboundary

--myboundary
Content-Disposition: inline

...data...
--myboundary
Content-Disposition: inline; filename="filename.jpg"

...data...
--myboundary
Content-Disposition: attachment

--myboundary--
```
See also: [rfc1341: The Multipart Content-Type](https://www.w3.org/Protocols/rfc1341/7_2_Multipart.html)

## Usage
There are two parts to swashbuckle usage.
1. Startup configuration, which sets up the swagger middleware.
2. Controller, where attributes are added to controller actions that describe the action for swashbuckle, so that it can generate swagger docs for the swagger UI.

### Startup Configuration
Startup configuration is where the swagger middleware is added, and the operation filter for the Multipart functionality is applied.

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
Swashbuckle attributes are applied to controller actions to describe how the action is invoked through a multipart mime request.

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

