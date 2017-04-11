<properties
    pageTitle="快取 ASP.NET 工作階段狀態提供者 |Microsoft Azure"
    description="瞭解如何儲存 ASP.NET 工作階段狀態使用 Azure Redis 快取"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="09/01/2016"
    ms.author="sdanie" />

# <a name="aspnet-session-state-provider-for-azure-redis-cache"></a>ASP.NET 工作階段狀態提供者 Azure Redis 快取

Azure Redis 的快取提供可用來儲存您的工作階段的狀態，而不是記憶體內的快取中或 SQL Server 資料庫中的工作階段狀態提供者。 若要使用快取的工作階段狀態提供者，請先設定您的快取，然後設定您的 ASP.NET 應用程式，使用 Redis 快取工作階段狀態 NuGet 套件的快取。

通常不是實務在真實世界雲端應用程式，若要避免使用者工作階段，儲存的狀態，但一些方法影響效能與延展性多個其他人。 如果您有儲存狀態，最好的方法是狀態的小數量，並將其儲存在 cookie。 如果不可行下, 一步最佳解決方案是 ASP.NET 工作階段狀態使用的分散式、 記憶體內的快取提供者。 從的效能與延展性的觀點來看表現最差的解決方案是使用資料庫備份工作階段狀態提供者。 本主題提供的指導方針 Azure Redis 快取使用 ASP.NET 工作階段狀態提供者。 其他工作階段狀態選項的詳細資訊，請參閱[ASP.NET 工作階段狀態選項](#aspnet-session-state-options)。

## <a name="store-aspnet-session-state-in-the-cache"></a>在 [快取中儲存 ASP.NET 工作階段狀態

若要在 Visual Studio 中使用 Redis 快取工作階段狀態 NuGet 封裝設定用戶端應用程式，以滑鼠右鍵按一下 [專案**總管**] 中的，然後選擇 [**管理 NuGet 套件**。

![Azure 意指快取管理 NuGet 套件](./media/cache-aspnet-session-state-provider/redis-cache-manage-nuget-menu.png)

搜尋文字] 方塊中輸入**RedisSessionStateProvider**從結果]，選取它，按一下 [**安裝**]。

