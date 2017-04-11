<properties
    pageTitle="SQL 資料庫服務的所有主題 |Microsoft Azure"
    description="Azure 服務的所有主題的資料表命名為存在於 http://azure.microsoft.com/documentation/articles/、 標題和描述的 SQL 資料庫。"
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="genemi"/>


# <a name="all-topics-for-azure-sql-database-service"></a>Azure SQL 資料庫服務的所有主題

本主題列出每個會直接套用至 Azure **SQL 資料庫**服務的主題。 您可以使用**Ctrl + F**，尋找目前感興趣的主題中搜尋關鍵字此網頁。




## <a name="new"></a>新

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 1 | [Daxko/CSI 用於 Azure，加速開發週期及增強其客戶服務及效能](sql-database-implementation-daxko.md) | 瞭解如何 Daxko/CSI 使用 SQL 資料庫加速開發週期和增強效能及其客戶服務 |
| 2 | [Azure 提供 GEP 到和效率](sql-database-implementation-gep.md) | 深入了解 GEP 如何連絡其他全域的客戶，並獲得更有效率使用 SQL 資料庫 |
| 3 | [使用 Azure SnelStart 具有快速展開速率為每個月 1000 個新 Azure SQL 資料庫 business 服務](sql-database-implementation-snelstart.md) | 深入了解 SnelStart 快速展開速率為每個月 1000 個新 Azure SQL 資料庫 business 服務所使用的 SQL 資料庫 |
| 4 | [Umbraco 使用快速佈建和小數位數的服務千分位在雲端的租用戶的 Azure SQL 資料庫](sql-database-implementation-umbraco.md) | 進一步瞭解 Umbraco 如何使用 SQL 資料庫快速佈建和縮放比例數以千計的租用戶在雲端服務 |
| 5 | [管理使用 PowerShell 的 Azure SQL 資料庫](sql-database-manage-powershell.md) | 使用 PowerShell 的 azure SQL 資料庫管理。 |
| 6 | [Azure AD MFA SQL 資料庫與 SQL Data Warehouse SSMS 支援](sql-database-ssms-mfa-authentication.md) | SQL 資料庫及 SQL Data Warehouse 使用 SSMS 多重層層防護驗證。 |
| 7 | [說明資料庫交易單位 (DTUs) 和彈性的資料庫交易單位 (eDTUs)](sql-database-what-is-a-dtu.md) | 了解哪些 Azure SQL 資料庫交易單位是。 |


## <a name="updated-articles-sql-database"></a>更新文件時，SQL 資料庫

此區段列出文件已更新，更新已大或嚴重的位置。 每個更新的文件，會顯示粗略新增的 markdown 文字的程式碼片段。 文件已更新至**2016年-10-05** **2016年-08 22**的日期範圍內。

