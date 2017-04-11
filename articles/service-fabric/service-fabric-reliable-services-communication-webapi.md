<properties
   pageTitle="服務與 ASP.NET Web API 通訊 |Microsoft Azure"
   description="瞭解如何使用 ASP.NET Web API OWIN 自我裝載可靠的服務 API 中實作逐步服務通訊。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="get-started-service-fabric-web-api-services-with-owin-self-hosting"></a>快速入門︰ 使用 OWIN 自我裝載的服務布料的轉印圖樣 Web API 服務

Azure 服務布料的轉印圖樣置於 power 您將當您決定要如何通訊與使用者與其他服務。 本教學課程著重於實作服務通訊 ASP.NET Web API 使用.NET (OWIN) 自我裝載服務結構可靠的服務 API 中開啟 Web 介面。 我們會將可靠的服務隨插即用通訊 API 深 delve。 我們也會告訴您如何設定 [自訂通訊接聽的逐步範例中使用 Web API。


## <a name="introduction-to-web-api-in-service-fabric"></a>在服務布料的轉印圖樣的網路 API 簡介

ASP.NET Web API 是建立 HTTP Api 上方.NET Framework 常用且強大的架構。 如果您不熟悉架構，請參閱[快速入門 ASP.NET Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) ，瞭解更多。

在服務布料的轉印圖樣的網路 API 是相同的 ASP.NET Web API 您熟悉且愛。 不同的是如何在您的*主機*API Web 應用程式。 您無法使用 Microsoft 網際網路資訊服務 (IIS)。 若要進一步瞭解差異，讓我們來分割為兩個部分︰

 1. Web API 應用程式 （包括控制站與模型）
 2. Host （主機) （網頁伺服器，通常是 IIS）

Web API 應用程式本身不會變更。 無不同從 Web API 應用程式，您可能會寫入過去的經驗，並您應該可以直接移大部分的應用程式碼。 但如果您已在 IIS，主機應用程式可能會稍有不同的用來主控。 我們可以裝載的組件之前，現在就讓我們開始熟悉的多個內容︰ API Web 應用程式。


## <a name="create-the-application"></a>建立應用程式

開始使用單一無服務在 Visual Studio 2015 中建立新的服務布料的轉印圖樣應用程式︰

