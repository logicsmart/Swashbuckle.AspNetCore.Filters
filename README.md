# Swashbuckle.AspNetCore.Filters.Multipart
Adds multipart mime functionality to swashbuckle


## Usage


### Filter Configuration

#### .Net Core 3.1 sample
Startup.cs
```C#

```

#### .Net 6 sample
Program.cs
```C#

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

