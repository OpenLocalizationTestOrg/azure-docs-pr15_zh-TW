<properties 
    pageTitle="轉接常見問題集 |Microsoft Azure"
    description="回答 Azure 轉送相關的一些常見問題集問題。"
    services="service-bus"
    documentationCenter="na"
    authors="jtaubensee"
    manager=""
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="jotaub" />

# <a name="relay-faq"></a>轉送常見問題集

本文將回答 Microsoft Azure 轉送一些常見問題集疑問。 您也可以造訪[Azure 支援常見問題集](http://go.microsoft.com/fwlink/?LinkID=185083)的一般 Azure 價格和支援資訊。 下列主題會包含︰

- [關於 Azure 轉送的一般問題](#general-questions)
- [價格](#pricing)
- [配額](#quotas)
- [訂閱及命名空間管理](#subscription-and-namespace-management)
- [疑難排解](#troubleshooting)

## <a name="general-questions"></a>一般問題

### <a name="what-is-azure-relay"></a>什麼是 Azure 轉送？

[轉送服務](relay-what-is-it.md)，提供無障礙主控和從任何位置存取 WCF 服務的能力。 換句話說，這可讓執行 Azure 資料中心與企業內部部署環境中的混合式應用程式。

### <a name="what-is-a-relay-namespace"></a>什麼是轉送命名空間？

[命名空間](Relay-create-namespace-portal.md)來處理轉送資源應用程式中的提供範圍容器。 建立一個需要使用轉送，會開始的第一個步驟之一。

## <a name="pricing"></a>價格

此區段回答價格結構轉送一些常見問題集疑問。 您也可以造訪[Azure 支援常見問題集](http://go.microsoft.com/fwlink/?LinkID=185083)的一般的 Microsoft Azure 價格資訊。 完整轉送價格的詳細資訊，請參閱[服務匯流排定價詳細資料](https://azure.microsoft.com/pricing/details/service-bus/)。

### <a name="how-do-you-charge-for-relay"></a>您該如何收費轉送？

如需轉送價格的完整資訊，請參閱[服務匯流排定價詳細資料][Pricing overview]。 除了所述的價格，您會為您的應用程式已佈建資料中心以外的出口相關聯的資料傳輸。

### <a name="what-usage-of-relay-is-subject-to-data-transfer"></a>使用何種方式轉送應採用愛爾蘭的資料傳輸？

轉送包含 5 GB 每個月，每個訂閱的資料輸入。 有輸入出口其他 Azure 免費的轉送所使用的資料。

轉送資料收費的寄件者僅輸入時，為轉送接聽程式不會造成資料收費。 例如，如果您傳送 1 GB，只計費 1 gb，即使接聽程式也會收到 1 GB，以及可能外部 Azure 的資料中心。

### <a name="how-are-relay-hours-calculated"></a>如何計算轉送時間？

轉送小時被計費期間每個轉送是 「 開啟 」 在給定的計費期間的時間的累計數。 轉送隱含產生並開啟指定的命名空間時轉送接聽第一次連接至該地址。 只有在從其位址的最後一個接聽中斷連線時，就會關閉轉送。 因此，用於帳單轉送會被視為 」 開啟 「 從第一個轉送接聽連接到最後一個轉送接聽中斷命名空間的時間。 換句話說，轉送會被視為開啟每一次或更多轉送接聽程式連線至其服務匯流排地址。

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-given-relay"></a>如果我有多個接聽程式連線至指定的轉送？

在某些情況下，單一轉送可能有多個連線的接聽程式。 轉送會被視為 「 開啟 」 時至少有一個轉送接聽已連線至該。 新增其他接聽開啟轉送造成其他轉送小時。 轉送寄件者 （用戶端，呼叫或傳送訊息給轉送） 數連線到轉送也有不會影響轉送小時計算。

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>郵件量表如何計算的 WCF 轉送？

**這只適用於 WCF 轉送並不是混合式連線的成本**

一般而言，計費郵件所計算的轉送使用相同的方法，如上述仲介實體 （佇列、 主題及訂閱）。 然而，有幾個主要的差異︰

傳送訊息給服務匯流排轉送時的處理方式 」 完整透過 「 傳送至接收訊息，而非傳送至服務匯流排轉送轉送接聽後面接著傳送到轉送接聽程式。 因此，兩個計費訊息就會導致要求回覆樣式服務引動 （最多 64 KB) 針對轉送接聽︰ 一個計費的郵件，以要求與回應一個計費訊息 (假設回應也是\<= 64 KB)。 這與使用佇列溝通用戶端和服務。 情況中，在相同的邀請回覆模式需要邀請傳送至佇列，後面接著佇列/送達佇列中的服務，後面接著回覆傳送至另一個佇列及移除佇列/送達從該佇列用戶端。 使用相同 (\<= 64 KB) 整個大小假設，操作佇列圖樣會因此導致四個計費的郵件，按兩次計費實作使用轉送的同一個模式的數字。 當然，有許多優點，若要使用佇列來達到這個模式，例如持續性，並載入資源撫平。 下列優惠可能左右對齊的額外費用。

開啟使用 netTCPRelay WCF 繫結的轉送處理郵件，不會顯示為個別郵件，但資料流傳送系統的資料。 換句話說，只有寄件者及接聽具有完善的個別框架設定訊息傳送/接收使用此繫結。 因此，使用 netTCPRelay 繫結轉送的所有資料都會被都視為來計算計費郵件資料流。 在此情況下，服務匯流排會計算資料的總數量傳送，或透過 5 分鐘為基礎的每個個別轉送，與該總計除以 64 KB，以決定該期間問題轉送計費訊息的數目。

## <a name="quotas"></a>配額

|配額名稱|範圍|類型|超過時的行為|值|
|---|---|---|---|---|
|同時接聽轉接上|實體|靜態|其他連線的後續要求會被拒絕和例外狀況會收到呼叫程式碼。|25|
|同時轉送接聽程式|系統|靜態|其他連線的後續要求會被拒絕和例外狀況會收到呼叫程式碼。|2000|
|每個服務命名空間中的所有轉送結束點的同時轉送連線|系統|靜態|-|5000|
|每個服務命名空間轉送端點|系統|靜態|-|10000|
|郵件大小[NetOnewayRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.netonewayrelaybinding.aspx)和[NetEventRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.neteventrelaybinding.aspx)轉送|系統|靜態|超過這些配額的內送郵件都會被拒絕和例外狀況會收到呼叫程式碼。|64 KB
|郵件大小[HttpRelayTransportBindingElement](https://msdn.microsoft.com/library/microsoft.servicebus.httprelaytransportbindingelement.aspx)和[NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx)轉送|系統|靜態|-|無限制|

### <a name="does-relay-have-any-usage-quotas"></a>轉送是否有任何使用量配額？

根據預設，任何雲端服務 Microsoft 設定會彙總的每月使用量配額的所有客戶的訂閱。 因為我們瞭解您可能需要超過這些限制，請連絡客戶服務隨時，讓我們瞭解您的需求並適當地調整這些限制。 服務匯流排彙總的使用量配額如下所示︰

- 5 億郵件
- 2 百萬轉送小時

同時執行保留停用已超過其使用量配額限制在特定月份的客戶帳戶的權利，我們會提供電子郵件通知，並進行多次嘗試採取任何動作前，先連絡客戶。 客戶超過這些配額仍可負責超過配額的費用。

#### <a name="naming-restrictions"></a>命名限制

6-50 個字元的長度之間只能轉接命名空間的名稱。

## <a name="subscription-and-namespace-management"></a>訂閱及命名空間管理

### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>如何將命名空間移轉到另一個 Azure 訂閱？

您可以使用 PowerShell 命令 (找到，請參閱[以下](../service-bus-messaging/service-bus-powershell-how-to-provision.md#migrate-a-namespace-to-another-azure-subscription)) 若要將命名空間移到另一個 Azure 訂閱。 若要執行的作業，命名空間必須已經是作用中。 也執行命令的使用者必須是來源和目標訂閱的管理員。

## <a name="troubleshooting"></a>疑難排解

### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-their-suggested-actions"></a>有哪些 Azure 轉送 Api 和建議的動作所產生的例外狀況？

[轉送例外][Relay exceptions]本文說明一些例外狀況，建議的動作。

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>什麼是共用的 Access 簽章和語言支援產生簽章？

共用的 Access 簽章會根據 SHA – 256 安全雜湊或 Uri 驗證機制。 如需如何建立您自己的簽章中節點，PHP、 Java 和 C 的資訊\#，請參閱[共用 Access 簽章][]。

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[共用的 Access 簽章]: service-bus-sas-overview.md

## <a name="next-steps"></a>後續步驟︰

- [建立命名空間](relay-create-namespace-portal.md)
- [開始使用.NET](relay-hybrid-connections-dotnet-get-started.md)
- [快速入門節點](relay-hybrid-connections-node-get-started.md)