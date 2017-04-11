<properties
   pageTitle="將需求品質的 ExpressRoute |Microsoft Azure"
   description="本頁面提供設定和管理 QoS ExpressRoute 電路詳細的需求。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="expressroute-qos-requirements"></a>需求 ExpressRoute 品質

商務用 Skype 有各種需要差異的 QoS 處理的工作量。 如果您打算使用透過 ExpressRoute 語音服務，您應該遵循如下所述的需求。

![](./media/expressroute-qos/expressroute-qos.png)

>[AZURE.NOTE] Microsoft 對等只適用於需求品質。 您收到 Azure 公用對等和 Azure 私人對等的網路流量的 DSCP 值會重設為 0。 

下表提供使用商務用 Skype 的 DSCP 標示的清單。 如需詳細資訊，請參閱[管理 QoS 商務用 Skype 的](https://technet.microsoft.com/library/gg405409.aspx)。

| **流量類別** | **處理方式 （DSCP 標示）** | **負載商務用 Skype** |
|---|---|---|
| **語音** | EF (46) | Skype / Lync 語音 |
| **互動式** | AF41 (34) | 視訊 |
|   | AF21 (18) | 應用程式共用 | 
| **預設值** | AF11 (10) | 檔案傳輸|
|   | CS0 (0) | 其他項目| 


- 您應該分類負載，並標示的右 DSCP 值。 依照所提供的指導方針[以下](https://technet.microsoft.com/library/gg405409.aspx)如何在您的網路中設定 DSCP 標示。

- 您應該設定，並在您的網路支援多個 QoS 佇列中。 語音必須獨立課程和接收 EF 處理方式在 RFC 3246 中指定。 

- 您可以決定佇列機制、 壅塞偵測原則，以及每個流量類別的頻寬配置。 不過，必須保留標記 skype 商務負載 DSCP。 如果您使用的 DSCP 標示未列於上方，例如 AF31 (26)，您必須重新撰寫此 DSCP 值設為 0 之前封包傳送至 Microsoft。 Microsoft 只會傳送封包標示 DSCP 值上表所示。 

## <a name="next-steps"></a>後續步驟

- 請參閱[路由](expressroute-routing.md)及[NAT](expressroute-nat.md)的需求。
- 請參閱下列連結設定 ExpressRoute 連線。

    - [建立 ExpressRoute 電路](expressroute-howto-circuit-classic.md)
    - [設定路由](expressroute-howto-routing-classic.md)
    - [連結 VNet ExpressRoute 電路](expressroute-howto-linkvnet-classic.md)
