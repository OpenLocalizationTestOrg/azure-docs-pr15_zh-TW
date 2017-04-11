<properties
    pageTitle="服務匯流排轉送概觀 |Microsoft Azure"
    description="服務匯流排轉送概觀。"
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
    ms.date="09/01/2016"
    ms.author="sethm"/>


# <a name="overview-of-service-bus-relay"></a>服務匯流排轉送的概觀

服務匯流排的主要元件是集中式 （但高負載平衡）*轉送*服務可讓您建立的 Azure 資料中心與您的內部部署企業版環境中執行混合式應用程式。  服務匯流排轉送支援各種不同的傳輸通訊協定和 web 服務標準。 這包含 SOAP WS-*，與偶數其餘部分。 轉送服務，讓您能夠安全地公開位於公司企業網路公用雲端，而不需開啟防火牆連線，或要求侵入變更公司網路基礎結構的 Windows Communication Foundation (WCF) 服務，協助您混合式部署的應用程式。 

![轉送概念](./media/service-bus-relay-overview/sb-relay-01.png)

轉送服務支援傳統單向訊息，要求/回應訊息，並對等通訊。 也支援在網際網路範圍內啟用發佈/訂閱案例與點對點提升的效率的雙向通訊端通訊的事件通訊群組。 

轉送訊息模式中，內部部署服務連線到透過連外的連接埠轉送服務，並建立繫結至特定的側翼地址的通訊的雙向通訊端。 用戶端可以再與內部部署服務通訊傳送訊息給轉送服務目標側翼地址。 轉送服務會然後 「 轉送 」 郵件已經在位置的雙向通訊端透過內部部署服務。 用戶端便不需要直接連接到內部部署服務，不需要知道位置服務駐留，與內部部署服務並不需要任何輸入的連接埠的防火牆上開啟。

您啟動您內部部署的服務與使用 WCF 」 轉送 」 繫結套件轉送服務之間的連線。 在幕後轉送繫結對應至新傳輸繫結元素設計用來建立的整合與在雲端服務匯流排 WCF 頻道元件。 

## <a name="next-steps"></a>後續步驟

如需瞭解服務匯流排轉接的詳細資訊，請參閱下列主題。

- [Azure 服務匯流排架構概觀](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- [如何使用服務匯流排轉送服務](service-bus-dotnet-how-to-use-relay.md)

 