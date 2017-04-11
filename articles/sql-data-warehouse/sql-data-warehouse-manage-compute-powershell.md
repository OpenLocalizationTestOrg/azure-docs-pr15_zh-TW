<properties
   pageTitle="管理 Azure SQL Data Warehouse (PowerShell) 中的計算 power |Microsoft Azure"
   description="若要管理的 PowerShell 工作計算 power。 縮放比例調整 DWUs 計算資源。 或者，將游標暫停於，然後繼續儲存成本的計算資源。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/13/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-powershell"></a>管理運算中 Azure SQL Data Warehouse (PowerShell) 的能力

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-manage-compute-overview.md)
- [入口網站](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [其餘](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


藉由出縮放比例效能計算資源和記憶體以符合您的工作量的變更要求。 儲存成本縮放比例返回資源峰時間或暫停計算期間完全。 

此工作的集合使用 Azure 入口網站︰

- 小數位數計算
- 暫停運算
- 履歷表運算

若要瞭解此問題，請參閱[管理計算概觀][]。


## <a name="before-you-begin"></a>開始之前

### <a name="install-the-latest-version-of-azure-powershell"></a>安裝最新版的 PowerShell 的 Azure

> [AZURE.NOTE]  若要使用 PowerShell 的 Azure SQL Data Warehouse，您需要 PowerShell 的 Azure 版本 1.0.3 或更大。  若要確認您目前的版本執行命令**取得模組 ListAvailable-名稱 Azure**。 您可以從[Microsoft 網站的平台安裝程式][]安裝最新版本。  如需詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure][]。

### <a name="get-started-with-azure-powershell-cmdlets"></a>開始使用 Azure PowerShell cmdlet

若要開始使用︰

1. 開啟 Azure PowerShell。 
2. 在 PowerShell 提示中，執行下列命令，登入至 Azure 資源管理員，並選取您的訂閱。

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>縮放比例運算能力

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

若要變更 DWUs，請使用[設定 AzureRmSqlDatabase][] PowerShell 指令程式。 以下範例將 DW1000 的服務等級目標設定資料庫是裝載在伺服器 MyServer MySQLDW。 

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>暫停運算

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

若要暫停資料庫，請使用[暫停 AzureRmSqlDatabase][]指令程式。 下列範例會暫停名為 Database02 名為 Server01 的伺服器上的資料庫。 伺服器是在名為 ResourceGroup1 Azure 資源群組。 

> [AZURE.NOTE] 請注意，如果您的伺服器是 foo.database.windows.net，「 foo 」 作為-伺服器名稱中的 PowerShell 指令程式。

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
一種變化，此範例中下一個擷取 $database 物件的資料庫。 然後，它會管道[暫停 AzureRmSqlDatabase][]物件。 結果會儲存在物件 resultDatabase。 最後一個命令顯示的結果。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>履歷表運算

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

若要開始資料庫，請使用[履歷表 AzureRmSqlDatabase][]指令程式。 下列範例會啟動名為 Database02 名為 Server01 的伺服器上的資料庫。 伺服器是在名為 ResourceGroup1 Azure 資源群組。 

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

一種變化，此範例中下一個擷取 $database 物件的資料庫。 接著管道[履歷表 AzureRmSqlDatabase][]物件，然後將結果儲存在 $resultDatabase。 最後一個命令顯示的結果。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>後續步驟

其他管理工作，請參閱[管理概觀][]。

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[管理概觀]: ./sql-data-warehouse-overview-manage.md
[如何安裝和設定 PowerShell 的 Azure]: ./powershell-install-configure.md
[管理計算概觀]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[履歷表 AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[暫停 AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[設定 AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx

<!--Other Web references-->
[Microsoft Web 平台安裝程式]: https://aka.ms/webpi-azps
[Azure portal]: http://portal.azure.com/
