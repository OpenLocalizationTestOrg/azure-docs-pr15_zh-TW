<properties
    pageTitle="什麼是 Azure 轉送？ |Microsoft Azure"
    description="Azure 轉送的概觀"
    services="service-bus"
    documentationCenter=".net"
    authors="banisadr"
    manager="timlt"
    editor="" />

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="babanisa" />

# <a name="what-is-azure-relay"></a>什麼是 Azure 轉送？

Azure 轉送服務，協助您混合式部署的應用程式，讓您能夠安全地公開位於公司企業網路公用雲端，而不需開啟防火牆連線，或要求侵入變更公司網路基礎結構的服務。 轉送支援各種不同的傳輸通訊協定和 web 服務標準。

轉送服務支援傳統單向要求/回應，以及對等流量。 也支援在網際網路範圍內啟用發佈/訂閱案例與點對點提升的效率的雙向通訊端通訊的事件通訊群組。 

轉送的資料傳輸模式中，內部部署服務連線到透過連外的連接埠轉送服務，並建立繫結至特定的側翼地址的通訊的雙向通訊端。 用戶端然後可以藉由傳送流量，指定目標側翼地址轉送服務通訊與內部部署的服務。 轉送服務會然後 「 轉接 」 的每個用戶端專用的雙向通訊端內部部署服務的資料。 用戶端便不需要直接連接到內部部署服務，不需要知道位置服務駐留，與內部部署服務並不需要任何輸入的連接埠的防火牆上開啟。

轉送所提供的重要功能元素雙向、 未緩衝處理通訊跨越網路界限與 TCP 類似節流端點探索、 連線狀態，而且重疊端點安全性。 例如，它可以會的範圍在一部的電腦上的單一應用程式端點更必須變更網路環境侵入 VPN 技術時在 VPN 網路層級整合技術不同轉送的功能。

Azure 轉送有兩個功能︰

1. [混合式連線](#hybrid-connections)-使用開啟標準 Web 通訊端啟用多平台的案例

2. [WCF 轉送](#wcf-relays)使用 Windows Communication Foundation 啟用遠端程序的通話

混合式連線和 WCF 轉送啟用安全連線至公司企業網路中的 assests。 使用的是下列詳細說明您特定需求而定︰

|                                    | WCF 轉送 | 混合式連線 |
| ---------------------------------- |:---------:|:------------------:|
| **WCF**                            |     x     |                    |
| **.NET 核心**                      |           |         x          |
| **.NET framework**                 |     x     |         x          |
| **JavaScript/NodeJS**              |           |         x          |
| **Java***                          |           |         x          |
| **標準的開啟通訊協定**  |           |         x          |
| **多個 RPC Programing 模型** |           |         x          |
*<sub>依一般的顯示狀態</sub>

## <a name="hybrid-connections"></a>混合式連線

Azure 轉送混合式連線功能安全的開啟通訊協定的發展現有的可轉送功能會執行任何平台上、 和有基本 WebSocket 功能，包括明確 WebSocket API 共同任何語言網頁瀏覽器。 混合式連線以 HTTP 和 WebSockets 為基礎。

## <a name="wcf-relays"></a>WCF 轉送

WCF 轉送運作的完整.NET Framework (NETFX) 以及 WCF。 您啟動您內部部署的服務與使用 WCF 」 轉送 」 繫結套件轉送服務之間的連線。 在幕後轉送繫結對應至新傳輸繫結元素設計用來建立的整合與在雲端服務匯流排 WCF 頻道元件。

## <a name="service-history"></a>服務歷程記錄

混合式連線 transplants 前者，則平均名為 「 Biztalk 」 功能，在 Azure 服務匯流排 WCF 轉送。 新的混合式連線能力補充現有 WCF 轉送與下列兩個服務功能會有可見的未來; 轉送服務中並排他們共用常見的閘道器，但否則實作不同。

WCF 轉送是舊版的轉送提供許多客戶可能已經使用他們的 WCF programing 模型。

## <a name="next-steps"></a>後續步驟︰

- [轉送常見問題集](relay-faq.md)
- [建立命名空間](relay-create-namespace-portal.md)
- [開始使用.NET](relay-hybrid-connections-dotnet-get-started.md)
- [快速入門節點](relay-hybrid-connections-node-get-started.md)