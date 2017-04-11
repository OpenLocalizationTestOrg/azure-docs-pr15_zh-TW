<properties 
    pageTitle="服務匯流排訊息範例概觀 |Microsoft Azure"
    description="分類，並說明服務匯流排訊息的每個連結的範例。"
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

# <a name="service-bus-messaging-samples"></a>服務匯流排訊息範例

服務匯流排訊息範例示範[服務匯流排訊息](https://azure.microsoft.com/services/service-bus/)（雲端服務） 和[Windows Server 服務匯流排的](https://msdn.microsoft.com/library/dn282144.aspx)主要功能。 本文將分類，並說明可用的每個連結的範例。

>[AZURE.NOTE] 使用 SDK 未安裝服務匯流排範例。 若要取得範例，請造訪[Azure SDK 範例頁面](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5)。
>
>此外，有更新的服務匯流排訊息範例一組[以下](https://github.com/Azure-Samples/azure-servicebus-messaging-samples)（本文撰寫時，他們會不本文中所述）。  

轉接範例，請參閱[服務匯流排轉送範例](../service-bus-relay/service-bus-relay-samples.md)。

## <a name="service-bus-messaging"></a>服務匯流排訊息

下列範例會說明如何撰寫使用服務匯流排訊息的應用程式。

注意訊息的範例，以存取您的服務匯流排命名空間連線字串。

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

### <a name="getting-started-samples"></a>快速入門的範例

這些範例說明基本郵件功能。

|範例名稱|描述|最小 SDK 版本|顯示狀態|
|---|---|---|---|
|[快速入門︰ 使用佇列訊息](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3)|示範如何使用 Microsoft Azure 服務匯流排傳送及接收郵件佇列中。|1.8|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|
|[快速入門︰ 訊息與主題](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5)|示範如何使用 Microsoft Azure 服務匯流排傳送及接收郵件中有多個訂閱的主題。|1.8|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|

### <a name="exploring-features"></a>探索功能

下列範例會示範服務匯流排的各種功能。

|範例名稱|描述|最小 SDK 版本|顯示狀態|
|---|---|---|---|
|[HTTP 權杖提供者](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5)|示範驗證服務匯流排 HTTP/其餘的用戶端的不同方式。|2.1|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|
|[服務匯流排 HTTP 用戶端](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a)|示範如何傳送郵件，並從透過 HTTP/其餘服務匯流排接收郵件。|2.3|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|
|[服務匯流排 Autoforwarding](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b)|示範如何自動轉寄郵件佇列中、 訂閱，或 deadletter 佇列到另一個佇列中或主題。 它也會示範如何傳送郵件到佇列中或透過傳輸佇列中的主題。|2.3|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|
|[仲介訊息︰ WCF 頻道工作階段範例](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451)|示範如何使用 Microsoft Azure 服務匯流排使用 Windows Communication Foundation (WCF) 頻道。 此範例顯示 WCF 通道傳送及接收郵件，透過服務匯流排佇列中的使用。 此範例會顯示在服務匯流排工作階段和非工作階段通訊。|1.8|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|
|[代理訊息︰ 交易](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e)|自動將示範如何使用 Microsoft Azure 服務匯流排訊息交易範圍內的功能，才能確保已確認訊息作業批次。|1.8|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|
|[使用其餘仲介訊息︰ 管理作業](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88)|示範如何管理上執行作業使用其餘服務匯流排。|1.8|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|
|[資源提供者 REST Api](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203)|示範如何使用新的服務匯流排 RDFE REST Api 來管理命名空間和訊息的項目。|1.8|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|
|[仲介訊息︰ WCF 服務工作階段範例](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2)|示範如何使用 Microsoft Azure 服務匯流排使用 WCF 服務模型。 此範例顯示 WCF 服務模型完成工作階段為基礎的通訊，透過服務匯流排佇列中的使用。|1.8|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|
|[仲介訊息︰ 要求回應](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8)|示範如何使用 Microsoft Azure 服務匯流排] 和 [要求/回應功能。 此範例顯示簡單的用戶端與伺服器通訊透過服務匯流排佇列。|1.8|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|
|[仲介訊息︰ 無法傳送信件佇列](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8)|示範如何使用 Microsoft Azure 服務匯流排 」 和 「 郵件 」 無法傳送信件佇列 」 功能。 此範例顯示的簡單的寄件者和收件者透過服務匯流排佇列中進行通訊。|1.8|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|
|[仲介訊息︰ 延遲的郵件](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879)|示範如何使用 Microsoft Azure 服務匯流排的郵件延功能。 此範例顯示的簡單的寄件者和收件者透過服務匯流排佇列中進行通訊。|1.8|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|
|[仲介訊息︰ 工作階段訊息](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4)|示範如何使用 Microsoft Azure 服務匯流排和訊息工作階段功能。 此範例顯示簡單的寄件者及接收者透過服務匯流排佇列中進行通訊。|1.8|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|
|[仲介訊息︰ 要求回應主題](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e)|示範如何實作使用 Microsoft Azure 服務匯流排主題和訂閱的要求/回應模式。 此範例顯示簡單的用戶端與伺服器通訊透過服務匯流排主題。|1.8|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|
|[仲介訊息︰ 要求回應佇列](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf)|示範如何使用 Microsoft Azure 服務匯流排] 和 [要求/回應功能。 此範例顯示簡單的用戶端與伺服器通訊透過兩個服務匯流排佇列。|1.8|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|
|[仲介訊息︰ 重複的偵測](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25)|示範如何使用佇列中的 Microsoft Azure 服務匯流排重複訊息偵測。 它會建立兩個佇列的重複偵測啟用與另一個不重複偵測。|1.8|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|
|[仲介訊息︰ 非同步訊息](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74)|示範如何使用 Microsoft Azure 服務匯流排收發郵件非同步佇列中。 佇列中提供的低耦合的非同步寄件者及接收者的任何數字之間的通訊 （此處單一收件者）。|1.8|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|
|[進階篩選條件，仲介訊息︰](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)|示範如何使用 Microsoft Azure 服務匯流排發佈/訂閱進階篩選。 它建立不同的篩選器定義主題和 3 的訂閱、 傳送訊息給主題中，並會接收來自訂閱的所有郵件。|1.8|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|
|[仲介訊息︰ 郵件預先擷取](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d)|示範如何使用 Microsoft Azure 服務匯流排郵件預先擷取功能。 它會示範如何使用時接收郵件預先擷取功能。|1.8|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|

## <a name="service-bus-reference-tools"></a>服務匯流排參考工具

下列範例會示範服務的各種其他功能。

|範例名稱|描述|最小 SDK 版本|顯示狀態|
|---|---|---|---|
|[服務匯流排總管](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)|服務匯流排瀏覽器可讓使用者連線至服務匯流排服務命名空間，並以簡單的方式管理訊息的項目。 此工具提供進階的功能，例如匯入/匯出功能，以及測試訊息的實體和轉送服務的能力。|1.8|Microsoft Azure 服務匯流排;Windows Server 的服務匯流排|
|[授權︰ SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93)|本範例會示範如何建立和管理服務的 Microsoft Azure Active Directory 存取控制 （也稱為存取控制服務或 ACS） 的身分識別的服務匯流排搭配使用。|N/A|Microsoft Azure 服務匯流排|

## <a name="next-steps"></a>後續步驟

請參閱下列主題服務匯流排的概觀。

- [服務匯流排訊息概觀](service-bus-messaging-overview.md)
- [服務匯流排架構](service-bus-architecture.md)
- [服務匯流排基本概念](service-bus-fundamentals-hybrid-solutions.md)
