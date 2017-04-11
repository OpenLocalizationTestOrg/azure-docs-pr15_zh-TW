<properties
    pageTitle="Azure 通知集線器和 Bing 空間資料的地理納入範圍之推入通知 |Microsoft Azure"
    description="在本教學課程中，您將學習如何呈現 Azure 通知集線器和 Bing 空間資料的位置為基礎的推播通知。"
    services="notification-hubs"
    documentationCenter="windows"
    keywords="推入通知，推入通知"
    authors="dend"
    manager="yuaxu"
    editor="dend"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/31/2016"
    ms.author="dendeli"/>
    
# <a name="geo-fenced-push-notifications-with-azure-notification-hubs-and-bing-spatial-data"></a>Azure 通知集線器和 Bing 空間資料的地理納入範圍之推入通知
 
 > [AZURE.NOTE] 若要完成此教學課程中，您必須使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02)。

在本教學課程中，您將學習如何呈現 Azure 通知集線器和 Bing 空間資料，從運用通用 Windows 平台應用程式中的位置為基礎的推播通知。

##<a name="prerequisites"></a>必要條件
首先和最重要，您必須確定您擁有所有軟體和服務必要條件︰

* [Visual Studio 2015 更新 1](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)或更新版本 （[社群 Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409)會同時執行）。 
* 最新版[Azure SDK](https://azure.microsoft.com/downloads/)的詳細資訊。 
* [Bing 地圖服務開發人員中心帳戶](https://www.bingmapsportal.com/)您可以免費建立一個，並將它與您的 Microsoft 帳戶關聯。 

##<a name="getting-started"></a>快速入門

現在就讓我們開始建立專案。 在 Visual Studio 中，開始新專案的類型**空白的應用程式 (通用 Windows)**。

![](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

建立專案完成後，您應該控管本身的應用程式。 現在讓我們來設定地理圍欄基礎結構的所有項目。 我們會使用這個 Bing 服務，因為沒有公用的 REST API 結束點，以讓我們來查詢的特定位置的圖文框︰

    http://spatial.virtualearth.net/REST/v1/data/
    
您將需要指定下列參數，使其運作︰

* **資料來源識別碼**與**資料來源名稱**-資料來源包含各種 bucketed 的中繼資料，例如位置和營業作業的 Bing 地圖服務 API 中。 您可以閱讀更多關於這些步驟。 
* **實體名稱**– 您想要使用的參考點通知的實體。 
* **Bing 地圖服務 API 金鑰**– 這是您較舊版本時取得您所建立的 Bing 開發人員中心帳戶金鑰。
 
讓我們來執行安裝程式深度探討的每個以上的項目。

##<a name="setting-up-the-data-source"></a>設定 [資料來源

您可以在 [Bing 地圖服務開發人員中心中執行。 只要按一下上方導覽列中**的資料來源**，然後選取 [**管理資料來源**。

![](./media/notification-hubs-geofence/bing-maps-manage-data.png)

如果未使用過之前的 Bing 地圖服務 API，很可能會有不會任何資料來源，以便您只要建立一個新上傳至資料來源的資料上即可。 請確定您填寫所有必要的欄位︰

![](./media/notification-hubs-geofence/bing-maps-create-data.png)

您可能會好奇 – 什麼資料檔案，而什麼應該您要上傳嗎？ 在進行這項測試，我們可以只使用樣本管道型框舊金山機場 waterfront 區域︰

    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    
上述代表此實體︰

![](./media/notification-hubs-geofence/bing-maps-geofence.png)

只複製並貼上上述字串至新的檔案並將其儲存為**NotificationHubsGeofence.pipe**，在 Bing 開發人員中心上傳。

>[AZURE.NOTE]您可能會提示您指定新的金鑰**主要****查詢鍵**不同。 只要建立一個新的金鑰，透過儀表板，並重新整理資料來源上傳頁面。

一旦您上傳的資料檔案，您必須以確定您發佈的資料來源。 

移至 [**管理資料來源**，您可以跟我們上方尋找清單中的資料來源，在 [**動作**] 欄中按一下 [上**發佈**。 在一點，您應該會看到您在 [**發佈資料來源**] 索引標籤中的資料來源︰

![](./media/notification-hubs-geofence/bing-maps-published-data.png)

如果您按一下 [**編輯**]，您可以查看我們引進了哪些位置︰

![](./media/notification-hubs-geofence/bing-maps-data-details.png)

此時，入口網站不會顯示您 geofence 我們所建立，-我們已指定的位置是在右側附近的確認訊息之後的邊界。

現在您擁有的資料來源的需求。 若要取得詳細資料要求 url 的 API 號碼，Bing 地圖服務開發人員中心中，按一下 [**資料來源**，並選取**資料來源資訊**。

![](./media/notification-hubs-geofence/bing-maps-data-info.png)

**查詢 URL**是我們可以在這裡之後。 這是針對我們可以執行查詢，以檢查裝置是否目前位置的範圍內的端點。 若要執行這項檢查，我們只需要執行取得呼叫針對查詢 URL，以加上的下列參數︰

    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY

如此一來您正在指定目標點，我們會從裝置取得，Bing 地圖服務會自動執行 geofence 中的計算。 當您執行瀏覽器 （或捲曲） 的要求時，您會收到標準 JSON 回應︰

![](./media/notification-hubs-geofence/bing-maps-json.png)

點實際上是指定的範圍內，只會發生此回應。 如果不是，就會出現空白**結果**連結︰

![](./media/notification-hubs-geofence/bing-maps-nores.png)

##<a name="setting-up-the-uwp-application"></a>設定 UWP 應用程式

現在，我們已準備好的資料來源，我們可以開始處理我們先前引導 UWP 應用程式。

首先和最重要，我們必須啟用位置服務應用程式。 若要這麼做，請按兩下`Package.appxmanifest`檔案**總管**] 中的。

![](./media/notification-hubs-geofence/vs-package-manifest.png)

在剛開啟封裝內容索引標籤，按一下**功能**，請確定您選取**的位置**︰

![](./media/notification-hubs-geofence/vs-package-location.png)

為宣告位置功能，建立新的資料夾中名稱為方案`Core`，並新增新的檔案，稱為`LocationHelper.cs`:

![](./media/notification-hubs-geofence/vs-location-helper.png)

`LocationHelper`此時是相當基本類別本身它是 – 讓我們來取得系統 API 透過使用者位置︰

    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }

您可以閱讀更多關於取得 UWP 正式的[MSDN 文件](https://msdn.microsoft.com/library/windows/apps/mt219698.aspx)中的應用程式中的使用者的位置。

若要檢查的位置擷取實際運作，請開啟 [主頁面的程式碼側 (`MainPage.xaml.cs`)。 建立新的事件處理常式`Loaded`事件在`MainPage`建構函式︰

    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }

事件處理常式實作如下所示︰

    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }

請注意我們宣告為非同步處理常式因為`GetCurrentLocation`awaitable，而且因此需要執行非同步內容中。 此外，在某些情況下可能，結果 null 位置 （例如服務會停用的位置或應用程式被拒位置存取權限），因為我們要確認的處理與 null 的核取。

執行應用程式。 確認您允許存取的位置︰

![](./media/notification-hubs-geofence/notification-hubs-location-access.png)

一次應用程式啟動，您應該能夠看到座標**輸出**視窗中︰

![](./media/notification-hubs-geofence/notification-hubs-location-output.png)

現在您已經瞭解位置擷取運作 – 隨意移除測試事件處理常式的載入，因為我們無法使用它不再。

下一步是擷取位置的變更。 回到`LocationHelper`類別，並新增的事件處理常式`PositionChanged`:

    geolocator.PositionChanged += Geolocator_PositionChanged;

實作會顯示在 [**輸出**視窗中的位置座標︰

    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }

##<a name="setting-up-the-backend"></a>設定後端

下載[GitHub.NET 後端樣本](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers)。 下載完成後，開啟`NotifyUsers`資料夾，再 –`NotifyUsers.sln`檔案。

設定`AppBackend`為**啟動專案**的專案，並啟動它。

![](./media/notification-hubs-geofence/vs-startup-project.png)

專案已設定為傳送至目標裝置的推入通知，因此我們需要執行只有兩件事 – 交換正確的連線字串的通知中心，並新增傳送通知時，才使用者 geofence 內邊界識別。

若要設定中的連接字串，`Models`資料夾開啟`Notifications.cs`。 `NotificationHubClient.CreateClientFromConnectionString`函數應該包含通知中心，您可以取得[Azure 入口網站](https://portal.azure.com)（查詢**設定**] 中**存取原則**刀內） 的相關資訊。 儲存更新的設定檔。

現在，我們需要建立模型的 Bing 地圖服務 API 結果。 若要執行這項作業的最簡單方法是以滑鼠右鍵按一下`Models`] 資料夾中，**新增** > **類別**。 將其命名`GeofenceBoundary.cs`。 一旦完成，則將 JSON 複製 Visual Studio 使用**編輯**和第一節中所討論的 API 回應 > **[選擇性貼上** > **貼上 JSON 為類別**。 

我們確保會還原完全依設計序列化物件的方式。 設定您的結果類別看起來應該像這樣︰

    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }

接下來，開啟`Controllers`  >  `NotificationsController.cs`。 我們需要調整帳戶目標經緯度文章通話。 如需，只要新增兩個字串 」 函數簽章 –`latitude`和`longitude`。

    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)

建立新類別名為專案內`ApiHelper.cs`-我們會用來連線到 Bing，以檢查指向邊界交叉口。 實作`IsPointWithinBounds`函數，像這樣︰

    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }

>[AZURE.NOTE] 請確定替代較早取得從 Bing 開發人員中心 （相同適用於 API 金鑰） 查詢 URL 的 API 端點。 

如果查詢結果，這表示指定的點的已是 geofence，範圍內，我們將`true`。 如果有任何結果，請 Bing 就告訴我們的點超出查閱圖文框中，因此我們傳回`false`。

返回`NotificationsController.cs`，建立前切換陳述式的檢查︰

    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }

如此一來，通知會只傳送點時範圍內。

##<a name="testing-push-notifications-in-the-uwp-app"></a>測試 UWP 應用程式中的 [推入通知

回到 UWP 應用程式，我們現在應該可以測試通知。 內`LocationHelper`課程，請建立新的函數︰ `SendLocationToBackend`:

    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }

>[AZURE.NOTE] 交換`POST_URL`到我們在前一節中所建立的部署的 web 應用程式的位置。 現在，可在本機上，執行，但當您處理部署公用版本，您需要託管外部的提供者。

現在就讓我們確定我們註冊推播通知 UWP 應用程式。 在 Visual Studio 中，按一下 [**專案** > **儲存** > **建立應用程式存放區之間的關聯**。

![](./media/notification-hubs-geofence/vs-associate-with-store.png)

當您登入您的開發人員帳戶時，請確定您選取現有的應用程式或建立新封裝關聯。 

移至 [開發人員中心，並開啟您剛剛建立的應用程式。 按一下 [**服務** > **推入通知** > **Live Services 網站**。

![](./media/notification-hubs-geofence/ms-live-services.png)

在網站上，記下**應用程式密碼**] 和 [**套件 SID**。 您必須同時 Azure 入口網站 – 中開啟您的通知中心，按一下 [**設定** > **通知服務** > **Windows (WNS)**和 [必要] 欄位中輸入資訊。

![](./media/notification-hubs-geofence/notification-hubs-wns.png)

按一下 [**儲存**]。

以滑鼠右鍵按一下**方案總管]**中的**參照**，然後選取 [**管理 NuGet 套件**。 我們需要新增至**Microsoft Azure 服務匯流排管理文件庫**的參照 – 只要搜尋`WindowsAzure.Messaging.Managed`並將其新增至您的專案。

![](./media/notification-hubs-geofence/vs-nuget.png)

如需進行測試，我們可以建立`MainPage_Loaded`事件處理常式一次，並為其新增此程式碼片段︰

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }

上述註冊通知中心應用程式。 您已準備好移 ！ 

在 [ `LocationHelper`、 內`Geolocator_PositionChanged`處理常式，您可以新增一段的測試程式碼會強制將 geofence 內的位置︰

    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");

我們無法傳遞實數座標 （其中可能不是範圍內的時間），並使用預先定義的測試的值，因為我們將會看到顯示於更新通知︰

![](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

##<a name="whats-next"></a>下一步是什麼？

有一些您可能需要除了以上，請確定的解決方案是實際執行準備的步驟。

首先和最重要，您可能需要確認 geofences 動態。 這需要一些額外的工作和 Bing API 才能無法上傳現有的資料來源中的新邊界。 如需主題的詳細資訊，請參閱[Bing 空間資料服務 API 文件](https://msdn.microsoft.com/library/ff701734.aspx)。

第二，您正在確保傳送是正確的參與者，您可能會想進行目標透過[標記](notification-hubs-tags-segment-push-message.md)。

如上所示的方案中您可能會有各種不同的目標平台，我們已限制系統的特定功能 geofencing 狀況的描述。 然而，通用的 Windows 平台提供偵測[geofences 右--現成的](https://msdn.microsoft.com/windows/uwp/maps-and-location/set-up-a-geofence)功能。

如需有關通知集線器功能的詳細資訊，請查看我們[的說明文件入口網站](https://azure.microsoft.com/documentation/services/notification-hubs/)。
