<properties
    pageTitle="設定使用 PowerShell 的 Azure SQL 資料庫伺服器層級的防火牆規則 |Microsoft Azure"
    description="瞭解如何設定存取 SQL Azure 資料庫的 IP 位址的防火牆。"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/09/2016"
    ms.author="sstein"/>


# <a name="configure-azure-sql-database-server-level-firewall-rules-by-using-powershell"></a>設定使用 PowerShell 的 Azure SQL 資料庫伺服器層級的防火牆規則


> [AZURE.SELECTOR]
- [概觀](sql-database-firewall-configure.md)
- [Azure 入口網站](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Azure SQL 資料庫使用防火牆規則，以允許您的伺服器和資料庫的連線。 您可以在 SQL 資料庫伺服器選擇性地允許存取資料庫中定義主要資料庫或使用者資料庫的伺服器層級和資料庫層級的防火牆的設定。

> [AZURE.IMPORTANT] 若要允許從 Azure 的應用程式連線至資料庫伺服器，必須先啟用 Azure 連線。 如需有關防火牆規則和 azure 啟用連線的詳細資訊，請參閱[Azure SQL 資料庫防火牆](sql-database-firewall-configure.md)。 如果您要製作 Azure 雲端邊界內的連線，您可能必須開啟一些額外的 TCP 連接埠。 如需詳細資訊，請參閱 「 V12 的 SQL 資料庫︰ 與內外部 」 的[限制，超出 1433 ADO.NET 4.5] 和 [SQL 資料庫 V12 的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)] 區段。


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-server-firewall-rules"></a>建立伺服器防火牆規則

伺服器層級的防火牆，您可以建立規則，更新，並使用 PowerShell 的 Azure 刪除。

若要建立新的伺服器層級的防火牆規則，請執行 [新增-AzureRmSqlServerFirewallRule] （https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx) 指令程式。 下列範例會啟用的 IP 位址範圍 Contoso 在伺服器上。

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'       

若要修改現有的伺服器層級防火牆規則，請執行 [設定-AzureRmSqlServerFirewallRule] （https://msdn.microsoft.com/library/azure/mt603789(v=azure.300\).aspx) 指令程式。 下列範例會變更規則命名 ContosoFirewallRule 可接受的 IP 位址的範圍。

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'

若要刪除現有的伺服器層級防火牆規則，請執行 [移除-AzureRmSqlServerFirewallRule] （https://msdn.microsoft.com/library/azure/mt603588(v=azure.300\).aspx) 指令程式。 下列範例會刪除命名 ContosoFirewallRule 的規則。

    Remove-AzureRmSqlServerFirewallRule –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'


## <a name="manage-firewall-rules-by-using-powershell"></a>使用 PowerShell 來管理防火牆規則

您也可以使用 PowerShell 來管理防火牆規則。 如需詳細資訊，請參閱下列主題︰

* [新 AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx)
* [移除 AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603588(v=azure.300\).aspx)
* [設定 AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603789(v=azure.300\).aspx)
* [取得 AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603586(v=azure.300\).aspx)


## <a name="next-steps"></a>後續步驟

有關如何使用 TRANSACT-SQL 建立伺服器層級和資料庫層級的防火牆規則，請參閱[使用 T SQL 設定 Azure SQL 資料庫伺服器層級和資料庫層級的防火牆規則](sql-database-configure-firewall-settings-tsql.md)。

如需如何建立使用其他方法的伺服器層級的防火牆規則，請參閱資訊︰

- [設定 Azure SQL 資料庫伺服器層級的防火牆規則，使用 [Azure 入口網站](sql-database-configure-firewall-settings.md)
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
