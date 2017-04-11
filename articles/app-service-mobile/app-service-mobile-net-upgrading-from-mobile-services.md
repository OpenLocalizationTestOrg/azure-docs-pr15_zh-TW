<properties
    pageTitle="從行動裝置的服務升級至 Azure 應用程式服務"
    description="瞭解如何輕鬆升級您的行動電話服務應用程式至應用程式服務行動應用程式"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>升級您現有的.NET Azure 行動訊息服務應用程式服務

行動應用程式服務是建立使用 Microsoft Azure 的行動應用程式的新方式。 若要深入瞭解，請參閱[哪些行動應用程式？]。

本主題說明如何從 Azure 行動服務升級現有的.NET 後端應用程式，以新的應用程式服務行動應用程式。 當您執行此升級時，可以繼續現有的行動電話服務應用程式運作。   如果您需要升級 Node.js 後端應用程式，請參閱[升級 Node.js 行動服務](./app-service-mobile-node-backend-upgrading-from-mobile-services.md)。

當行動的後端升級至 Azure 應用程式服務時，它會有權存取所有的應用程式服務功能，並根據[應用程式服務價格]、 不有行動電話服務的價格向收費。

##<a name="migrate-vs-upgrade"></a>升級和移轉

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] 建議您[執行移轉](app-service-mobile-migrating-from-mobile-services.md)之前進行升級。 如此一來，您可以將應用程式的兩個版本放在相同的應用程式服務方案，並會造成任何其他成本。

###<a name="improvements-in-mobile-apps-net-server-sdk"></a>在行動應用程式.NET server SDK 的改良功能

升級至新的[行動應用程式 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)有下列好處︰

- 更多的彈性 NuGet 相依性。 主控環境不再提供 NuGet 套件的版本，您可以使用替代相容的版本。 不過，如果有新的要徑 bugfixes 或行動伺服器 SDK 或相依性的安全性更新，您必須手動更新您的服務。

