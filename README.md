# What is it?
A starter project demonstrating a secure dotnet core web api endpoint using Steeltoe and TAS SSO tile.

## Prerequisite 

[.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
[Tanzu Application Service](https://tanzu.vmware.com/application-service)

## Resources

- [Steeltoe initializr](https://start.steeltoe.io/)
- [Get Started](https://steeltoe.io/security-providers/get-started/sso)
- [Whats New Steeltoe 3.x vs 2.x](https://docs.steeltoe.io/api/v3/welcome/whats-new.html)

## Area of focus in the codebase

Protect View and Controller methods using the `[Authorize]` class or method decorator.

> `Program.cs`

```csharp
.AddCloudFoundryConfiguration()
```

> `Startup.cs`

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddOptions();
    services.AddAuthentication(options =>
        {
            options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
            options.DefaultChallengeScheme = CloudFoundryDefaults.AuthenticationScheme;
        })
        .AddCookie((options) =>
        {
            // set values like login url, access denied path, etc here
            options.AccessDeniedPath = new PathString("/Home/AccessDenied");
        })
        // Add Cloud Foundry authentication service
        .AddCloudFoundryOpenIdConnect(Configuration); 
}

public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    // Use the protocol from the original request when generating redirect uris
    // (eg: when TLS termination is handled by an appliance in front of the app)
    app.UseForwardedHeaders(new ForwardedHeadersOptions
    {
        ForwardedHeaders = ForwardedHeaders.XForwardedProto
    });

    app.UseHttpsRedirection();
    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllerRoute(
            name: "default",
            pattern: "{controller=Home}/{action=Index}/{id?}");
    });
}
```