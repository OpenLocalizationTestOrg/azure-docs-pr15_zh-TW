<properties
   pageTitle="設定 SQL server 防火牆概觀 |Microsoft Azure"
   description="瞭解如何設定防火牆 SQL 資料庫伺服器層級和資料庫層級的防火牆規則，以管理存取權。"
   keywords="資料庫防火牆"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor="cgronlun"
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/14/2016"
   ms.author="rickbyh"/>

# <a name="configure-azure-sql-database-firewall-rules---overview"></a>設定 Azure SQL 資料庫防火牆規則\-概觀


> [AZURE.SELECTOR]
- [概觀](sql-database-firewall-configure.md)
- [Azure 入口網站](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL 資料庫提供 Azure 和其他以網際網路為基礎的應用程式的關聯式資料庫服務。 為了協助保護您的資料，防火牆避免所有存取您的資料庫伺服器鍵，直到您指定的電腦有權限。 防火牆授與存取資料庫根據每個要求的原始 IP 位址。

若要設定您的防火牆，您可以建立指定的可接受的 IP 位址範圍的防火牆規則。 您可以建立防火牆規則的伺服器和資料庫層級。

- **伺服器層級的防火牆規則︰**這些規則啟用用戶端存取整個 Azure SQL server，也就是所有資料庫中相同的邏輯伺服器。 這些規則會儲存在**主要**資料庫。 您可以設定伺服器層級的防火牆規則，使用入口網站，或使用 SQL 陳述式。
- **資料庫層級的防火牆規則︰**這些規則啟用用戶端存取個別的資料庫中 Azure SQL 資料庫伺服器。 您可以建立的每個資料庫這些規則，它們會儲存在個別的資料庫。 （您可以建立**主要**資料庫的資料庫層級的防火牆的規則）。這些規則可能很有幫助限制存取特定 （安全） 的資料庫中相同的邏輯伺服器。 資料庫層級的防火牆規則只可以使用 SQL 陳述式來進行設定。

**建議︰**Microsoft 建議使用資料庫層級的防火牆規則，盡可能提升安全性，並讓資料庫更可攜式。 系統管理員和有相同的存取要求的許多資料庫，而且您不想要花個別設定每個資料庫的時間，使用伺服器層級的防火牆規則。


## <a name="firewall-overview"></a>防火牆概觀

一開始的防火牆封鎖所有 TRANSACT-SQL 存取 Azure SQL server。 若要開始使用您的 Azure SQL server，您必須移至 Azure 入口網站，並指定一個或多個啟用存取 Azure SQL server 的伺服器層級的防火牆規則。 您可以使用防火牆規則，指定所允許的 IP 位址範圍，從網際網路]，以及是否 Azure 應用程式可以嘗試連線到您 Azure SQL server。

若要選擇性地授與存取權只是下列其中一項 Azure SQL server 中的資料庫]，您必須建立資料庫層級規則必要的資料庫。 指定資料庫防火牆規則的伺服器層級的防火牆規則中指定的 IP 位址範圍以外的 IP 位址範圍，請確定用戶端 IP 位址是資料庫層級規則中指定範圍內。

連線嘗試從網際網路和 Azure 必須先通過防火牆之前連絡您的 Azure SQL server 或 SQL 資料庫]，如下圖所示。

   ![圖表描述的防火牆設定。][1]

## <a name="connecting-from-the-internet"></a>從網際網路連線

當電腦嘗試從網際網路連線至資料庫伺服器時，防火牆會先檢查完整的防火牆規則集針對要求的原始的 IP 位址︰

- 要求的 IP 位址已在其中一個伺服器層級的防火牆規則中指定的範圍內，如果連線是授與您 Azure SQL 資料庫伺服器。

- 如果要求的 IP 位址無法在其中一個伺服器層級的防火牆規則中指定的範圍內，檢查資料庫層級的防火牆規則。 要求的 IP 位址已在其中一個資料庫層級的防火牆規則中指定的範圍內，如果連線是只授有相符的資料庫層級規則的資料庫。

- 如果要求的 IP 位址沒有任何的伺服器層級或資料庫層級的防火牆規則，連線邀請失敗中指定範圍內。

> [AZURE.NOTE] 若要從本機電腦存取 Azure SQL 資料庫，請確定您的網路和本機電腦上的防火牆可讓 TCP 連接埠 1433年外的通訊。


## <a name="connecting-from-azure"></a>從 Azure 連線

