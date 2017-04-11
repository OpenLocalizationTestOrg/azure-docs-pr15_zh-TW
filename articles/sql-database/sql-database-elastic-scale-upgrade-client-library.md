< 屬性
    
    pageTitle="Upgrade to the latest elastic database client library | Microsoft Azure" 
    description="Upgrade apps and libraries using Nuget" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>升級 [應用程式，使用最新的彈性的資料庫用戶端文件庫

[彈性的資料庫用戶端文件庫](sql-database-elastic-database-client-library.md)的最新版本，可透過 NuGetand NuGetPackage 管理員介面 Visual Studio 中。 升級包含修正錯誤，而且支援的用戶端文件庫的新功能。

**最新版本︰**移至[Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)。

重建應用程式的新文件庫，以及變更現有晶怪地圖管理員中繼資料儲存在支援的新功能 Azure SQL 資料庫。

順序執行這些步驟可確保舊版本的用戶端文件庫不會再出現在您的環境中何時更新中繼資料的物件，這表示舊版本的中繼資料的物件就不會建立升級之後。   

## <a name="upgrade-steps"></a>升級的步驟

**1.升級您的應用程式。** 在 Visual Studio 中，請下載並將所有使用文件庫; 開發專案的參照的最新的用戶端文件庫版本然後重建及部署。 

 * 在 Visual Studio 解決方案中，選取 [**工具** --> **NuGet 封裝管理員** -->  **管理 NuGet 套件的解決方案**。 
 * (Visual Studio 2013)在左側面板中，選取**更新**]，然後選取套件**Azure SQL 資料庫彈性色階用戶端文件庫**會出現在視窗上的 [**更新**] 按鈕。
 * (Visual Studio 2015)設定升級**可用**的 [篩選] 方塊。 選取的套件更新，然後按一下 [**更新**] 按鈕。
    
 
 * 建立並部署。 

**2.升級您的指令碼。** 如果您使用**PowerShell**指令碼管理擊碎，[下載新文件庫的版本](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)，並將它複製到您用來執行指令碼的目錄。 

**3.升級您分割合併的服務。** 如果您使用彈性的資料庫分割合併工具重新組織 sharded[下載和部署工具的最新版本](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)的資料。 您可以找到服務的詳細說明升級步驟[以下](sql-database-elastic-scale-overview-split-and-merge.md)。 

**4。 升級晶怪地圖管理員資料庫**。 升級支援您晶怪地圖 Azure SQL 資料庫中的中繼資料。  有兩種方法可以完成的首要，使用 PowerShell 或 C#。 這兩個選項如下所示。

***使用 PowerShell 選項 1︰ 升級中繼資料***

1. 從[這裡](http://nuget.org/nuget.exe)下載最新的命令列公用適用於 NuGet 並儲存到資料夾。 

2. 開啟命令提示字元、 瀏覽至相同的資料夾，然後執行命令︰`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`

3. 瀏覽至包含新用戶端 DLL 版本，您只要下載，例如子資料夾︰`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`

4. 下載彈性的資料庫的用戶端升級 scriptlet[指令碼中心 」](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)，並將其儲存到包含 DLL 的相同資料夾。

5. 從該資料夾中，執行 「 PowerShell.\upgrade.ps1 「 命令提示字元中，然後依照提示操作。
 
***選項 2︰ 升級使用 C# 的中繼資料***

或者，建立一個 Visual Studio 應用程式，便會開啟您 ShardMapManager、 逐一查看所有擊碎，並執行的中繼資料升級，則可電話方法[UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx)和[UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx)範例如下︰ 

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy); 
    smm.UpgradeGlobalStore(); 
    
    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations()) 
    {   
       smm.UpgradeLocalStore(loc); 
    } 

中繼資料的升級技巧可以多次套用不傷害。 例如，如果您已經更新之後，較舊的用戶端版本不小心建立晶怪，您可以升級再次執行過所有擊碎，以確保最新版的中繼資料是整個基礎結構的簡報。 

**附註︰** 新版本的用戶端文件庫的發佈至日期繼續使用晶怪地圖管理員中繼資料的先前版本上 Azure SQL 資料庫，反之亦然。   不過，若要最新的用戶端的一些新功能的優點，中繼資料需要升級。   請注意的中繼資料升級不會影響所有使用者資料或特定應用程式的資料，只物件晶怪地圖管理員所建立和使用。  繼續操作上述的升級順序透過應用程式。 

## <a name="elastic-database-client-version-history"></a>彈性的資料庫用戶端版本歷程記錄 

版本歷程記錄，請移至[Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]  


<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
 