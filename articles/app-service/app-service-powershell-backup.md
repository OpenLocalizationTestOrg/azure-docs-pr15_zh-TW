<properties
    pageTitle="使用 PowerShell 來備份及還原應用程式服務應用程式"
    description="瞭解如何使用 PowerShell 來備份及還原 Azure 應用程式服務中的應用程式"
    services="app-service"
    documentationCenter=""
    authors="NKing92"
    manager="wpickett"
    editor="" />

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="nicking"/>
# <a name="use-powershell-to-back-up-and-restore-app-service-apps"></a>使用 PowerShell 來備份及還原應用程式服務應用程式

> [AZURE.SELECTOR]
- [PowerShell](app-service-powershell-backup.md)
- [REST API](../app-service-web/websites-csm-backup.md)

瞭解如何使用 PowerShell 的 Azure 備份及還原[應用程式服務應用程式](https://azure.microsoft.com/services/app-service/web/)。 如需 web 應用程式的備份，包括需求與限制，請參閱[備份 Azure 應用程式服務中的 web 應用程式](../app-service-web/web-sites-backup.md)。

## <a name="prerequisites"></a>必要條件
若要使用 PowerShell 來管理您的應用程式備份，您需要下列項目︰

- **SA URL**允許讀取與寫入存取權的 Azure 儲存體容器。 如需說明 SA Url，請參閱[瞭解 SA 模型](../storage/storage-dotnet-shared-access-signature-part-1.md)。 如需管理使用 PowerShell 的 Azure 儲存體的範例，請參閱[使用 Azure PowerShell 的 Azure 儲存體](../storage/storage-powershell-guide-full.md)。
- **A 資料庫連線字串**如果您想要備份的資料庫，以及您的 web 應用程式。

### <a name="how-to-generate-a-sas-url-to-use-with-the-web-app-backup-cmdlets"></a>如何產生 SA URL 的 web 應用程式備份指令程式搭配使用
使用 PowerShell，可能會產生 SA URL。 以下是如何產生一個可以使用本文所述的指令程式的範例。

        $storageAccountName = "<your storage account's name>"
        $storageAccountRg = "<your storage account's resource group>"

        # This returns an array of keys for your storage account. Be sure to select the appropriate key. Here we select the first key as a default.
        $storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccountRg -Name $storageAccountName
        $context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey[0].Value

        $blobContainerName = "<name of blob container for app backups>"
        $sasUrl = New-AzureStorageContainerSASToken -Name $blobContainerName -Permission rwdl -Context $context -ExpiryTime (Get-Date).AddMonths(1) -FullUri

## <a name="install-azure-powershell-132-or-greater"></a>安裝 PowerShell 的 Azure 1.3.2 大於或等於

如需安裝和使用 PowerShell 的 Azure 指示，請參閱[使用 PowerShell 的 Azure 與 Azure 資源管理員](../powershell-install-configure.md)。

## <a name="create-a-backup"></a>建立備份

您可以使用 [新增 AzureRmWebAppBackup cmdlet 來建立 web 應用程式的備份。

        $sasUrl = "<your SAS URL>"
        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"

        $backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl

這會自動產生的名稱以建立備份。 如果您想要提供您的備份的名稱，請使用 [BackupName 選擇性參數。

        $backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl -BackupName MyBackup

若要包含在備份的資料庫，第一次建立資料庫的備份設定，使用 [新增 AzureRmWebAppDatabaseBackupSetting 指令程式，然後提供資料庫參數新增 AzureRmWebAppBackup 指令程式的設定]。 資料庫參數接受陣列的資料庫設定，讓您以備份一個以上的資料庫。

        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbBackup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupName MyBackup -StorageAccountUrl $sasUrl -Databases $dbSetting1,$dbSetting2

## <a name="get-backups"></a>取得備份

取得 AzureRmWebAppBackupList 指令程式會傳回陣列的所有 web 應用程式的備份。 您必須提供的 web 應用程式和其資源群組的名稱。

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $backups = Get-AzureRmWebAppBackupList -Name $appName -ResourceGroupName $resourceGroupName

若要取得特定的備份，請使用取得 AzureRmWebAppBackup 指令程式。

        $backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102

您也可以在任何備份管理 cmdlet，以方便使用的管線 web 應用程式物件。

        $app = Get-AzureRmWebApp -Name ContosoApp -ResourceGroupName Default-Web-WestUS
        $backupList = $app | Get-AzureRmWebAppBackupList
        $backup = $app | Get-AzureRmWebAppBackup -BackupId 10102

## <a name="schedule-automatic-backups"></a>排程自動備份

您可以排程為指定的間隔自動發生的備份。 若要設定的備份排程，使用 [編輯 AzureRmWebAppBackupConfiguration 指令程式。 這個指令程式會使用多個參數︰

- **名稱**-web 應用程式的名稱。
- **ResourceGroupName** -包含 web 應用程式的資源群組的名稱。
- **位置**-選用。 Web 應用程式位置的名稱。
- **StorageAccountUrl** -用來儲存備份 Azure 存放容器 SA URL。
- **FrequencyInterval** -數字值應該所做的備份的頻率。 必須是正整數。
- **FrequencyUnit** -的頻率備份應該所做的時間單位。 選項為小時和天。
- **RetentionPeriodInDays** -幾天之後再刪除自動儲存自動備份。
- **開始時間**-選用。 自動備份何時應該開始時間。 如果這是空值會立即開始備份。 必須是日期時間。
- **資料庫**-選用。 備份資料庫的 DatabaseBackupSettings 陣列。
- **KeepAtLeastOneBackup** -選擇性切換參數。 提供此 if 備份應該一律放在儲存帳戶，無論多久。

以下是如何使用這個 cmdlet 的範例。

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $slotName = "StagingSlot"
        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        Edit-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName -Slot $slotName `
          -StorageAccountUrl "<your SAS URL>" -FrequencyInterval 6 -FrequencyUnit Hour -Databases $dbSetting1,$dbSetting2 `
          -KeepAtLeastOneBackup -StartTime (Get-Date).AddHours(1)

若要取得目前的備份排程，請使用取得 AzureRmWebAppBackupConfiguration 指令程式。 這可能是很適合用於修改已設定的排程。

        $configuration = Get-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName

        # Modify the configuration slightly
        $configuration.FrequencyInterval = 2
        $configuration.FrequencyUnit = "Day"

        # Apply the new configuration by piping it into the Edit-AzureRmWebAppBackupConfiguration cmdlet
        $configuration | Edit-AzureRmWebAppBackupConfiguration

## <a name="restore-a-web-app-from-a-backup"></a>從備份檔案還原 web 應用程式

若要從備份檔案還原 web 應用程式，使用還原 AzureRmWebAppBackup 指令程式。 若要使用這個 cmdlet 最簡單的方法是管道備份的物件中擷取取得 AzureRmWebAppBackup cmdlet 或取得 AzureRmWebAppBackupList 指令程式。

備份的物件之後，您可以將還原 AzureRmWebAppBackup 指令程式來進行管道。 指定覆寫切換參數，表示您要備份的內容，並覆寫 web 應用程式的內容。 如果備份包含資料庫，以及還原的資料庫。

        $backup | Restore-AzureRmWebAppBackup -Overwrite

以下是如何使用還原 AzureRmWebAppBackup 指定的所有參數的範例。

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $slotName = "StagingSlot"
        $blobName = "ContosoBackup.zip"
        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        Restore-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -Slot $slotName -StorageAccountUrl "<your SAS URL>" -BlobName $blobName -Databases $dbSetting1,$dbSetting2 -Overwrite

## <a name="delete-a-backup"></a>刪除備份

若要刪除備份，請使用移除 AzureRmWebAppBackup 指令程式。 這會將備份移除您儲存的帳戶。 指定您應用程式的名稱、 其 [資源] 群組中和您想要刪除的備份的識別碼。

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        Remove-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupId 10102

您也可以將移除 AzureRmWebAppBackup cmdlet，將其刪除管道備份的物件。

        $backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102
        $backup | Remove-AzureRmWebAppBackup -Overwrite
