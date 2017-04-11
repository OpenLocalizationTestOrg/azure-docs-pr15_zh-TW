<properties
   pageTitle="Azure SQL 資料庫一般限制和準則"
   description="本頁面說明一些一般限制 Azure SQL 資料庫的互通性及支援的區域。"
   services="sql-database"
   documentationCenter="na"
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/06/2016"
   ms.author="carlrab" />

# <a name="azure-sql-database-general-limitations-and-guidelines"></a>Azure SQL 資料庫一般限制和準則

本主題提供一般限制和準則 Azure SQL 資料庫。 瞭解的配額與資源管理支援，請參閱本主題結尾的[其他資源](#additional-guidelines)。

## <a name="connectivity-and-authentication"></a>連線與驗證

  - Windows 驗證不受支援。 請參閱[管理資料庫及 Azure SQL 資料庫中的登入](sql-database-manage-logins.md)。 不過，Azure Active Directory 驗證僅支援特定的限制。 請參閱[連線至 SQL Azure Active Directory 驗證使用的資料庫](sql-database-aad-authentication.md)。

  - Microsoft Azure SQL 資料庫支援表格式資料串流 (TDS) 通訊協定的用戶端版本 7.3 或更新版本。

  - 僅 TCP/IP 允許連線。

  - 不支援的 SQL Server 2008 SQL Server 瀏覽器，因為 Microsoft Azure SQL 資料庫並沒有動態連接埠、 只連接埠 1433年。

## <a name="sql-server-agentjobs"></a>SQL Server 代理程式/工作

Microsoft Azure SQL 資料庫不支援 SQL Server 代理程式，不過您可以使用彈性的工作，一個用於許多資料庫中執行的工作。 如需關於彈性工作的詳細資訊，請參閱[彈性的工作](sql-database-elastic-jobs-overview.md)。

## <a name="sql-server-collation-support"></a>SQL Server 定序支援

使用 Microsoft Azure SQL 資料庫的預設資料庫定序是**SQL_LATIN1_GENERAL_CP1_CI_AS**，其中**LATIN1_GENERAL**是英文 （美國）、 **CP1**是字碼頁 1252年、 **SONT**不區分大小寫，而**另存新檔**是輔色區分。 您無法變更 V12 資料庫的定序。 如需有關如何設定定序的詳細資訊，請參閱[自動分頁 (SQL)](https://msdn.microsoft.com/library/ms184391.aspx)。

## <a name="naming-requirements"></a>命名需求

基於安全性理由不允許特定的使用者名稱。 您無法使用下列名稱︰

 - **管理員**
 - **系統管理員**
 - **來賓**
 - **根**
 - **索**

SQL Server 識別項規則，必須符合的所有新的物件名稱。 如需詳細資訊，請參閱[識別碼](https://msdn.microsoft.com/library/ms175874.aspx)。

此外，登入] 和 [使用者名稱不可以包含 \ （不支援 Windows 驗證） 的字元。

## <a name="additional-guidelines"></a>其他指導方針

- 本文所述的一般限制，除了 SQL 資料庫會有特定的資源配額和根據您的**服務層級**的限制。 如需服務層的概觀，請參閱[SQL 資料庫服務層](sql-database-service-tiers.md)。

- 其他 SQL 資料庫限制，請參閱[Azure SQL 資料庫資源限制](sql-database-resource-limits.md)。

- 安全性相關的指導方針，請參閱[Azure SQL 資料庫安全性指導方針和限制](sql-database-security-guidelines.md)。

- 其他相關的區域周圍 Azure SQL 資料庫具有與內部部署版本的 SQL Server，例如 SQL Server 2014 和 SQL Server 2016 相容性。 在 V12 新版 Azure SQL 資料庫具有此區域中進行多的改良功能。 如需詳細資訊，請參閱[什麼是 SQL 資料庫 V12 的新增功能](sql-database-v12-whats-new.md)。

- 驅動程式可用性和支援的 SQL 資料庫上的資訊，請參閱[連線的文件庫的 SQL 資料庫和 SQL Server](sql-database-libraries.md)。
