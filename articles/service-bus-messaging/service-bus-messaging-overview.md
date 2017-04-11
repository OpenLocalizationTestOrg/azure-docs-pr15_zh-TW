<properties
    pageTitle="服務匯流排訊息概觀 |Microsoft Azure"
    description="在雲端服務匯流排訊息︰ 有彈性的資料傳送"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.date="09/27/2016"
    ms.author="sethm"/>


# <a name="service-bus-messaging-flexible-data-delivery-in-the-cloud"></a>服務匯流排訊息︰ 在雲端的彈性的資料傳送

Microsoft Azure 服務匯流排是可靠的資訊傳送服務。 這項服務的目的是更輕鬆地通訊。 當兩個以上的合作對象想交換資訊時，所需的通訊機制。 服務匯流排是仲介，或第三方通訊機制。 這是在真實世界郵政服務類似。 郵遞區號服務進行全球任何位置傳送不同類型的字母和使用各種不同的傳遞提供封包很容易。

與郵政服務進行字母，服務匯流排是從寄件者和收件者的彈性的資訊傳送。 訊息服務可確保已傳送的資訊，即使雙方永遠都不會同時線上同時，，或其無法完全相同的時間。 如此一來，訊息很類似類似電話 （或電話使用是-之前通話等候和來電者 ID，也就是更多像仲介訊息的方式） 非代理通訊時，傳送一個字母。

郵件的寄件者也可以要求送達特性包括交易、 重複偵測、 以時間為基礎的到期日和批次處理各種。 這些模式有郵遞區號為以及︰ 重複傳遞、 必要的簽章、 地址變更或回收。

服務匯流排支援兩種不同的訊息模式︰*轉送*和*仲介訊息*。

## <a name="service-bus-relay"></a>服務匯流排轉送

[轉接](../service-bus-relay/service-bus-relay-overview.md)元件服務匯流排的是支援各種不同的傳輸通訊協定和 Web 服務標準集中式 （但高負載平衡） 服務。 這包含 SOAP WS-*，與偶數其餘部分。 [轉送服務](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md)會提供各種不同的轉送連線選項，並交涉直接對等連線時，就可能可以幫助。 服務匯流排.NET 開發人員使用 Windows Communication Foundation (WCF)，效能與合用性，兩者都最佳化，並提供透過 SOAP 和其餘的介面轉送服務的完整功能。 這可讓整合服務匯流排與任何 SOAP 或程式設計環境的其餘部分。

轉送服務支援傳統單向訊息，要求/回應訊息，並對等通訊。 也支援在網際網路範圍內啟用事件分配發行-訂閱案例與點對點提升的效率的雙向通訊端通訊。 轉送訊息模式中，內部部署服務連線到透過連外的連接埠轉送服務，並建立繫結至特定的側翼地址的通訊的雙向通訊端。 用戶端可以再與內部部署服務通訊傳送訊息給轉送服務目標側翼地址。 轉送服務會然後 「 轉送 」 郵件已經在位置的雙向通訊端透過內部部署服務。 用戶端便不需要直接連接到內部部署的服務，也不必要知道服務駐留，及內部部署服務並不需要任何輸入的連接埠位置的防火牆上開啟。

您起始連線您內部部署的服務與轉送服務之間，使用 WCF 」 轉送 」 繫結套件。 在幕後轉送繫結對應傳輸設計用來建立的整合與在雲端服務匯流排 WCF 頻道元件的繫結項目。

服務匯流排轉送提供許多優點，但需要兩者的用戶端與伺服器在線上，才能傳送及接收訊息一次。 這是最佳的通訊 HTTP 樣式，以要求可能不是通常久，或用戶端的連線偶，瀏覽器，例如行動應用程式]，依此類推。 代理訊息支援低耦合的通訊，並在它自己的優點。用戶端與伺服器可以連線時需要在非同步方式中執行的作業。

## <a name="brokered-messaging"></a>代理訊息

相較於轉送配置[仲介訊息](service-bus-queues-topics-subscriptions.md)可以想像為非同步，或者 「: 分離 」。 若要同時在線上產生者 （寄件者） 和消費者 （接收器） 沒有。 耗用的廠商會收到通知，直到訊息基礎結構可靠 （例如佇列） 儲存 」 代理人 」 中的郵件。 這個選項可讓中斷連線，請主動; 的分散式應用程式的元件例如，在進行的維修作業，或因為元件當機，而不影響整個系統。 此外，接收應用程式只能有上線特定的日期，例如只需執行結尾的工作日庫存管理系統的時間。

核心元件服務匯流排仲介訊息基礎結構的是佇列主題，以及訂閱。  主要差異在於主題支援可用於複雜內容路由與傳遞邏輯，包括傳送給多個收件者的發佈/訂閱功能。 這些元件啟用新非同步訊息案例，例如暫時耦合發佈/訂閱，與負載平衡。 如需有關這些訊息的實體的詳細資訊，請參閱[服務匯流排佇列主題，以及訂閱](service-bus-queues-topics-subscriptions.md)。

就跟轉送基礎結構的 WCF 和.NET Framework 程式設計，並透過其餘提供仲介訊息的功能。

## <a name="next-steps"></a>後續步驟

若要瞭解更多關於服務匯流排訊息，請參閱下列主題。

- [服務匯流排基本概念](service-bus-fundamentals-hybrid-solutions.md)
- [服務匯流排佇列主題，以及訂閱](service-bus-queues-topics-subscriptions.md)
- [如何使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
- [如何使用服務匯流排主題和訂閱](./service-bus-dotnet-how-to-use-topics-subscriptions.md)
 
