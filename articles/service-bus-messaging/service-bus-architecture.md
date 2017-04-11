<properties 
    pageTitle="服務匯流排架構 |Microsoft Azure"
    description="將說明訊息及轉送處理 Azure 服務匯流排架構。"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/11/2016"
    ms.author="sethm" />

# <a name="service-bus-architecture"></a>服務匯流排架構

本文將說明的郵件和轉送處理 Azure 服務匯流排架構。

## <a name="service-bus-scale-units"></a>服務匯流排刻度] 單位

服務匯流排被依*刻度] 單位*。 刻度] 單位為單位的部署，並包含所有必要的元件執行服務。 每個區域部署一或多個服務匯流排刻度] 單位。

服務匯流排命名空間] 已對應至刻度] 單位。 刻度] 單位處理所有類型的服務匯流排實體︰ 轉送和仲介訊息的實體佇列、 主題 （[訂閱]）。 服務匯流排刻度] 單位包含下列元件︰

- **閘道器節點的一組。** 閘道器節點驗證傳入要求和處理轉送要求。 每個閘道器節點有的公用 IP 位址。

- **訊息代理人節點一組。** 訊息的代理人節點處理相關訊息的實體的要求。

- **一個閘道存放區。** 閘道器儲存區存放在此刻度] 單位中定義每個實體的資料。 閘道器 store 實作 SQL Azure 資料庫的上方。

- **多個訊息存放區。** 訊息儲存保留所有佇列、 主題及訂閱此刻度] 單位中定義的郵件。 同時也包含所有的訂閱資料。 除非啟用[分割訊息的實體](service-bus-partitioning.md)，佇列或主題] 已對應至一個訊息存放區。 訂閱會儲存在相同的訊息存放區為其父系的主題。 除了服務匯流排[進階版訊息](service-bus-premium-messaging.md)訊息儲存實作上方 SQL Azure 資料庫。

## <a name="containers"></a>容器

每個訊息的實體會被指派特定容器。 容器是使用一個訊息商店此容器的所有相關資料的邏輯建構。 每個容器會被指派到訊息的代理人節點。 一般而言，還有更多容器訊息代理人節點。 因此，每個訊息的代理人節點載入多個容器。 訊息的代理人節點的容器的組織同樣會載入所有訊息的代理人節點。 如果載入的圖樣變更 （例如，下列其中一項非常忙碌容器取得），或如果訊息的代理人節點就會暫時無法使用，轉散發容器之間的訊息的代理人節點。

## <a name="processing-of-incoming-messaging-requests"></a>處理的內送郵件的要求

當用戶端到服務匯流排傳送邀請時，Azure 負載平衡器會將其傳送到任何閘道器的節點。 閘道器節點授權要求。 如果要求疑慮訊息的實體佇列、 主題 （訂閱），閘道器節點的實體閘道器存放區中尋找，並決定哪些訊息存放區中的實體位於。 然後查閱哪一個訊息的代理人節點目前正在服務此容器，並會傳送要求給該訊息的代理人節點。 訊息的代理人節點處理要求，並更新容器存放區中的實體狀態。 訊息的代理人節點傳送回應回閘道器節點，會傳送至發出的原始邀請用戶端適當的回應。

![處理的內送郵件的要求](./media/service-bus-architecture/IC690644.png)

## <a name="processing-of-incoming-relay-requests"></a>內送轉送要求的處理

當用戶端到服務匯流排傳送邀請時，Azure 負載平衡器會將其傳送到任何閘道器的節點。 如果邀請聆聽要求，閘道器節點會建立新的轉送。 如果邀請特定的轉送的連線要求，擁有轉送的閘道器節點連線要求轉送給閘道器節點。 擁有轉送的閘道器節點聆聽用戶端，提出建立要收到連線要求的閘道器節點的暫時頻道接聽傳送側翼要求。

建立轉送連線時，用戶端可以交換透過用於側翼閘道器節點的訊息。

![內送轉送要求的處理](./media/service-bus-architecture/IC690645.png)

## <a name="next-steps"></a>後續步驟

既然您已讀取服務匯流排架構，若要開始的概觀請參考下列連結︰

- [服務匯流排訊息概觀](service-bus-messaging-overview.md)
- [服務匯流排基本概念](service-bus-fundamentals-hybrid-solutions.md)
- [使用服務匯流排佇列佇列訊息解決方案](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)
