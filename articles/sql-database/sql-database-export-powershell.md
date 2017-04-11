<properties
    pageTitle="封存 BACPAC 檔案使用 PowerShell 的 Azure SQL 資料庫"
    description="封存 BACPAC 檔案使用 PowerShell 的 Azure SQL 資料庫"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/15/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="archive-an-azure-sql-database-to-a-bacpac-file-by-using-powershell"></a>封存 BACPAC 檔案使用 PowerShell 的 Azure SQL 資料庫

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


本文所提供的指示，封存 Azure SQL 資料庫[BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)檔案 （儲存在 Azure Blob 儲存體） 使用 PowerShell。

當您需要建立 Azure SQL 資料庫的封存時，您可以將資料庫結構描述和資料匯出至 BACPAC 檔案。 只要 ZIP 檔案副檔名為.bacpac BACPAC 檔案。 可以稍後再儲存 BACPAC 檔案，Azure Blob 儲存體中或本機存放在內部部署的位置。 它可以也可以匯入至 Azure SQL 資料庫或 SQL Server 安裝內部部署。

**考量**

- 如要交易一致封存，您必須確定沒有寫入活動發生期間匯出]，或您要匯出從 Azure SQL 資料庫的[交易一致的複本](sql-database-copy.md)。
- 封存至 Azure Blob 儲存體 BACPAC 檔案的大小上限為 200 GB。 若要封存較大的 BACPAC 檔案本機存放區，請使用[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx)命令提示字元公用程式。 此公用程式隨附 Visual Studio 和 SQL Server。 您也可以[下載](https://msdn.microsoft.com/library/mt204009.aspx)最新版本的 SQL Server Data Tools，以取得此公用程式。
- 不支援使用 BACPAC 檔案封存到進階版 Azure 儲存體。
- 如果匯出作業超過 20 小時，則可能會取消。 若要增加匯出期間的效能，您可以︰
 - 暫時增加您的服務等級。
 - 停止所有讀取和寫入匯出期間的活動。
 - 使用所有大型資料表上的非 null 值[叢集的索引](https://msdn.microsoft.com/library/ms190457.aspx)。 沒有叢集索引，如果花更長的時間超過 6-12 小時，仍然無法匯出。 這是因為需要完成，嘗試將整個表格匯出資料表掃描匯出服務。 決定是否表格適用於匯出的好方法是執行**DBCC SHOW_STATISTICS** ，並確認*RANGE_HI_KEY*不是空值，且其值具有良好的通訊群組。 如需詳細資訊，請參閱[DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)。

> [AZURE.NOTE] BACPACs 不打算使用備份與還原作業。 Azure SQL 資料庫會自動建立的每個使用者資料庫的備份。 如需詳細資訊，請參閱[SQL 資料庫自動備份](sql-database-automated-backups.md)。

若要完成此文件，您需要下列項目︰

- Azure 的訂閱。
- Azure SQL 資料庫。
- [Azure 標準儲存體帳戶](../storage/storage-create-storage-account.md)，請使用 blob 儲存 BACPAC 標準存放容器。


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]




## <a name="export-your-database"></a>匯出您的資料庫

[新增-AzureRmSqlDatabaseExport] （https://msdn.microsoft.com/library/azure/mt707796(v=azure.300\).aspx) cmdlet 送出服務匯出資料庫要求。 根據您的資料庫的大小，匯出作業可能需要一些時間才能完成。

> [AZURE.IMPORTANT] 若要確保交易一致的 BACPAC 檔案，應該第一個[建立資料庫的複本](sql-database-copy-powershell.md)，，然後匯出資料庫複本。


     $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password


## <a name="monitor-the-progress-of-the-export-operation"></a>監控匯出作業的進度

執行 [新增 AzureRmSqlDatabaseExport] 之後 （https://msdn.microsoft.com/library/azure/mt603644(v=azure.300\).aspx)，您可以要求的狀態執行檢查 [取得-AzureRmSqlDatabaseImportExportStatus] (https://msdn.microsoft.com/library/azure/mt707794(v=azure.300\).aspx)。 執行此要求之後，立即通常會傳回**狀態︰ InProgress**。 當您看到**狀態︰ 成功**即完成匯出。


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="export-sql-database-example"></a>匯出 SQL 資料庫範例

下列範例會將現有的 SQL 資料庫匯出 BACPAC，並顯示如何檢查匯出作業的狀態。

若要執行的範例，有幾個變數，您需要您的資料庫，儲存空間帳戶特定的值取代。 在[Azure 入口網站](https://portal.azure.com)中，瀏覽至您儲存的帳戶取得儲存體帳戶名稱、 blob 容器名稱及索引鍵的值。 您可以找到索引鍵，即可在您儲存帳戶刀**便捷鍵**。

取代下列`VARIABLE-VALUES`特定 Azure 資源的值。 資料庫名稱是您想要匯出的現有資料庫。



    $subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Database to export
    $DatabaseName = "DATABASE-NAME"
    $ResourceGroupName = "RESOURCE-GROUP-NAME"
    $ServerName = "SERVER-NAME"
    $serverAdmin = "ADMIN-NAME"
    $serverPassword = "ADMIN-PASSWORD" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

    # Generate a unique filename for the BACPAC
    $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

    # Storage account info for the BACPAC
    $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
    $BacpacUri = $BaseStorageUri + $bacpacFilename
    $StorageKeytype = "StorageAccessKey"
    $StorageKey = "YOUR STORAGE KEY"

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password
    $exportRequest

    # Check status of the export
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="next-steps"></a>後續步驟

- 若要瞭解如何匯入使用 Powershell 的 Azure SQL 資料庫，請參閱[匯入使用 PowerShell BACPAC](sql-database-import-powershell.md)。


## <a name="additional-resources"></a>其他資源

- [新 AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt707796(v=azure.300\).aspx)
- [取得 AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794(v=azure.300\).aspx)