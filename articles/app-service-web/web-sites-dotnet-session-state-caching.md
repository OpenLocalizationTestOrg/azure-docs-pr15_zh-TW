<properties 
    pageTitle="Azure Redis Azure 應用程式服務中的快取的工作階段狀態" 
    description="瞭解如何使用 Azure 快取服務支援 ASP.NET 工作階段狀態快取。" 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="Rick-Anderson" 
    manager="wpickett" 
    editor="none"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="get-started-article" 
    ms.date="06/27/2016" 
    ms.author="riande"/>


# <a name="session-state-with-azure-redis-cache-in-azure-app-service"></a>Azure Redis Azure 應用程式服務中的快取的工作階段狀態


本主題說明如何使用 Azure Redis 快取服務工作階段狀態。

如果您的 ASP.NET web 應用程式使用工作階段狀態，您必須設定外部工作階段狀態提供者 （Redis 快取服務或 SQL Server 工作階段狀態提供者）。 如果您使用的工作階段的狀態，並不是使用外部的提供者，您會侷限於至您的 web 應用程式的執行個體之一。 Redis 快取服務是最快速及簡單啟用。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

##<a id="createcache"></a>建立快取
請遵循[這些指示](../cache-dotnet-how-to-use-azure-redis-cache.md#create-cache)建立快取。

##<a id="configureproject"></a>新增 RedisSessionStateProvider NuGet 封裝至您的 web 應用程式
安裝 NuGet`RedisSessionStateProvider`套件。  使用下列命令，從套件管理員主控台安裝 (**工具** > **NuGet 封裝管理員** > **封裝管理員主控台**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
若要從**工具**安裝 > **NuGet 封裝管理員** > **管理解決方案的 NugGet 套件**，搜尋`RedisSessionStateProvider`。

如需詳細資訊，請參閱[NuGet RedisSessionStateProvider 頁面](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ )，並[設定快取用戶端](../cache-dotnet-how-to-use-azure-redis-cache.md#NuGet)。

##<a id="configurewebconfig"></a>修改 Web.Config 檔案
除了讓組件參考的快取，NuGet 套件，請*web.config*檔案中新增虛設常式項目。 

1. 開啟*web.config*並尋找**工作階段狀態**項目。

1. 輸入的值`host`， `accessKey`， `port` （SSL 連接埠應該 6380），並設定`SSL`至`true`。 您可以從您的快取執行個體[Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)刀取得這些值。 如需詳細資訊，請參閱[連線至快取](../cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache)。 請注意新的快取的預設為停用非 SSL 連接埠。 如需有關如何啟用非 SSL 連接埠的詳細資訊，請參閱[Azure Redis 快取中的快取設定](https://msdn.microsoft.com/library/azure/dn793612.aspx)主題中的 [[存取連接埠](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts)] 區段。 下列標記會顯示所做的變更*web.config，特別是所做的變更*連接埠*、*主機*accessKey* *，及*ssl *。

          <system.web>;
            <customErrors mode="Off" />;
            <authentication mode="None" />;
            <compilation debug="true" targetFramework="4.5" />;
            <httpRuntime targetFramework="4.5" />;
            <sessionState mode="Custom" customProvider="RedisSessionProvider">;
              <providers>;  
                  <!--<add name="RedisSessionProvider" 
                    host = "127.0.0.1" [String]
                    port = "" [number]
                    accessKey = "" [String]
                    ssl = "false" [true|false]
                    throwOnError = "true" [true|false]
                    retryTimeoutInMilliseconds = "0" [number]
                    databaseId = "0" [number]
                    applicationName = "" [String]
                  />;-->;
                 <add name="RedisSessionProvider" 
                      type="Microsoft.Web.Redis.RedisSessionStateProvider" 
                      port="6380"
                      host="movie2.redis.cache.windows.net" 
                      accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
                      ssl="true" />;
              <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />;-->;
              </providers>;
            </sessionState>;
          </system.web>;


##<a id="usesessionobject"></a>使用工作階段物件程式碼
若要開始使用的工作階段物件中 ASP.NET 程式碼是最後一個步驟。 您使用**Session.Add**方法將物件新增至工作階段狀態。 這個方法使用關鍵值組來儲存工作階段狀態快取中的項目。

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

下列程式碼會將此值擷取工作階段狀態。

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue; 

您也可以使用 web 應用程式中的快取物件 Redis 快取。 取得詳細資訊，請參閱[Azure Redis 快取在 15 分鐘 MVC 影片應用程式](https://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/)。
如需有關如何使用 ASP.NET 工作階段狀態的詳細資訊，請參閱[ASP.NET 工作階段狀態概觀][]。

>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

## <a name="whats-changed"></a>變更的項目
* 若要變更的指南，從網站應用程式服務請參閱︰ [Azure 應用程式服務與程式影響現有 Azure 服務](http://go.microsoft.com/fwlink/?LinkId=529714)

  *以[Rtf Anderson](https://twitter.com/RickAndMSFT)*
  
  [installed the latest]: http://www.windowsazure.com/downloads/?sdk=net  
  [ASP.NET 工作階段狀態概觀]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
 