![建立新的服務布料的轉印圖樣應用程式](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

使用 Web API 無服務的 Visual Studio 範本是您可以使用。 在本教學課程中，我們會建立 Web API 專案中從頭開始，會產生的如果您選取此範本將得到的結果。

選取空白的無狀態服務專案，若要瞭解如何建立 Web API 專案中從頭開始，或是可以無服務 Web API 範本開始，只要遵循。  

![建立單一無狀態服務](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

第一步是 Web API 的即提取有些 NuGet 套件。 我們想要使用的封裝是 Microsoft.AspNet.WebApi.OwinSelfHost。 此套件包含所有必要的 Web API 套件及*主應用程式*套件。 這會很重要更新版本。

![建立 Web API 使用 NuGet 套件管理員](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

已安裝套件之後，就可以開始建置出基本的 Web API 專案結構。 如果您已使用 Web API，則應該不陌生專案結構。 新增來`Controllers`目錄及簡單值控制器︰

**ValuesController.cs**

```csharp
using System.Collections.Generic;
using System.Web.Http;
    
namespace WebService.Controllers
{
    public class ValuesController : ApiController
    {
        // GET api/values 
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5 
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values 
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5 
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5 
        public void Delete(int id)
        {
        }
    }
}

```

接下來，新增專案根目錄登錄路由、 格式器及任何其他設定設定啟動類別。 這也是 Web API 插入至*host （主機）*，稍後再將會重新檢查的位置。 

**Startup.cs**

```csharp
using System.Web.Http;
using Owin;

namespace WebService
{
    public static class Startup
    {
        public static void ConfigureApp(IAppBuilder appBuilder)
        {
            // Configure Web API for self-host. 
            HttpConfiguration config = new HttpConfiguration();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );

            appBuilder.UseWebApi(config);
        }
    }
}
```

這是應用程式組件。 現在，我們已設定只基本網頁 API 專案版面配置。 為止，它不應該看起來不同從 Web API 您可能會寫入過去的專案或基本的 Web API 範本。 您的商務邏輯會控制站與模型像往常一樣。

現在我們做什麼的相關主機服務，讓我們可以實際執行？

## <a name="service-hosting"></a>裝載的服務

服務布料的轉印圖樣，在您的服務會執行中*服務主機處理程序*執行服務程式碼可執行檔。 當您使用可靠的服務 API 撰寫服務時，您的服務專案只編譯可執行檔註冊您的服務類型，並執行程式碼。 當您在服務.net 布料的轉印圖樣撰寫服務時，這是在大部分情況下，則為 true。 當您開啟 Program.cs 無服務專案時，您應該會看到︰

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric.Services.Runtime;

internal static class Program
{
    private static void Main()
    {
        try
        {
            ServiceRuntime.RegisterServiceAsync("WebServiceType",
                context => new WebService(context)).GetAwaiter().GetResult();

            ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(WebService).Name);

            // Prevents this host process from terminating so services keeps running. 
            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

如果這個樣子過進入點與主控台應用程式，這是因為它。

進一步的詳細資料的服務主機處理程序與服務註冊已超過本文的範圍。 但是請務必瞭解現在*在自己的程序執行服務程式碼*。

## <a name="self-host-web-api-with-an-owin-host"></a>自動將 Web API 託管 OWIN host （主機）

假設您網站的 API 應用程式碼裝載在自己的程序，如何您將它連結到網頁伺服器？ 輸入[OWIN](http://owin.org/)。 OWIN 是只要.NET web 應用程式與網頁伺服器之間合約。 傳統方式使用 ASP.NET （最多 MVC 5) 時，web 應用程式緊密結合，透過 System.Web iis。 不過，網頁 API 實作 OWIN，，所以您可以從網頁伺服器主控撰寫分開獨立的 web 應用程式。 因此，您可以使用*自動裝載*OWIN 網頁伺服器，您可以開始在自己的程序。 此完全符合我們先前所提到的服務布料的轉印圖樣裝載模型。

本文中，我們將使用武士 OWIN 主 Web API 應用程式。 武士是開啟來源 OWIN 主機實作內建在[System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener.aspx)和 Windows [HTTP 伺服器 API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)。

> [AZURE.NOTE] 若要進一步瞭解武士，移至[武士網站](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana)。 如需如何使用武士自我裝載 Web API 的快速概觀，請參閱[使用的 OWIN Self-Host ASP.NET Web API 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api)。


## <a name="set-up-the-web-server"></a>設定網頁伺服器

可靠的服務 API 提供您可以插入在允許使用者和用戶端連線至服務的通訊堆疊通訊進入點︰

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

網頁伺服器 （與您在未來，例如 WebSockets 使用其他通訊堆疊） 應該使用 ICommunicationListener 介面正確與系統整合。 原因會變成更清楚看到下列步驟。

首先，建立一個名為 OwinCommunicationListener 實作 ICommunicationListener:

**OwinCommunicationListener.cs**

```csharp
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;
using System;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}
```

ICommunicationListener 介面提供三種方法來管理您的服務的通訊接聽程式︰

 - *OpenAsync*。 開始接聽要求。
 - *CloseAsync*。 停止接聽要求、 完成 「 執行中 」 的要求，並正常關機。
 - *中止*。 取消所有項目，並立即停止。

若要開始，新增私用類別成員項接聽需要函數。 這些會透過建構函式初始化並設定聆聽 URL 時稍後使用。

```csharp
internal class OwinCommunicationListener : ICommunicationListener
{
    private readonly ServiceEventSource eventSource;
    private readonly Action<IAppBuilder> startup;
    private readonly ServiceContext serviceContext;
    private readonly string endpointName;
    private readonly string appRoot;

    private IDisposable webApp;
    private string publishAddress;
    private string listeningAddress;

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
        : this(startup, serviceContext, eventSource, endpointName, null)
    {
    }

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
    {
        if (startup == null)
        {
            throw new ArgumentNullException(nameof(startup));
        }

        if (serviceContext == null)
        {
            throw new ArgumentNullException(nameof(serviceContext));
        }

        if (endpointName == null)
        {
            throw new ArgumentNullException(nameof(endpointName));
        }

        if (eventSource == null)
        {
            throw new ArgumentNullException(nameof(eventSource));
        }

        this.startup = startup;
        this.serviceContext = serviceContext;
        this.endpointName = endpointName;
        this.eventSource = eventSource;
        this.appRoot = appRoot;
    }
   

    ...

```

## <a name="implement-openasync"></a>實作 OpenAsync

若要設定網頁伺服器，您需要兩個部分資訊︰

 - *URL 路徑前置字元*。 雖然這是選擇性的請務必為您設定此現在，讓您可以安全地主控您的應用程式中的多個 web 服務。
 - *連接埠*。

您取得網頁伺服器的連接埠之前，請務必瞭解服務架構提供應用程式層級，做為基礎的作業系統上執行應用程式之間緩衝。 因此，服務布料的轉印圖樣會提供服務的*結束點*的設定方式。 服務布料的轉印圖樣可確保結束點可供您使用的服務。 如此一來，您不需要設定您自己的基礎 OS 環境。 您輕鬆地可以裝載服務布料的轉印圖樣應用程式在不同環境中，而不必變更您的應用程式。 （例如，您可以主控 Azure 或您自己的資料中心的同一個應用程式。）

設定 PackageRoot\ServiceManifest.xml HTTP 端點︰

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="8281" />
    </Endpoints>
</Resources>

```

這個步驟很重要，因為服務主機處理程序執行 [限制認證 （網路服務的 Windows）] 底下。 這表示您的服務無法存取 HTTP 端點上設定自己。 藉由使用端點組態，服務布料的轉印圖樣設定適當的存取控制清單 (ACL) 知道上接聽服務的 url。 服務布料的轉印圖樣也提供標準的位置，以設定結束點。


回 OwinCommunicationListener.cs，您就可以開始實作 OpenAsync。 這是您啟動網頁伺服器的位置。 首先，取得的結束點資訊，並建立接聽服務的 URL。 URL 會接聽是否使用中狀態的服務或狀態服務而有所不同。 設定狀態服務，接聽需要建立唯一的地址，其接聽每個狀態的服務複本。 如需無狀態服務，可以可更容易地址。 

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
    var protocol = serviceEndpoint.Protocol;
    int port = serviceEndpoint.Port;

    if (this.serviceContext is StatefulServiceContext)
    {
        StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}{3}/{4}/{5}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/',
            statefulServiceContext.PartitionId,
            statefulServiceContext.ReplicaId,
            Guid.NewGuid());
    }
    else if (this.serviceContext is StatelessServiceContext)
    {
        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/');
    }
    else
    {
        throw new InvalidOperationException();
    }
    
    ...

```

請注意，以下使用 「 http://+ 」。 這是以確定網頁伺服器傾聽所有可用的位址，包括本機、 FQDN 及電腦 IP。

OpenAsync 實作是最重要的原因為何網頁伺服器 （或任何通訊堆疊） 實作 ICommunicationListener，而不是只將它開啟直接從`RunAsync()`服務。 從 OpenAsync 傳回的值是上接聽網頁伺服器的位址。 當系統傳回此位址時，它會與服務註冊地址。 服務架構提供一個 API，讓服務名稱，然後要求至此地址的用戶端和其他服務。 這是很重要，因為服務地址不是靜態。 服務用於資源平衡與可用性叢集內移動。 這是可以讓解決服務的聆聽位址的用戶端機制。

這一點之後，OpenAsync 開始網頁伺服器，並傳回接聽的地址。 附註 」 http://+ 」，但之前 OpenAsync 傳回的地址，其接聽 [+] 會取代 IP 或目前在節點的 FQDN。 這個方法所傳回的地址是什麼註冊與系統。 還有什麼用戶端和其他服務時看到他們尋求服務的地址。 用戶端正確連接到它，需要實際的 IP 或 FQDN 地址。

```csharp
    ...

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    try
    {
        this.eventSource.Message("Starting web server on " + this.listeningAddress);

        this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

        this.eventSource.Message("Listening on " + this.publishAddress);

        return Task.FromResult(this.publishAddress);
    }
    catch (Exception ex)
    {
        this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

        this.StopWebServer();

        throw;
    }
}

```

請注意此參照傳遞建構函式 OwinCommunicationListener 啟動類別。 網頁伺服器使用此啟動執行個體來啟動 Web API 應用程式。

`ServiceEventSource.Current.Message()`列會出現在診斷事件視窗之後，當您執行的應用程式，確認已順利啟動網頁伺服器。

## <a name="implement-closeasync-and-abort"></a>實作 CloseAsync 及中止

最後，實作 CloseAsync 及中止停止網頁伺服器。 處置 OpenAsync 時所建立的伺服器控點，可停止網頁伺服器。

```csharp
public Task CloseAsync(CancellationToken cancellationToken)
{
    this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);
            
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);
    
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.webApp != null)
    {
        try
        {
            this.webApp.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}
```

實作，在本例 CloseAsync 和中止只會停止網頁伺服器。 您可以選擇性地 CloseAsync 中執行更多正常協調的關機的網頁伺服器。 例如關機可能等待完成後再傳回 「 執行中 」 的要求。

## <a name="start-the-web-server"></a>啟動 web 伺服器

現在，您準備好要建立並傳回 OwinCommunicationListener 開始網頁伺服器執行個體。 回服務類別 (WebService.cs) 中，覆寫`CreateServiceInstanceListeners()`方法︰

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                           .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                           .Select(endpoint => endpoint.Name);

    return endpoints.Select(endpoint => new ServiceInstanceListener(
        serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
}
```

這是 Web API*應用程式*和 OWIN*主機*位置最後符合。 Host （主機） (OwinCommunicationListener) 提供的*應用程式*(Web API) 透過啟動類別執行個體。 服務布料的轉印圖樣然後管理其生命週期。 同一個樣式通常與任何通訊堆疊已追蹤。

## <a name="put-it-all-together"></a>全部整合

在此範例中，您不需要執行任何動作`RunAsync()`方法，讓該覆寫只會移除。

最終服務實作應該非常簡單。 只需要建立通訊接聽程式︰

```csharp
using System;
using System.Collections.Generic;
using System.Fabric;
using System.Fabric.Description;
using System.Linq;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace WebService
{
    internal sealed class WebService : StatelessService
    {
        public WebService(StatelessServiceContext context)
            : base(context)
        { }

        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                                   .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                                   .Select(endpoint => endpoint.Name);

            return endpoints.Select(endpoint => new ServiceInstanceListener(
                serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
        }
    }
}
```

完成`OwinCommunicationListener`類別︰

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        private readonly ServiceEventSource eventSource;
        private readonly Action<IAppBuilder> startup;
        private readonly ServiceContext serviceContext;
        private readonly string endpointName;
        private readonly string appRoot;

        private IDisposable webApp;
        private string publishAddress;
        private string listeningAddress;

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
            : this(startup, serviceContext, eventSource, endpointName, null)
        {
        }

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
        {
            if (startup == null)
            {
                throw new ArgumentNullException(nameof(startup));
            }

            if (serviceContext == null)
            {
                throw new ArgumentNullException(nameof(serviceContext));
            }

            if (endpointName == null)
            {
                throw new ArgumentNullException(nameof(endpointName));
            }

            if (eventSource == null)
            {
                throw new ArgumentNullException(nameof(eventSource));
            }

            this.startup = startup;
            this.serviceContext = serviceContext;
            this.endpointName = endpointName;
            this.eventSource = eventSource;
            this.appRoot = appRoot;
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
            var protocol = serviceEndpoint.Protocol;
            int port = serviceEndpoint.Port;

            if (this.serviceContext is StatefulServiceContext)
            {
                StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}{3}/{4}/{5}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/',
                    statefulServiceContext.PartitionId,
                    statefulServiceContext.ReplicaId,
                    Guid.NewGuid());
            }
            else if (this.serviceContext is StatelessServiceContext)
            {
                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/');
            }
            else
            {
                throw new InvalidOperationException();
            }

            this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            try
            {
                this.eventSource.Message("Starting web server on " + this.listeningAddress);

                this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

                this.eventSource.Message("Listening on " + this.publishAddress);

                return Task.FromResult(this.publishAddress);
            }
            catch (Exception ex)
            {
                this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

                this.StopWebServer();

                throw;
            }
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.webApp != null)
            {
                try
                {
                    this.webApp.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}
```

現在，您必須放所有片段的位置，您的專案看起來應該像一般的 Web API 應用程式可靠的服務 API 進入點與 OWIN host （主機）︰


![網路 API 可靠的服務 API 進入點與 OWIN host （主機）](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## <a name="run-and-connect-through-a-web-browser"></a>執行，並透過網頁瀏覽器連線

如果您尚未這麼做，[設定您的開發環境](service-fabric-get-started.md)。


您現在可以建立並部署您的服務。 在 Visual Studio 中建立並部署應用程式，請按**F5** 。 在 [診斷事件] 視窗中，您應該會看到訊息，指出 http://localhost:8281 上開啟的網頁伺服器 /。


![Visual Studio 診斷事件視窗](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE] 如果已由其他處理程序，在您的電腦上開啟連接埠，您可能會看到以下錯誤。 這表示無法開啟的接聽程式。 如果是這樣，請嘗試使用不同的連接埠 ServiceManifest.xml 端點設定。


一旦執行服務，請開啟瀏覽器，並瀏覽至[http://localhost:8281/api 值](http://localhost:8281/api/values)進行測試。

## <a name="scale-it-out"></a>查看不按比例縮放

縮放取出狀態的 web 應用程式通常表示新增更多機器及設定 web 應用程式在旋轉。 服務布料的轉印圖樣的協調流程引擎這麼做為您只要叢集加入新的節點。 當您建立無服務的執行個體時，您可以指定您想要建立的執行個體數目。 服務布料的轉印圖樣會叢集節點的數字的執行個體。 然後，可確保建立多個執行個體的任何一個節點。 您也可以指示服務指定執行個體計數**-1** ，永遠建立的每個節點的執行個體布料的轉印圖樣。 如此一來，可確保，每當您新增擴充叢集節點，無狀態服務的執行個體將會建立新的節點。 此值是當您建立的服務執行個體設定服務執行個體的屬性。 您可以透過 PowerShell 來這麼做︰

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

您也可以執行此 Visual Studio 無服務專案中定義的預設服務時︰

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

如需有關如何建立應用程式及服務執行個體的詳細資訊，請參閱[部署應用程式](service-fabric-deploy-remove-applications.md)。

## <a name="next-steps"></a>後續步驟

[使用 Visual Studio 偵錯應用程式服務布料的轉印圖樣](service-fabric-debugging-your-application.md)
