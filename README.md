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
---------------
## This is a sample usage with Http cartridge
### Create a custom command/query

response
```C#
public class MySampleQueryResponse
{
    //define output parameters
    public string MyProperty { get; set; }
    public MySampleQueryResponse(string myProperty)
    {
        MyProperty = myProperty;
    }
}
```
request
```C#
using MediatR;
```
```C#
public class MySampleRequest : IRequest<MySampleResponse>
{
    //define input parameters if needed
}
```
Command handler
```C#
using AutoMapper;
using Liquid.Core.Context;
using Liquid.Core.Telemetry;
using Liquid.Domain;
using MediatR;
```
```C#
public class MySampleHandler : RequestHandlerBase,IRequestHandler<MySampleQuery, MySampleQueryResponse>
{
    public MySampleHandler(IMediator mediatorService
                              , ILightContext contextService
                              , LightTelemetrytelemetry Service
                              , IMapper mapperService)
        : base(mediatorService, contextService,telemetryService, mapperService)
    {
    }
    public Task<MySampleQueryResponse> HandleSampleQueryrequest, CancellationToken cancellationToken)
    {
        //implement business code here
    }
}
```
### Create a controller to expose the command/query
```C#
using Liquid.Core.Context;
using Liquid.Core.Localization;
using Liquid.Core.Telemetry;
using Liquid.WebApi.Http.Controllers;
using MediatR;
```
```C#

public class MySampleController : BaseController
{       
    public MySampleController(ILoggerFactory loggerFactory
                                , IMediator mediator
                                , ILightContext context
                                , ILightTelemetry telemetry
                                , ILocalization localization) 
        : base(loggerFactory, mediator, context, telemetry, lization)
    {
    }
    [HttpGet()]
    ///This action execute the command handler
    public async Task<IActionResult> Get() =>  await ExecuteAsync(new MySampleQuery());    
}

```
### Include dependency injections in Startup.cs
```C#
services.TryAddSingleton<IHttpContextAccessor, HttpContextAccessor>();
services.AddAutoMapper(typeof(MySampleQuery).Assembly);
services.ConfigureLiquidHttp();
services.AddDomainRequestHandlers(GetType().Assembly);
```