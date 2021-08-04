Liquid Application Framework - WebApi
===================================

This repository is part of the [Liquid Application Framework](https://github.com/Avanade/Liquid-Application-Framework), a modern Dotnet Core Application Framework for building cloud native microservices.

Liquid.WebApi
-----------
This package contains the web api subsystem of Liquid, along with Http and GRPC implementation using Mediatr. In order to use it, add the main package (Liquid.WebApi.Http or Liquid.WebApi.Grpc) to your project, along with the specific implementation that you will need. 

[Liquid Application Framework](https://github.com/Avanade/Liquid-Application-Framework) - Web API base classes and supported cartridges

|Available Cartridges|Badges|
|:--|--|
|[Liquid.WebApi.Grpc](https://github.com/Avanade/Liquid.Services/tree/main/src/Liquid.WebApi.Grpc)|[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=Avanade_Liquid.WebApi.Grpc&metric=alert_status)](https://sonarcloud.io/dashboard?id=Avanade_Liquid.WebApi.Grpc)|
|[Liquid.WebApi.Http](https://github.com/Avanade/Liquid.Services/tree/main/src/Liquid.WebApi.Http)|[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=Avanade_Liquid.WebApi.Http&metric=alert_status)](https://sonarcloud.io/dashboard?id=Avanade_Liquid.WebApi.Http)|

Getting Started
==
> This is a sample usage with Http cartridge

To create web api's using Liquid Application Framework you shoud create you domain command handlers, using [Liquid.Domain](https://github.com/Avanade/Liquid.Domain). See a fast track guide on [getting started](https://github.com/Avanade/Liquid.Domain#readme).

With your domain command handlers implemented, you just need to implement LiquidControllerBase inheritance, and define actions for your handlers as exemplified :

```C#
using Liquid.WebApi.Http.Controllers;
using MediatR;
```
```C#
public class SampleController : LiquidControllerBase
{
    public SampleController(IMediator mediator) : base(mediator)
    {
    }

    [HttpGet("Sample")]
    public async Task<IActionResult> Get() => await ExecuteAsync(new SampleRequest(), HttpStatusCode.OK);
}

```
To register domain handlers, liquid configurations, and all Liquid resources services, call dependency injection extension method in the Startup.cs class
```C#
public void ConfigureServices(IServiceCollection services)
{
    services.AddLiquidHttp(typeof(SampleRequest).Assembly);
} 
```
To use liquid middlewares for scoped logging and context, swagger, exception handler and culture, call ApplicationBuilder extension method:
```C#
 public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{  
    app.UseLiquidConfigure();
}
```

Once the startup and builder is configured using the extension methods as above, it will be necessary to set Liquid default configuration. 
> sample using file provider
```Json
{  
  "Liquid": {
    "swagger": {
      "name": "v1",
      "host": "",
      "schemes": [ "http", "https" ],
      "title": "Liquidv2.SimpleApi",
      "version": "v1",
      "description": "Simple WebApi Sample.",
      "SwaggerEndpoint": {
        "url": "/swagger/v1/swagger.json",
        "name": "SimpleWebApiSample"
      }
    },
    //set default thread culture, considering that the culture middleware prioritizes the culture informed in the request.
    "culture": {
      "defaultCulture": "pt-BR"
    },
    //Set context keys that context middleware should obtain from request and set as scoped context.
    "httpScopedContext": {
      "keys": [
        {
          "keyName": "Connection",
          "required": true
        }
      ]
    },
    //Set keys that logging middleware should obtain from request and set as scoped logging header.
    "HttpScopedLogging": {
      "keys": [
        {
          "keyName": "Connection",
          "required": true
        }
      ]
    }
  }
}

```