<properties 
   pageTitle="監視使用情況和 Azure 搜尋服務中的統計資料 |Microsoft Azure |裝載的雲端搜尋服務" 
   description="Azure 搜尋] 時，在 Microsoft Azure 裝載的雲端搜尋服務追蹤資源消耗和索引的大小。" 
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="jhubbard" 
   editor=""
   tags="azure-portal"/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required" 
   ms.date="05/17/2016"
   ms.author="heidist"/>

# <a name="monitor-usage-and-statistics-in-an-azure-search-service"></a>監視使用情況與搜尋 Azure 服務中的統計資料

追蹤成長索引和文件大小，可協助您主動調整之前正中的上限您所建立的服務的容量。 

若要監控資源使用狀況，計算和統計資料服務會輕鬆地檢視在[Azure 入口網站](https://portal.azure.com)，但您也可以取得資訊以程式設計方式如果您要建立自訂的服務管理工具。 本文說明這兩種技術的步驟。

您也可以使用新的搜尋流量分析功能，以了解將索引層級的活動。 請造訪[Azure 搜尋流量分析](search-traffic-analytics.md)，即可開始使用。

##<a name="view-counts-and-metrics-in-the-portal"></a>在入口網站檢視計數和指標 

1. [Azure 入口網站](https://portal.azure.com)登入。 

2. 開啟您的 Azure 搜尋服務的服務儀表板。 您可以在 [首頁] 頁面上，找到服務的磚，或您可以從瀏覽 JumpBar 上瀏覽至服務。 如需逐步指示，請參閱[建立服務](search-create-service-portal.md)。

使用情況] 區段包含量表，告訴您可用的資源的哪些部分是目前使用中。

  ![][1]

回收的共用的服務都有一個複本的最大值和分割每個。 此外，僅可支援 10000 份文件的總或 50 MB 的資料，視何者先。

##<a name="get-index-statistics-using-the-rest-api"></a>取得使用 REST API 索引統計資料

Azure 搜尋 REST API 和.NET SDK 提供服務計量以程式設計方式存取。  如果您使用[索引子](https://msdn.microsoft.com/library/azure/dn946891.aspx)從 Azure SQL 資料庫或 DocumentDB，有其他的 API 載入索引是可以取得您所需要的數字。 

  + [取得索引統計資料](https://msdn.microsoft.com/library/azure/dn798942.aspx)
  + [計算文件](https://msdn.microsoft.com/library/azure/dn798924.aspx)
  + [取得索引狀態](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## <a name="next-steps"></a>後續步驟

檢閱[限制和容量](search-limits-quotas-capacity.md)判斷磁碟分割區和必須如果現有的容量沒有足夠的複本。 

瀏覽[管理您的搜尋服務上 Microsoft Azure](search-manage.md)服務管理中的詳細資訊。

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG




 