>[AZURE.IMPORTANT] 如果您使用的從進階版層的叢集功能，您必須使用[RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 或更新版本，或例外會擲回 expression.error。 此最新的變更。如需詳細資訊，請參閱[v2.0.0 重大變更的詳細資料](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details)。

![Azure 意指快取工作階段狀態提供者](./media/cache-aspnet-session-state-provider/redis-cache-session-state-provider.png)

Redis 工作階段狀態提供者 NuGet 套件 StackExchange.Redis.StrongName 套件上有相依性。 如果您將會安裝在 project 中沒有 StackExchange.Redis.StrongName 套件。 請注意，除了強式 StackExchange.Redis.StrongName 套件也 StackExchange.Redis 非-強式版本。 如果您的專案使用的您必須解除安裝之前, 或之後安裝 Redis 工作階段狀態提供者 NuGet 套件的非-強式 StackExchange.Redis 版本，否則您將會取得命名衝突您專案中。 如需有關這些套件的詳細資訊，請參閱[設定.NET 快取用戶端](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)。

下載 NuGet 套件，並新增必要的組件參照，並新增動作到 web.config 檔案包含 ASP.NET 應用程式使用 Redis 快取工作階段狀態提供者所需的設定新增下一節。

    <sessionState mode="Custom" customProvider="MySessionStateStore">
        <providers>
        <!--
        <add name="MySessionStateStore"
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
        />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false"/>
        </providers>
    </sessionState>

[註解] 區段中提供的每個屬性的範例設定屬性的範例。

設定屬性與您在 Microsoft Azure 入口網站中的快取刀中的值，並設定其他的值，視。 如需存取您的快取屬性的指示，請參閱[設定 Redis 快取設定](cache-configure.md#configure-redis-cache-settings)。

-   **host （主機）** – 指定您快取結束點。
-   **連接埠**– 使用非 SSL 連接埠或 SSL 連接埠，ssl 而定。
-   **accessKey** – 使用快取的主要或次要鍵。
-   **ssl** – 如果您想要保護快取/用戶端通訊與 ssl; 則為 true否則為 false。 請務必指定正確的連接埠。
    -   新的快取的預設為停用非 SSL 連接埠。 指定為 true，此設定為使用 SSL 連接埠。 如需有關如何啟用非 SSL 連接埠的詳細資訊，請參閱[設定快取](cache-configure.md)主題中的 [[存取連接埠](cache-configure.md#access-ports)] 區段。
-   **throwOnError** – 如果您想要的例外狀況發生失敗或 false 時，如果您想要自動失效作業則為 true。 您可以檢查失敗，請核取靜態 Microsoft.Web.Redis.RedisSessionStateProvider.LastException 屬性。 預設值為 true。
-   在此間隔內，以毫秒為單位重試**retryTimeoutInMilliseconds** – 失敗的作業。 第一個重試之後 20 毫秒為單位，然後重試會發生 retryTimeoutInMilliseconds 間隔到期，直到每秒。 緊接這段操作重試一次一最後一次。 如果仍然失敗，例外狀況回給呼叫者，視 throwOnError 設定而定。 預設值是 0 表示沒有重試。
-   **databaseId** – 指定来使用哪一個資料庫快取輸出的資料。 如果未指定會使用預設值 0。
-   **applicationName** – 鍵會儲存在為意指`{<Application Name>_<Session ID>}_Data`。 這可讓多個應用程式共用相同的金鑰。 此為選用參數，如果您不提供其使用預設值。
-   **connectionTimeoutInMilliseconds** – 此設定可讓您覆寫 connectTimeout StackExchange.Redis 用戶端的設定。 如果未指定預設 connectTimeout 的 5000 使用設定。 如需詳細資訊，請參閱[StackExchange.Redis 設定模型](http://go.microsoft.com/fwlink/?LinkId=398705)。
-   **operationTimeoutInMilliseconds** – 此設定可讓您覆寫 syncTimeout StackExchange.Redis 用戶端的設定。 如果未指定會使用預設 syncTimeout 設定為 1000年。 如需詳細資訊，請參閱[StackExchange.Redis 設定模型](http://go.microsoft.com/fwlink/?LinkId=398705)。

如需有關這些屬性的詳細資訊，請參閱在[宣佈使用 ASP.NET 工作階段狀態提供者意指](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)原始的部落格文章公告。

別忘了為註解的標準 InProc 工作階段狀態提供者一節您 web.config。

    <!-- <sessionState mode="InProc"
         customProvider="DefaultSessionProvider">
         <providers>
            <add name="DefaultSessionProvider"
                  type="System.Web.Providers.DefaultSessionStateProvider,
                        System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                        PublicKeyToken=31bf3856ad364e35"
                  connectionStringName="DefaultConnection" />
          </providers>
    </sessionState> -->

之後，請執行下列步驟，會將您的應用程式設定為使用 Redis 快取工作階段狀態提供者。 當您在應用程式中使用工作階段狀態時，它會儲存在 Azure Redis 快取執行個體。

>[AZURE.NOTE] 請注意，必須序列化快取中儲存的資料，可以在預設儲存的資料與記憶體內 ASP.NET 工作階段狀態提供者。 使用意指工作階段狀態提供者時，請確定儲存在階段中的資料類型是序列化。

## <a name="aspnet-session-state-options"></a>ASP.NET 工作階段狀態選項

- 記憶體工作階段狀態提供者-在此提供者會儲存在記憶體工作階段狀態。 使用此提供者的優點是簡單且快速。 不過，您無法擴充 Web 應用程式如果您因為其分散記憶體提供者中使用。

- Sql Server 工作階段狀態提供者-此提供者會在 Sql Server 中儲存工作階段狀態。 如果您想要保存工作階段中的狀態常設儲存空間，您應該使用此提供者。 您可以調整您的 Web 應用程式，但使用 Sql Server 工作階段會影響效能，在您的 Web 應用程式。

- 分佈的記憶體工作階段狀態提供者例如 Redis 快取工作階段狀態提供者的這個提供者，可讓您運用這兩者的最佳。 您的 Web 應用程式可以有簡單、 快速及調整的工作階段狀態提供者。 但是您可以保留在此提供者的快取中儲存工作階段狀態，和您的應用程式必須考量中將所有相關分佈的記憶體快取例如暫時網路失敗談話時的特性的考量。 使用快取的最佳作法，請參閱從 Microsoft 模式與作法[Azure 雲端應用程式的設計及實作](https://github.com/mspnp/azure-guidance)[快取指引](../best-practices-caching.md)。

如需工作階段狀態和其他的最佳作法的詳細資訊，請參閱[Web 開發最佳作法 （建置真實世界雲端應用程式搭配使用 Azure）](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices)。

## <a name="next-steps"></a>後續步驟

請參閱[Azure Redis 快取的 ASP.NET 輸出快取提供者](cache-aspnet-output-cache-provider.md)。
