<properties
    pageTitle="使用 T SQL azure SQL 資料庫伺服器層級和資料庫層級的防火牆規則 |Microsoft Azure"
    description="瞭解如何設定存取 SQL Azure 資料庫的 IP 位址的防火牆。"
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
    ms.author="rickbyh"/>


# <a name="configure-azure-sql-database-server-level-and-database-level-firewall-rules-using-t-sql"></a>設定使用 T SQL Azure SQL 資料庫伺服器層級和資料庫層級的防火牆規則


> [AZURE.SELECTOR]
- [概觀](sql-database-firewall-configure.md)
- [Azure 入口網站](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL 資料庫使用防火牆規則，以允許您的伺服器和資料庫的連線。 您可以選擇性地允許資料庫的存取權 Azure SQL 資料庫伺服器中定義母片或使用者資料庫的伺服器層級和資料庫層級的防火牆的設定。

> [AZURE.IMPORTANT] 若要允許從 Azure 的應用程式連線至資料庫伺服器，必須先啟用 Azure 連線。 如需有關防火牆規則和 azure 啟用連線的詳細資訊，請參閱[Azure SQL 資料庫防火牆](sql-database-firewall-configure.md)。 如果您要製作 Azure 雲端邊界內的連線，您可能必須開啟一些額外的 TCP 連接埠。 如需詳細資訊，請參閱**V12 SQL 資料庫︰ 與內外部** [1433 ADO.NET 4.5] 和 [SQL 資料庫 V12 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)的區段


## <a name="server-level-firewall-rules"></a>伺服器層級的防火牆規則

僅限伺服器層級的本金登入或 Azure Active Directory 系統管理員可以建立使用 TRANSACT-SQL 的伺服器層級的防火牆規則。

1. 啟動 [查詢] 視窗，並使用 SQL Server Management Studio 中連線到虛擬主要資料庫。
2. 伺服器層級的防火牆規則可以選取、 建立、 更新，或從 [查詢] 視窗中刪除。
3. 若要建立或更新的伺服器層級的防火牆規則，請執行`sp_set_firewall_rule`預存程序。 下列範例會啟用的 IP 位址範圍 Contoso 在伺服器上。<br/>首先請查看哪些規則已存在於。

        SELECT * FROM sys.firewall_rules ORDER BY name;

    接下來，新增防火牆規則。

        EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
            @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

    若要刪除的伺服器層級的防火牆規則，請執行 sp_delete_firewall_rule 預存程序。 下列範例會刪除命名 ContosoFirewallRule 的規則。
 
        EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
 如需有關這些預存程序的詳細資訊，請參閱[sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx)和[sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx)。

## <a name="database-level-firewall-rules"></a>資料庫層級的防火牆規則

只有具備 [**控制**] 權限 （例如資料庫擁有者） 資料庫的資料庫使用者可以建立資料庫層級的防火牆規則。

1. 建立您的 IP 位址的伺服器層級防火牆之後, 啟動透過 [傳統] 入口網站或 SQL Server Management Studio 中的查詢] 視窗。
2. 連線到您要建立資料庫層級的防火牆規則資料庫。

    若要建立新的或更新現有的資料庫層級的防火牆規則，請執行`sp_set_database_firewall_rule`預存程序。 下列範例會建立新的防火牆規則命名 ContosoFirewallRule。
 
        EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
            @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
    若要刪除現有的資料庫層級的防火牆規則，請執行`sp_delete_database_firewall_rule`預存程序。 下列範例會刪除命名 ContosoFirewallRule 的規則。
`
   接著 sp_delete_database_firewall_rule @name = N'ContosoFirewallRule 」

如需有關這些預存程序的詳細資訊，請參閱[sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx)和[sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx)。

## <a name="next-steps"></a>後續步驟

針對如何建立伺服器層級的防火牆規則的文件使用其他方法，請參閱︰ 

- [設定 Azure SQL 資料庫伺服器層級的防火牆規則，使用 [Azure 入口網站](sql-database-configure-firewall-settings.md)
- [設定使用 PowerShell 的 Azure SQL 資料庫伺服器層級的防火牆規則](sql-database-configure-firewall-settings-powershell.md)
- [設定使用 REST API Azure SQL 資料庫伺服器層級的防火牆規則](sql-database-configure-firewall-settings-rest.md)

建立資料庫的教學課程，請參閱[建立 SQL 資料庫中使用 [Azure 入口網站的分鐘](sql-database-get-started.md)。
從開啟的來源或協力廠商應用程式連線至 Azure SQL 資料庫的說明，請參閱[用戶端快速開始到 SQL 資料庫的程式碼範例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。
若要瞭解如何瀏覽至資料庫，請參閱[管理資料庫存取和登入安全性](https://msdn.microsoft.com/library/azure/ee336235.aspx)。


## <a name="additional-resources"></a>其他資源

- [保護您的資料庫](sql-database-security.md)
- [SQL Server 資料庫引擎和 Azure SQL 資料庫的資訊安全中心](https://msdn.microsoft.com/library/bb510589)
