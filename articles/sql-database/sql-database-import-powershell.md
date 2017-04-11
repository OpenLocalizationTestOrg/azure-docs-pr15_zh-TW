<properties
    pageTitle="匯入 BACPAC 檔案建立使用 PowerShell 的 Azure SQL 資料庫 |Microsoft Azure"
    description="匯入 BACPAC 檔案建立使用 PowerShell 的 Azure SQL 資料庫"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="08/31/2016"
    ms.author="sstein"/>

# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-by-using-powershell"></a>匯入 BACPAC 檔案建立使用 PowerShell 的 Azure SQL 資料庫

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

本文提供建立匯入[BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)檔案使用 PowerShell 的 Azure SQL 資料庫的說明。

從 BACPAC 檔案 (.bacpac) 匯入 Azure 儲存體 blob 容器建立資料庫。 如果您沒有安裝 Azure 儲存體中的 BACPAC 檔案，請參閱[封存 BACPAC 檔案使用 PowerShell 的 Azure SQL 資料庫](sql-database-export-powershell.md)。 如果您已經有 BACPAC 檔案的不是 Azure 儲存體，[使用 AzCopy 輕鬆以上傳至您的 Azure 儲存體帳戶](../storage/storage-use-azcopy.md#blob-upload)。

> [AZURE.NOTE] Azure SQL 資料庫會自動建立，及維護您可以還原的每個使用者資料庫的備份。 如需詳細資訊，請參閱[SQL 資料庫自動備份](sql-database-automated-backups.md)。


若要匯入 SQL 資料庫，您需要下列項目︰

- Azure 的訂閱。 如果您需要的 Azure 訂閱，只要按一下 [**免費試用版**頂端的 [此頁面中，，然後回到完成這份文件。
- 您想要匯入資料庫 BACPAC 檔案。 BACPAC 必須[Azure 儲存體帳戶](../storage/storage-create-storage-account.md)blob 容器中。



[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]



## <a name="set-up-the-variables-for-your-environment"></a>設定您的環境變數

有幾個變數要取代您的資料庫及您儲存的帳戶的特定值中的範例值。

伺服器名稱應該在上一個步驟中選取的訂閱中目前存在的伺服器。 您應該要建立在資料庫的伺服器。 不支援匯入資料庫，直接將彈性的資料庫。 但您可以先匯入到單一資料庫，並將資料庫移至資料庫。

資料庫名稱是您要為新資料庫的名稱。

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


下列變數會從儲存的帳戶是您 BACPAC 的所在位置。 在[Azure 入口網站](https://portal.azure.com)中，瀏覽至您要取得這些值的儲存空間帳戶。 您可以從您的儲存空間帳戶刀按一下**所有設定**]，然後按一下 [**索引鍵**來尋找主要便捷鍵。

Blob 名稱是您想要建立資料庫的現有 BACPAC 檔案的名稱。 您必須包含.bacpac 副檔名。

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


執行 [取得認證] （https://msdn.microsoft.com/library/azure/hh849815(v=azure.300\).aspx) 指令程式開啟的視窗要求您的使用者名稱和密碼。 輸入 SQL 資料庫伺服器 ($ServerName 從上方)，以及不使用者名稱和 Azure 帳戶密碼管理員登入和密碼。

    $credential = Get-Credential


## <a name="import-the-database"></a>匯入的資料庫

這個命令送出服務匯入資料庫要求。 根據您的資料庫的大小，匯入作業可能需要一些時間才能完成。

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000


## <a name="monitor-the-progress-of-the-operation"></a>監控作業的進度

執行 [新增 AzureRmSqlDatabaseImport] 之後 （https://msdn.microsoft.com/library/azure/mt707793(v=azure.300\).aspx)，您可以要求的狀態執行檢查 [取得-AzureRmSqlDatabaseImportExportStatus] (https://msdn.microsoft.com/library/azure/mt707794(v=azure.300\).aspx)。

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="sql-database-powershell-import-script"></a>SQL 資料庫 PowerShell 匯入指令碼


    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="next-steps"></a>後續步驟

- 若要瞭解的查詢匯入的 SQL 資料庫連線到，請參閱[連線至 SQL Server Management Studio 與 SQL 資料庫並執行範例 T SQL 查詢](sql-database-connect-query-ssms.md)