| &nbsp; | 文件 | 更新的文字、 程式碼片段 | 更新的時機 |
| --: | :-- | :-- | :-- |
| 8 | [管理使用 PowerShell 的 Azure SQL 資料庫](sql-database-command-line-tools.md) | 建立資源群組我們 SQL 資料庫] 及 [新增 AzureRmResourceGroup (https://msdn.microsoft.com/library/azure/mt759837.aspx) 指令程式的相關 Azure 資源。 *$resourceGroupName = 「 resourcegroup1 」 $resourceGroupLocation = 「 northcentralus 」 新增 AzureRmResourceGroup-命名 $resourceGroupName-位置 $resourceGroupLocation*如需詳細資訊，請參閱使用 PowerShell 的 Azure 與 Azure 資源管理員 (.../ powershell azure-資源 manager.md）。 範例指令碼，請參閱建立 SQL 資料庫 PowerShell 指令碼 (sql-資料庫-開始-開始-powershell.md create-a-sql-database-powershell-script)。 **建立 SQL 資料庫伺服器**建立新增 AzureRmSqlServer (https://msdn.microsoft.com/library/azure/mt603715.aspx) cmdlet SQL 資料庫伺服器。 *Server1*換成您的伺服器的名稱。 在所有 Azure SQL 資料庫伺服器，伺服器名稱必須是唯一的。 如果已經存在的伺服器名稱，您會看到的錯誤。 這個命令，可能需要幾分鐘才能完成。 Resou | 2016-09-14 |
| 9 | [複製使用 PowerShell 的 Azure SQL 資料庫](sql-database-copy-powershell.md) |   SQL 資料庫來源 （現有的資料庫複製）-$sourceDbName = 」 db1 」 $sourceDbServerName = 」 server1 」 $sourceDbResourceGroupName = 「 rg1 」 SQL 資料庫複本 （若要建立新的資料庫），$copyDbName = 「 db1_copy 」 $copyDbServerName = 」 server2 」 $copyDbResourceGroupName = 「 rg2 」 複製到相同的伺服器-新增 AzureRmSqlDatabaseCopy ResourceGroupName $sourceDbResourceGroupName 資料庫-伺服器名稱 $sourceDbServerName-資料庫 $sourceDbName-CopyDatabaseName $copyDbName 資料庫複製到不同的伺服器--新增 AzureRmSqlDatabaseCopy ResourceGroupName $sourceDbResourceGroupName-伺服器名稱 $sourceDbServerName-資料庫 $sourceDbName-CopyResourceGroupName $copyDbResourceGroupName-CopyServerName $copyDbServerName-CopyDatabaseName $copyDbName 將資料庫複製到彈性的資料庫集區-$poolName = 「 pool1 」 新增 AzureRmSqlDatabaseCopy ResourceGroupName $sourceDbResourceGroupName-伺服器名稱 $sourceDbServerName-資料庫 $sourceDbName-CopyReso | 2016-09-08 |
| 10 | [C# 建立彈性的資料庫集區](sql-database-elastic-pool-create-csharp.md) |   Console.WriteLine （「 伺服器防火牆...」）。 FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule （_sqlMgmtClient _resourceGroupName、 _serverName、 _firewallRuleName、 _startIpAddress、 _endIpAddress）; Console.WriteLine (「 伺服器防火牆: 「 + fwr。FirewallRule.Id); Console.WriteLine （「 彈性集區...」）。 ElasticPoolCreateOrUpdateResponse epr = CreateOrUpdateElasticDatabasePool （_sqlMgmtClient _resourceGroupName、 _serverName、 _poolName、 _poolEdition、 _poolDtus、 _databaseMinDtus、 _databaseMaxDtus）; Console.WriteLine (「 彈性的資料庫: 「 + epr。ElasticPool.Id); Console.WriteLine("Database..."); DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase （_sqlMgmtClient _resourceGroupName、 _serverName、 _databaseName、 _poolName）; Console.WriteLine (「 資料庫: 「 + dbr。Database.Id); Console.WriteLine （「 按任何鍵，繼續...」）; Console.ReadKey(); } 靜態 ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupNa | 2016-09-14 |
| 11 | [識別資料庫適合彈性的資料庫資料庫的 PowerShell 指令碼](sql-database-elastic-pool-database-assessment-powershell.md) | **的彈性的資料庫集區候選項目，我們排除母片]，並已在資料庫中的任何資料庫。您可以新增多個資料庫來排除的清單視**$ListOfDBs = 取得 AzureRmSqlDatabase 伺服器名稱 $servername。Split('.') 0 ResourceGroupName $ResourceGroupName / 位置物件 {$_。資料庫名稱 notin （「 主要 」）-和 $_。CurrentServiceLevelObjectiveName notin (「 ElasticPool 」)-和 $_。CurrentServiceObjectiveName notin (「 ElasticPool 」)} $outputConnectionString = 「 資料來源 = $outputServerName; 整合式的安全性 = false; 初始目錄 = $outputdatabaseName;使用者識別碼 = $outputDBUsername;密碼 = $outputDBpassword 」 $destinationTableName = 」 resource_stats_output 」**建立資料庫資料表中輸出指標集合**$sql = 」 如果 NOT EXISTS (選取 * sys.objects 從何處 object_id = OBJECT_ID(N'$($destinationTableName)') 中, 輸入 (N'U')) 開始建立資料表 $($destinationTableName) (database_name varchar(128) slo varchar （20）、 end_time datetime、 avg_cpu 浮動時間、 平均_ | 2016-09-29 |
| 12 | [SQL 資料庫教學課程︰ 使用 Azure 入口網站，以分鐘為單位建立 SQL 資料庫](sql-database-get-started.md) |   ! 新的 sql 資料庫 1 (。 / media/sql-database-get-started/sql-database-new-database-1.png) 3。 按一下以開啟 [SQL 資料庫刀**SQL 資料庫**]。 根據您的訂閱與您現有的物件 （例如現有伺服器） 的數目，而異此刀內容。  ! 新的 sql 資料庫 2 (。 / media/sql-database-get-started/sql-database-new-database-2.png) 4。 在**資料庫名稱**] 文字方塊中，提供您的第一個資料庫-例如 「 我資料庫 」 的名稱。 綠色的核取記號會指出您所提供的有效的名稱。  ! 新的 sql 資料庫 3 (。 / media/sql-database-get-started/sql-database-new-database-3.png) 5。 如果您有多個訂閱，請選取 [訂閱]。 6。 在 [**資源群組**] 底下，按一下 [**建立新**並提供您的第一個資源群組-例如 「 我的資源-群組 」 的名稱。 綠色的核取記號會指出您所提供的有效的名稱。  ! 新的 sql 資料庫 4 (。 / media/sql-database-get-started/sql-database-new-database-4.png) 7。 在下 **選取來源* | 2016-09-08 |
| 13 | [嘗試 SQL 資料庫︰ 使用 C#，與 SQL 資料庫文件庫建立.NET SQL 資料庫](sql-database-get-started-csharp.md) | **建立主要存取資源的服務**Active Directory (AD) 應用程式，且我們需要驗證我們 C 應用程式服務原則，就會建立下列 PowerShell 指令碼。 指令碼輸出我們需要上述 C 範例值。 詳細資訊，請參閱使用 Azure PowerShell 來建立服務主體存取資源 (.../ 資源-群組-驗證-服務-principal.md）。  登入 Azure。  新增 AzureRmAccount 如果您有多個訂閱，取消註解，並將為您想要使用的訂閱。  $subscriptionId ="{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx} 」 設定 AzureRmContext SubscriptionId $subscriptionId 新 AAD 應用程式提供這些值。  $appName 是您的應用程式的顯示名稱，您的目錄中必須是唯一。  不需要 $uri 不是真正的 uri。  $secret 是您所建立的密碼。  $appName ="{應用程式名稱} 」 $uri = 」 http://{app-name} 」 $secret ="{應用程式密碼} 」 建立 AAD 應用程式 $azureAdApplication = 新增 AzureRmADApp | 2016-09-23 |
| 14 | [使用 [Azure 入口網站管理 Azure SQL 資料庫](sql-database-manage-portal.md) | 若要檢視或更新您的資料庫設定，請按一下 [SQL 資料庫刀在想要的設定: ！ SQL 資料庫設定 (。 / media/sql-database-manage-portal/settings.png)**如何找到 SQL 資料庫的完整伺服器名稱？** 若要檢視您的資料庫伺服器名稱，請按一下 [ **SQL 資料庫**刀中的 [**概觀**，然後記下的伺服器名稱: ！ SQL 資料庫設定 (。 / media/sql-database-manage-portal/server-name.png)**如何管理防火牆規則來控制對我的 SQL server 與資料庫的存取權？** 若要檢視、 建立或更新防火牆規則，按一下 [**設定伺服器防火牆**上**SQL 資料庫**刀。 如需詳細資訊，請參閱設定使用 Azure 入口網站 (sql-資料庫-設定-防火牆-settings.md) Azure SQL 資料庫伺服器層級的防火牆規則。 ! 防火牆規則 (。 / media/sql-database-manage-portal/sql-database-firewall.png)**如何變更我 SQL 資料庫服務層] 或 [效能層級？** 若要更新的 SQL 資料庫的服務層] 或 [效能層級，請按一下 [* * 價格層 (s | 2016-09-20 |





## <a name="get-started"></a>快速入門

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 15 | [建立多租用戶應用程式與隔離與效率 Azure SQL 資料庫](sql-database-build-multi-tenant-apps.md) | 瞭解如何 SQL 資料庫建立多租用戶應用程式 |
| 16 | [連線至 SQL Server Management Studio 與 SQL 資料庫，並執行範例 T SQL 查詢](sql-database-connect-query-ssms.md) | 瞭解如何使用 SQL Server 管理 Studio (SSMS) 連線到 SQL Azure 資料庫。 然後，執行範例查詢使用 TRANSACT-SQL (T SQL)。 |
| 17 | [使用.NET (C#) 連線到 SQL 資料庫](sql-database-develop-dotnet-simple.md) | 使用範例中的程式碼此快速開始建立新的應用程式與 C# 及功能強大的 Azure SQL 資料庫在雲端關聯式資料庫的備份。 |
| 18 | [建立新的彈性的資料庫集區 Azure 入口網站](sql-database-elastic-pool-create-portal.md) | 如何新增擴充的彈性資料庫資料庫更容易管理及共用跨多個資料庫的資源將 SQL 資料庫設定。 |
| 19 | [探索 Azure SQL 資料庫教學課程](sql-database-explore-tutorials.md) | 進一步瞭解 SQL 資料庫功能 |
| 20 | [SQL 資料庫教學課程︰ 使用 Azure 入口網站，以分鐘為單位建立 SQL 資料庫](sql-database-get-started.md) | 瞭解如何為您設定 SQL 資料庫邏輯伺服器、 伺服器防火牆規則，SQL 資料庫及範例資料。 此外，瞭解如何與連線用戶端工具、 設定使用者，並設定資料庫防火牆規則。 |
| 21 | [Azure SQL 資料庫保護並保護](sql-database-helps-secures-and-protects.md) | 了解如何協助保護 SQL 資料庫及保護公式 |
| 22 | [Azure SQL 資料庫學習&amp;調整](sql-database-learn-and-adapt.md) | 瞭解如何 SQL 資料庫會學習並調整 |
| 23 | [選擇 [SQL Server 選項雲端: (PaaS) 的 Azure SQL 資料庫或 SQL Server Azure Vm (IaaS) 上](sql-database-paas-vs-sql-server-iaas.md) | 瞭解哪一個雲端 SQL Server 選項是否符合您的應用程式: (PaaS) 的 Azure SQL 資料庫或雲端上 Azure 虛擬機器中的 SQL Server。 |
| 24 | [即時 azure SQL 資料庫縮放比例](sql-database-scale-on-the-fly.md) | 進一步瞭解 SQL 資料庫即時的縮放比例 |
| 25 | [探索 Azure SQL 資料庫解決方案快速入門](sql-database-solution-quick-starts.md) | 深入了解 Azure SQL 資料庫解決方案 |
| 26 | [您的環境中的運作 azure SQL 資料庫](sql-database-works-in-your-environment.md) | 瞭解如何 SQL 資料庫可協助保護與保護 |



## <a name="build-an-app"></a>建立應用程式

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 27 | [疑難排解、 診斷，並避免 SQL 連線錯誤和暫時性錯誤 SQL 資料庫](sql-database-connectivity-issues.md) | 瞭解如何疑難排解、 診斷，並避免 SQL 連接錯誤或暫時 Azure SQL 資料庫中的錯誤。  |
| 28 | [連線到 Visual Studio 與 SQL 資料庫](sql-database-connect-query.md) | 撰寫 C# 查詢並連線到 SQL 資料庫中的程式。 IP 位址、 連接字串，secure store 及免費的 Visual Studio 的相關資訊。 |
| 29 | [除了 ADO.NET 4.5 及 SQL 資料庫 V12 1433 的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md) | Azure SQL 資料庫 V12 從 ADO.NET 用戶端連線有時候會略過 proxy，並直接與資料庫互動。 1433 以外的連接埠變得很重要。 |
| 30 | [SQL 資料庫用戶端應用程式的 SQL 錯誤碼︰ 資料庫連線錯誤及其他問題](sql-database-develop-error-messages.md) | 深入了解 SQL 錯誤碼 SQL 資料庫用戶端應用程式，例如常見的資料庫連線錯誤、 資料庫複製問題及一般錯誤。  |
| 31 | [在 Windows 中使用 PHP 來連線到 SQL 資料庫](sql-database-develop-php-simple.md) | 顯示範例 PHP 程式，以從 Windows 用戶端連線到 Azure SQL 資料庫，並提供連結所需的用戶端軟體必要元件。 |
| 32 | [嘗試 SQL 資料庫︰ 使用 C#.NET 與 SQL 資料庫文件庫建立 SQL 資料庫](sql-database-get-started-csharp.md) | 試試看 SQL 資料庫開發 SQL 及 C# 應用程式，並與 C#.net 使用 SQL 資料庫文件庫中建立 Azure SQL 資料庫。 |
| 33 | [開始使用 JSON 功能 Azure SQL 資料庫](sql-database-json-features.md) | Azure SQL 資料庫可讓您以分析、 查詢和 JavaScript 物件標記法 (JSON) 標記法中資料的格式。 |
| 34 | [疑難排解連線問題到 Azure SQL 資料庫](sql-database-troubleshoot-common-connection-issues.md) | 找出並解決常見的連線錯誤 Azure SQL 資料庫的步驟。 |
| 35 | [錯誤] 資料庫伺服器上的不存在 」 連線至 sql 資料庫時](sql-database-troubleshoot-connection.md) | 疑難排解上的 [資料庫伺服器不是目前可用的錯誤時，應用程式連線到 SQL 資料庫。 |



## <a name="develop"></a>開發

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 36 | [驗證應用程式從 [程式碼存取 SQL 資料庫時，取得所需的值](sql-database-client-id-keys.md) | 建立服務主要從程式碼存取 SQL 資料庫。 |
| 37 | [使用 Windows 上 JDBC Java 來連線到 SQL 資料庫](sql-database-develop-java-simple.md) | 將呈現可用來連線到 Azure SQL 資料庫的 Java 程式碼範例。 此範例使用 JDBC，並在 Windows 用戶端電腦上執行。 |
| 38 | [使用 Node.js 連線至 SQL 資料庫](sql-database-develop-nodejs-simple.md) | 將呈現可用來連線到 Azure SQL 資料庫的 Node.js 程式碼範例。 |
| 39 | [SQL 資料庫開發概觀](sql-database-develop-overview.md) | 進一步瞭解可用的連線文件庫，應用程式連線至 SQL 資料庫的最佳作法。 |
| 40 | [使用 Python 連線至 SQL 資料庫](sql-database-develop-python-simple.md) | 將呈現可用來連線到 Azure SQL 資料庫的 Python 程式碼範例。 |
| 41 | [使用 [注音標示連線至 SQL 資料庫](sql-database-develop-ruby-simple.md) | 讓您執行後可連線到 Azure SQL 資料庫的並列文字的程式碼範例。 |
| 42 | [快速入門暫時 Azure SQL 資料庫中的資料表](sql-database-temporal-tables.md) | 瞭解如何開始使用 Azure SQL 資料庫中的暫時表格。 |



## <a name="performance-and-scale"></a>效能和小數位數

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 43 | [SQL 資料庫顧問](sql-database-advisor.md) | Azure SQL 資料庫顧問提供您現有的 SQL 資料庫，可提升目前的查詢效能的建議。 |
| 44 | [SQL 資料庫顧問使用 Azure 入口網站](sql-database-advisor-portal.md) | 您可以使用 Azure SQL 資料庫顧問 Azure 入口網站中，檢閱及實作您現有的 SQL 資料庫，可提升目前的查詢效能的建議。 |
| 45 | [Azure SQL 資料庫事先概觀](sql-database-benchmark-overview.md) | 本主題說明 Azure SQL 資料庫基準測試用來測量 Azure SQL 資料庫的效能。 |
| 46 | [何時使用彈性的資料庫資料庫？](sql-database-elastic-pool-guidance.md) | 彈性的資料庫資料庫是可用資源所共用的群組彈性的資料庫的集合。 這份文件提供協助評估適群組的資料庫中使用的彈性的資料庫集區的指引。 |
| 47 | [開始使用彈性的資料庫工具](sql-database-elastic-scale-get-started.md) | 基本的彈性的資料庫工具] 功能 Azure SQL 資料庫，包括輕鬆執行範例應用程式的說明。 |
| 48 | [SQL 資料庫的常見問題集](sql-database-faq.md) | 常見的問題客戶所做的回答問雲端資料庫與 Azure SQL 資料庫、 Microsoft 的關聯式資料庫管理系統 (RDBMS) 和資料庫以在雲端服務。 |
| 49 | [快速入門的記憶體 （預覽版本） 中的 SQL 資料庫](sql-database-in-memory.md) | SQL 記憶體內技術大幅改善效能的交易和分析的工作量。 瞭解如何利用這些技術。 |
| 50 | [若要改善您的應用程式的效能 SQL 資料庫中使用記憶體內 OLTP （預覽版本）](sql-database-in-memory-oltp-migration.md) | 採用記憶體內 OLTP 改善現有的 SQL 資料庫交易的效能。 |
| 51 | [監視器 OLTP 記憶體內儲存空間](sql-database-in-memory-oltp-monitoring.md) | 估計值和監視 XTP 記憶體內儲存使用，容量。解決容量錯誤 41823 |
| 52 | [作業系統查詢中的存放區 Azure SQL 資料庫](sql-database-operate-query-store.md) | 了解如何操作查詢中的存放區 Azure SQL 資料庫 |
| 53 | [SQL 資料庫效能充分](sql-database-performance.md) | Azure SQL 資料庫提供的效能工具，可協助您找出可改善目前的查詢效能的區域。 |
| 54 | [Azure SQL 資料庫及單一資料庫的效能](sql-database-performance-guidance.md) | 本文可協助您判斷哪一個，選擇您的應用程式層。 也建議以調整您的應用程式，以取得最從 Azure SQL 資料庫的方法。 |
| 55 | [Azure SQL 資料庫查詢效能充分](sql-database-query-performance.md) | 查詢效能監視 Azure SQL 資料庫的識別大部分的 CPU 使用查詢。 |
| 56 | [變更服務層和效能的層級 （價格層） SQL 資料庫](sql-database-scale-up.md) | 變更服務層級和 Azure SQL 資料庫的效能層級會顯示如何向上或向下調整 SQL 資料庫。 變更 Azure SQL 資料庫的價格層。 |
| 57 | [監視 Azure SQL 資料庫中的資料庫效能](sql-database-single-database-monitor.md) | 深入了解監控 Azure 工具與動態管理檢視資料庫的選項。 |
| 58 | [SQL 資料庫效能調整提示](sql-database-troubleshoot-performance.md) | 效能調整 Azure SQL 資料庫中透過評估和改進的秘訣。 |
| 59 | [如何使用批次處理改善 SQL 資料庫應用程式的效能](sql-database-use-batching-to-improve-performance.md) | 本主題提供證據的批次的資料庫作業大幅 imroves 速度能與延展性 Azure SQL 資料庫應用程式。 雖然批次技巧適用於任何 SQL Server 資料庫，本文的焦點會放在 Azure。 |



## <a name="tools-for-scaling-out"></a>延展工具

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 60 | [設計模式 multitenant SaaS 應用程式和 Azure SQL 資料庫](sql-database-design-patterns-multi-tenancy-saas-applications.md) | 本文將告訴您的需求及執行位於雲端環境的應用程式需要考慮的 multitenant 資料庫及各種必須做的取捨這些模式與相關聯的常見的資料結構模式。 同時也會說明如何 Azure SQL 資料庫]，以彈性的集區和彈性的工具，協助解決這些需求，無洩漏的方式。 |
| 61 | [移轉現有的資料庫來擴充](sql-database-elastic-convert-to-use-elastic-tools.md) | 轉換使用彈性的資料庫工具建立晶怪地圖管理員 sharded 資料庫 |
| 62 | [建立可調整雲端資料庫](sql-database-elastic-database-client-library.md) | 建立可調整.NET 資料庫應用程式，與彈性的資料庫用戶端文件庫 |
| 多為 63 | [正在晶怪對應管理員](sql-database-elastic-database-perf-counters.md) | ShardMapManager 類別及資料相依路由效能計數器 |
| 64 | [新增晶怪使用彈性的資料庫工具](sql-database-elastic-scale-add-a-shard.md) | 如何將新擊碎晶怪使用彈性的縮放比例 Api 設定。 |
| 65 | [部署分割合併服務](sql-database-elastic-scale-configure-deploy-split-and-merge.md) | 分割及合併彈性的資料庫工具 |
| 66 | [資料相依路由](sql-database-elastic-scale-data-dependent-routing.md) | 如何使用.NET 應用程式中的 ShardMapManager 類別資料相依路由]，[Azure SQL 資料庫的彈性資料庫的功能 |
| 67 | [彈性的資料庫工具常見問題集](sql-database-elastic-scale-faq.md) | 常見問題集 Azure SQL 資料庫彈性的小數位數。 |
| 68 | [彈性的資料庫工具詞彙](sql-database-elastic-scale-glossary.md) | 彈性的資料庫工具所用的字詞的說明 |
| 69 | [擴展 Azure SQL 資料庫](sql-database-elastic-scale-introduction.md) | 軟體與服務 (SaaS) 開發人員可使用這些工具雲端中輕鬆建立彈性、 可調整資料庫 |
| 70 | [用來存取彈性的用戶端資料庫的認證](sql-database-elastic-scale-manage-credentials.md) | 如何設定正確的層級認證，為唯讀，彈性的資料庫應用程式的管理員 |
| 71 | [多晶怪查詢](sql-database-elastic-scale-multishard-querying.md) | 查詢多個執行擊碎使用的彈性的資料庫用戶端文件庫。 |
| 72 | [移動縮放出雲端資料庫之間的資料](sql-database-elastic-scale-overview-split-and-merge.md) | 說明如何操作擊碎和移動透過使用彈性資料庫 Api 的自我代管服務的資料。 |
| 73 | [擴充晶怪地圖管理員使用的資料庫](sql-database-elastic-scale-shard-map-management.md) | 如何使用 ShardMapManager，彈性的資料庫用戶端文件庫 |
| 74 | [分割合併的安全性設定](sql-database-elastic-scale-split-merge-security-configuration.md) | 設定 x409 加密的憑證 |
| 75 | [升級 [應用程式，使用最新的彈性的資料庫用戶端文件庫](sql-database-elastic-scale-upgrade-client-library.md) | 升級 [應用程式，並使用 Nuget 文件庫 |
| 76 | [實體架構彈性資料庫的用戶端文件庫](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) | 用於彈性的資料庫用戶端文件庫和實體架構編碼資料庫 |
| 77 | [使用 Dapper 彈性的資料庫用戶端文件庫](sql-database-elastic-scale-working-with-dapper.md) | 使用 Dapper 彈性的資料庫用戶端文件庫。 |
| 78 | [有彈性的資料庫的工具和列層級安全性多租用戶應用程式](sql-database-elastic-tools-multi-tenant-row-level-security.md) | 瞭解如何使用與資料列層級安全性的彈性的資料庫工具建立有彈性的資料層的應用程式支援多重租用戶擊碎 Azure SQL 資料庫。 |



## <a name="elastic-jobs"></a>彈性的工作

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 79 | [建立及管理不按比例縮放出 Azure SQL 資料庫時使用彈性的工作 （預覽版本）](sql-database-elastic-jobs-create-and-manage.md) | 逐步建立和管理的彈性的資料庫作業。 |
| 80 | [彈性的資料庫工作快速入門](sql-database-elastic-jobs-getting-started.md) | 如何使用彈性的資料庫作業 |
| 81 | [管理縮放出雲端資料庫](sql-database-elastic-jobs-overview.md) | 說明彈性的資料庫作業服務 |
| 82 | [建立及管理 SQL 資料庫彈性的資料庫工作使用 PowerShell （預覽版本）](sql-database-elastic-jobs-powershell.md) | PowerShell 用來管理 Azure SQL 資料庫集區 |
| 83 | [安裝彈性的資料庫工作概觀](sql-database-elastic-jobs-service-installation.md) | 逐步執行安裝的彈性工作功能。 |
| 84 | [解除安裝彈性的資料庫作業元件](sql-database-elastic-jobs-uninstall.md) | 如何解除安裝彈性的資料庫作業工具 |



## <a name="elastic-pools"></a>彈性的資料庫

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 鍵入 [85 | [什麼是 Azure 的彈性資料庫？](sql-database-elastic-pool.md) | 管理百數以千計的使用資料庫的資料庫。 一一組績效單位價格可以分佈集區。 將會在資料庫或縮小。 |
| 86 | [C# 建立彈性的資料庫集區](sql-database-elastic-pool-create-csharp.md) | 使用 C# 資料庫開發技巧，以便您在許多資料庫間共用資源，Azure SQL 資料庫中建立可調整彈性資料庫集區。 |
| 87 | [使用 PowerShell 來建立新的彈性的資料庫資料庫](sql-database-elastic-pool-create-powershell.md) | 瞭解如何使用 PowerShell 來擴充 Azure SQL 資料庫來建立管理多個資料庫可調整彈性資料庫集區的資源。 |
| 88 | [識別資料庫適合彈性的資料庫資料庫的 PowerShell 指令碼](sql-database-elastic-pool-database-assessment-powershell.md) | 彈性的資料庫資料庫是可用資源所共用的群組彈性的資料庫的集合。 這份文件提供協助評估適合使用的彈性的資料庫集區群組的資料庫的 Powershell 指令碼。 |
| 89 | [監控和管理與 C# 彈性的資料庫集區](sql-database-elastic-pool-manage-csharp.md) | 您可以使用 C# 資料庫開發技術來管理 Azure SQL 資料庫彈性的資料庫集區。 |
| 90 | [監控和管理 Azure 入口網站的彈性的資料庫集區](sql-database-elastic-pool-manage-portal.md) | 瞭解如何使用 Azure 入口網站和 SQL 資料庫的內建智慧管理、 監控及向右大小調整彈性資料庫集區最佳化資料庫效能及管理成本。 |
| 91 | [監控和管理使用 PowerShell 彈性的資料庫集區](sql-database-elastic-pool-manage-powershell.md) | 瞭解如何使用 PowerShell 來管理彈性的資料庫資料庫。 |
| 頁，共 92 | [監控和管理 TRANSACT-SQL 彈性的資料庫集區](sql-database-elastic-pool-manage-tsql.md) | 使用 T SQL 彈性的資料庫中建立 Azure SQL 資料庫。 或使用 T SQL 移動 datbase 與集區登出。 |
| 93 | [彈性的資料庫資料庫計費和價格資訊](sql-database-elastic-pool-price.md) | 彈性的資料庫集區的特定價格資訊。 |



## <a name="elastic-query"></a>彈性的查詢

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 94 | [報告跨縮放出雲端資料庫 （預覽版本）](sql-database-elastic-query-getting-started.md) | 如何使用交叉資料庫資料庫查詢 |
| 95 | [開始使用跨資料庫查詢 （垂直分割） （預覽版本）](sql-database-elastic-query-getting-started-vertical.md) | 垂直分割資料庫中使用彈性的資料庫查詢的方式 |
| 96 | [報告跨縮放出雲端資料庫 （預覽版本）](sql-database-elastic-query-horizontal-partitioning.md) | 如何設定在水平分割的彈性的查詢 |
| 97 | [Azure SQL 資料庫彈性的資料庫查詢概觀 （預覽版本）](sql-database-elastic-query-overview.md) | 彈性的查詢功能的概觀 |
| 98 | [在不同的結構描述 （預覽版本） 的雲端資料庫間的查詢](sql-database-elastic-query-vertical-partitioning.md) | 如何設定在垂直分割的跨資料庫查詢 |



## <a name="elastic-transaction"></a>彈性的交易

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 99 | [在雲端的資料庫間的分散式的交易](sql-database-elastic-transactions-overview.md) | 彈性的資料庫交易 Azure SQL 資料庫的概觀 |



## <a name="backup-and-recovery"></a>備份及復原

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 100 | [SQL 資料庫的備份](sql-database-automated-backups.md) | 瞭解內建的 SQL 資料庫，可讓您將資料庫備份時間返回前一個點的 Azure SQL 資料庫，或複製到新的資料庫中的地理區域 （最多 35 天） 的資料庫。 |
| 101 | [業務連續性 Azure SQL 資料庫的概觀](sql-database-business-continuity.md) | 瞭解如何 Azure SQL 資料庫支援雲端業務連續性及修復資料庫，可以協助您隨時執行的重要的雲端應用程式。 |
| 102 | [如何從 Azure SQL 資料庫備份還原單一資料表](sql-database-cloud-migrate-restore-single-table-azure-backup.md) | 瞭解如何從 Azure SQL 資料庫備份還原單一資料表。 |
| 103 | [設計雲端損毀修復使用 SQL 資料庫中的 [作用中的地理複寫的應用的程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md) | 瞭解如何設計雲端損毀修復解決方案的業務連續性規劃地理複寫用應用程式資料與 Azure SQL 資料庫的備份。 |
| 104 | [還原次要 Azure SQL 資料庫或容錯移轉](sql-database-disaster-recovery.md) | 瞭解如何復原資料庫的區域資料中心中斷或失敗 Azure SQL 資料庫作用中地理複寫，與地理還原功能。 |
| 105 | [執行損毀復原向下切入](sql-database-disaster-recovery-drills.md) | 瞭解指引與執行損毀復原切入，使用 Azure SQL 資料庫的最佳做法保留您的任務要徑商務應用程式，同時以失敗及中斷。 |
| 106 | [使用 SQL 資料庫彈性資料庫的應用程式損毀修復策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) | 瞭解如何設計您的雲端解決方案損毀修復選擇正確的容錯移轉模式。 |
| 107 | [若要修正晶怪地圖使用 RecoveryManager 類別](sql-database-elastic-database-recovery-manager.md) | 使用 RecoveryManager 類別來解決問題晶怪地圖 |
| 108 | [匯入 BACPAC 檔案建立 Azure SQL 資料庫](sql-database-import.md) | 建立 Azure SQL 資料庫匯入現有的 BACPAC 檔案。 |
| 109 | [Azure SQL 資料庫還原至先前的點，Azure 入口網站的時間](sql-database-point-in-time-restore-portal.md) | 還原時間點 Azure SQL 資料庫。 |
| 110 | [使用 PowerShell 的時間點還原 Azure SQL 資料庫](sql-database-point-in-time-restore-powershell.md) | Azure SQL 資料庫還原至先前的點時間 |
| 112 | [復原使用自動化的資料庫備份的 Azure SQL 資料庫](sql-database-recovery-using-backups.md) | 瞭解時間點還原，可讓您回復 Azure SQL 資料庫到上一個點時間 （最多 35 天）。 |
| 113 | [還原已刪除的 Azure SQL 資料庫，使用 [Azure 入口網站](sql-database-restore-deleted-database-portal.md) | 還原已刪除的 Azure SQL 資料庫 （Azure 入口網站）。 |
| 114 | [使用 PowerShell 還原已刪除的 Azure SQL 資料庫](sql-database-restore-deleted-database-powershell.md) | 還原已刪除的 Azure SQL 資料庫 (PowerShell)。 |
| 115 | [還原至先前的點的資料庫，時間，還原已刪除的資料庫，或從資料中心中斷復原](sql-database-troubleshoot-backup-and-restore.md) | 瞭解如何復原雲端資料庫的錯誤和中斷 Azure SQL 資料庫中使用備份與複本。 |



## <a name="migrate"></a>移轉

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 116 | [將 SQL Server 資料庫匯出至 SqlPackage BACPAC 檔案](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Microsoft Azure SQL 資料庫，資料庫移轉匯出資料庫]、 [匯出 BACPAC 檔案，sqlpackage |
| 117 | [使用 SQL Server Management Studio BACPAC 檔案匯出的 SQL Server 資料庫](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Microsoft Azure SQL 資料庫，資料庫移轉匯出資料庫]、 [匯出 BACPAC 檔案]，匯出資料層應用程式精靈 |
| 118 | [從 SqlPackage BACPAC 檔案匯入到 SQL 資料庫](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md) | Microsoft Azure SQL 資料庫，資料庫的轉換匯入資料庫，匯入 BACPAC 檔案，sqlpackage |
| 119 | [從 BACPAC 匯入至 SSMS SQL 資料庫](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Microsoft Azure SQL 資料庫，資料庫部署資料庫移轉，匯入資料庫，匯出資料庫，移轉精靈 |
| 120 | [將 SQL Server 資料庫移轉到使用部署至 Microsoft Azure 資料庫] 精靈的資料庫的 SQL 資料庫](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) | Microsoft Azure SQL 資料庫，資料庫移轉、 Microsoft Azure 資料庫] 精靈 |
| 121 | [將 SQL Server 資料庫移轉到使用交易複寫 Azure SQL 資料庫](sql-database-cloud-migrate-compatible-using-transactional-replication.md) | Microsoft Azure SQL 資料庫，資料庫移轉匯入資料庫交易複寫 |
| 122 | [決定使用 SqlPackage.exe SQL 資料庫相容性](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md) | Microsoft Azure SQL 資料庫，資料庫移轉、 SQL 資料庫的相容性，SqlPackage |
| 123 | [使用 SQL Server Management Studio 決定 SQL 資料庫移轉到 Azure SQL 資料庫前的相容性](sql-database-cloud-migrate-determine-compatibility-ssms.md) | Microsoft Azure SQL 資料庫，資料庫移轉、 SQL 資料庫相容性、 匯出資料層應用程式精靈 |
| 124 | [使用 SQL Azure 移轉精靈來修正 SQL Server 資料庫相容性問題，再移轉到 Azure SQL 資料庫](sql-database-cloud-migrate-fix-compatibility-issues.md) | Microsoft Azure SQL 資料庫，資料庫移轉、 相容性，SQL Azure 移轉精靈 |
| 125 | [將 SQL Server 資料庫移轉到使用 SQL Server Data Tools for Visual Studio Azure SQL 資料庫](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) | SQL Azure 移轉精靈，SSDT、 Microsoft Azure SQL 資料庫，資料庫移轉、 相容性 |
| 126 | [修正使用 SQL Server Management Studio 之前移轉到 SQL 資料庫的 SQL Server 資料庫的相容性問題](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) | Microsoft Azure SQL 資料庫，資料庫移轉、 相容性，SQL Azure 移轉精靈 |
| 127 | [封存 BACPAC 檔案使用 PowerShell 的 Azure SQL 資料庫](sql-database-export-powershell.md) | 封存 BACPAC 檔案使用 PowerShell 的 Azure SQL 資料庫 |
| 128 | [匯入 BACPAC 檔案建立使用 PowerShell 的 Azure SQL 資料庫](sql-database-import-powershell.md) | 匯入 BACPAC 檔案建立使用 PowerShell 的 Azure SQL 資料庫 |
| 129 | [從 CSV 載入 Azure SQL Data Warehouse （一般檔案）](sql-database-load-from-csv-with-bcp.md) | 對於較小的資料的大小，使用 bcp Azure SQL 資料庫將資料匯入。 |
| 130 | [伺服器、 之間的訂閱，及與 Azure 登出之間移動資料庫](sql-database-troubleshoot-moving-data.md) | 快速步驟來複製，移動及移轉資料和 Azure SQL 資料庫中的資料庫。 |



## <a name="move-data-in-and-out"></a>將資料移回和取出

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 131 | [SQL Server 資料庫移轉到雲端中的 SQL 資料庫](sql-database-cloud-migrate.md) | 瞭解如何在內部部署 SQL Server 資料庫移轉到雲端中的 Azure SQL 資料庫。 使用資料庫移轉工具來測試之前資料庫移轉的相容性。 |
| 132 | [複製 Azure SQL 資料庫](sql-database-copy.md) | 建立一份 Azure SQL 資料庫 |
| 133 | [封存 BACPAC 檔案 Azure 入口網站 Azure SQL 資料庫](sql-database-export.md) | 封存 BACPAC 檔案 Azure 入口網站 Azure SQL 資料庫 |



## <a name="security"></a>安全性

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 134 | [使用 Azure Active Directory 驗證連線到 SQL 資料庫或 SQL Data Warehouse](sql-database-aad-authentication.md) | 瞭解如何使用 Azure Active Directory 驗證連線至 SQL 資料庫。 |
| 135 | [永遠加密︰ 保護機密 SQL 資料庫中的資料與 Windows 認證存放區中儲存您的加密金鑰](sql-database-always-encrypted.md) | 保護機密 SQL 資料庫中的資料以分鐘為單位。 |
| 136 | [永遠加密︰ 保護機密 SQL 資料庫中的資料和 Azure 金鑰保存庫中儲存您的加密金鑰](sql-database-always-encrypted-azure-key-vault.md) | 保護機密 SQL 資料庫中的資料以分鐘為單位。 |
| 137 | [SQL 資料庫-舊版用戶端支援及 IP 端點變更的稽核](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) | 進一步瞭解 SQL 資料庫下層用戶端支援，IP 端點變更的稽核。 |
| 138 | [設定使用 PowerShell 的 Azure SQL 資料庫伺服器層級的防火牆規則](sql-database-configure-firewall-settings-powershell.md) | 瞭解如何設定存取 SQL Azure 資料庫的 IP 位址的防火牆。 |
| 139 | [設定使用 REST API Azure SQL 資料庫伺服器層級的防火牆規則](sql-database-configure-firewall-settings-rest.md) | 瞭解如何設定存取 SQL Azure 資料庫的 IP 位址的防火牆。 |
| 140 | [設定使用 T SQL Azure SQL 資料庫伺服器層級和資料庫層級的防火牆規則](sql-database-configure-firewall-settings-tsql.md) | 瞭解如何設定存取 SQL Azure 資料庫的 IP 位址的防火牆。 |
| 141 | [開始使用 SQL 資料庫動態資料 Masking （Azure 入口網站）](sql-database-dynamic-data-masking-get-started.md) | 如何開始使用 SQL 資料庫動態資料 Masking Azure 入口網站中 |
| 142 | [開始使用 SQL 資料庫動態資料 Masking （Azure 傳統入口網站）](sql-database-dynamic-data-masking-get-started-portal.md) | 如何開始使用 SQL 資料庫動態資料 Masking Azure 傳統入口網站中 |
| 143 | [設定 Azure SQL 資料庫防火牆規則\-概觀](sql-database-firewall-configure.md) | 瞭解如何設定防火牆 SQL 資料庫伺服器層級和資料庫層級的防火牆規則，以管理存取權。 |
| 144 | [SQL 資料庫教學課程︰ 建立使用者帳戶存取及管理資料庫的 SQL 資料庫](sql-database-get-started-security.md) | 瞭解如何建立使用者帳戶存取及管理資料庫。 |
| 145 | [SQL 資料庫驗證與授權︰ 授與存取權](sql-database-manage-logins.md) | SQL 資料庫安全性管理，了解如何便於管理資料庫存取和登入安全性的伺服器層級的本金帳戶。 |
| 146 | [Azure SQL 資料庫的安全性指導方針和限制](sql-database-security-guidelines.md) | 進一步瞭解 Microsoft Azure SQL 資料庫指導方針，與安全性相關的限制。 |
| 147 | [開始使用 SQL 資料庫威脅偵測](sql-database-threat-detection-get-started.md) | 如何開始使用 Azure 入口網站中的 SQL 資料庫威脅偵測 |
| 148 | [如何執行一般的系統管理工作，例如重設管理員密碼在 Azure SQL 資料庫](sql-database-troubleshoot-permissions.md) | 說明如何將 SQL 資料庫中執行一般的系統管理工作。 例如，重設管理員密碼，授與及移除存取。 |



## <a name="manage-your-database"></a>管理您的資料庫

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 149 | [開始使用 SQL 資料庫稽核](sql-database-auditing-get-started.md) | 開始使用 SQL 資料庫稽核 |
| 150 | [設定 Azure SQL 資料庫伺服器層級的防火牆規則使用 Azure 入口網站](sql-database-configure-firewall-settings.md) | 瞭解如何設定存取 Azure SQL server 的 IP 位址的防火牆。 |
| 151 | [複製使用 Azure 入口網站 Azure SQL 資料庫](sql-database-copy-portal.md) | 建立一份 Azure SQL 資料庫 |
| 152 | [管理使用 Azure 自動化 Azure SQL 資料庫](sql-database-manage-automation.md) | 深入了解如何 Azure 自動化服務可用於管理在 Azure SQL 資料庫。 |
| 153 | [概觀︰ 管理工具 SQL 資料庫](sql-database-manage-overview.md) | 比較工具和管理 Azure SQL 資料庫的選項 |
| 154 | [使用動態管理檢視監控 Azure SQL 資料庫](sql-database-monitoring-with-dmvs.md) | 瞭解如何偵測並使用動態管理檢視監控 Microsoft Azure SQL 資料庫診斷一般效能問題。 |
| 155 | [保護您的 SQL 資料庫](sql-database-security.md) | 深入了解 Azure SQL 資料庫和 SQL Server 的安全性，包括雲端之間的差異與 SQL Server 內部部署時，以驗證、 授權、 連線的安全性、 加密與規範。 |



## <a name="extended-events"></a>擴充的事件

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 156 | [SQL 資料庫中的延伸事件的事件檔案目標程式碼](sql-database-xevent-code-event-file.md) | PowerShell 和 TRANSACT-SQL 提供兩階段的程式碼範例示範事件檔案中的目標擴充的事件 Azure SQL 資料庫]。 Azure 儲存體必要屬於這種情況。 |
| 157 | [撥打緩衝 SQL 資料庫中的延伸事件的目標程式碼](sql-database-xevent-code-ring-buffer.md) | 提供由輕鬆及快速撥打緩衝目標，Azure SQL 資料庫中使用的 SQL 程式碼範例。 |
| 158 | [SQL 資料庫中的延伸的事件](sql-database-xevent-db-diff-from-svr.md) | 說明 Azure SQL 資料庫中的延伸的事件 (XEvents)，以及如何事件工作階段稍有不同的 Microsoft SQL Server 事件工作階段。 |



## <a name="geo-replication"></a>地理複寫

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 159 | [啟動 Azure SQL 資料庫的計畫之內的容錯移轉，與 Azure 入口網站](sql-database-geo-replication-failover-portal.md) | 使用 [Azure 入口網站的 Azure SQL 資料庫啟動計畫之內的容錯移轉 |
| 160 | [使用 PowerShell 的 Azure SQL 資料庫啟動計畫之內的容錯移轉](sql-database-geo-replication-failover-powershell.md) | 使用 PowerShell 的 Azure SQL 資料庫啟動計畫之內容錯移轉 |
| 161 | [啟動計畫之內的容錯移轉與 SQL Azure SQL 資料庫](sql-database-geo-replication-failover-transact-sql.md) | 使用 SQL Azure SQL 資料庫啟動計畫之內的容錯移轉 |
| 162 | [概觀︰ SQL 資料庫作用中地理複寫](sql-database-geo-replication-overview.md) | 作用中的地理複寫可讓您設定的 Azure 資料中心的所有資料庫 4 的複本。 |
| 163 | [設定地理複寫 Azure SQL 資料庫 Azure 入口網站](sql-database-geo-replication-portal.md) | 使用 [Azure 入口網站的 Azure SQL 資料庫設定地理複寫 |
| 164 | [使用 PowerShell 的 Azure SQL 資料庫設定地理複寫](sql-database-geo-replication-powershell.md) | 使用 PowerShell 的 Azure SQL 資料庫設定作用中的地理複寫 |
| 165 | [如何管理 Azure SQL 資料庫安全性之後損毀修復](sql-database-geo-replication-security-config.md) | 本主題說明安全性管理資料庫還原或容錯移轉後的安全性的考量。 |
| 166 | [設定地理複寫與 TRANSACT-SQL Azure SQL 資料庫](sql-database-geo-replication-transact-sql.md) | 使用 SQL Azure SQL 資料庫設定地理複寫 |
| 167 | [地理還原從地理多餘的備份檔案，使用 [Azure 入口網站 Azure SQL 資料庫](sql-database-geo-restore-portal.md) | 地理還原 Azure SQL 資料庫從地理多餘的備份檔案 （Azure 入口網站）。 |
| 168 | [使用 PowerShell 地理重複備份還原 Azure SQL 資料庫](sql-database-geo-restore-powershell.md) | 將新的伺服器從地理多餘的備份檔案還原 Azure SQL 資料庫 |



## <a name="upgrade"></a>升級

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 169 | [改良與相容性層級 130 Azure SQL 資料庫中的查詢效能](sql-database-compatibility-level-query-performance-130.md) | 步驟和決定的相容性層級最適合您的資料庫 Azure SQL 資料庫或 Microsoft SQL Server 上的工具 |
| 170 | [管理雲端應用程式使用 SQL 資料庫作用中地理複寫循環升級](sql-database-manage-application-rolling-upgrade.md) | 瞭解如何使用 Azure SQL 資料庫地理複寫支援線上升級您的雲端應用程式。 |
| 171 | [升級至 Azure SQL 資料庫 V12 使用 Azure 入口網站](sql-database-upgrade-server-portal.md) | 說明如何升級至 Azure SQL 資料庫 V12 包括如何升級網站和商務資料庫，以及如何升級 V11 伺服器移轉的資料庫，直接將使用 Azure 入口網站的彈性的資料庫集區。 |
| 172 | [使用 PowerShell 來 Azure SQL 資料庫 V12 升級](sql-database-upgrade-server-powershell.md) | 說明如何升級至 Azure SQL 資料庫 V12 包括如何升級網站和商務資料庫，以及如何升級 V11 伺服器直接將使用 PowerShell 彈性的資料庫資料庫移轉的資料庫。 |
| 173 | [規劃及準備升級至 SQL 資料庫 V12](sql-database-v12-plan-prepare-upgrade.md) | 準備工作，並限制升級至新版 Azure SQL 資料庫 V12 的相關說明。 |
| 174 | [在 SQL 資料庫 V12 中的新功能](sql-database-v12-whats-new.md) | 說明為何，現在升級版本 V12 助益的雲端中使用 Azure SQL 資料庫 business 系統。 |
| 175 | [網站與商務版日落的常見問題集](sql-database-web-business-sunset-faq.md) | 瞭解當 Azure SQL 網頁和商務資料庫會已停用，並進一步瞭解的功能和新的服務層的功能。 |



## <a name="tools"></a>工具

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 176 | [管理使用 PowerShell 的 Azure SQL 資料庫](sql-database-command-line-tools.md) | 使用 PowerShell 的 azure SQL 資料庫管理。 |
| 177 | [SQL 資料庫教學課程︰ 將 Excel 連接至 Azure SQL 資料庫，並建立報告](sql-database-connect-excel.md) | 瞭解如何在 Microsoft Excel 連線到雲端中的 Azure SQL 資料庫。 將 Excel 資料匯入的報告及資料探索。 |
| 178 | [建立 SQL 資料庫及執行常見的資料庫設定工作，透過 PowerShell cmdlet](sql-database-get-started-powershell.md) | 立即瞭解如何使用 PowerShell 建立 SQL 資料庫。 管理資料庫設定的一般工作時，可以透過 PowerShell cmdlet。 |
| 179 | [快速入門 Azure SQL 資料同步處理 （預覽版本）](sql-database-get-started-sql-data-sync.md) | 本教學課程中，可協助您開始使用 Azure SQL 資料同步處理 （預覽版本）。 |
| 180 | [使用 SQL Server Management Studio 管理 Azure SQL 資料庫](sql-database-manage-azure-ssms.md) | 瞭解如何使用 SQL Server Management Studio 管理 SQL 資料庫伺服器和資料庫。 |
| 181 | [使用 [Azure 入口網站管理 Azure SQL 資料庫](sql-database-manage-portal.md) | 瞭解如何使用 Azure 入口網站管理中使用 [Azure 入口網站的雲端關聯式資料庫。 |
| 182 | [變更服務層和效能的層級 （價格層） 使用 PowerShell SQL 資料庫](sql-database-scale-up-powershell.md) | 變更服務層級和 Azure SQL 資料庫的效能層級會顯示如何使用 PowerShell 向上或向下調整 SQL 資料庫。 變更使用 PowerShell 的 Azure SQL 資料庫的價格層。 |
| 183 | [使用 SQL Server Management Studio 中 Azure RemoteApp 連線至 SQL 資料庫](sql-database-ssms-remoteapp.md) | 若要瞭解如何使用 SQL Server Management Studio 中 Azure RemoteApp 安全性和效能連線至 SQL 資料庫時使用此教學課程 |



## <a name="reference"></a>參照

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 184 | [SQL 資料庫與 SQL Server 的連線庫](sql-database-libraries.md) | 列出每個用戶端程式可用來連線到 Azure SQL 資料庫或 Microsoft SQL Server 的驅動程式的最小的版本號碼。 連結提供關於驅動程式社群而不是由 Microsoft 所發行版本資訊。 |
| 185 | [Azure SQL 資料庫資源限制](sql-database-resource-limits.md) | 本頁面說明一些常見的資源限制 Azure SQL 資料庫。 |
| 186 | [Azure SQL 資料庫 TRANSACT-SQL 差異](sql-database-transact-sql-information.md) | 小於完全支援的 Azure SQL 資料庫 TRANSACT-SQL 陳述式 |



## <a name="miscellaneous"></a>其他

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 187 | [複製使用 PowerShell 的 Azure SQL 資料庫](sql-database-copy-powershell.md) | 建立使用 PowerShell 的 Azure SQL 資料庫的複本 |
| 188 | [複製 SQL Azure SQL 資料庫](sql-database-copy-transact-sql.md) | 建立使用 SQL Azure SQL 資料庫的複本 |
| 189 | [Azure SQL 資料庫一般限制和準則](sql-database-general-limitations.md) | 本頁面說明一些一般限制 Azure SQL 資料庫的互通性及支援的區域。 |
| 190 | [定價層建議 SQL 資料庫](sql-database-service-tier-advisor.md) | 變更價格時中 Azure 入口網站，價格層建議層是提供建議您最好層適合用來執行現有 Azure SQL 資料庫的工作量。 定價層說明 SQL 資料庫的服務層和效能層級。 |


&nbsp;

<hr/>

<a name="extras_append"></a>

## <a name="extras"></a>Extras]

<a name="extra_related_articles"></a>

### <a name="related-articles-from-other-azure-services"></a>從其他 Azure 服務的相關的文章

- [備份 Azure 中 Azure 應用程式服務應用程式](../app-service-web/web-sites-backup.md)

<a name="extra_documentation_tools"></a>

### <a name="documentation-tools"></a>文件工具

- [更新本項 Azure SQL 資料庫](http://azure.microsoft.com/updates/?service=sql-database)

- [篩選搜尋 Microsoft Azure 的所有文件的類型](http://azure.microsoft.com/search/documentation/)

<a name="extra_learning_path_graphics"></a>

### <a name="learning-path-graphics"></a>學習路徑圖形

- [所有 Microsoft Azure 服務學習路徑圖形](http://azure.microsoft.com/documentation/learning-paths/)

- [學習路徑︰ 瞭解 Azure SQL 資料庫](http://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)

- [學習路徑︰ SQL 資料庫彈性的小數位數](http://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/)


<!--
AzIxAA.ExtraAppend.sql-database.txt
C:\_MainW\VS15\AzureIndexAllArticles\AzureIndexAllArticles\In-Out\In\

This bullet link is improperly disallowed by publishing automation due to presence of string 'docuXXmentation/arXXticles':
- [Search SQL Database documentation, with filters](http://azure.microsoft.com/docuXXmentation/arXXticles/?service=sql-database)
-->