當連線至資料庫伺服器嘗試從 Azure 應用程式時，防火牆驗證所允許 Azure 連線。 設定開始和結束的地址等於 0.0.0.0 防火牆指示允許這些連線。 如果不允許連線的嘗試，要求沒有達到 Azure SQL 資料庫伺服器。

> [AZURE.IMPORTANT] 此選項會設定為允許來自 Azure 包括來自其他客戶的訂閱的所有連線的防火牆。 當選取這個選項，請確定您的登入與使用者權限限制只有授權的使用者存取。

您可以啟用從 Azure 中的兩種方法的連線︰

- 建立新的伺服器時，請在[Microsoft Azure 入口網站](https://portal.azure.com/)中，選取 [**允許存取伺服器 azure 服務**的核取方塊。

- 在[傳統入口網站](http://go.microsoft.com/fwlink/p/?LinkID=161793)，從 [**設定**] 索引標籤，在伺服器上，在 [**允許的服務**] 區段中，按一下**[是]** **Microsoft Azure 服務**。

## <a name="creating-the-first-server-level-firewall-rule"></a>建立第一個的伺服器層級的防火牆規則

使用[Azure 入口網站](https://portal.azure.com/)，或以程式設計方式使用 PowerShell 的 Azure 的 REST API，您可以建立第一個的伺服器層級的防火牆設定。 後續的伺服器層級防火牆規則可以建立並使用這些方法，以及管理為透過 TRANSACT-SQL。 若要改善效能，伺服器層級的防火牆規則會暫時快取資料庫層級。 若要重新整理快取，請參閱[DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx)。 如需有關伺服器層級的防火牆規則的詳細資訊，請參閱[如何︰ 設定使用 Azure 入口網站 Azure SQL server 防火牆](sql-database-configure-firewall-settings.md)。

## <a name="creating-database-level-firewall-rules"></a>建立資料庫層級的防火牆規則

第一個伺服器層級的防火牆設定之後，您可能會想要限制存取特定的資料庫。 如果您的伺服器層級防火牆規則中指定的範圍以外的資料庫層級的防火牆規則中指定的 IP 位址範圍，僅有資料庫層級範圍中的 IP 位址的用戶端才可以存取資料庫。 您可以讓 128 資料庫層級的防火牆規則，為資料庫的最大值。 您可以建立及管理透過 TRANSACT-SQL 母片與使用者的資料庫的資料庫層級的防火牆規則。 如需設定資料庫層級的防火牆規則的詳細資訊，請參閱[sp_set_database_firewall_rule （Azure SQL 資料庫）](https://msdn.microsoft.com/library/dn270010.aspx)。

## <a name="programmatically-managing-firewall-rules"></a>以程式設計方式管理防火牆規則

除了 Azure 入口網站中，以防火牆規則，都可以使用 TRANSACT-SQL REST API 與 PowerShell 的 Azure，以程式設計方式來管理。 下表說明適用於每一種方法的命令集。


### <a name="transact-sql"></a>TRANSACT-SQL

| 類別目錄檢視] 或 [預存程序                                                           | 層級     | 描述                                          |
|--------------------------------------------------------------------------------------------|-----------|------------------------------------------------------|
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx)                   | 伺服器    | 顯示目前的伺服器層級的防火牆規則     |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx)             | 伺服器    | 建立或更新的伺服器層級的防火牆規則       |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx)          | 伺服器    | 移除伺服器層級的防火牆規則                  |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx)        | 資料庫  | 顯示目前的資料庫層級的防火牆規則   |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx)    | 資料庫  | 建立或更新的資料庫層級的防火牆規則 |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) | 資料庫 | 移除資料庫層級的防火牆規則                |

### <a name="rest-api"></a>REST API

