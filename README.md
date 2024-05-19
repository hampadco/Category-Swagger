 Category-Swagger  version :6

  در سواگر (Swagger) امکان دسته بندی کنترلرها و نمایش سرویس های مرتبط با هر کنترلر به صورت جداگانه وجود دارد. این کار را می توانید از طریق تگ ها (Tags) در سواگر انجام دهید.

مراحل کار به این صورت است:

 **تعریف تگ ها برای هر کنترلر**
   در کلاس کنترلر خود، از دکوراتور `[ApiController]` و در داخل آن از ویژگی `[Route]` استفاده کنید و یک تگ برای آن کنترلر تعریف کنید.
   
   مثال:
   ```csharp
   [ApiController]
   [Route("api/[controller]")]
   [Produces("application/json")]
   [Tags("ControllerName")]
   public class MyController : ControllerBase
   { 
       // متدهای اکشن
   }
   ```

 **تنظیم سواگر برای نمایش تگ ها**
   در فایل `Startup.cs` در متد `ConfigureServices`، تنظیمات سواگر را طوری انجام دهید که تگ ها را نمایش دهد:

   ```csharp
   services.AddSwaggerGen(c =>
   {
       c.SwaggerDoc("v1", new OpenApiInfo { Title = "My API", Version = "v1" });
       c.EnableAnnotations();
       c.DocInclusionPredicate((docName, apiDesc) =>
       {
           var actionApiModel = apiDesc.ActionDescriptor.GetApiConventionDescriptionSummary();
           return actionApiModel != null;
       });
   });
   ```

 **نمایش تگ ها در رابط کاربری سواگر**
   بعد از اجرای برنامه و رفتن به صفحه سواگر (معمولا `https://localhost:<port>/swagger`)، در گوشه سمت راست بالا یک دراپ دان (Dropdown) برای نمایش تگ ها خواهید دید. با انتخاب هر تگ، فقط سرویس های مربوط به آن کنترلر نمایش داده می شوند.

با این روش می توانید کنترلرهای خود را دسته بندی کرده و سرویس های هر کنترلر را به صورت جداگانه در سواگر مشاهده کنید. این کار باعث می شود رابط کاربری سواگر برای شما ساده تر و قابل مدیریت تر شود




***************************************************************************************************************************************



در نسخه 7 .NET کمی متفاوت است. برای اضافه کردن تگ ها (Tags) در Swagger و دسته بندی کنترلرها در .NET 7، می توانید از روش زیر استفاده کنید:

 در کلاس `Program.cs`، بعد از `builder.Services.AddControllers();` کد زیر را اضافه کنید:

```csharp
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo { Title = "My API", Version = "v1" });
    c.TagActionsBy(api =>
    {
        if (api.GroupName != null)
        {
            return new[] { api.GroupName };
        }

        var controllerName = api.ActionDescriptor.RouteValues["controller"];
        return new[] { controllerName };
    });
    c.DocInclusionPredicate((docName, apiDesc) =>
    {
        var actionApiModel = apiDesc.ActionDescriptor.GetApiConventionDescriptionSummary();
        return actionApiModel != null;
    });
});
```

 در کلاس کنترلر، از `[ApiController]` و `[Route]` استفاده کنید و برای هر کنترلر یک `[ApiExplorerSettings(GroupName = "GroupName")]` تعریف کنید. مثال:

```csharp
[ApiController]
[Route("api/[controller]")]
[ApiExplorerSettings(GroupName = "ControllerName")]
public class MyController : ControllerBase
{
    // متدهای اکشن
}
```

با این تنظیمات، در رابط کاربری Swagger یک Dropdown برای انتخاب تگ ها (Tags) نمایش داده می شود. هر تگ، سرویس های مربوط به یک کنترلر خاص را نشان می دهد.

توجه داشته باشید که در نسخه 7 .NET، استفاده از `[ApiExplorerSettings(GroupName = "GroupName")]` برای تگ گذاری کنترلرها توصیه می شود. همچنین، در تنظیمات Swagger باید `c.TagActionsBy` را به روش بالا اضافه کنید تا Swagger بتواند تگ ها را از GroupName استخراج کند.
