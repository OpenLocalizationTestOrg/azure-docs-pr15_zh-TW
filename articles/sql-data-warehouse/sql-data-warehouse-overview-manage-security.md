<properties
   pageTitle="保護資料庫安全之在 SQL Data Warehouse |Microsoft Azure"
   description="設定資料庫中 Azure SQL Data Warehouse 安全性開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/24/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="secure-a-database-in-sql-data-warehouse"></a>保護資料庫安全之 SQL Data Warehouse 中

> [AZURE.SELECTOR]
- [安全性概觀](sql-data-warehouse-overview-manage-security.md)
- [驗證](sql-data-warehouse-authentication.md)
- [加密 （入口網站）](sql-data-warehouse-encryption-tde.md)
- [加密 (T SQL)](sql-data-warehouse-encryption-tde-tsql.md)

本文將引導保護 Azure SQL Data Warehouse 資料庫的基本概念。 這份文件，取得您開始使用的限制存取，資源保護資料，並監控資料庫上的活動。

## <a name="connection-security"></a>連線安全性

連線安全性是指您如何限制和安全連線到您使用防火牆規則和連線加密的資料庫。

防火牆規則會使用伺服器和資料庫拒絕連線嘗試從有明確 whitelisted 的 IP 位址。 若要允許從您的應用程式或用戶端電腦的公用 IP 位址的連線，您必須先建立使用 Azure 入口網站、 REST API 或 PowerShell 的伺服器層級的防火牆規則。 最佳作法是，您應該限制盡可能透過您的伺服器防火牆允許的 IP 位址範圍。  若要從本機電腦存取 Azure SQL Data Warehouse，請確定您的網路和本機電腦上的防火牆可讓 TCP 連接埠 1433年外的通訊。  如需詳細資訊，請參閱[Azure SQL 資料庫防火牆][]、 [sp_set_firewall_rule][]，與[sp_set_database_firewall_rule][]。

預設會加密連線至您的 SQL Data Warehouse。  若要停用加密修改的連線設定會略過。

## <a name="authentication"></a>驗證

驗證指的是證明您的身分識別，連線至資料庫時。 SQL Data Warehouse 目前支援 SQL Server 驗證的使用者名稱和密碼，以及 Azure Active Directory。 

當您建立的邏輯伺服器資料庫時，您可以指定 「 伺服器管理員 」 登入的使用者名稱與密碼。 使用這些認證，您可以驗證的資料庫擁有者或 「 dbo 」，透過 SQL Server 驗證該伺服器上的任何資料庫。

不過，最佳作法是您組織的使用者應使用不同的帳戶進行驗證。 您可以限制權限授與應用程式，並減少惡意活動的風險，以避免應用程式碼很容易受到 SQL 插入攻擊這種方式。 

若要建立 SQL Server 驗證的使用者，連線到您的伺服器管理員登入與伺服器上**主版**的資料庫，建立新的伺服器登入。  此外，則建議您先建立主 Azure SQL Data Warehouse 使用者的資料庫中的使用者。 建立母片中的使用者，讓使用者登入使用 SSMS 等工具，但未指定的資料庫名稱。  您也可以使用物件總管檢視 [SQL server 上的 [所有資料庫。

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

然後，連線到您的**SQL Data Warehouse 資料庫**與您的伺服器管理員登入，並建立資料庫的使用者，根據您剛剛建立的伺服器登入。

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

如果使用者會進行其他操作，例如建立登入，或建立新的資料庫，也必須被指派給`Loginmanager`和`dbmanager`主要資料庫中的角色。 如需有關這些其他角色和驗證到 SQL 資料庫的詳細資訊，請參閱[管理資料庫及 Azure SQL 資料庫中的登入][]。  如需詳細的 SQL Data Warehouse Azure AD 的詳細資訊，請參閱[連線至 SQL 資料倉庫來使用 Azure Active Directory 驗證。][]


## <a name="authorization"></a>授權

授權是指您可以執行 Azure SQL Data Warehouse 資料庫中，這由您的使用者帳戶角色的成員資格和權限。 最佳作法是，您應該授與使用者所需的最低權限。 Azure SQL Data Warehouse 很容易這管理 T SQL 中的角色︰

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

您已使用連接的伺服器管理員帳戶是 db_owner，擁有執行任何動作，在資料庫中的權限的成員。 儲存此帳戶部署結構描述升級及其他管理作業。 從您的應用程式資料庫連線應用程式所需的最低權限與使用其他限制權限 」 ApplicationUser 「 帳戶。

若要進一步限制使用者可以進行哪些 Azure SQL 資料庫的方法有種︰

- 微調[權限][]可讓您控制的作業，您可以在個別的資料行、 資料表、 檢視、 程序及其他資料庫中的物件。 大部分的控制，並授與必要的最低權限使用微調權限。 微調權限系統較為複雜，且需要一些研究有效地使用。
- 若要建立更強大的應用程式使用者帳戶或較少功能強大的管理帳戶可非 db_datareader 和 db_datawriter[資料庫角色][]。 內建的固定的資料庫角色提供簡單的方式來授與權限，但可能會導致授與非必要的權限。
- [預存程序][]可以用來限制資料庫可以採取的動作。

從 Azure 傳統入口網站管理資料庫及邏輯伺服器或使用 Azure 資源管理員 API 會控制入口網站的使用者帳戶的角色指派。 如需有關本主題的詳細資訊，請參閱[Azure 入口網站中的角色型存取控制][]。

## <a name="encryption"></a>加密

Azure SQL 資料倉庫透明資料加密 (TDE) 可協助防止惡意活動的潛在威脅執行其餘您資料的即時加密和解密。  當您加密資料庫時，相關聯的備份與交易記錄檔加密不需要變更您的應用程式。 TDE 加密整個資料庫的儲存空間，使用名為資料庫加密金鑰對稱索引鍵。 SQL 資料庫中的內建的伺服器憑證受資料庫加密金鑰。 內建的伺服器憑證都是唯一的每個 SQL 資料庫伺服器。 Microsoft 會自動將這些憑證，至少每個 90 天。 使用 SQL Data Warehouse 的加密演算法是 AES 256。 TDE 的一般描述，請參閱[透明資料加密][]。

您可以將加密資料庫使用[Azure 入口網站][Encryption with Portal]或[T SQL][Encryption with TSQL]。

## <a name="next-steps"></a>後續步驟

如詳細資料和連線到您使用不同的通訊協定的 SQL Data Warehouse 的範例，請參閱[連線至 SQL Data Warehouse][]。

<!--Image references-->

<!--Article references-->
[連線至 SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[使用 Azure Active Directory 驗證連線到 SQL Data Warehouse]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Azure SQL 資料庫防火牆]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[資料庫角色]: https://msdn.microsoft.com/library/ms189121.aspx
[管理資料庫及 Azure SQL 資料庫中的登入]: https://msdn.microsoft.com/library/ee336235.aspx
[權限]: https://msdn.microsoft.com/library/ms191291.aspx
[預存程序]: https://msdn.microsoft.com/library/ms190782.aspx
[透明資料加密]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Azure 入口網站中的角色型存取控制]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
