<properties
   pageTitle="SQL 資料庫安全性概觀"
   description="深入了解 Azure SQL 資料庫和 SQL Server 的安全性，包括雲端之間的差異與 SQL Server 內部部署時，以驗證、 授權、 連線安全性、 加密與規範。"
   services="sql-database"
   documentationCenter=""
   authors="tmullaney"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="06/09/2016"
   ms.author="thmullan;jackr"/>


# <a name="securing-your-sql-database"></a>保護您的 SQL 資料庫

## <a name="overview"></a>概觀

本文將引導保護資料層的使用 Azure SQL 資料庫的應用程式的基本概念。 特別是，本文可帶您入門的資源的限制存取，保護資料，及監視活動資料庫上的建立的[快速入門 SQL 資料庫教學課程](sql-database-get-started.md)。 在所有類別的 SQL 安全性功能的完整概觀，請參閱[SQL Server 資料庫引擎和 Azure SQL 資料庫的資訊安全中心](https://msdn.microsoft.com/library/bb510589)。 其他資訊也適用於[安全性和 Azure SQL 資料庫技術的白皮書 （英文）](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF)。

## <a name="connection-security"></a>連線安全性

連線安全性是指您如何限制和安全連線到您使用防火牆規則和連線加密的資料庫。

防火牆規則會使用伺服器和資料庫拒絕連線嘗試從有明確 whitelisted 的 IP 位址。 若要允許您的應用程式或用戶端電腦的公用 IP 位址嘗試連線到新的資料庫，您必須先建立使用 Azure 傳統入口網站、 REST API 或 PowerShell 的伺服器層級的防火牆規則。 最佳作法是，您應該限制盡可能透過您的伺服器防火牆允許的 IP 位址範圍。 如需詳細資訊，請參閱[Azure SQL 資料庫防火牆](https://msdn.microsoft.com/library/ee621782)。

所有 Azure SQL 資料庫連線都需要加密 (SSL/TLS) 在所有的時間時資料是 「 在傳輸時會 」，並從資料庫。 在 [應用程式的連接字串，您必須指定參數加密連線並**信任伺服器憑證 （這是您如果複製連線字串登出 Azure 傳統入口網站），否則連線將不會驗證身分識別的伺服器，並會容易 」 中-攔截 」 攻擊。 ADO.NET 驅動程式，例如，這些連線字串參數是**加密 = True**和**TrustServerCertificate = False**。 如需詳細資訊，請參閱[Azure SQL 資料庫連線加密和憑證驗證](https://msdn.microsoft.com/library/azure/ff394108#encryption)。


## <a name="authentication"></a>驗證

驗證指的是證明您的身分識別，連線至資料庫時。 SQL 資料庫支援驗證的兩種的類型︰

 - **SQL 驗證**，可使用的使用者名稱和密碼
 - **Azure Active Directory 驗證**，可使用的 Azure Active Directory 受管理的身分識別和支援管理，整合網域

當您建立的邏輯伺服器資料庫時，您可以指定 「 伺服器管理員 」 登入的使用者名稱與密碼。 使用這些認證，您可以驗證的資料庫擁有者或 「 dbo。 」 的伺服器上的任何資料庫 如果您想要使用 Azure Active Directory 驗證，您必須建立另一個名為 「 Azure AD 管理員，請 「 允許管理 Azure AD 使用者和群組的伺服器管理員。 此管理員也可以執行一般的伺服器管理員可以的所有作業。 如何建立 Azure AD 管理員才能 Azure Active Directory 驗證的逐步解說，請參閱[連線至 SQL 資料庫來使用 Azure Active Directory 驗證](sql-database-aad-authentication.md)。

最佳作法是您的應用程式應該使用不同的帳戶進行驗證，如此一來，您可以限制權限授與應用程式，並減少惡意活動的風險，以避免應用程式碼很容易受到 SQL 插入攻擊。 建議的方法是，建立[包含資料庫使用者](https://msdn.microsoft.com/library/ff929188)，可讓您直接驗證單一資料庫的應用程式。 您可以建立包含的資料庫使用者會使用 SQL 驗證執行以下 T SQL 命令連線到伺服器管理員登入您使用者資料庫時︰

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

如果您建立的 Azure AD 管理員，您可以建立包含的資料庫使用者會使用 Azure Active Directory 驗證，請執行下列 T SQL 命令連線到您身為 Azure AD 管理員的使用者資料庫時︰

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

在任一情況下，這些使用者認證，而不是連線到資料庫伺服器管理員登入，應該指定應用程式的連線字串。

如需有關驗證到 SQL 資料庫的詳細資訊，請參閱[管理資料庫及 Azure SQL 資料庫中的登入](sql-database-manage-logins.md)。


## <a name="authorization"></a>授權
授權是指您可以執行 Azure SQL 資料庫中，這由您的使用者帳戶角色的成員資格和權限。 最佳作法是，您應該授與使用者所需的最低權限。 Azure SQL 資料庫可讓此容易管理 T SQL 中的角色︰

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

您已使用連接的伺服器管理員帳戶是 db_owner，擁有執行任何動作，在資料庫中的權限的成員。 儲存此帳戶部署結構描述升級及其他管理作業。 從您的應用程式資料庫連線應用程式所需的最低權限與使用其他限制權限 」 ApplicationUser 「 帳戶。

若要進一步限制使用者可以進行哪些 Azure SQL 資料庫的方法有種︰

* 若要建立更強大的應用程式使用者帳戶或較少功能強大的管理帳戶可非 db_datareader 和 db_datawriter[資料庫角色](https://msdn.microsoft.com/library/ms189121)。
* 微調[權限](https://msdn.microsoft.com/library/ms191291)可讓您控制的作業，您可以在個別的資料行、 資料表、 檢視、 程序及其他資料庫中的物件。
* 若要安全地暫時提高權限可[模擬](https://msdn.microsoft.com/library/vstudio/bb669087)和[模組登入](https://msdn.microsoft.com/library/bb669102)。
* 可以使用[資料列層級安全性](https://msdn.microsoft.com/library/dn765131)限制的列使用者可以存取。
* [資料 Masking](sql-database-dynamic-data-masking-get-started.md)可以用來限制的機密資料的曝光度。
* [預存程序](https://msdn.microsoft.com/library/ms190782)可以用來限制資料庫可以採取的動作。

從 Azure 傳統入口網站管理資料庫及邏輯伺服器或使用 Azure 資源管理員 API 會控制入口網站的使用者帳戶的角色指派。 如需有關本主題的詳細資訊，請參閱[Azure 入口網站中的角色型存取控制](../active-directory./role-based-access-control-configure.md)。


## <a name="encryption"></a>加密

Azure SQL 資料庫可協助保護您的資料時，就 「 在其餘部分中，「 加密資料，或儲存在資料庫檔案和使用[透明資料加密](http://go.microsoft.com/fwlink/?LinkId=526242)的備份。 若要將加密的資料庫，為資料庫擁有者連線，以及執行︰

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

若要將您的資料密碼加密的其他方法，請考慮︰

* 若要使用不同的加密金鑰加密特定的資料行或甚至是儲存格資料的[儲存格層級加密](https://msdn.microsoft.com/library/ms179331.aspx)。
* 如果您需要的硬體安全性模組或集中管理您的加密金鑰階層，請考慮使用[SQL server Azure VM 中 Azure 金鑰保存庫](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)。
* [永遠加密](https://msdn.microsoft.com/library/mt163865.aspx)（以預覽版本） 可讓您加密透明應用程式，並允許用戶端，而不必共用加密金鑰以 SQL 資料庫加密用戶端應用程式內的機密資料。

## <a name="auditing"></a>稽核

稽核與追蹤資料庫事件可協助您維護法規遵循並識別可疑的活動。 SQL 資料庫稽核可讓您中以稽核記錄事件登入您的 Azure 儲存體帳戶。 SQL 資料庫稽核也整合了 Microsoft Power BI，以促進向下切入報告與分析。 如需詳細資訊，請參閱[開始使用 SQL 資料庫稽核](sql-database-auditing-get-started.md)。

SQL 資料庫威脅偵測提供額外的稽核上方的安全性層級。 讓您回應威脅發生異常活動上提供的安全性警告。 如需詳細資訊，請參閱[開始使用 SQL 資料庫威脅偵測](sql-database-threat-detection-get-started.md)。  

## <a name="compliance"></a>法規遵循

除了上方的功能和功能，可協助您的應用程式也符合各種不同的安全性規範要求，Azure SQL 資料庫參與一般稽核和法規遵循標準的數字與認證。 如需詳細資訊，請參閱[Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/)，您可以在哪裡找到[SQL 資料庫規範認證](https://azure.microsoft.com/support/trust-center/services/)的最新的清單。
