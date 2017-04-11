<properties 
    pageTitle="將資料移動 Azure 電腦學習 Azure SQL 資料庫 |Azure" 
    description="建立 SQL 資料表和 SQL 資料表載入資料" 
    services="machine-learning" 
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

# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>將資料移動 Azure 電腦學習 Azure SQL 資料庫

本主題概述移動資料從一般檔案 （CSV 或 TSV 格式），或是從內部部署 SQL Server Azure SQL 資料庫中儲存的資料的選項。 將資料移至雲端的這些任務是小組資料科學程序的一部分。

主題會概述的電腦學習時，將資料移至內部部署的 SQL Server 的選項，請參閱[移動到 SQL Server Azure 虛擬機器上的資料](machine-learning-data-science-move-sql-server-virtual-machine.md)。

下列**功能表**連結至主題說明如何將內嵌的資料到目標環境可以儲存及處理期間小組資料科學程序 (TDSP) 資料。

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

下表摘要將資料移到 Azure SQL 資料庫的選項。

<b>來源</b> |<b>目標︰ Azure SQL 資料庫</b> |
-------------- |--------------------------------|
<b>一般檔案 （CSV 或 TSV 格式）</b> |<a href="#bulk-insert-sql-query">大量插入 SQL 查詢 |
<b>內部部署的 SQL Server</b> | 1.<a href="#export-flat-file">匯出到一般檔案<br> 2. <a href="#insert-tables-bcp">SQL 資料庫移轉精靈]<br> 3.<a href="#db-migration">資料庫後，設定與還原<br> 4. <a href="#adf">azure 資料工廠 |


## <a name="prereqs"></a>必要條件
此處所述的程序會要求您有︰

* **Azure 訂閱**。 如果您沒有訂閱，您可以註冊[免費試用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure 儲存體帳戶**。 您可以使用 Azure 儲存體帳戶將資料儲存在本教學課程。 如果您沒有安裝 Azure 儲存體帳戶，請參閱[建立儲存的帳戶](storage-create-storage-account.md#create-a-storage-account)。 您建立儲存帳戶後，您必須以取得帳戶金鑰] 用來存取儲存空間。 請參閱[檢視、 複製及重新產生的儲存空間便捷鍵](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)。
* **Azure SQL 資料庫**的存取。 如果您必須設定 Azure SQL 資料庫，[開始使用 Microsoft Azure SQL 資料庫](../sql-database/sql-database-get-started.md)提供如何佈建 Azure SQL 資料庫的新的執行個體的資訊。
* 安裝和設定**PowerShell 的 Azure**本機。 如需相關指示，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。

**資料**︰ 移轉程序會示範使用[NYC 計程車資料集](http://chriswhong.com/open-data/foil_nyc_taxi/)。 NYC 計程車資料集包含在出差資料和 fairs 上的資訊，就可以使用，如下所述的文章，Azure blob 儲存體︰ [NYC 計程車資料](http://www.andresmh.com/nyctaxitrips/)。 中[NYC 計程車往返資料集的描述](machine-learning-data-science-process-sql-walkthrough.md#dataset)所提供的範例和描述這些檔案。
 
您可以配合您自己的資料集此處所述的程序，或使用 NYC 計程車資料集的說明，請依照下列步驟。 若要將 NYC 計程車資料集上傳到您內部部署的 SQL Server 資料庫，請依照[大量匯入資料至 SQL Server 資料庫](machine-learning-data-science-process-sql-walkthrough.md#dbload)中的程序。 這些指示適用於 SQL Server 上 Azure 虛擬機器，但上傳至內部部署的 SQL Server 的程序相同。


## <a name="file-to-azure-sql-database"></a>將資料來源中的一般檔案移到 Azure SQL 資料庫

一般檔案 （CSV 或 TSV 格式化） 中的資料可以移至使用大量插入 SQL 查詢的 SQL Azure 資料庫。

### <a name="bulk-insert-sql-query"></a>大量插入 SQL 查詢

使用大量插入 SQL 查詢的程序的步驟很類似將資料來源中的一般檔案移至 SQL Server，Azure VM 上的各節所述。 如需詳細資訊，請參閱[大量插入 SQL 查詢](machine-learning-data-science-move-sql-server-virtual-machine.md#insert-tables-bulkquery)。


##<a name="sql-on-prem-to-sazure-sql-database"></a>移動資料從內部部署的 SQL Server Azure SQL 資料庫

如果來源資料儲存在內部部署的 SQL Server，有各種可能的方式將資料移至 Azure SQL 資料庫的︰

1. [匯出到一般檔案](#export-flat-file) 
2. [SQL 資料庫移轉精靈]](#insert-tables-bcp)
3. [設定資料庫後，與還原](#db-migration)
4. [Azure 資料工廠](#adf)

前三個步驟，兩者十分相似這些涵蓋這些相同的程序的章節中[SQL Server Azure 虛擬機器上移動資料](machine-learning-data-science-move-sql-server-virtual-machine.md)。 下列指示中會提供適當的章節，在主題中的連結。

###<a name="export-flat-file"></a>匯出到一般檔案

匯出到一般檔案的步驟會類似所涵蓋的[匯出到一般檔案](machine-learning-data-science-move-sql-server-virtual-machine.md#export-flat-file)。

###<a name="insert-tables-bcp"></a>SQL 資料庫移轉精靈]

使用 SQL 資料庫移轉精靈的步驟很類似涵蓋的[SQL 資料庫移轉精靈]](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-migration)。

###<a name="db-migration"></a>設定資料庫後，與還原

使用資料庫備份與還原的步驟很類似未包含在[備份及還原的資料庫](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-backup)。

###<a name="adf"></a>Azure 資料工廠

[將資料從內部部署 SQL server Azure 資料工廠與 SQL Azure 来移動](machine-learning-data-science-move-sql-azure-adf.md)的主題中提供將資料移到 Azure SQL 資料庫與 Azure 資料工廠 (ADF) 的程序。 本主題說明如何將資料從內部部署的 SQL Server 資料庫移至 Azure SQL 資料庫，透過 Azure Blob 儲存體使用 ADF。 

請考慮使用 ADF 資料需要存取內部部署和雲端資源的混合式情境中操作持續移轉和時的資料交易或需要修改，或已被移轉時，對其新增的商務邏輯。 ADF 允許排程和監視使用管理資料定期移動的簡易 JSON 指令碼的工作。 ADF 也有其他功能，例如支援複雜的作業。




