<properties
   pageTitle="探索 Azure SQL 資料庫教學課程 |Microsoft Azure"
   description="進一步瞭解 SQL 資料庫功能"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="08/24/2016"
   ms.author="carlrab"/>
   
# <a name="explore-azure-sql-database-tutorials"></a>探索 Azure SQL 資料庫教學課程

下列連結將您帶往每個所列的功能區和簡單的步驟來開始教學課程，每個區域的概觀。 解決方案範圍快速開始示範使用 SQL 資料庫的完整的解決方案根據真實案例中，請參閱[Azure SQL 資料庫解決方案快速啟動](sql-database-solution-quick-starts.md)]。

## <a name="using-sql-server-management-studio"></a>使用 SQL Server Management Studio 中

在下列教學課程中，您將學習如何使用 SQL Server Management Studio 中管理及查詢 Azure SQL 資料庫。


> [AZURE.IMPORTANT] 建議您永遠保持同步處理與更新 Microsoft Azure SQL 資料庫使用 Management Studio 中的最新版本。 [更新 SQL Server Management Studio 中](https://msdn.microsoft.com/library/mt238290.aspx)。


| 教學課程  | 描述  |
|---|---|---|
| [連線到 Azure SQL 資料庫伺服器層級的本金登入](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| 在本教學課程中，您可以瞭解如何連線到 Azure SQL 資料庫伺服器層級的本金登入。|
| [為使用者連線到 Azure SQL 資料庫](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | 在本教學課程中，您將學習如何使用資料庫層級的使用者帳戶 Azure SQL 資料庫連線。|
||||

## <a name="elastic-pools"></a>彈性的資料庫

在下列教學課程中，您將學習如何使用[彈性的集區](sql-database-elastic-pool.md)管理有廣泛不同及預期使用模式的多個資料庫的效能目標。

| 教學課程  | 描述  |
|---|---|---|
| [建立彈性的資料庫](sql-database-elastic-pool-create-portal.md) | 在本教學課程中，您可以瞭解如何建立可調整 Azure SQL 資料庫的資料庫。 |
| [監控彈性的資料庫](sql-database-elastic-pool-manage-portal.md#elastic-database-monitoring) | 在本教學課程中，您可以瞭解如何監控個別的彈性資料庫的潛在問題。 |
| [新增資料庫資源的提醒](sql-database-elastic-pool-manage-portal.md#add-an-alert-to-a-pool-resource) | 在本教學課程中，您可以瞭解如何新增規則到電子郵件傳送給個人或提醒字串 URL 端點，當您設定使用閾值資源的資源。 |
| [將資料庫移到彈性的資料庫](sql-database-elastic-pool-manage-portal.md#move-a-database-into-an-elastic-pool) | 在本教學課程中，您可以瞭解如何將資料庫移到彈性的資料庫。 |
| [將資料庫移出彈性的資料庫](sql-database-elastic-pool-manage-portal.md#move-a-database-out-of-an-elastic-pool) | 在本教學課程中，您可以瞭解如何將資料庫移出彈性的資料庫。 |
| [變更效能集區設定](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) | 在本教學課程中，您可以瞭解如何調整集區的效能和儲存空間限制。 |
||||

## <a name="elastic-database-jobs"></a>彈性的資料庫作業

在下列教學課程中，您將學習如何使用[彈性的資料庫作業](sql-database-elastic-jobs-overview.md)。

| 教學課程  | 描述  |
|---|---|---|
| [開始使用彈性的資料庫工具](sql-database-elastic-scale-get-started.md) | 在本教學課程中，您可以瞭解如何使用彈性的資料庫工具使用簡單的 sharded 應用程式的功能。 |
| [Azure SQL 資料庫彈性工作快速入門](sql-database-elastic-jobs-getting-started.md)  | 在此教學課程中，您將瞭解如何以如何建立及管理工作的管理群組的相關的資料庫。  | 
||||

## <a name="elastic-queries"></a>彈性的查詢

在下列教學課程中，您將學習如何執行[彈性的查詢](sql-database-elastic-query-overview.md)。 

| 教學課程  | 描述  |
|---|---|---|
| [查詢在水平分割 (sharded) 資料庫）](sql-database-elastic-query-getting-started.md) | 您可以在本教學課程，瞭解如何從水平分割 (sharded) 資料庫使用[彈性的查詢](sql-database-elastic-query-overview.md)中的所有資料庫建立報表 |
| [查詢在垂直分割資料庫）](sql-database-elastic-query-getting-started-vertical.md#create-database-objects) | 您可以在本教學課程，瞭解如何從中的所有資料庫建立報表垂直查詢[彈性](sql-database-elastic-query-overview.md)的資料庫 |
| [移轉現有的資料庫，延展](sql-database-elastic-convert-to-use-elastic-tools.md)| 在本教學課程中，您瞭解水平不按比例縮放 （晶怪） Azure SQL 資料庫。 |
||||

## <a name="performance-optimization"></a>效能最佳化

在下列教學課程中，您將學習最佳化[效能的單一的資料庫](sql-database-performance-guidance.md)。 最佳化效能的多個資料庫，請參閱[彈性的資料庫](#elastic-pools)。

| 教學課程  | 描述  |
|---|---|---|
| [變更您的資料庫的服務層和效能層級](sql-database-scale-up.md#change-the-service-tier-and-performance-level-of-your-database) | 在本教學課程中，您可以瞭解如何縮放，或使用服務層 Azure SQL 資料庫的效能不按比例縮放。 |
| [SQL 資料庫顧問查詢效能充分](sql-database-performance.md#performance-overview) | 在本教學課程中，您可以瞭解如何開啟並使用 SQL 資料庫 Advisor 查詢效能充分。|
| [SQL 資料庫顧問效能的建議](sql-database-advisor.md#viewing-recommendations) | 在本教學課程中，您可以瞭解如何檢視，並套用 SQL 資料庫顧問效能的建議。 |
| [檢閱上方 CPU 使用查詢](sql-database-query-performance.md#review-top-cpu-consuming-queries)| 在本教學課程中，您可以瞭解如何使用 SQL 資料庫顧問查詢效能充分檢閱上方的 CPU 使用查詢。|
| [檢視個別的查詢詳細資料](sql-database-query-performance.md#viewing-individual-query-details)| 在本教學課程中，您可以瞭解如何使用 SQL 資料庫顧問查詢效能充分檢視個別的查詢效能詳細資料。|
||||

## <a name="sql-database-migration-and-archive"></a>SQL 資料庫移轉與封存 

在下列教學課程中，您將學習需[移轉現有的 SQL Server 資料庫到 Azure SQL 資料庫](sql-database-cloud-migrate.md)。

| 教學課程  | 描述  |
|---|---|---|
| [偵測使用 SQL Server Data Tools for Visual Studio 的相容性問題](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | 在本教學課程中，您可以瞭解如何使用 SQL Server Data Tools for Visual Studio 決定 Azure SQL 資料庫相容性。 |
| [使用 SQL Server Data Tools for Visual Studio 修正相容性問題](sql-database-cloud-migrate-fix-compatibility-issues-ssdt#fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | 在本教學課程中，您可以瞭解如何使用 SQL Server Data Tools for Visual Studio 修正 Azure SQL 資料庫的相容性問題。 |
| [決定使用 SqlPackage.exe SQL 資料庫相容性](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md#using-sqlpackageexe) | 在本教學課程中，您可以瞭解如何使用 SQLPackage.exe 命令列公用程式，來判斷 Azure SQL 資料庫相容性。|
| [決定使用 SSMS SQL 資料庫相容性](sql-database-cloud-migrate-determine-compatibility-ssms.md#using-sql-server-management-studio) |在本教學課程中，您可以瞭解如何使用 SQL Server Management Studio 決定 Azure SQL 資料庫相容性。|
| [將 SQL Server 資料庫移轉到使用部署至 Microsoft Azure 資料庫] 精靈的資料庫的 SQL 資料庫](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md#use-the-deploy-database-to-microsoft-azure-database-wizard) | 在本教學課程中，您將學習如何將相容的 SQL Server 資料庫移轉到 SQL Server Management Studio 中使用 Microsoft Azure 資料庫] 精靈部署資料庫 Azure SQL 資料庫。
| [將 SQL Server 資料庫匯出至 SSMS BACPAC 檔案](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | 在本教學課程中，您將學習如何使用 SQL Server Management Studio 中的 [匯出資料層應用程式精靈 BACPAC 檔案匯出相容的 SQL Server 資料庫。|
| [將 SQL Server 資料庫匯出至 SqlPackage BACPAC 檔案](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | 在本教學課程中，您將學習如何使用 SQLPackage.exe 命令列公用程式 BACPAC 檔案匯出相容的 SQL Server 資料庫。|
| [BACPAC 檔案匯入使用 SSMS Azure SQL 資料庫](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | 在本教學課程中，您將學習如何將 Azure SQL 資料庫從 SQL Server Management Studio 中使用 [匯出資料層應用程式精靈 BACPAC 檔案匯入資料庫。 |
| [BACPAC 檔案匯入使用 SqlPackage Azure SQL 資料庫](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage) | 在本教學課程中，您將學習如何將 SQLPackage 命令列公用程式 BACPAC 檔案從 Azure SQL 資料庫匯入資料庫。 |
| [BACPAC 檔案匯入使用 Azure 入口網站 Azure SQL 資料庫](sql-database-import.md) | 在本教學課程中，您將學習如何將從 BACPAC 檔案中使用 [Azure 入口網站 Azure blob 儲存 Azure SQL 資料庫匯入資料庫。|
| [BACPAC 檔案匯入使用 PowerShell 的 Azure SQL 資料庫](sql-database-import-powershell.md) | 在本教學課程中，您將學習如何將從 BACPAC 檔案使用 PowerShell 的 Azure SQL 資料庫匯入資料庫。|
| [封存使用 Azure 入口網站的 Azure SQL 資料庫](sql-database-export.md#export-your-database) | 在本教學課程中，您可以瞭解如何封存 BACPAC 檔案 Azure 入口網站 Azure SQL 資料庫。 |
| [封存使用 PowerShell 的 Azure SQL 資料庫](sql-database-export-powershell.md) | 在本教學課程中，您可以瞭解如何封存 BACPAC 檔案使用 PowerShell 的 Azure SQL 資料庫。 |
| [複製使用 Azure 入口網站的 Azure SQL 資料庫](sql-database-copy.md#copy-your-sql-database) | 在本教學課程中，您可以瞭解如何複製使用 Azure 入口網站的 Azure SQL 資料庫。 |
| [複製使用 PowerShell 的 Azure SQL 資料庫](sql-database-copy-powershell#copy-your-sql-database) | 在本教學課程中，您可以瞭解如何複製使用 PowerShell 的 Azure SQL 資料庫。 |
| [複製 SQL Azure SQL 資料庫](sql-database-copy-transact-sql.md#copy-your-sql-database) | 在本教學課程中，您可以瞭解如何複製 SQL Azure SQL 資料庫。 |
||||

##<a name="develop"></a>開發

在下列教學課程中，您將瞭解[SQL 資料庫開發](sql-database-develop-overview.md)和使用[連線文件庫](sql-database-libraries.md)。

| 教學課程  | 描述  |
|---|---|---|
| [使用.NET (C#) 連線到 SQL 資料庫](sql-database-develop-dotnet-simple.md) | 在本教學課程中，您可以瞭解如何連線到使用 C# Azure SQL 資料庫。 |
| [使用 Java 連線至 SQL 資料庫](sql-database-develop-java-simple.md) | 在本教學課程中，您可以瞭解如何連線到使用 Java Azure SQL 資料庫。 |
| [使用 Node.js 連線至 SQL 資料庫](sql-database-develop-nodejs-simple.md) | 在本教學課程中，您可以瞭解如何連線到使用 Node.js Azure SQL 資料庫。 |
| [使用 PHP 連線至 SQL 資料庫](sql-database-develop-php-simple.md) | 在本教學課程中，您可以瞭解如何連線到使用 PHP Azure SQL 資料庫。 |
| [使用 Python 連線至 SQL 資料庫](sql-database-develop-python-simple.md) | 在本教學課程中，您可以瞭解如何連線到使用 Python Azure SQL 資料庫。 |
| [使用 [注音標示連線至 SQL 資料庫](sql-database-develop-ruby-simple.md) | 在本教學課程中，您可以瞭解如何連線到使用 [注音標示 Azure SQL 資料庫。 |
||||
 
## <a name="database-access"></a>資料庫存取

在下列教學課程中，您會學習如何[建立及管理登入與使用者](sql-database-manage-logins.md)。

| 教學課程  | 描述  |
|---|---|---|
| [建立 Azure SQL 資料庫伺服器層級的防火牆規則使用 Azure 入口網站](sql-database-configure-firewall-settings.md)  | 在本教學課程中，您可以瞭解如何設定使用 Azure 入口網站的 SQL 資料庫伺服器層級防火牆。  |
| [建立使用 TRANSACT-SQL 資料庫層級的防火牆規則](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules) | 在本教學課程中，您將學習如何建立使用 SQL 資料庫層級的防火牆規則。|
| [管理使用 SQL server 層級的防火牆規則](sql-database-configure-firewall-settings-tsql.md#manage-server-level-firewall-rules-through-transact-sql) | 在本教學課程中，您將學習如何管理使用 SQL Azure SQL 資料庫伺服器層級的防火牆。|
| [管理伺服器層級的防火牆規則使用 PowerShell](sql-database-configure-firewall-settings-powershell.md#manage-firewall-rules-using-powershell) | 在本教學課程中，您將學習如何管理使用 PowerShell 的 Azure SQL 資料庫伺服器層級防火牆。|
| [管理伺服器層級的防火牆規則使用 REST API](sql-database-configure-firewall-settings-rest.md#manage-firewall-rules-using-the-service-management-rest-api) | 在本教學課程中，您將學習如何管理使用重設 API Azure SQL 資料庫伺服器層級的防火牆。|
| [連線到 Azure SQL 資料庫伺服器層級的本金登入](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| 在本教學課程中，您可以瞭解如何連線到 Azure SQL 資料庫伺服器層級的本金登入。|
| [授與存取資料庫至登入](sql-database-manage-logins.md#granting-database-access-to-a-login() | 在本教學課程中，您可以瞭解如何授與資料庫的存取權的伺服器層級登入。|
| [建立新的資料庫使用者使用 SSMS](sql-database-get-started-security.md#create-new-database-user-using-ssms) | 在本教學課程中，您可以瞭解如何使用 SSMS 現有資料庫中建立新的資料庫使用者。|
| [授與新的資料庫使用者 db_owner 權限](sql-database-get-started-security.md#grant-new-database-user-dbowner-permissions) | 在本教學課程中，您可以瞭解如何將使用者 db_owner 權限授與現有的資料庫。|
| [為使用者連線到 Azure SQL 資料庫](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | 在本教學課程中，您可以瞭解如何連線到 Azure SQL 資料庫使用的資料庫層級的使用者帳戶。|
||||


## <a name="data-security"></a>資料的安全性

在下列教學課程中，您將學習如何[保護 Azure SQL 資料庫的資料](sql-database-security.md)。 

| 教學課程  | 描述  |
|---|---|---|
| [啟用威脅偵測資料庫使用 Azure 入口網站](sql-database-threat-detection-get-started.md#set-up-threat-detection-for-your-database) | 在本教學課程中，您可以瞭解如何設定在資料庫的 Azure 入口網站的威脅偵測。|
| [保護機密資料 uisng 永遠加密](sql-database-always-encrypted-azure-key-vault.md) | 在本教學課程中，您可以使用永遠加密精靈來保護機密 Azure SQL 資料庫中的資料。|
| [使用透明資料加密的安全 senstive 資料](https://msdn.microsoft.com/library/dn948096.aspx)| 在本教學課程中，您可以瞭解如何使用透明資料加密保護 senstive 資料。|
| [加密資料欄](https://msdn.microsoft.com/library/ms179331.aspx)| 在本教學課程中，您可以瞭解如何加密使用 TRANSACT-SQL 中的資料欄。|
| [設定 [動態資料遮罩](sql-database-dynamic-data-masking-get-started.md#set-up-dynamic-data-masking-for-your-database-using-the-azure-portal)  | 在本教學課程中，您可以瞭解如何設定 Azure SQL 資料庫的動態資料遮罩。 |
||||

## <a name="business-continuity-and-query-scale-out"></a>業務連續性和查詢擴充

在下列教學課程中，您將學習如何使用[地理還原和作用中的地理複寫](sql-database-business-continuity.md)reccover 至從錯誤]，業務連續性和查詢擴充。

| 教學課程  | 描述  |
|---|---|---|
| [Azure SQL 資料庫還原至先前的點，Azure 入口網站的時間](sql-database-point-in-time-restore-portal.md)| 在本教學課程中，您可以瞭解如何將您的資料庫至之前還原使用 Azure 入口網站的時間。|
| [使用 PowerShell 的時間點還原 Azure SQL 資料庫](sql-database-point-in-time-restore-powershell.md) | 您可以在本教學課程，瞭解如何還原至之前的資料庫，時間使用 PowerShell|
| [還原已刪除的 Azure SQL 資料庫，使用 [Azure 入口網站](sql-database-restore-deleted-database-portal.md) | 在本教學課程中，您可以瞭解如何還原已刪除的資料庫，使用 [Azure 入口網站。|
| [還原已刪除使用 PowerShell 的 Azure SQL 資料庫](sql-database-restore-deleted-database-powershell.md) | 在本教學課程中，您可以瞭解如何還原已刪除的資料庫，使用 PowerShell。|
| [使用 [Azure 入口網站的 Azure SQL 資料庫設定地理複寫](sql-database-geo-replication-portal.md)| 在本教學課程中，您可以瞭解如何設定使用中地理-使用複寫 Azure 入口網站。|
| [使用 PowerShell 的 Azure SQL 資料庫設定地理複寫](sql-database-geo-replication-powershell.md)| 您可以在本教學課程，瞭解如何設定使用中的地理複寫使用 PowerShell。|
| [使用 SQL Azure SQL 資料庫設定地理複寫](sql-database-geo-replication-transact-sql.md)| 您可以在本教學課程，瞭解如何設定作用中的地理複寫使用 TRANSACT-SQL。|
| [使用 [Azure 入口網站的 Azure SQL 資料庫啟動計畫之內的容錯移轉](sql-database-geo-replication-failover-portal.md) | 在本教學課程，瞭解如何容錯移轉至地理複寫副複本使用 Azure 入口網站。|
| [使用 PowerShell 的 Azure SQL 資料庫啟動計畫之內容錯移轉](sql-database-geo-replication-failover-powershell.md) | 在此教學課程中，您將瞭解到地理複寫次要複本使用 PowerShell 容錯移轉的方式。|
| [使用 SQL Azure SQL 資料庫啟動計畫之內的容錯移轉](sql-database-geo-replication-failover-transact-sql.md) | 在此教學課程中，您將瞭解到地理複寫次要複本使用 TRANSACT-SQL 容錯移轉的方式。|
||||

## <a name="data-sync"></a>資料同步處理

在本教學課程中，您將學習[資料同步處理](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)。

| 教學課程  | 描述  |
|---|---|---| 
| [快速入門 Azure SQL 資料同步處理 （預覽版本）](sql-database-get-started-sql-data-sync.md)  | 在此教學課程中，您將瞭解 Azure SQL 資料同步處理使用 Azure 傳統入口網站的基本工作。 |
||||

## <a name="next-steps"></a>後續步驟

[探索 Azure SQL 資料庫解決方案快速入門](sql-database-solution-quick-starts.md)
