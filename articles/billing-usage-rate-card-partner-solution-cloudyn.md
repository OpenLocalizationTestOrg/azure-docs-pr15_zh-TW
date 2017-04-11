<properties
   pageTitle="Microsoft Azure 使用情況和 RateCard Api 啟用 Cloudyn 為客戶提供 ITFM |Microsoft Azure"
   description="從 Microsoft Azure 計費合作夥伴 Cloudyn，其 Azure 計費 Api 整合其產品的體驗上提供唯一的觀點來看。  這是尤其有用的 Azure 和 Cloudyn 有興趣使用/嘗試 Cloudyn Azure 服務的客戶。"
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="08/16/2016"
   ms.author="mobandyo;bryanla"/>

# <a name="microsoft-azure-usage-and-ratecard-apis-enable-cloudyn-to-provide-itfm-for-customers"></a>Microsoft Azure 的使用狀況] 和 [RateCard Api 啟用為客戶提供 ITFM Cloudyn

Cloudyn、 Microsoft 開發夥伴和前置提供者的雲端管理功能，選擇新的 Microsoft Azure 資源使用狀況和 RateCard Api 私人預覽。  使用 API 提供資料的存取權估計 Azure 消耗訂閱。 RateCard API 提供非企業合約 EA 客戶的所有的 Azure 服務的完整價格的資訊。 這些 Api 整合在一起，將那些 Cloudyn 所提供的 IT 財務管理 (ITFM) 工具的輸入提供完整的資訊為基礎。

## <a name="introduction"></a>簡介

所謂 「 乘法 」 的資料使用方式 API RateCard API 的資料 (使用 [單位] 價格 [$unit] = 詳細的使用情況和成本) 建立最細微、 精確且可靠帳單資訊可用 Azure 今天。

![ITFM 概觀][1]

使用這些 Api 提供客戶的使用情況和成本，允許 Cloudyn 分析客戶帳戶以簡單、 程式設計的方式，並為其客戶執行各種 ITFM 工作上的重要資訊。

## <a name="integrating-cloudyn-with-the-ratecard-and-usage-apis"></a>整合 Cloudyn RateCard 與使用方式的 Api
RateCard API 需要幾個輸入的參數的詳細資訊，例如地區資訊、 貨幣格式與地區設定，但最重要的項目 OfferDurableID，指定的 Azure 提供客戶類型使用 （Pay-as-you-Go、 舊版 6 及 12 個月的認可計劃、 MSDN 優惠、 MPN 優惠、 優惠和其他人）。 在[Azure 使用情況和帳單入口網站](https://account.windowsazure.com/Subscriptions)，在 「 提供識別碼 」 的指定訂閱找 OfferDurableID。

在[Cloudyn 的 Azure](https://www.cloudyn.com/microsoft-azure/)服務註冊、 客戶可以新增 OfferDurableID 程式碼，可讓 Cloudyn 提取透過 RateCard API 其相關價格資訊。  不同類型的優惠的詳細資訊可以找到一個[Microsoft Azure 提供詳細資料](https://azure.microsoft.com/support/legal/offer-details/)] 頁面。

![Cloudyn ITFM 引擎概觀][2]

Cloudyn 用途使用情況和 RateCard Api，除了 Azure 效能 API，若要建立其他的層級的視覺效果、 分析、 提醒、 報告、 成本管理及當中的建議，提供 Azure 客戶可靠企業雲端 ITFM 工具。

## <a name="cloudyn-itfm-use-cases-enabled-by-usage-and-ratecard-api-integration"></a>啟用的使用狀況] 和 [RateCard API 整合 Cloudyn ITFM 使用案例
一般 Cloudyn ITFM 使用啟用的使用方式的情況下，而且 RateCard Api 包括︰

+ **成本分析**-可讓雲端中斷下任何原生識別維度 （提供者、 服務、 帳戶、 地區等） 的成本。 Azure 使用和 RateCard Api 這個簡單的工作，提供讓最細的使用方式解析，成本每個帳戶，是然後分組，做為篩選依據 Cloudyn 和呈現給使用者，圖形或表格式表單中的資料。

![成本分析圓形圖][3]

+ **成本資源分派 360** -可讓財務與 IT 管理員，以發掘實際成本分解、 驅動程式和雲端部署的趨勢。 進一步可讓管理員輕鬆地建立部署費用與業務單位、 部門、 地區和更多]，提供前所未有見解雲端成本，以及促進企業退款和 showbacks 之間的關聯。 Azure 使用和 RateCard Api 當作輸入 Cloudyn 的成本資源分派引擎，它輔助 Api 定義方法和商務邏輯配置標記或 untaggable 資源。

![成本資源分派 360 圖表][4]

+ **Cost-Effective 縮放**-提供增進工作的虛擬機器中，向右調整建議，以減少過大或過度能夠電腦上的客戶的費用。 它會檢查虛擬機器 CPU 和 RAM 指標 （透過效能 API) 的執行階段 （透過使用 API) 以及成本 （透過 RateCard API)。 Cloudyn 提供正確調整建議根據增進 CPU 或大的 RAM 資源 （效能），然後在乘以價格 delta (RateCard) 的估計的儲蓄計算的實際時間-使用狀況 （使用方法） 增進電腦的 Vm 之間。

