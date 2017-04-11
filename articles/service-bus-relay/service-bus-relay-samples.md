<properties 
    pageTitle="服務匯流排轉送範例概觀 |Microsoft Azure"
    description="分類，並說明的連結，每個服務匯流排轉送範例。"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/07/2016"
    ms.author="sethm" />

# <a name="service-bus-relay-samples"></a>服務匯流排轉送範例

服務匯流排轉送範例示範[服務匯流排轉接](https://azure.microsoft.com/services/service-bus/)中的主要功能。 本文將分類，並說明可用的每個連結的範例。

>[AZURE.NOTE] 使用 SDK 未安裝服務匯流排範例。 若要取得範例，請造訪[Azure SDK 範例頁面](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5)。
>
>此外，有更新的服務匯流排轉送範例一組[以下](https://github.com/Azure-Samples/azure-servicebus-relay-samples)（本文撰寫時，他們會不本文中所述）。  

訊息的範例，請參閱[服務匯流排訊息範例](../service-bus-messaging/service-bus-samples.md)。

## <a name="service-bus-relay"></a>服務匯流排轉送

下列範例會說明如何撰寫使用服務匯流排轉送服務應用程式。

注意轉送範例，以存取您的服務匯流排命名空間連線字串。

### <a name="to-obtain-a-connection-string-for-azure-service-bus"></a>Azure 服務匯流排取得連線字串

1. [Azure 入口網站](http://portal.azure.com)登入。

1. 在左欄中，按一下 [**服務匯流排**]。

1. 按一下您在清單中的命名空間的名稱。

3. 在 [命名空間刀中，按一下 [**共用存取原則**]。

4. 在 [**共用存取原則**刀中，按一下 [ **RootManageSharedAccessKey**]。

6. 複製到剪貼簿的連線字串。

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>若要取得連線字串的 Windows server 服務匯流排

1. 執行下列 PowerShell 指令程式︰

    ```
    get-sbClientConfiguration
    ```

2. 貼入範例 App.config 檔案中的連接字串。

## <a name="service-bus-relay"></a>服務匯流排轉送

示範服務匯流排轉送的範例。

### <a name="getting-started"></a>快速入門

|範例名稱|描述|最小 SDK 版本|顯示狀態|
|---|---|---|---|
|[轉送訊息︰ Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3)|示範如何在 Azure 上執行的服務匯流排用戶端和服務。 此範例會以程式設計方式設定服務匯流排。 僅限的環境及安全性資訊會儲存在設定檔中。|1.8|Microsoft Azure 服務匯流排|
|[轉送訊息驗證︰ 共用的密碼](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02)|示範如何使用發行者的名稱以及發行者密碼驗證服務匯流排。|1.8|Microsoft Azure 服務匯流排|
|[轉送訊息驗證︰ WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831)|示範如何以公開 HTTP 服務，不需要用戶端使用者驗證]。|1.8|Microsoft Azure 服務匯流排|
|[轉送訊息繫結︰ WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0)|示範如何使用**WebHttpRelayBinding**繫結傳回使用 Web 程式設計模型的二進位資料。|1.8|Microsoft Azure 服務匯流排|
|[轉送訊息繫結︰ NetTcp 轉送](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692)|示範如何使用**NetTcpRelayBinding**繫結。|1.8|Microsoft Azure 服務匯流排|

### <a name="exploring-features"></a>探索功能

示範各種不同的服務匯流排轉送功能的範例。

|範例名稱|描述|最小 SDK 版本|顯示狀態|
|---|---|---|---|
|[轉送訊息驗證︰ 簡單 WebToken](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392)|示範如何使用簡單的 web token 認證服務匯流排驗證方法。 樣本是類似回音範例中，進行一些變更。 具體來說，此範例會在 ServiceHost （服務） 和 ChannelFactory （用戶端） 應用程式新增行為。|1.8|Microsoft Azure 服務匯流排|
|[轉送訊息︰ 負載平衡](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8)|示範如何使用 Microsoft Azure 服務匯流排將訊息傳送給多個接收器。 它會顯示一個簡單的服務，透過**NetTcpRelayBinding**繫結的用戶端與通訊多個執行的個體|1.8|Microsoft Azure 服務匯流排|
|[轉送訊息繫結︰ 網路事件](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977)|示範如何在 Microsoft Azure 服務匯流排使用**NetEventRelayBinding**繫結。|1.8|Microsoft Azure 服務匯流排|
|[轉送訊息繫結︰ WS2007Http 工作階段](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb)|示範如何使用**WS2007HttpRelayBinding**繫結啟用可靠的工作階段。 也會顯示如何指定服務匯流排認證，而不是以程式設計方式的設定檔中。|1.8|Microsoft Azure 服務匯流排|
|[轉送訊息繫結︰ WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5)|示範如何使用**WS2007HttpRelayBinding**繫結訊息安全性的安全時仍需要用戶端驗證服務匯流排的端對端郵件。|1.8|Microsoft Azure 服務匯流排|
|[轉送訊息︰ 中繼資料交換](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e)|示範如何以公開使用轉送繫結的中繼資料端點。 **MetadataExchange**支援下列轉送繫結︰ **NetTcpRelayBinding**、 **NetOnewayRelayBinding**、 **BasicHttpRelayBinding**及**WS2007HttpRelayBinding**。|1.8|Microsoft Azure 服務匯流排|
|[轉送訊息繫結︰ NetTcp 郵遞](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161)|示範如何設定**NetTcpRelayBinding**繫結至支援會先建立一個轉送的連線，並可能的話，請用戶端和服務之間的直接連接自動切換的**混合式**連線模式。|1.8|Microsoft Azure 服務匯流排|
|[轉送訊息繫結︰ NetTcp MsgSec 使用者名稱](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392)|示範如何使用郵件安全性**NetTcpRelayBinding**繫結。|1.8|Microsoft Azure 服務匯流排|
|[轉送訊息繫結︰ 網路 Oneway](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a)|示範如何公開及取用使用**NetOnewayRelayBinding**繫結的服務端點。|1.8|Microsoft Azure 服務匯流排|
|[轉送訊息繫結︰ WS2007Http 簡單](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a)|示範如何使用**WS2007HttpRelayBinding**繫結。 它會示範簡單的服務使用沒有安全性選項，並不需要用戶端驗證。|1.8|Microsoft Azure 服務匯流排|

## <a name="next-steps"></a>後續步驟

請參閱下列主題服務匯流排的概觀。

- [服務匯流排轉送概觀](service-bus-relay-overview.md)
- [服務匯流排架構](../service-bus-messaging/service-bus-architecture.md)
- [服務匯流排基本概念](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)