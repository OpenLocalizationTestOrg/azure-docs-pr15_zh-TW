<properties
   pageTitle="Azure SQL 資料庫安全性指導方針和限制 |Microsoft Azure"
   description="進一步瞭解 Microsoft Azure SQL 資料庫指導方針，與安全性相關的限制。"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="10/18/2016"
   ms.author="rickbyh"/>

# <a name="azure-sql-database-security-guidelines-and-limitations"></a>Azure SQL 資料庫安全性指導方針和限制

本主題將說明 Microsoft Azure SQL 資料庫指導方針和安全性相關的限制。 管理 Azure SQL 資料庫的安全性時，請考慮下列幾點。

## <a name="access-to-the-virtual-master-database"></a>虛擬主要資料庫的存取權

一般而言，只有系統管理員都需要主資料庫的存取權。 透過在每個資料庫中建立的非系統管理員所包含的資料庫使用者應例行資料庫的存取權每個使用者。 當您使用含的資料庫使用者時，您不需要建立主版的資料庫中的登入。 如需詳細資訊，請參閱[包含資料庫使用者-讓您資料庫可攜式](https://msdn.microsoft.com/library/ff929188.aspx)。


## <a name="firewall"></a>防火牆

SQL Server 防火牆後，針對整個 Azure SQL Server 範圍通常透過入口網站設定及應只准許由系統管理員所用的 IP 位址。 若要建立開啟必要的 IP 位址範圍每個資料庫的資料庫範圍的防火牆規則，連線到使用者資料庫，然後使用 [ [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) SQL 陳述式。

Azure SQL 資料庫服務僅適用於透過 TCP 連接埠 1433年的。 若要從您的電腦存取 SQL 資料庫，請確定您的用戶端電腦防火牆可讓 TCP 連接埠 1433年外寄的 TCP 通訊。 如果不需要的其他應用程式，封鎖 TCP 連接埠 1433年的傳入的連線。 

連線程序的一部分，從 Azure 虛擬機器連線會被重新導向到不同的 IP 位址和連接埠，唯一的每個工作者角色。 連接埠號碼位於 11000 11999 的範圍。 如需有關 TCP 連接埠的詳細資訊，請參閱[1433 ADO.NET 4.5] 和 [SQL 資料庫 V12 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)。


## <a name="authentication"></a>驗證

使用 Active Directory 驗證 （整合式安全性） 可能的話。 設定 AD 驗證資訊，請參閱[連線至 SQL 資料庫來使用 Azure Active Directory 驗證](sql-database-aad-authentication.md)，和 SQL Server 線上活頁簿中的 [[選擇驗證模式](https://msdn.microsoft.com/library/ms144284.aspx)。 

使用包含資料庫的使用者增強延展性。 如需詳細資訊，請參閱[包含資料庫使用者-讓您資料庫可攜式](https://msdn.microsoft.com/library/ff929188.aspx)、[建立使用者 (SQL)](https://technet.microsoft.com/library/ms173463.aspx)，以及[所包含的資料庫](https://technet.microsoft.com/library/ff929071.aspx)。

資料庫引擎會關閉閒置超過 30 分鐘的連線。 連線必須登入，才可以使用。

作用中的連線至 SQL 資料庫持續要求 （執行資料庫引擎） 重新授權至少每 10 個小時。 資料庫引擎嘗試重新授權使用最初提交的密碼，任何使用者輸入需要。 基於效能，SQL 資料庫中重設密碼時沒有連線重新驗證，即使連線重設因為連接共用。 這是不同的內部部署的 SQL Server 的行為。 如果連線已最初授權已經變更密碼，必須終止連線，然後使用新密碼進行新連線]。 具有刪除資料庫連線權限的使用者可以使用 [[刪除](https://msdn.microsoft.com/library/ms173730.aspx)] 命令，明確終止連線到 SQL 資料庫。

## <a name="logins-and-users"></a>登入與使用者

管理時登入與 SQL 資料庫中的使用者，有一些限制。


- 您必須連線至**主版**的資料庫時執行``CREATE/ALTER/DROP DATABASE``陳述式。 -無法變更或遭到捨棄資料庫中的使用者主要資料庫對應至的伺服器層級的本金登入。 
- 美式英文是預設語言的伺服器層級的本金登入。
- 只有系統管理員 （伺服器層級的本金登入或 Azure AD 系統管理員） 和**主要**資料庫中的**dbmanager**資料庫角色的成員具有權限執行``CREATE DATABASE``和``DROP DATABASE``陳述式。
- 您必須連線至主版的資料庫時執行``CREATE/ALTER/DROP LOGIN``陳述式。 不過不建議使用的登入。 請改用所包含的資料庫使用者。
- 若要連線至使用者資料庫，您必須提供連線字串中的資料庫的名稱。
- 只有伺服器層級的本金登入與**主要**資料庫中的**loginmanager**資料庫角色的成員具有權限執行``CREATE LOGIN``， ``ALTER LOGIN``，及``DROP LOGIN``陳述式。
- 執行時``CREATE/ALTER/DROP LOGIN``和``CREATE/ALTER/DROP DATABASE``陳述式 ADO.NET 應用程式中的，使用參數化的命令不允許。 如需詳細資訊，請參閱[命令及參數值](https://msdn.microsoft.com/library/ms254953.aspx)。
- 執行時``CREATE/ALTER/DROP DATABASE``和``CREATE/ALTER/DROP LOGIN``陳述式，每個陳述式必須是唯一的陳述式 TRANSACT-SQL 批次。 否則，會發生錯誤。 例如，下列 TRANSACT-SQL 會檢查是否有資料庫。 如果有的話，``DROP DATABASE``陳述式稱為 「 若要移除的資料庫。 因為``DROP DATABASE``陳述式不批次中的陳述式中，執行下列 SQL 陳述式結果錯誤。

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

- 執行時``CREATE USER``帳單與``FOR/FROM LOGIN``選項，它必須是唯一的陳述式 TRANSACT-SQL 批次。
- 執行時``ALTER USER``帳單與``WITH LOGIN``選項，它必須是唯一的陳述式 TRANSACT-SQL 批次。
- 若要``CREATE/ALTER/DROP``使用者需要``ALTER ANY USER``資料庫的權限。
- 當資料庫角色的擁有者，嘗試以新增或移除另一個資料庫使用者或從該資料庫的角色時，可能會發生下列錯誤︰**使用者或角色 「 名稱 」 不存在於此資料庫。** 使用者看不到擁有者，就會發生這個錯誤。 若要解決此問題，授與角色的擁有者``VIEW DEFINITION``使用者權限。 

如需登入與使用者的詳細資訊，請參閱[管理資料庫及 Azure SQL 資料庫中的登入](sql-database-manage-logins.md)。


## <a name="see-also"></a>另請參閱

[Azure SQL 資料庫防火牆](sql-database-firewall-configure.md)

[如何︰ 設定防火牆設定 （Azure SQL 資料庫）](sql-database-configure-firewall-settings.md)

[管理資料庫及 Azure SQL 資料庫中的登入](sql-database-manage-logins.md)

[SQL Server 資料庫引擎和 Azure SQL 資料庫的資訊安全中心](https://msdn.microsoft.com/library/bb510589)
