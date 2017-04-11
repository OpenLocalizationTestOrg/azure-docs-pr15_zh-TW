<properties
    pageTitle="SQL 資料庫教學課程︰ 開始使用安全性"
    description="瞭解如何建立使用者帳戶存取及管理資料庫。"
    keywords=""
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/17/2016"
    ms.author="carlrab"/>

# <a name="sql-database-tutorial-create-sql-database-user-accounts-to-access-and-manage-a-database"></a>SQL 資料庫教學課程︰ 建立使用者帳戶存取及管理資料庫的 SQL 資料庫


> [AZURE.SELECTOR]
- [開始使用教學課程](sql-database-get-started-security.md)
- [授與存取權](sql-database-manage-logins.md)

在本教學課程中，您可以瞭解如何使用 SQL Server 管理 Studio (SSMS) 至︰

- 登入 SQL 資料庫伺服器層級的本金登入。
- 建立 SQL 資料庫使用者帳戶。
- 授與 SQL 資料庫使用者[db_owner 權限](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0)。
- 連線到與使用者帳戶的伺服器層級本金不是 SQL 資料庫。

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]


[AZURE.INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]


## <a name="next-steps"></a>後續步驟
現在，當您完成本教學課程中 SQL 資料庫，並建立使用者帳戶並授與使用者帳戶 dbo 權限，您準備好深入瞭解[SQL 資料庫安全性](sql-database-manage-logins.md)。


