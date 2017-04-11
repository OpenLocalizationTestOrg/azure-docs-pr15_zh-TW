<properties
    pageTitle="註冊管理"
    description="本主題說明如何註冊裝置與通知集線器，才會收到推播通知。"
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="registration-management"></a>註冊管理

##<a name="overview"></a>概觀

本主題說明如何註冊裝置與通知集線器，才會收到推播通知。 主題說明登錄高層級的然後介紹註冊裝置的兩個主要模式: [通知] 中心內，直接從裝置登錄和註冊透過應用程式後端。 


##<a name="what-is-device-registration"></a>什麼是裝置註冊

使用通知中樞的裝置註冊使用**登錄**或**安裝**完成。

#### <a name="registrations"></a>註冊
與標記與可能的範本，註冊產生關聯的裝置的相關的平台通知服務 (PNS) 控點。 ChannelURI、 裝置權杖或 GCM 註冊識別碼，可能是 PNS 控點。 標記用來將通知傳送到正確的設定的裝置控點。 如需詳細資訊，請參閱[路徑和標籤運算式](notification-hubs-tags-segment-push-message.md)。 範本會用來實作每個註冊轉換。 如需詳細資訊，請參閱[範本](notification-hubs-templates-cross-platform-push-messages.md)。

#### <a name="installations"></a>安裝
正在增強安裝包含的推入紙袋註冊相關的屬性。 它是最新和最佳註冊您的裝置。 不過，它不支援的用戶端還.NET SDK ([後端作業的通知中心 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/))。  這表示如果您從用戶端裝置本身註冊，則必須使用[通知集線器 REST API](https://msdn.microsoft.com/library/mt621153.aspx)方法來支援安裝。 如果您使用的後端服務，您應該可以使用[的後端作業的通知中心 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)。

以下是一些使用安裝的主要優點︰

* 建立或更新安裝完整是冪。 因此，重新沒有任何重複的登錄疑慮。
* 安裝模型可讓您更容易個別推入-針對特定裝置。 系統標籤**「 $InstallationId: [installationId] 」**就會自動新增的每個安裝基礎註冊。 因此，您可以通話傳送到此標籤至特定的裝置而不需要執行任何額外的程式碼。
* 使用安裝也可讓您執行部分註冊更新。 使用[JSON 修補程式標準](https://tools.ietf.org/html/rfc6902)修補程式方法要求部分安裝的更新。 當您想要更新登錄標籤時，這是特別有用。 您不需要往整個註冊，然後再重新傳送所有的上一個標籤。

安裝最多可以包含下列屬性。 如需安裝的完整清單內容查看，請[建立或覆寫 REST API 安裝](https://msdn.microsoft.com/library/azure/mt621153.aspx)或[安裝屬性](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation_properties.aspx)。

    // Example installation format to show some supported properties
    {
        installationId: "",
        expirationTime: "",
        tags: [],
        platform: "",
        pushChannel: "",
        ………
        templates: {
            "templateName1" : {
                body: "",
                tags: [] },
            "templateName2" : {
                body: "",
                // Headers are for Windows Store only
                headers: {
                    "X-WNS-Type": "wns/tile" }
                tags: [] }
        },
        secondaryTiles: {
            "tileId1": {
                pushChannel: "",
                tags: [],
                templates: {
                    "otherTemplate": {
                        bodyTemplate: "",
                        headers: {
                            ... }
                        tags: [] }
                }
            }
        }
    }

 

請務必請注意，註冊和安裝預設不會再過期。

註冊和安裝必須包含每個裝置/通道有效 PNS 控點。 因為只能在用戶端應用程式，在裝置上取得 PNS 控點，一個模式是直接在用戶端應用程式的裝置上註冊。 另一方面的安全性考量和與標籤相關的商務邏輯可能會要求您管理中的應用程式後端的裝置註冊。 

#### <a name="templates"></a>範本

如果您想要使用[的範本](notification-hubs-templates-cross-platform-push-messages.md)，裝置安裝也會保留在 JSON 該裝置相關聯的所有範本設定格式 （請參閱上述範例）。 範本名稱協助目標的同一個裝置不同的範本。

請注意，每個範本名稱對應至範本本文和一組選擇性的標籤。 此外，每個平台可以有其他範本內容。 Windows 市集 （使用 WNS） 和 Windows Phone 8 （使用 MPNS），另一組標題可以範本的一部分。 若是 APNs，您可以在其中一個常數或範本運算式設定到期屬性。 安裝的完整清單屬性請參閱[建立或覆寫安裝其餘](https://msdn.microsoft.com/library/azure/mt621153.aspx)的主題。

#### <a name="secondary-tiles-for-windows-store-apps"></a>第二個磚的 Windows 市集應用程式

Windows 市集用戶端應用程式，將通知傳送至第二個方塊的傳送至主要的相同。 這也支援安裝。 請注意，第二個方塊不同 ChannelUri，在您的用戶端應用程式上 SDK 控點的無障礙。

SecondaryTiles 字典所用的用來建立您的 Windows 市集應用程式的 SecondaryTiles 物件相同 TileId。
與主要 ChannelUri，ChannelUris 的第二個方塊可以隨時變更。 若要在 [更新] 通知中心保留在安裝，請裝置必須重新整理其與目前的次要並排 ChannelUris。


##<a name="registration-management-from-the-device"></a>從裝置註冊管理

當用戶端應用程式的管理裝置註冊後, 端負責只傳送通知。 用戶端應用程式保持在最新狀態 PNS 控點，並登錄標籤。 下圖說明這個模式。

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

裝置第一次從 PNS，擷取 PNS 控點，然後使用 [通知] 中心內，直接註冊。 註冊成功後，應用程式後端可以傳送目標註冊通知。 如需有關如何傳送通知的詳細資訊，請參閱[路徑和標籤運算式](notification-hubs-tags-segment-push-message.md)。
請注意，在此情況下，您會使用只接聽從裝置存取您的通知集線器權限。 如需詳細資訊，請參閱[安全性](notification-hubs-push-notification-security.md)。

從裝置註冊是最簡單的方法，但它有一些缺點。
第一個缺點是用戶端應用程式可以只更新其標籤時使用中應用程式。 例如，如果使用者已註冊時的第一個裝置註冊其他標記 （例如，海鷹） 的運動團隊與相關的標籤的兩個裝置，第二個裝置不會收到通知海鷹相關直到第二個裝置上的應用程式執行一次。 更多通常來說，標籤受到多個裝置，管理標籤後端時需要的選項。
第二個缺點註冊管理從用戶端應用程式的是，因為應用程式可以駭客入侵，保護特定的標記註冊，需要額外進行處理，述區段 」 標記層級安全性]。



#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>註冊通知中樞從裝置使用安裝的程式碼範例 

此時，只支援使用[通知集線器 REST API](https://msdn.microsoft.com/library/mt621153.aspx)。

您也可以使用使用[JSON 修補程式標準](https://tools.ietf.org/html/rfc6902)更新安裝此修補程式方法。

    class DeviceInstallation
    {
        public string installationId { get; set; }
        public string platform { get; set; }
        public string pushChannel { get; set; }
        public string[] tags { get; set; }
    }

    private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
         string hubName, string listenConnectionString)
    {
        if (deviceInstallation.installationId == null)
            return HttpStatusCode.BadRequest;

        // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
        ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
        string hubResource = "installations/" + deviceInstallation.installationId + "?";
        string apiVersion = "api-version=2015-04";

        // Determine the targetUri that we will sign
        string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

        //=== Generate SaS Security Token for Authorization header ===
        // See, https://msdn.microsoft.com/library/azure/dn495627.aspx
        string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

        using (var httpClient = new HttpClient())
        {
            string json = JsonConvert.SerializeObject(deviceInstallation);

            httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

            var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    string installationId = null;
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a installation id in application data, create and store as application data.
    if (!settings.ContainsKey("__NHInstallationId"))
    {
        installationId = Guid.NewGuid().ToString();
        settings.Add("__NHInstallationId", installationId);
    }

    installationId = (string)settings["__NHInstallationId"];

    var deviceInstallation = new DeviceInstallation
    {
        installationId = installationId,
        platform = "wns",
        pushChannel = channel.Uri,
        //tags = tags.ToArray<string>()
    };

    var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                        "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

    if (statusCode != HttpStatusCode.Accepted)
    {
        var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    else
    {
        var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }

   

#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>從裝置使用登錄通知中樞註冊的程式碼範例


這些方法建立或更新要在其上呼叫的裝置註冊。 這表示才能更新控點或標籤，您必須覆寫整個註冊。 請記住，登錄暫時性，，因此您永遠應該是以特定的裝置必須目前標籤可靠存放區。


    // Initialize the Notification Hub
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // The Device id from the PNS
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // If you are registering from the client itself, then store this registration id in device
    // storage. Then when the app starts, you can check if a registration id already exists or not before
    // creating.
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a registration id in application data, store in application data.
    if (!settings.ContainsKey("__NHRegistrationId"))
    {
        // make sure there are no existing registrations for this push handle (used for iOS and Android)    
        string newRegistrationId = null;
        var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
        foreach (RegistrationDescription registration in registrations)
        {
            if (newRegistrationId == null)
            {
                newRegistrationId = registration.RegistrationId;
            }
            else
            {
                await hub.DeleteRegistrationAsync(registration);
            }
        }

        newRegistrationId = await hub.CreateRegistrationIdAsync();

        settings.Add("__NHRegistrationId", newRegistrationId);
    }
     
    string regId = (string)settings["__NHRegistrationId"];

    RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
    registration.RegistrationId = regId;
    registration.Tags = new HashSet<string>(YourTags);

    try
    {
        await hub.CreateOrUpdateRegistrationAsync(registration);
    }
    catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
    {
        settings.Remove("__NHRegistrationId");
    }


## <a name="registration-management-from-a-backend"></a>註冊後端管理

管理登錄後端需要撰寫額外的程式碼。 從裝置的應用程式必須提供後端處理更新的 PNS 每次應用程式啟動時 （以及標記與範本） 後, 端必須更新通知中心此控點。 下圖說明這項設計。

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

管理登錄後端的優點包括修改標籤以進行之註冊，即使在裝置上的對應應用程式會停用，並新增標籤至其註冊之前先驗證的用戶端應用程式的能力。


#### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>註冊使用安裝後端通知中樞的程式碼範例

用戶端裝置仍取得其 PNS 控點和相關的安裝內容之前，並可執行註冊和授權標籤等後端上通話自訂的 API。後端可以運用[通知中心 SDK 的後端作業](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)。

您也可以使用使用[JSON 修補程式標準](https://tools.ietf.org/html/rfc6902)更新安裝此修補程式方法。
 

    // Initialize the Notification Hub
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // Custom API on the backend
    public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
    {

        Installation installation = new Installation();
        installation.InstallationId = deviceUpdate.InstallationId;
        installation.PushChannel = deviceUpdate.Handle;
        installation.Tags = deviceUpdate.Tags;

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                installation.Platform = NotificationPlatform.Mpns;
                break;
            case "wns":
                installation.Platform = NotificationPlatform.Wns;
                break;
            case "apns":
                installation.Platform = NotificationPlatform.Apns;
                break;
            case "gcm":
                installation.Platform = NotificationPlatform.Gcm;
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }


        // In the backend we can control if a user is allowed to add tags
        //installation.Tags = new List<string>(deviceUpdate.Tags);
        //installation.Tags.Add("username:" + username);

        await hub.CreateOrUpdateInstallationAsync(installation);

        return Request.CreateResponse(HttpStatusCode.OK);
    }


#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>註冊使用登錄識別碼的裝置的通知集線器的程式碼範例

從您的應用程式後端，您可以執行登錄基本 CRUDS 作業。 例如︰

    var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");
            
    // create a registration description object of the correct type, e.g.
    var reg = new WindowsRegistrationDescription(channelUri, tags);

    // Create
    await hub.CreateRegistrationAsync(reg);

    // Get by id
    var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

    // update
    r.Tags.Add("myTag");

    // update on hub
    await hub.UpdateRegistrationAsync(r);

    // delete
    await hub.DeleteRegistrationAsync(r);


後端必須處理並行之間註冊更新。 服務匯流排提供註冊管理樂觀控制項。 在 HTTP 層級，這被實作 ETag 使用登錄管理作業。 如果更新遭到拒絕並行基於引發例外狀況的 Microsoft Sdk 無障礙會使用這項功能。 應用程式後端負責處理這些例外狀況，如有必要，請重試更新。