<properties
   pageTitle="Azure SQL Data Warehouse 的 PowerShell cmdlet"
   description="Azure SQL Data Warehouse 包括來暫停或繼續資料庫的方法，尋找上方的 PowerShell 指令程式。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess;mausher"/>

# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>PowerShell cmdlet 和 SQL Data Warehouse 的 REST Api

可以使用 PowerShell 的 Azure cmdlet 或 REST Api 來管理許多 SQL Data Warehouse 管理工作。  以下是如何使用 PowerShell 命令來自動化您 SQL Data Warehouse 中的一般工作的一些範例。  一些建議其他範例，請參閱[管理延展性與其他][]的文件。

> [AZURE.NOTE]  使用 PowerShell 的 Azure 與 SQL Data Warehouse，您需要 PowerShell 的 Azure 版本 1.0.3 或更大。  您可以執行，以檢查您的版本**取得模組 ListAvailable-名稱 Azure**。  從[Microsoft 網站的平台安裝程式][]，可以安裝最新版本。  如需有關如何安裝最新版本的詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure][]。

## <a name="get-started-with-azure-powershell-cmdlets"></a>開始使用 Azure PowerShell cmdlet

1. 開啟 Windows PowerShell。 
2. 在 PowerShell 提示中，執行下列命令，登入至 Azure 資源管理員，並選取您的訂閱。

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>暫停 SQL 資料倉庫範例

將游標暫停於名為 「 Database02 」 名為 「 Server01。 」 的伺服器上的資料庫  伺服器是在名為 「 ResourceGroup1。 「 Azure 資源群組 

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
一種變化，此範例中管道[暫停 AzureRmSqlDatabase][]擷取的物件。  如此一來，資料庫已暫停。 最後一個命令顯示的結果。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>啟動 SQL 資料倉庫範例

繼續作業資料庫的名稱為 「 Database02 」 的伺服器名稱為 「 Server01 」。 伺服器都包含在名為 「 ResourceGroup1。 」 資源群組

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

一種變化，此範例中擷取名為 「 Database02 」，從名為 「 Server01 」 中名為 「 ResourceGroup1。 」 的資源群組所包含的伺服器的資料庫 它管道[履歷表 AzureRmSqlDatabase][]擷取的物件。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [AZURE.NOTE] 請注意，如果您的伺服器是 foo.database.windows.net，「 foo 」 作為-伺服器名稱中的 PowerShell 指令程式。

## <a name="frequently-used-powershell-cmdlets"></a>經常使用 PowerShell cmdlet

使用 Azure SQL Data Warehouse 經常使用下列 PowerShell 指令程式。

- [取得 AzureRmSqlDatabase][]
- [取得 AzureRmSqlDeletedDatabaseBackup][]
- [取得 AzureRmSqlDatabaseRestorePoints][]
- [新 AzureRmSqlDatabase][]
- [移除 AzureRmSqlDatabase][]
- [還原 AzureRmSqlDatabase][] 
- [履歷表 AzureRmSqlDatabase][]
- [選取 AzureRmSubscription][]
- [設定 AzureRmSqlDatabase][]
- [暫停 AzureRmSqlDatabase][]

## <a name="next-steps"></a>後續步驟
如需 PowerShell 範例，請參閱︰

- [建立使用 PowerShell SQL Data Warehouse][]
- [還原資料庫][]

所有的工作，可以使用 PowerShell 來自動化哪些的清單，請參閱[Azure SQL 資料庫 Cmdlet][]。  這可以與其他自動化工作的清單，請參閱[Azure SQL 資料庫的作業][]。

<!--Image references-->

<!--Article references-->
[如何安裝和設定 PowerShell 的 Azure]: ./powershell-install-configure.md
[建立使用 PowerShell SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision-powershell.md
[還原資料庫]: ./sql-data-warehouse-restore-database-powershell.md
[管理與其他延展性]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL 資料庫 Cmdlet]: https://msdn.microsoft.com/library/mt574084.aspx
[Azure SQL 資料庫的作業]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[取得 AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[取得 AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[取得 AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[新 AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[移除 AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[還原 AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[履歷表 AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[選取 AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[設定 AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[暫停 AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx

<!--Other Web references-->
[Microsoft Web 平台安裝程式]: https://aka.ms/webpi-azps
