<properties
    pageTitle="設定 SQL 資料庫伺服器層級的防火牆規則 |Microsoft Azure"
    description="瞭解如何設定存取 Azure SQL server 的 IP 位址的防火牆。"
    services="sql-database"
    documentationCenter=""
    authors="BYHAM"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article" 
    ms.date="08/30/2016"
    ms.author="rickbyh;carlrab"/>


# <a name="configure-an-azure-sql-database-server-level-firewall-rule-using-the-azure-portal"></a>設定 Azure SQL 資料庫伺服器層級的防火牆規則使用 Azure 入口網站


> [AZURE.SELECTOR]
- [概觀](sql-database-firewall-configure.md)
- [Azure 入口網站](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)

Azure SQL server 會使用防火牆規則，允許連線至您的伺服器和資料庫。 您可以在 Azure SQL server 邏輯伺服器選擇性地允許存取資料庫中定義母片或使用者資料庫的伺服器層級和資料庫層級的防火牆的設定。 本主題探討伺服器層級的防火牆規則。

> [AZURE.IMPORTANT] 若要允許從 Azure 應用程式連線到您 Azure SQL server，您必須啟用 Azure 連線。 若要瞭解如何防火牆規則工作，請參閱[如何設定 Azure SQL server 防火牆\-概觀](sql-database-firewall-configure.md)。 如果您要製作 Azure 雲端邊界內的連線，您可能必須開啟一些額外的 TCP 連接埠。 如需詳細資訊，請參閱**V12 SQL 資料庫︰ 與內外部** [1433 ADO.NET 4.5] 和 [SQL 資料庫 V12 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)的區段

**建議︰**系統管理員和許多資料庫具有相同的存取要求，而且您不想要花個別設定每個資料庫的時間，使用伺服器層級的防火牆規則。 Microsoft 建議使用資料庫層級的防火牆規則可能的話，若要提升安全性，並讓資料庫更可攜式。

[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>管理現有的伺服器層級的防火牆規則，透過 Azure 入口網站

重複步驟，管理伺服器層級的防火牆規則。

- 若要新增目前的電腦，請按一下 [新增用戶端 IP。
- 若要新增額外的 IP 位址，在 [規則名稱、 開始 IP 位址及輸入結束 IP 位址。
- 若要修改現有的規則，請按一下任何規則中的欄位，並修改。
- 若要刪除現有的規則，將游標停留在規則直到結尾的資料列出現的 [X]。 按一下 [X] 以移除此規則]。

按一下 [**儲存**] 儲存變更。

## <a name="next-steps"></a>後續步驟

如何文章︰ 如何使用 TRANSACT-SQL 建立伺服器層級和資料庫層級的防火牆規則，請參閱[使用 T SQL 設定 Azure SQL 資料庫伺服器層級和資料庫層級的防火牆規則](sql-database-configure-firewall-settings-tsql.md)。 

針對如何建立伺服器層級的防火牆規則的文件使用其他方法，請參閱︰ 

- [設定使用 PowerShell 的 Azure SQL 資料庫伺服器層級的防火牆規則](sql-database-configure-firewall-settings-powershell.md)
- [設定使用 REST API Azure SQL 資料庫伺服器層級的防火牆規則](sql-database-configure-firewall-settings-rest.md)

建立資料庫的教學課程，請參閱[建立 SQL 資料庫中使用 [Azure 入口網站的分鐘](sql-database-get-started.md)。
從開啟的來源或協力廠商應用程式連線至 Azure SQL 資料庫的說明，請參閱[用戶端快速開始到 SQL 資料庫的程式碼範例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。
若要瞭解如何瀏覽至資料庫，請參閱[管理資料庫存取和登入安全性](https://msdn.microsoft.com/library/azure/ee336235.aspx)。


## <a name="additional-resources"></a>其他資源

- [保護您的資料庫](sql-database-security.md)
- [SQL Server 資料庫引擎和 Azure SQL 資料庫的資訊安全中心](https://msdn.microsoft.com/library/bb510589)


<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 
