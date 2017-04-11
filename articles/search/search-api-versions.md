<properties
   pageTitle="Azure 搜尋的 API 版本 |Microsoft Azure |搜尋 API"
   description="Azure 搜尋 REST Api 和.NET sdk 的用戶端文件庫的版本原則。"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="08/16/2016"
   ms.author="brjohnst"/>

# <a name="api-versions-in-azure-search"></a>Azure 搜尋中的 API 版本

Azure 搜尋會定期復原取出功能更新。 有時候，但並不一定會更新要求我們發佈我們 API 的新版本，才能保留回溯相容性。 發佈新版本，可讓您控制何時及如何整合搜尋服務的更新程式碼。

一般而言，我們嘗試發佈只有必要時，新的版本，因為它可以包含一些投入升級您的程式碼，以使用新的 API 版本。 如果我們需要變更某些方面的 API 的線回溯相容性的方式，我們將只發佈新版本。 可能是因為修正現有的功能，或變更現有的 API 表面區域的新功能。

我們遵循 SDK 更新相同的規則。 Azure 搜尋 SDK 遵循的[語意版本設定](http://semver.org/)規則，這表示它的版本有三個部分︰ 主要與次要、 建立數字 (例如，1.1.0)。 我們將發行新若中斷回溯相容性的變更只 SDK 的主要版本。 不分行功能更新，我們會增加的次要版本，並修正錯誤的我們只會增加建置版本。

##<a name="snapshot-of-current-versions"></a>目前版本的快照 

以下目前版本的所有的快照程式設計介面 Azure 搜尋。 這份文件各節可以找到指南和其他詳細資料。

介面|最新的主要版本|狀態
----------|-------------------------|------
[.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)|1.1 （英文)|可用，通常發行年 2 月 2016
[.NET SDK 預覽](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx)|2.0 預覽|預覽、 發行年 8 月 2016
[服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)|2015-02 28|推出
[服務 REST API 預覽](search-api-2015-02-28-preview.md)|2015 02-28 預覽|預覽
[管理 REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx)|2015-08 19|推出

REST api，包括`api-version`每一項是必要。 如此可讓您輕鬆地鎖定目標特定的版本，例如預覽 API。 下列範例會說明如何`api-version`指定參數︰

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2015-02-28

> [AZURE.NOTE] 雖然每一條`api-version`，我們建議您所有的 API 要求使用相同的版本。 新的 API 版本介紹屬性或無法辨識的舊版本的作業時，這是如此。 混合 API 版本可以有非預期的結果，應避免。
> 
服務 REST API 及管理 REST API 是彼此獨立版本。 任何相似性的版本號碼會共同損害。

推出 （或 GA） Api 可以生產環境中使用，而且會而有所 Azure 服務等級協定。 預覽版本有實驗永遠不會移轉至 GA 版的功能。 **我們強烈建議使用生產應用程式中的預覽 Api。**

##<a name="sdk-version-roadmap"></a>SDK 版本的藍圖

每個版本的.NET SDK 目標服務 REST API 的特定版本。 功能會導入 REST API 中第一次，和在 SDK，則實作。

.NET SDK 現在推出，而工作已在進行中的下一個版本。 下表的外觀; SDK 的未來版本，讓您了解接下來。

.NET SDK 版本|REST API 版本|功能|知道
----------------|----------------|--------|---
1.1 （英文)|2015-02 28|Lucene 查詢語法|2016 年 2 月
2.0 預覽|2015 02-28 預覽|自訂分析器 Azure Blob 與表格索引子欄位對應 Etag|2016 年 8 月
2.x|新版 GA API|相同 2.0 預覽|最早 Q4 2016

##<a name="about-preview-and-generally-available-versions"></a>關於預覽和推出版本

Azure 搜尋永遠預先發行實驗的功能，透過 REST API 第一次，然後透過.NET SDK 的發行版本。

預覽功能不一定會移轉至 GA 發行版本。 穩定，可能不會變更，但小型回溯相容修正與增強功能，則會被視為 GA 版本中的功能，而預覽功能，可進行測試和實驗功能設計及實作收集意見反應的目標。 

不過，因為預覽功能會有所變更，我們建議您不要撰寫會在預覽版本的相依性的實際執行程式碼。 如果您使用的是舊版的預覽，我們建議您升級至推出 (GA) 版本。 

針對.NET SDK︰ 程式碼移轉的指引，請參閱[升級.NET SDK](search-dotnet-sdk-migration.md)。

開放表示 Azure 搜尋現在已在服務等級協定 (SLA)。 SLA 位於[Azure 搜尋服務等級協定](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

