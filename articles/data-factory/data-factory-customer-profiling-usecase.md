<properties 
    pageTitle="使用大小寫的客戶剖析" 
    description="瞭解如何 Azure 資料工廠用來建立的資料導向工作流程 （管線） 若要設定檔遊戲客戶。" 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/06/2016" 
    ms.author="shlo"/>

# <a name="use-case---customer-profiling"></a>使用大小寫的客戶剖析

Azure 資料工廠是用來實作 Cortana 智慧的套件解決方案加速器的許多服務。  如需有關 Cortana 智慧的詳細資訊，請造訪[Cortana 智慧套件](http://www.microsoft.com/cortanaanalytics)。 在此文件中，我們會說明簡單的使用案例可協助您開始使用了解如何 Azure 資料工廠可以解決常見狀況分析問題。

您只需要存取，然後試用這個簡單的使用案例是[Azure 訂閱](https://azure.microsoft.com/pricing/free-trial/)。  您可以部署[範例](data-factory-samples.md)本文所述的步驟，即可實作這項使用案例的範例。

## <a name="scenario"></a>案例

Contoso 是建立多個平台的遊戲遊戲公司︰ 遊戲主控台、 手持裝置與個人電腦 (Pc)。 為玩家進行這些遊戲，大量記錄資料會產生追蹤使用模式，遊戲樣式，以及使用者的喜好設定。  人口，地區，與組合時的產品資料 Contoso 可以執行分析，以增強玩家的經驗的相關指引，並目標升級的地方，您的遊戲購買。 

Contoso 的目標是識別向上銷售/交叉銷售商機根據遊戲歷程記錄的其播放新增磁碟機商務等比級數令人讚嘆的功能，並為客戶提供更好的體驗。 此使用案例中，我們使用遊戲公司的商務範例。 公司想要最佳化其遊戲根據玩家的行為。 這些原則適用於任何想要加入周圍其產品與服務客戶增強他們的客戶體驗的企業。

## <a name="challenges"></a>挑戰


## <a name="solution-overview"></a>解決方案概觀

這個簡單的使用案例可供如何使用 Azure 資料工廠內嵌、 準備、 轉換、 分析和發佈資料的範例。

![端對端工作流程](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

此圖說明如何資料管線之後，出現在 Azure 入口網站已部署。

1.  **PartitionGameLogsPipeline**讀取 blob 儲存體中的原始遊戲事件，並建立磁碟分割區以年、 月和日。
2.  **EnrichGameLogsPipeline**聯結地理程式碼參考資料分割遊戲事件，並豐富的資料對應至對應的地理位置的 IP 位址。
3.  **AnalyzeMarketingCampaignPipeline**管線使用 enriched 的資料，並建立包含行銷行銷活動的效益的最終輸出的廣告資料程序。

在此範例中，資料工廠用來協調活動的複製輸入的資料、 轉換及程序資料，並輸出至 Azure SQL 資料庫的最後一個資料。  您也可以以視覺化方式呈現資料管線的網路、 管理，並監控他們的狀態，從 UI。

## <a name="benefits"></a>優點

最佳化其使用者設定檔分析，並將其對齊與商務目標，遊戲公司是快速收集使用模式和分析其行銷活動的效益。