| API                  | 層級  | 描述                                                      |
|----------------------|--------|------------------------------------------------------------------|
| [清單防火牆規則](https://msdn.microsoft.com/library/azure/dn505715.aspx)  | 伺服器 | 顯示目前的伺服器層級的防火牆規則                 |
| [建立防火牆規則](https://msdn.microsoft.com/library/azure/dn505712.aspx) | 伺服器 | 建立或更新的伺服器層級的防火牆規則                   |
| [設定防火牆規則](https://msdn.microsoft.com/library/azure/dn505707.aspx)    | 伺服器 | 更新現有的伺服器層級防火牆規則的屬性 |
| [刪除防火牆規則](https://msdn.microsoft.com/library/azure/dn505706.aspx) | 伺服器 | 移除伺服器層級的防火牆規則                              |


### <a name="azure-powershell"></a>Azure PowerShell

| Cmdlet                                                                                              | 層級  | 描述                                                      |
|-----------------------------------------------------------------------------------------------------|--------|------------------------------------------------------------------|
| [取得 AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx)    | 伺服器 | 傳回目前的伺服器層級的防火牆規則                  |
| [新 AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx)    | 伺服器 | 建立新的伺服器層級的防火牆規則                         |
| [設定 AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx)    | 伺服器 | 更新現有的伺服器層級防火牆規則的屬性 |
| [移除 AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) | 伺服器 | 移除伺服器層級的防火牆規則                              |

> [AZURE.NOTE] 可以有設定為 5 分鐘延遲，變更才會生效的防火牆設定與方法很類似。

## <a name="troubleshooting-the-database-firewall"></a>疑難排解資料庫防火牆

Microsoft Azure SQL 資料庫服務的存取未如預期運作時，請考慮下列幾點︰

- **本機的防火牆設定︰**您的電腦存取 Azure SQL 資料庫之前，您可能需要 TCP 連接埠 1433年的電腦上建立的防火牆例外狀況。 如果您要製作 Azure 雲端邊界內的連線，您可能必須開啟其他的連接埠。 如需詳細資訊，請參閱**V12 SQL 資料庫︰ 與內外部** [1433 ADO.NET 4.5] 和 [SQL 資料庫 V12 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)的區段。

- **網路位址轉譯 (NAT):**NAT，因為 IP 位址使用您的電腦連線至 Azure SQL 資料庫可能會不同於您的電腦 IP 設定的設定] 所示的 IP 位址。 若要檢視您的電腦已連線至 Azure 使用的 IP 位址，請登入入口網站，並瀏覽到裝載您的資料庫伺服器上的 [**設定**] 索引標籤。 在 [**允許的 IP 位址**] 區段中，會顯示**目前的用戶端 IP 位址**。 按一下 [**新增**至**允許的 IP 位址**，以允許此電腦存取伺服器。

- **允許清單變更還未採取效果︰**可能為 5 分鐘延遲，變更才會生效 Azure SQL 資料庫防火牆設定與方法很類似。

- **登入未獲授權，或使用不正確的密碼︰**如果登入 Azure SQL 資料庫伺服器上沒有權限，或使用密碼不正確，被拒 Azure SQL 資料庫伺服器的連線。 建立防火牆設定僅提供用戶端的機會，在嘗試連線到您的伺服器。每個用戶端必須提供必要的安全性憑證。 如需備妥登入的詳細資訊，請參閱管理資料庫、 登入和 Azure SQL 資料庫中的使用者。

- **動態 IP 位址︰**如果您有網際網路連線的動態 IP 位址，您都無法透過防火牆，您可以嘗試下列解決方案其中一項︰

 - 尋求指派給您存取 Azure SQL 資料庫伺服器的用戶端電腦的 IP 位址範圍中的網際網路服務提供者 (ISP)，然後將 IP 位址範圍新增為防火牆規則。

 - 取得的靜態 IP 位址改為您的用戶端電腦上，，然後將 IP 位址新增為防火牆規則。

## <a name="next-steps"></a>後續步驟

如何建立伺服器層級和資料庫層級的防火牆規則的文件，請參閱︰

- [設定 Azure SQL 資料庫伺服器層級的防火牆規則，使用 [Azure 入口網站](sql-database-configure-firewall-settings.md)
- [設定使用 T SQL Azure SQL 資料庫伺服器層級和資料庫層級的防火牆規則](sql-database-configure-firewall-settings-tsql.md)
- [設定使用 PowerShell 的 Azure SQL 資料庫伺服器層級的防火牆規則](sql-database-configure-firewall-settings-powershell.md)
- [設定使用 REST API Azure SQL 資料庫伺服器層級的防火牆規則](sql-database-configure-firewall-settings-rest.md)

建立資料庫的教學課程，請參閱[建立 SQL 資料庫中使用 [Azure 入口網站的分鐘](sql-database-get-started.md)。
從開啟的來源或協力廠商應用程式連線至 Azure SQL 資料庫的說明，請參閱[用戶端快速開始到 SQL 資料庫的程式碼範例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。
若要瞭解如何瀏覽至資料庫，請參閱[管理資料庫存取和登入安全性](https://msdn.microsoft.com/library/azure/ee336235.aspx)。



## <a name="additional-resources"></a>其他資源

- [保護您的資料庫](sql-database-security.md)
- [SQL Server 資料庫引擎和 Azure SQL 資料庫的資訊安全中心](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
