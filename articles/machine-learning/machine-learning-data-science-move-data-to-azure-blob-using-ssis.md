<properties
    pageTitle="將資料移至] 或從 Azure Blob 儲存體使用 SSIS 連接器 |Microsoft Azure"
    description="將資料移至] 或從使用 SSIS 連接器 Azure Blob 儲存體。"
    services="machine-learning,storage"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev" />

# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>將資料移至] 或從使用 SSIS 連接器 Azure Blob 儲存體

[SQL Server 整合服務 Feature Pack 的 Azure](https://msdn.microsoft.com/library/mt146770.aspx)提供元件連線至 Azure 傳輸 Azure 及內部部署資料來源與儲存在 Azure 中的程序資料之間的資料。

用來將資料移到及/或從 Azure Blob 儲存體技術指南以下連結︰

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


客戶有移動至雲端的內部部署資料，他們可以存取任何 Azure 服務運用一套 Azure 技巧的完整功能。 它可能會用於，例如，在 Azure 電腦學習或 HDInsight 叢集。

這通常是第一步是[SQL](machine-learning-data-science-process-sql-walkthrough.md)和[HDInsight](machine-learning-data-science-process-hive-walkthrough.md)逐步解說。

使用 SSIS 完成混合式資料整合案例中常見的業務需求的標準案例的討論，請參閱[使用 SQL Server 整合服務 Feature Pack 的 Azure 更多的實際操作](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx)部落格。

> [AZURE.NOTE] Azure blob 儲存體完成簡介，請參閱[Azure Blob 的基本概念](../storage/storage-dotnet-how-to-use-blobs.md)，並[Azure Blob 服務](https://msdn.microsoft.com/library/azure/dd179376.aspx)。

## <a name="prerequisites"></a>必要條件

若要執行本文所述的工作，您必須 Azure 的訂閱和 Azure 儲存體帳戶設定。 您必須知道您 Azure 儲存體帳戶名稱和帳戶金鑰上傳或下載的資料。

- 若要設定**Azure 訂閱**，請參閱[一個月免費的試用版](https://azure.microsoft.com/pricing/free-trial/)。

- 如需建立**儲存帳戶**的指示及取得帳戶和重要資訊，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)。


若要使用**SSIS 連接器**，您必須下載︰

- **SQL Server 2014 或 2016年標準 （或上方）**︰ 安裝包含 SQL Server 整合服務。
- **Microsoft SQL Server 2014 或 2016年整合服務 Feature Pack 的 Azure**︰ 這些可以下載，分別從[SQL Server 2014 整合服務](http://www.microsoft.com/download/details.aspx?id=47366)] 和 [ [SQL Server 2016 整合服務](https://www.microsoft.com/download/details.aspx?id=49492)的頁面。

> [AZURE.NOTE] SSIS SQL Server，與安裝，但不是會包含在 Express 版本。 在不同版本的 SQL Server 中包含哪些應用程式的資訊，請參閱[SQL Server 版本](http://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)

SSIS 上的訓練課程教材，請參閱[手上 ssis 訓練](http://www.microsoft.com/download/details.aspx?id=20766)

如何取得上執行的資訊，請建立簡單擷取、 轉換以及載入 (ETL) 套件，請參閱使用 SISS [SSIS 教學課程︰ 建立簡單的 ETL 封裝](https://msdn.microsoft.com/library/ms169917.aspx)。

## <a name="download-nyc-taxi-dataset"></a>下載 NYC 計程車資料集  
以下所述的範例會使用可公開使用資料集- [NYC 計程車往返](http://www.andresmh.com/nyctaxitrips/)資料集。 關於 173 百萬計程車其 NYC 在 2013 年包含資料集。 有兩種類型的資料︰ 出差詳細資料及 fare 資料。 為每個月有檔案，我們會有 24 中所有每一種是大約解壓縮的 2 GB 的檔案。


## <a name="upload-data-to-azure-blob-storage"></a>上傳至 Azure blob 儲存體的資料
若要移動使用 SSIS 資料功能至 Azure blob 儲存體來自內部部署套件，我們使用[**Azure Blob 上傳的工作**](https://msdn.microsoft.com/library/mt146776.aspx)，這裡所顯示的執行個體︰

![設定-資料-科學-vm](./media/machine-learning-data-science-move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)


此處所述的工作使用參數︰


功能變數|描述|
----------------------|----------------|
**AzureStorageConnection**|指定現有的 Azure 儲存體連接管理員，或建立新的參照指向裝載 blob 檔案 Azure 儲存體帳戶。|
**BlobContainer**|指定 blob 容器儲存為二進位大型物件的上傳的檔案的名稱。|
**BlobDirectory**|指定 blob 目錄上傳的檔案儲存為區塊 blob 的位置。 Blob 目錄是虛擬的階層式結構。 如果 blob 已經存在，it ia 取代。|
**LocalDirectory**|指定包含要上傳的檔案的本機目錄。|
**檔案名稱**|指定名稱篩選，以選取檔案，以指定的名稱的模式。 例如，MySheet\*.xls\*包括 MySheet001.xls 等 MySheetABC.xlsx 的檔案|
**TimeRangeFrom/TimeRangeTo**|指定時間範圍] 篩選。 檔案後*TimeRangeFrom*修改和*TimeRangeTo*之前都包含在內。|


> [AZURE.NOTE] **AzureStorageConnection**認證必須正確無誤，然後嘗試傳輸之前，必須存在於**BlobContainer** 。

## <a name="download-data-from-azure-blob-storage"></a>從 Azure blob 儲存體下載資料

若要從 Azure blob 儲存體至內部部署與 SSIS 儲存下載的資料，請使用[Azure Blob 上傳工作](https://msdn.microsoft.com/library/mt146779.aspx)的執行個體。

##<a name="more-advanced-ssis-azure-scenarios"></a>更多進階的 SSIS Azure 案例
我們請注意以下功能 SSIS 套件，讓更複雜共同處理包裝工作流程。 例如，blob 資料可能摘要直接將 HDInsight 叢集，其輸出無法下載，回到 blob 然後內部部署的儲存空間。 SSIS 可以使用其他 SSIS 連接器 HDInsight 叢集上執行登錄區與豬工作︰

- 若要使用 SSIS Azure HDInsight 叢集上執行的登錄區指令碼，請使用[Azure HDInsight 登錄區工作](https://msdn.microsoft.com/library/mt146771.aspx)。
- 若要使用 SSIS Azure HDInsight 叢集上執行的豬指令碼，請使用[Azure HDInsight 的豬工作](https://msdn.microsoft.com/library/mt146781.aspx)。
