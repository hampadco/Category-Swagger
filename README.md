 Category-Swagger

 بله، در سواگر (Swagger) امکان دسته بندی کنترلرها و نمایش سرویس های مرتبط با هر کنترلر به صورت جداگانه وجود دارد. این کار را می توانید از طریق تگ ها (Tags) در سواگر انجام دهید.

مراحل کار به این صورت است:

1. **تعریف تگ ها برای هر کنترلر**
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

2. **تنظیم سواگر برای نمایش تگ ها**
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

3. **نمایش تگ ها در رابط کاربری سواگر**
   بعد از اجرای برنامه و رفتن به صفحه سواگر (معمولا `https://localhost:<port>/swagger`)، در گوشه سمت راست بالا یک دراپ دان (Dropdown) برای نمایش تگ ها خواهید دید. با انتخاب هر تگ، فقط سرویس های مربوط به آن کنترلر نمایش داده می شوند.

با این روش می توانید کنترلرهای خود را دسته بندی کرده و سرویس های هر کنترلر را به صورت جداگانه در سواگر مشاهده کنید. این کار باعث می شود رابط کاربری سواگر برای شما ساده تر و قابل مدیریت تر شود.
