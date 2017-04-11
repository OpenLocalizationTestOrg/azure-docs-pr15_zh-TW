<properties
    pageTitle="建立使用 Azure 入口網站 Azure 搜尋服務 |Microsoft Azure |裝載的雲端搜尋服務"
    description="瞭解如何佈建使用 Azure 入口網站 Azure 搜尋服務。"
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-service-using-the-azure-portal"></a>建立使用 Azure 入口網站 Azure 搜尋服務

本指南會引導您完成程序建立的 （或佈建） 使用[Azure 入口網站](https://portal.azure.com/)Azure 搜尋服務。

本指南假設您已使用 Azure 訂閱，可以登入 Azure 入口網站。

## <a name="find-azure-search-in-the-azure-portal"></a>Azure 入口網站中尋找 Azure 搜尋
1. 移至[Azure 入口網站](https://portal.azure.com/)，並登入。
1. 按一下加號 （[+]） 中左上角。
2. 選取**資料 + 的儲存空間**。
3. 選取 [ **Azure 搜尋**]。

![](./media/search-create-service-portal/find-search.png)

## <a name="pick-a-service-name-and-url-endpoint-for-your-service"></a>選擇服務名稱和服務的 URL 端點
1. 您的服務名稱會根據您會撥打您 API 管理和使用搜尋服務的 Azure 搜尋服務的端點 URL 的一部分。
2. 在 [ **URL** ] 欄位中輸入您的服務名稱。 服務名稱︰
  * 只能包含大小寫字母、 數字或連字號 (「-」)
  * 無法使用虛線 (「-」) 為第一次 2 個字元或最後一個單一字元
  * 不能包含連續連字號 (「，」)
  * 之間 2 到 60 個字元的長度限制


## <a name="select-a-subscription-where-you-will-keep-your-service"></a>選取的訂閱，您會在此保留您的服務
如果您有多個訂閱，您可以選取其中任何一種會包含此 Azure 搜尋服務。

## <a name="select-a-resource-group-for-your-service"></a>選取您的服務的資源群組
建立新的資源群組，或選取現有的項目。 資源群組是 Azure 服務一起使用的資源的集合。 例如，如果您使用 Azure 搜尋索引是 SQL 資料庫，這兩種服務應該是相同的資源群組的一部分。

## <a name="select-the-location-where-your-service-will-be-hosted"></a>選取裝載您的服務的位置
Azure 服務中，為 Azure 搜尋位於會裝載於世界各地的資料中心。 請注意，[可能不同價格](https://azure.microsoft.com/pricing/details/search/)的地理位置。

## <a name="select-your-pricing-tier"></a>選取您的價格層
[Azure 搜尋目前是在多個價格層](https://azure.microsoft.com/pricing/details/search/)︰ 免費、 Basic 或標準。 每一個層級都有自己[的容量與限制](search-limits-quotas-capacity.md)。 取得指示，請參閱[價格層或 SKU 的選擇](search-sku-tier.md)。

在此案例中，我們已選擇標準層我們的服務。

## <a name="select-the-create-button-to-provision-your-service"></a>選取 「 建立 「] 按鈕，提供您的服務

![](./media/search-create-service-portal/create-service.png)

## <a name="scale-your-service"></a>調整您的服務

您的服務已佈建後，您可以不按比例縮放以符合您的需求。 如果您選擇標準層 Azure 搜尋服務，您可以調整您的服務中的兩個維度︰ 複本和分割。 如果您選擇的基本層，您僅可以新增的複本。

*__磁碟分割__*可讓您的服務，來儲存及搜尋多個文件。

*__複本__*可讓您處理較高的搜尋查詢-[服務需要達成唯讀 SLA 2 複本，並在需要 3 的複本，以獲得讀/寫 SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)的服務。

1. 移至您的 Azure 搜尋服務管理刀 Azure 入口網站中。
2. 在**設定**刀中，選取 [**縮放比例**]。
3. 您可以新增複本或磁碟分割區以調整您的服務。
  * 您無法調整您的服務過去 36 搜尋單位。 您的搜尋單位總數是複本和磁碟分割區的產品 (複本 * 磁碟分割區 = 總搜尋單位)。
  * 如果您選擇的基本層，您可以只不按比例縮放到 3 的複本。 基本服務繫結至單一磁碟分割。

![](./media/search-create-service-portal/scale-service.png)

## <a name="next"></a>下一步
佈建之後 Azure 搜尋服務，您就可以開始定義[Azure 搜尋索引](search-what-is-an-index.md)，讓您可以上傳並搜尋您的資料。

快速教學課程，請參閱[開始使用 Azure 搜尋在入口網站](search-get-started-portal.md)。
