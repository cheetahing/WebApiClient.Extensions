# WebApiClient.Extensions
[WebApiClient](https://github.com/dotnetcore/WebApiClient)项目的第三方扩展：[HttpClientFactory](https://github.com/aspnet/HttpClientFactory)、[SteeltoeOSS.Discovery](https://github.com/SteeltoeOSS/Discovery)

### 1 HttpClientFactory扩展

#### 1.1 Nuget
PM> `install-package WebApiClient.Extensions.HttpClientFactory`
<br/>支持 netstandard2.0 

#### 1.2 使用方法
声明远程http服务的的WebApiClient调用接口
```c#
[HttpHost("https:/localhost:5000")]
public interface INetApi : IHttpApi
{
    [HttpGet("api/values")]
    ITask<string[]> GetValuesAsync();

    [HttpGet("api/values/{id}")]
    ITask<string> GetValuesAsync(int id);
}
```

Startup相关配置
```c#
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpApiTypedClient<INetApi>();
    ...
}
```

Controller
```c#
public class HomeController : Controller
{
    public async Task<string> Index([FromServices]INetApi netApi, int id = 0)
    {
        var values = await netApi.GetValuesAsync();
        var value = await netApi.GetValuesAsync(id);
        return "ok";
    }
}
```
### 2 DiscoveryClient扩展

#### 2.1 Nuget
PM> `install-package WebApiClient.Extensions.DiscoveryClient`
<br/>支持 netstandard2.0 

#### 2.2 使用方法
声明微服务的WebApiClient调用接口
```c#
[HttpHost("http://NET-API")]
public interface INetApi : IHttpApi
{
    [HttpGet("api/values")]
    ITask<string[]> GetValuesAsync();

    [HttpGet("api/values/{id}")]
    ITask<string> GetValuesAsync(int id);
}
```

Startup相关配置
```c#
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    services.AddDiscoveryClient(Configuration);
    services.AddDiscoveryTypedClient<INetApi>();
    ...
}


// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    ...
    app.UseDiscoveryClient();
}
```

Controller
```c#
public class HomeController : Controller
{
    public async Task<string> Index([FromServices]INetApi netApi, int id = 0)
    {
        var values = await netApi.GetValuesAsync();
        var value = await netApi.GetValuesAsync(id);
        return "ok";
    }
}
```