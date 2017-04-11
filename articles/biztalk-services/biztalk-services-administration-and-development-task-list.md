<properties
    pageTitle="管理和開發工作] 清單中 Biztalk |Microsoft Azure"
    description="規劃及作業可協助部署 Azure BizTalk 服務。"
    services="biztalk-services"
    documentationCenter=""
    authors="msftman"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="deonhe"/>

# <a name="administration-and-development-task-list-in-biztalk-services"></a>管理與開發 BizTalk 服務中的工作清單  

## <a name="getting-started"></a>快速入門
使用 Microsoft Azure Biztalk 工作時，有幾個內部部署和雲端的元件，可考慮。 若要開始，請考慮下列程序流程︰  

|步驟|誰負責|任務|相關的連結|
|----|----|----|----|
|1。|系統管理員|建立 Microsoft Azure 訂閱使用 Microsoft 帳戶或組織帳戶|[Azure 傳統入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)|
|2。|系統管理員|建立或佈建 BizTalk 服務。|[建立 BizTalk 服務使用 Azure 傳統入口網站](http://go.microsoft.com/fwlink/p/?LinkID=302280)|
|3。|系統管理員|註冊您或貴公司的 Biztalk 部署|[登錄及更新 BizTalk Services 入口網站上的 BizTalk 服務部署](https://msdn.microsoft.com/library/azure/hh689837.aspx)|
|4。|系統管理員|適用於應用程式使用 BizTalk 介面卡服務來連線到內部部署線條商務 (LOB) 系統或使用佇列中或主題目的地。  建立 Azure 服務匯流排命名空間。 為此命名空間、 服務匯流排發行者的名稱，然後服務匯流排發行者金鑰值開發人員。|[如何︰ 建立或修改服務匯流排服務命名空間](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)並[取得發行者名稱和發行者鍵值](biztalk-issuer-name-issuer-key.md)|
|5。|開發人員|安裝 SDK，並在 Visual Studio 建立 BizTalk 服務專案。|[安裝 Azure Biztalk SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx)並[建立 Azure 多媒體訊息的端點](https://msdn.microsoft.com/library/azure/hh689766.aspx)|
|6。|開發人員|部署至 BizTalk 服務專案裝載於 Azure 您 BizTalk 服務。|[部署及重新整理 BizTalk 服務專案](https://msdn.microsoft.com/library/azure/hh689881.aspx)|
|7。|系統管理員|如果您使用的 EDI 會套用。  您可以新增合作夥伴和 Microsoft Azure BizTalk Services 入口網站上建立合約。 當您建立合約時，您可以新增橋及/或合約設定開發人員所建立的轉換。|[設定 EDI、 AS2 和 EDIFACT BizTalk Services 入口網站上](https://msdn.microsoft.com/library/azure/hh689853.aspx)|
|8。|系統管理員|使用 [Azure 傳統入口網站，監控 BizTalk 服務，包括效能指標的狀況。|[Biztalk︰ 儀表板]、 監視器和縮放比例] 索引標籤](http://go.microsoft.com/fwlink/p/?LinkID=302281)|
|9。|系統管理員|使用 Microsoft Azure BizTalk Services 入口網站，來管理橋檔案被處理時，使用 BizTalk 服務及追蹤訊息的成品。|[使用 [BizTalk Services 入口網站](https://msdn.microsoft.com/library/azure/dn874043.aspx)|
|10。|系統管理員|建立備份的計劃，以備份 BizTalk 服務。|[業務連續性和 BizTalk 服務中的損壞修復](https://msdn.microsoft.com/library/azure/dn509557.aspx) |  
## <a name="next-steps"></a>後續步驟
[教學課程和範例](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[在 Visual Studio 中建立專案](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[安裝 Azure Biztalk SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>概念
[在 Visual Studio 中建立專案](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
[EDI、 AS2 和 EDIFACT 訊息 （企業）](https://msdn.microsoft.com/library/azure/hh689898.aspx)  
## <a name="other-resources"></a>其他資源  
[新增來源與目的地，橋訊息端點](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[瞭解並建立訊息對應及轉換](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[使用 BizTalk 介面卡服務 (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[Azure BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=303664)
