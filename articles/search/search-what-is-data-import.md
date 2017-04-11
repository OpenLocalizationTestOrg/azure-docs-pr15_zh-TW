<properties
    pageTitle="Azure 搜尋中的資料上傳 |Microsoft Azure |裝載的雲端搜尋服務"
    description="瞭解如何上傳至 Azure 搜尋索引的資料。"
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="upload-data-to-azure-search"></a>上傳至 Azure 搜尋的資料
> [AZURE.SELECTOR]
- [概觀](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [其餘](search-import-data-rest-api.md)


## <a name="data-upload-models-in-azure-search"></a>資料上傳 Azure 搜尋中的模型
有兩種方式來填入您的 Azure 搜尋索引，您的資料。 第一個選項手動將推入您的資料使用 Azure 搜尋[REST API](search-import-data-rest-api.md)或[.NET SDK](search-import-data-dotnet.md)索引。 第二個選項是您的 Azure 搜尋索引[指向支援的資料來源](search-indexer-overview.md)，並讓 Azure 搜尋會自動將搜尋服務將您的資料。

本指南將只包含指示使用推入模型的資料上傳] （這受支援只有在[REST API](search-import-data-rest-api.md)和[.NET SDK](search-import-data-dotnet.md)），但您可以仍深入瞭解提取模式下。

### <a name="push-data-to-an-index"></a>要索引的推入資料

這種方法是指以程式設計方式，使其可供搜尋的 Azure 搜尋來傳送您的資料。 有極低延遲需求的應用程式 (例如︰ 如果您需要搜尋動態庫存資料庫同步作業)，推入模式是您唯一的選項。

您可以使用[REST API](https://msdn.microsoft.com/library/azure/dn798930.aspx)或[.NET SDK](search-import-data-dotnet.md)要索引的推入資料。 目前沒有工具支援的資料推送透過入口網站。

這種方法是提取模式比更有彈性，因為個別或批次，您可以上傳文件 （進位至每個批次或 16 MB 1000，不論限制何者先發生）。 推入模型也可讓您無論您資料的位置上載文件至 Azure 搜尋。

### <a name="pull-data-into-an-index"></a>在索引中擷取資料

擷取模型尋支援的資料來源，並自動將您 Azure 搜尋索引的資料上傳您。 追蹤變更及刪除現有的文件，除了辨識新文件索引子移除需要主動管理您的索引中的資料。

在 Azure 搜尋*索引子*，目前提供[Blob 儲存體 （預覽版本）](search-howto-indexing-azure-blob-storage.md)， [DocumentDB](http://aka.ms/documentdb-search-indexer)， [Azure SQL 資料庫及 SQL Server Azure Vm 上](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)透過實作這項功能。

重新功能是在[Azure 入口網站](search-import-data-portal.md)以及如[REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx)公開。