![成本有效調整大小][5]

+ **雲端移轉建議**-提供雲端財務建議移轉。 它會檢查使用者的目前部署主要雲端廠商的雲端資源的成本，並將它與比較相當於 Azure 上部署的成本。 接著會提供細的每個資源，財務型移轉 Azure 的建議。 之後評估所需的 Azure （根據效能指標] 和 [使用者喜好設定]） 相當於部署，Cloudyn 會使用 RateCard API 評估相當於 Azure 上部署的成本。

+ **效能報告**-啟用 Azure 的效能 API，這些報表提供陣列的 CPU 和 RAM 使用率最佳化建議的功能。 以下是執行個體使用情況報表範例，以平均的 CPU 使用率簡報執行個體分解。

![效能報告][6]

+ **類別管理員**-Cloudyn，讓缺乏組織的雲端資源順序中的強大功能。 它會提供使用者自由進行有效率的測量和報表建立自己專屬的類別 （標記），與商業實務排列。 此外，使用者可以輕鬆地管理分類不一致的標記 （也就是拼字錯誤和其他的差異），並自動偵測到正確的成本屬性標記的資源。

![類別管理員][7]

## <a name="video"></a>視訊

以下是顯示 Azure 客戶如何使用 Cloudyn Azure 和 Azure 計費 Api，取得其 Azure 消耗資料深入見解的短片。

> [AZURE.VIDEO cloudyn-provides-cloud-itfm-tools-via-microsoft-azure-apis]


## <a name="next-steps"></a>後續步驟

+ 啟動免費[Cloudyn 的 Azure](https://www.cloudyn.com/microsoft-azure/)試用版，看看如何取得成本透明度與自訂報告與分析，以及在您的 Microsoft Azure 雲端部署。
+ 如需 Azure 資源使用狀況和 RateCard Api 的概觀，請參閱[獲得深入見解到消耗 Microsoft Azure 資源](billing-usage-rate-card-overview.md)。
+ 請參閱[Azure 計費 REST API 參考](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)的兩個 Api 的一部分的 Api 提供 Azure 資源管理員所設定的詳細資訊。
+ 如果您想要深入瞭解向右將程式碼範例，請查看我們 Microsoft Azure 計費 API 程式碼範例[Azure 程式碼](https://azure.microsoft.com/documentation/samples/?term=billing)範例。

## <a name="learn-more"></a>深入瞭解
+ 若要進一步瞭解 Microsoft Azure Enterprise 合約 (EA) 優惠] 之下，請造訪 [企業版的授權 Azure] (https://azure.microsoft.com/pricing/enterprise-agreement/)
+ 請參閱[Azure 資源管理員的概觀](azure-resource-manager/resource-group-overview.md)，瞭解更多關於 Azure 資源管理員。
+ 如需有關的套件的必要大致瞭解雲端花、 Gartner 文章[IT 財務管理 (ITFM) 工具市場指南](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb)，請參閱協助工具的詳細資訊。

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Overview.png
[2]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Engine-Overview.png
[3]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Analysis-Pie-Chart.png
[4]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Allocation-360-Chart.png
[5]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Effective-Sizing.png
[6]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Performance-Reports.png
[7]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Category-Manager.png
