<properties
    pageTitle="Azure SQL 資料庫伺服器層級的防火牆規則使用 REST API |Microsoft Azure"
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


#  <a name="configure-azure-sql-database-server-level-firewall-rules-using-the-rest-api"></a>設定使用 REST API Azure SQL 資料庫伺服器層級的防火牆規則


> [AZURE.SELECTOR]
- [概觀](sql-database-firewall-configure.md)
- [Azure 入口網站](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL 資料庫使用防火牆規則，以允許您的伺服器和資料庫的連線。 您可以選擇性地允許資料庫的存取權 Azure SQL 資料庫伺服器中定義母片或使用者資料庫的伺服器層級和資料庫層級的防火牆的設定。

> [AZURE.IMPORTANT] 若要允許從 Azure 的應用程式連線至資料庫伺服器，必須先啟用 Azure 連線。 如需有關防火牆規則和 azure 啟用連線的詳細資訊，請參閱[Azure SQL 資料庫防火牆](sql-database-firewall-configure.md)。 如果您要製作 Azure 雲端邊界內的連線，您可能必須開啟一些額外的 TCP 連接埠。 如需詳細資訊，請參閱**V12 SQL 資料庫︰ 與內外部** [1433 ADO.NET 4.5] 和 [SQL 資料庫 V12 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)的區段


## <a name="manage-server-level-firewall-rules-through-rest-api"></a>管理伺服器層級的防火牆規則，透過 REST API
1. 管理透過 REST API 的防火牆規則必須經過驗證。 資訊，請參閱[開發人員的授權和 Azure 資源管理員 API 指南](../resource-manager-api-authentication.md)。
2. 伺服器層級可以建立規則，更新或刪除使用 REST API

    建立或更新的伺服器層級的防火牆規則，請執行下列放入方法︰
 
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}
    
    邀請內文

        {
         "properties": { 
            "startIpAddress": "{start-ip-address}", 
            "endIpAddress": "{end-ip-address}
            }
        } 
 

    若要移除現有的伺服器層級防火牆規則，請執行刪除方法使用下列步驟︰
     
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}


## <a name="manage-firewall-rules-using-the-rest-api"></a>管理使用 REST API 的防火牆規則

* [建立或更新防火牆規則](https://msdn.microsoft.com/library/azure/mt445501.aspx)
* [刪除防火牆規則](https://msdn.microsoft.com/library/azure/mt445502.aspx)
* [取得防火牆規則](https://msdn.microsoft.com/library/azure/mt445503.aspx)
* [列出所有設定防火牆規則](https://msdn.microsoft.com/library/azure/mt604478.aspx)
 
## <a name="next-steps"></a>後續步驟

如何文章︰ 如何使用 TRANSACT-SQL 建立伺服器層級和資料庫層級的防火牆規則，請參閱[使用 T SQL 設定 Azure SQL 資料庫伺服器層級和資料庫層級的防火牆規則](sql-database-configure-firewall-settings-tsql.md)。 

針對如何建立伺服器層級的防火牆規則的文件使用其他方法，請參閱︰ 

- [設定 Azure SQL 資料庫伺服器層級的防火牆規則，使用 [Azure 入口網站](sql-database-configure-firewall-settings.md)
- [設定使用 PowerShell 的 Azure SQL 資料庫伺服器層級的防火牆規則](sql-database-configure-firewall-settings-powershell.md)

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

 
