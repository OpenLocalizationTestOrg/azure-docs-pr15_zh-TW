<properties 
    pageTitle="事件集線器驗證與安全性模型概觀 |Microsoft Azure"
    description="事件集線器驗證與安全性模型概觀。"
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/16/2016"
    ms.author="sethm;clemensv" />

# <a name="event-hubs-authentication-and-security-model-overview"></a>事件集線器驗證與安全性模型概觀

事件集線器安全性模型符合下列需求︰

- 有效的認證的唯一裝置可以傳送到事件集線器的資料。
- 在裝置無法模擬另一個裝置。
- 將資料傳送到事件集線器，可以封鎖惡意裝置。

## <a name="device-authentication"></a>裝置驗證

事件集線器安全性模型為基礎[共用 Access 簽章 (SA)](../service-bus-messaging/service-bus-shared-access-signature-authentication.md)權杖與*事件發行者*的組合。 事件 publisher 定義事件中心虛擬結束點。 Publisher 僅能傳送郵件給事件中心。 無法從發行者接收郵件。

一般而言，事件中心採用一個 publisher 每一個裝置。 傳送給任何事件中樞的發行者的所有郵件都是佇列中的事件中心。 發行者啟用更細緻存取控制和節流設定。

每個裝置會被指派的唯一的權杖上, 傳到裝置。 權杖所產生的每個唯一的權杖不同的唯一 publisher 授與存取權。 一個 publisher 中，但沒有其他 publisher 只能傳送擁有權杖的裝置。 如果在多個裝置共用相同的權杖，每個這些裝置共用發行者。

雖然不建議使用，可能是以配備直接存取權授與事件中樞的權杖裝置。 保留此 token 任何裝置可以直接將該事件] 中心傳送訊息。 這類裝置將不會受到節流設定。 此外，您還無法裝置黑名單傳送給該事件的中心。

所有的權杖為 SA 金鑰登入。 一般而言，所有的權杖為登入以相同的金鑰。 裝置並不知道鍵。如此可避免裝置製造權杖。

### <a name="create-the-sas-key"></a>建立 SA 鍵

在建立的事件集線器命名空間，Azure 事件集線器就會產生名為**RootManageSharedAccessKey**256 位元 SA 金鑰。 此按鍵授與傳送、 聆聽，及管理命名空間的權限。 您可以建立其他的按鍵。 建議您產生授與傳送的權限到特定事件中樞的金鑰。 本主題的其餘部分，則會假設您命名此按鍵`EventHubSendKey`。

建立事件中心時，下列範例會建立傳送專用的機碼︰

```
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create Event Hub with a SAS rule that enables sending to that Event Hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>產生的權杖

您可以產生的權杖使用 SA 金鑰。 您必須產生單一的權杖，每一個裝置。 權杖然後可以使用下列方法會產生。 所有的權杖會產生使用**EventHubSendKey**金鑰。 每個權杖，就會被指派唯一 URI。

```
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

URI 呼叫此方法，將指定為`//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`。 所有的權杖，URI 是相同，與 exception 的`PUBLISHER_NAME`，應該針對每一個不同。 理想的情況下，`PUBLISHER_NAME`代表會收到該權杖裝置的識別碼。

這個方法會產生的權杖以下列結構︰

```
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

從 1970 年 1 月 1 日的秒數指定 token 的到期時間。 權杖範例如下︰

```
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

一般而言，權杖有為單位的週期類似或高於為單位的週期的裝置。 如果裝置的功能，以取得新的權杖，就可以用權杖與短為單位的週期。

### <a name="devices-sending-data"></a>傳送資料的裝置

只要權杖建立之後，每個裝置是提供給自己專屬的權杖。

當裝置插入事件集線器傳送資料時，裝置標籤傳送要求其的權杖。 若要防止攻擊竊取並竊取權杖，裝置和事件中心之間的通訊必須透過加密通道。

### <a name="blacklisting-devices"></a>黑名單裝置

如果權杖會竊取攻擊可以模擬的權杖已被竊取的裝置。 黑名單裝置呈現裝置無法使用，直到裝置指定新的權杖，會使用不同的 publisher。

## <a name="authentication-of-back-end-applications"></a>驗證的後端應用程式

若要驗證使用的裝置所產生的資料的後端應用程式，事件集線器採用類似用於服務匯流排主題模型的安全性模型。 事件集線器消費者群組等於服務匯流排主題的訂閱。 如果要建立消費者群組的要求伴隨權杖授與管理權限，事件] 中心內，或 [事件] 中心所屬的命名空間，用戶端可以建立消費者群組。 用戶端可以使用從消費者群組的資料，如果接收邀請伴隨消費者群組、 [事件] 中心內，或 [事件] 中心所屬的命名空間接收權限授與的權杖。

目前版本的服務匯流排個別訂閱不支援 SA 規則。 同樣適用於事件集線器消費者群組。 將兩個功能在未來加入 SA 支援。

沒有 SA 驗證個別消費者群組時，您可以使用 SA 鍵與一般索引鍵的安全性消費者的所有群組。 這種方法可讓應用程式使用的任何事件中樞的消費者群組的資料。

## <a name="next-steps"></a>後續步驟

若要進一步瞭解事件集線器，請造訪下列主題︰

- [事件集線器概觀]
- [佇列中的訊息解決方案]使用服務匯流排佇列。
- 完成[範例應用程式的使用事件集線器]。

[事件集線器概觀]: event-hubs-overview.md
[使用事件集線器範例應用程式]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[佇列中的訊息解決方案]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 