- 行動 SDK 更具彈性。 您可以明確地控制哪些功能與路由設定，例如驗證、 Api，表格及推入註冊結束點。 若要深入瞭解，請參閱[如何使用.NET 伺服器 SDK Azure Mobile 應用程式](app-service-mobile-net-upgrading-from-mobile-services.md#server-project-setup)。

- 支援其他 ASP.NET 專案類型和路由。 您現在可以主控您的行動裝置的後端專案的同一個專案中的 MVC 與 Web API 控制站。

- 支援新的應用程式服務驗證功能，可讓您在您的網頁和行動應用程式使用一般的驗證設定。

##<a name="overview"></a>基本升級的概觀

在許多情況下，在升級會非常簡單，切換到新的行動應用程式伺服器 SDK 並重新發佈您的程式碼，拖曳至新的行動應用程式執行個體。 有，不過這需要一些額外的設定，例如進階的驗證案例及處理某些案例排程的工作。 每個討論稍後的章節。

>[AZURE.TIP] 建議您閱讀並完全啟動升級前先瞭解此主題的其餘部分。 請記下您使用的任何功能的圖說文字下方。

行動電話服務用戶端 Sdk 都**不**相容的新的行動應用程式伺服器 SDK。 若要提供的服務應用程式的連續性，發佈變更至目前服務已發佈的用戶端的網站。 不過，您應該建立新的行動應用程式，做為重複。 若要避免造成其他財務成本相同的應用程式服務方案，您可以將此應用程式。

然後，您會有兩個版本的應用程式︰ 會保持不變，並提供的其中一個發佈應用程式中造成，另一個您可以升級並使用新的用戶端版本的目標。 您可以移動及測試您的程式碼，您的速度，但請確定您進行任何錯誤修正取得套用至兩者。 一旦您認為肆虐的應用程式更新為最新版本的用戶端所要的數字，您可以刪除原始的移轉應用程式是否您想要。

完整的外框的升級程序如下所示︰

1. 建立新的行動應用程式
2. 更新專案使用新的伺服器 Sdk
3. 發行用戶端應用程式的新版本
4. （選用）刪除您原始的移轉執行個體

##<a name="mobile-app-version"></a>建立第二個應用程式執行個體
升級的第一步是建立其會裝載您的應用程式的新版本的行動應用程式資源。 如果您已經有移轉現有的行動訊息服務，您會想要建立這一版相同的主機服務方案。 開啟[Azure 入口網站]，然後瀏覽至您的移轉應用程式。 請記下的應用程式服務，規劃上執行。

接下來，建立第二個應用程式執行個體的[.NET 後端建立指示](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app)執行。 當系統提示您選取應用程式服務方案，或 「 裝載計劃 」 選擇移轉應用程式的方案。

您可能會想要使用的相同的資料庫及通知] 中心內，如行動電話服務中的。 您可以將這些值，複製開啟[Azure 入口網站]，並瀏覽至原始的應用程式，然後按一下 [**設定** > **應用程式設定**。 **連接字串**，複製`MS_NotificationHubConnectionString`和`MS_TableConnectionString`。 瀏覽至您的新升級網站並貼上它們，覆寫任何現有的值。 重複此程序的任何其他應用程式設定您的應用程式需求。 如果不使用已移轉的服務，您可以瞭解[Azure 傳統入口網站]的 [行動電話服務] 區段的 [**設定**] 索引標籤的連接字串與應用程式設定。

複製的應用程式的 ASP.NET 專案，並將它發佈到您的新網站。 使用新的 URL 以更新的用戶端應用程式的複本，確認一切運作正常。

## <a name="updating-the-server-project"></a>更新伺服器專案

行動應用程式中提供新版[行動應用程式伺服器 SDK]提供許多的行動電話服務執行階段相同的功能。 首先，您應該移除所有參照到行動電話服務套件。 在 [NuGet 套件管理員] 中，搜尋`WindowsAzure.MobileServices.Backend`。 大部分的應用程式將會看到多個封包，包括`WindowsAzure.MobileServices.Backend.Tables`和`WindowsAzure.MobileServices.Backend.Entity`。 在此情況下，開始使用最低套件的相依性樹狀目錄中，例如`Entity`，並將它移除。 出現提示時，不會移除所有的相依性套件。 重複此程序，直到您移除了`WindowsAzure.MobileServices.Backend`本身。

此時，您必須不再參照行動服務 Sdk 的專案。

接下來，您將加入參照行動應用程式 SDK。 此升級時，大部分的開發人員會想要下載及安裝`Microsoft.Azure.Mobile.Server.Quickstart`封裝，這將會提取在整個所需的設定。

會有很多編譯器的錯誤 Sdk，之間的差異，但這些很容易地址，並包含此節的其餘部分中。

### <a name="base-configuration"></a>基本設定

然後，在 WebApiConfig.cs，您可以取代︰

        // Use this class to set configuration options for your mobile service
        ConfigOptions options = new ConfigOptions();

        // Use this class to set WebAPI configuration options
        HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

使用

        HttpConfiguration config = new HttpConfiguration();
        new MobileAppConfiguration()
            .UseDefaultConfiguration()
        .ApplyTo(config);

>[AZURE.NOTE] 如果您想要進一步瞭解新的.NET 伺服器 SDK，以及如何新增或移除功能從您的應用程式，請參閱[如何使用.NET 伺服器 SDK]主題。

如果您的應用程式可讓您使用的驗證功能，您也必須註冊 OWIN 介軟體。 在此情況下，您應該將上述設定程式碼移到新 OWIN 啟動類別中。

1. 新增 NuGet 套件`Microsoft.Owin.Host.SystemWeb`如果它已不包含在專案中。
2. 在 Visual Studio 中，以滑鼠右鍵按一下您的專案，然後選取 [**新增** -> **新項目**。 選取 [ **Web** -> **一般** -> **OWIN 啟動類別**。
3. 移動 MobileAppConfiguration 從以上的程式碼`WebApiConfig.Register()`至`Configuration()`啟動您的新類別的方法。

請確定`Configuration()`方法一句是︰

        app.UseWebApi(config)
        app.UseAppServiceAuthentication(config);

有其他變更有關驗證涵蓋的下方的完整驗證一節。

### <a name="working-with-data"></a>使用資料

在 [行動服務行動應用程式名稱 served 預設名稱為結構描述中的實體架構設定。

若要確保您有相同的結構描述之前一樣，使用下列設定結構描述中應用程式的 DbContext 參考︰

        string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");

請確定您有 MS_MobileServiceName 如果您執行上述設定。 如果您的應用程式自訂先前方式，您也可以提供其他的結構描述名稱。

### <a name="system-properties"></a>系統內容

#### <a name="naming"></a>命名

在 [Azure 行動服務伺服器 SDK，系統內容永遠包含雙底線 (`__`) 前置詞的屬性︰

- __createdAt
- __updatedAt
- __deleted
- __version

行動電話服務用戶端 Sdk 有特殊邏輯剖析此格式的 [系統內容。

Azure 行動應用程式]，在系統內容不會再具有特殊格式，並有下列名稱︰

- createdAt
- updatedAt
- 刪除的郵件
- 版本

行動應用程式用戶端 Sdk 使用新的系統屬性名稱，因此不不需要用戶端程式碼的任何變更。 不過，如果您直接對其餘的來電至您的服務就應該變更您的查詢會相應地。

#### <a name="local-store"></a>本機存放區

系統屬性的名稱變更表示行動服務的離線同步處理的本機資料庫不會與 Mobile 應用程式的相容。 如果可能的話，您應避免升級應用程式從行動電話服務直到之後的行動應用程式的變更擱置已傳送至伺服器的用戶端。 然後，升級的應用程式應該使用新的資料庫檔案名稱。

如果在用戶端應用程式升級行動服務的行動應用程式作業佇列中所做的變更擱置有時，系統資料庫必須更新以使用新的資料行名稱。 在 iOS，如下使用精簡的移轉，若要變更的資料行名稱。 在 Android 或.NET 受管理的用戶端，您應該撰寫自訂 SQL 若要重新命名您的資料物件表格的資料行。

在 iOS，您應該變更資料以符合下列實體核心資料結構的描述。 請注意，屬性`createdAt`，`updatedAt`和`version`不再`ms_`首碼︰

| 屬性 |  類型   | 附註                                                 |
|---------- |  ------ | -----------------------------------------------------|
| 識別碼        | 標示為必要的字串  | 在遠端存放區中的主索引鍵         |
| createdAt | 日期    | （選擇性） 對應至 createdAt 系統屬性         |
| updatedAt | 日期    | （選擇性） 對應至 updatedAt 系統屬性         |
| 版本   | 字串  | （選擇性） 用於偵測衝突，版本的地圖 |

#### <a name="querying-system-properties"></a>查詢系統內容

Azure 行動服務，在系統內容不會傳送根據預設，但僅在使用中的查詢字串要求時，才`__systemProperties`。 相反地，在 Azure Mobile 應用程式系統屬性會**一律選取**因為它們是伺服器 SDK 物件模型的一部分。

這項變更主要影響的網域管理員，例如副檔名的自訂實作`MappedEntityDomainManager`。 在行動服務中，如果在用戶端永遠不會要求任何系統內容]，可能是使用`MappedEntityDomainManager`，並未實際對應所有內容。 不過，在 Azure 行動應用程式，這些未對應的屬性會導致取得查詢中的錯誤。

若要解決此問題最簡單的方法是修改您 DTOs，讓他們繼承`ITableData`而不是`EntityData`。 然後，新增`[NotMapped]`屬性應該省略的欄位。

例如，下列定義`TodoItem`與沒有系統內容︰

    using System.ComponentModel.DataAnnotations.Schema;

    public class TodoItem : ITableData
    {
        public string Text { get; set; }

        public bool Complete { get; set; }

        public string Id { get; set; }

        [NotMapped]
        public DateTimeOffset? CreatedAt { get; set; }

        [NotMapped]
        public DateTimeOffset? UpdatedAt { get; set; }

        [NotMapped]
        public bool Deleted { get; set; }

        [NotMapped]
        public byte[] Version { get; set; }
    }

注意︰ 如果您收到錯誤`NotMapped`，新增組件的參考`System.ComponentModel.DataAnnotations`。

### <a name="cors"></a>CORS

行動電話服務會包括 CORS 一些支援的文繞圖的 ASP.NET CORS 解決方案。 若要授與開發人員更多的控制，因此您可以直接使用[支援的 ASP.NET CORS](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api)已經移除此文繞圖層。

如果使用 CORS 考量的主要區域所的`eTag`和`Location`必須允許標頭，為 Sdk 用戶端才能正常運作的順序。

### <a name="push-notifications"></a>推播通知
推入，您可能會發現遺失從伺服器 SDK 的主要項目是 PushRegistrationHandler 類別。 登錄處理方式稍有不同的行動應用程式，並不進行之註冊依預設會啟用。 管理標籤可能會使用自訂的 Api 來完成。 請參閱[註冊標籤](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags)相關指示，如需詳細資訊。

### <a name="scheduled-jobs"></a>排程的工作
排程的工作不內建行動應用程式，因此任何現有的工作，您必須在您的.NET 後端必須個別升級。 其中一個選項是在行動應用程式碼網站上建立排程的[Web 工作]。 您也可以設定擁有您的工作程式碼控制站，設定叫用預期排程端點[Azure 排程器]。

### <a name="miscellaneous-changes"></a>其他變更
現在必須具備可由行動用戶端的所有 ApiControllers`[MobileAppController]`屬性。 這是不再包含根據預設，因此其他 ApiControllers 到行動裝置的格式子不會受到影響。

`ApiServices`物件不再 SDK 的一部分。 若要存取 Mobile 應用程式設定，您可以使用下列步驟︰

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

同樣地，記錄現在已完成使用標準 ASP.NET 追蹤撰寫︰

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

##<a name="authentication"></a>驗證考量

行動電話服務的驗證元件現在已移到應用程式服務驗證和授權功能。 您可以瞭解啟用此網站閱讀[新增至您的行動應用程式的驗證](app-service-mobile-ios-get-started-users.md)主題。

部分提供者，例如 AAD、 Facebook 和 Google，您應該可以運用現有的註冊從複製應用程式。 您只需要瀏覽至身分識別提供者的網站，並將新的重新導向 URL 新增至註冊。 用戶端識別碼與密碼，然後設定應用程式服務驗證和授權。

### <a name="controller-action-authorization"></a>控制站動作授權
任何執行個體`[AuthorizeLevel(AuthorizationLevel.User)]`屬性必須現在會變更為使用標準 ASP.NET`[Authorize]`屬性。 此外，控制站現在匿名根據預設，與其他 ASP.NET 應用程式。
如果您使用其中一個 AuthorizeLevel 選項，例如管理員或應用程式，請注意，這些是消失。 您可以改為設定共用密碼 AuthorizationFilters，或設定以安全地啟用服務-服務呼叫 AAD 服務主要。

### <a name="getting-additional-user-information"></a>取得其他使用者的資訊

您可以取得額外的使用者資訊，包括存取權杖，透過`GetAppServiceIdentityAsync()`方法︰

        FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();

此外，如果您的應用程式上的使用者識別碼，耗費相依性，例如將它們儲存在資料庫，請務必請注意，行動電話服務與應用程式服務行動應用程式之間的使用者識別碼不同。 您仍然可以透過取得行動服務使用者識別碼。 所有 ProviderCredentials 子類別有的使用者識別碼屬性。 因此，繼續從之前的範例︰

        string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

如果您的應用程式會採取任何相依性的使用者識別碼，請務必讓您運用相同的登錄與身分識別提供者可能的話。 使用者識別碼通常是範圍只限所使用的應用程式註冊，好讓介紹新的登錄可能會建立問題比對其資料的使用者。

### <a name="custom-authentication"></a>自訂驗證

如果您的應用程式使用自訂的驗證方案，您會想要確認升級的網站擁有系統的存取權。 依照指示進行自訂驗證[.NET 伺服器 SDK 概觀]中新整合您的方案。 請注意自訂驗證元件是仍在預覽中。

##<a name="updating-clients"></a>更新的用戶端
操作的行動應用程式後端之後，您就可以使用使用您用戶端應用程式的新版本。 行動應用程式也包含新版本的用戶端 Sdk，並與伺服器升級上方，您需要移除之前安裝行動應用程式版本的行動服務 Sdk 的所有參考。

其中一個版本之間的主要變更是建構函式不再需要應用程式鍵。 您現在即可傳入您的行動應用程式的 URL。 例如，在.NET 用戶端上`MobileServiceClient`現在已建構函式︰

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

您可以閱讀有關安裝新的 Sdk 和使用新的結構，透過下列連結︰

- [iOS 版 3.0.0 或更新版本](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) 版本 2.0.0 或更新版本](app-service-mobile-dotnet-how-to-use-client-library.md)

如果您的應用程式讓您使用的推入通知，請記下每個平台的特定註冊指示，請為有一些變更，也。

當您準備好新的用戶端版本時，[試試看針對您已升級的伺服器的專案。 完成驗證後其運作方式，您可以釋放為 [客戶] 應用程式的新版本。 最後，當您的客戶有機會接收更新，您可以刪除您的應用程式的行動電話服務版本。 此時，您已完全升級至應用程式服務行動應用程式使用最新的行動應用程式伺服器 SDK。

<!-- URLs. -->

[Azure 入口網站]: https://portal.azure.com/
[Azure 傳統入口網站]: https://manage.windowsazure.com/
[什麼是行動應用程式？]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[在行動應用程式伺服器 SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure 排程器]: /en-us/documentation/services/scheduler/
[Web 工作]: ../app-service-web/websites-webjobs-resources.md
[如何使用.NET 伺服器 SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[應用程式服務價格]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[.NET 伺服器 SDK 概觀]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
