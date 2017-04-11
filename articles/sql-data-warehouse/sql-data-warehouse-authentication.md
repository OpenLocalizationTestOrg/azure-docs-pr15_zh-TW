<properties
   pageTitle="Azure SQL Data Warehouse 驗證 |Microsoft Azure"
   description="Azure Active Directory (AAD) 和 SQL Server 驗證 Azure SQL Data Warehouse。"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="byham"
   manager="barbkess"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/24/2016"
   ms.author="rickbyh;barbkess;sonyama"/>

# <a name="authentication-to-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse 驗證

> [AZURE.SELECTOR]
- [安全性概觀](sql-data-warehouse-overview-manage-security.md)
- [驗證](sql-data-warehouse-authentication.md)
- [加密 （入口網站）](sql-data-warehouse-encryption-tde.md)
- [加密 (T SQL)](sql-data-warehouse-encryption-tde-tsql.md)

若要連線至 SQL Data Warehouse，您必須傳入安全性憑證進行驗證。 在建立連線，某些連線設定是建立您的查詢工作階段。  

安全性，以及如何啟用連線至您的資料倉庫的詳細資訊，請參閱[安全 SQL Data Warehouse 中的資料庫][]。

## <a name="sql-authentication"></a>SQL 驗證
若要連線至 SQL Data Warehouse，您必須提供下列資訊︰

- 完整的伺服器名稱
- 指定 SQL 驗證
- 使用者名稱
- 密碼
- 預設資料庫 （選用）

依預設您連線到*母片*的資料庫，並不使用者資料庫。 若要連線至您的使用者資料庫，您可以選擇執行其中一項動作︰

- SQL Server 物件檔案總管中 SSDT，SSMS，或您的應用程式連線字串中註冊您的伺服器時，請指定預設資料庫。 例如，包括 InitialCatalog 參數 ODBC 連線。
- 建立工作階段中 SSDT 之前，醒目提示使用者資料庫。

> [AZURE.NOTE] 不支援的 SQL 陳述式**使用 MyDatabase;**變更資料庫連線。 如需連線至 SQL Data Warehouse 與 SSDT 的指引，請參閱[使用 Visual Studio 查詢][]文章。

## <a name="azure-active-directory-aad-authentication"></a>Azure Active Directory (AAD) 驗證

[Azure Active Directory] [What is Azure Active Directory]驗證是使用中 Azure Active Directory (Azure AD) 的身分識別連線到 Microsoft Azure SQL Data Warehouse 機制。 Azure Active Directory 驗證，您可以集中管理資料庫使用者的身分識別及其他 Microsoft 服務在一個中央位置。 中央識別碼管理提供單一位置管理 SQL Data Warehouse 使用者，並簡化權限管理。 

### <a name="benefits"></a>優點

Azure Active Directory 優點包括︰

- 提供 SQL Server 驗證的替代方案。
- 可協助會停止到資料庫伺服器的使用者身分識別。
- 允許在單一位置中的密碼旋轉
- 管理資料庫使用外部 (AAD) 群組的權限]。
- 啟用整合式的 Windows 驗證和其他形式的受支援的 Azure Active Directory 驗證會解決儲存密碼。
- 使用包含資料庫使用者進行驗證身分識別資料庫層級。
- 支援權杖型驗證的應用程式連線至 SQL Data Warehouse。
- 可透過 Active Directory 通用驗證多重因素驗證支援的 SQL Server Management Studio。 多重因素驗證的說明，請參閱[Azure AD MFA SQL 資料庫與 SQL Data Warehouse SSMS 支援](../sql-database/sql-database-ssms-mfa-authentication.md)。

> [AZURE.NOTE] Azure Active Directory 仍新，而有一些限制。 若要確保 Azure Active Directory 適合用您的環境，請參閱[Azure AD 功能和限制][]，特別是其他考量。

### <a name="configuration-steps"></a>設定步驟

請遵循下列步驟，設定 Azure Active Directory 驗證。

1. 建立並填入 Azure Active Directory
2. 可省略︰ 建立關聯，或變更目前的 Azure 訂閱相關聯的 active directory
3. 建立 Azure SQL Data Warehouse 的 Azure Active Directory 系統管理員。
4. 設定您的用戶端電腦
5. 對應至 Azure AD 身分識別資料庫中建立包含的資料庫使用者
6. 使用 Azure AD 身分識別連線到您的資料倉庫

目前 Azure Active Directory 使用者不會顯示在 SSDT 物件總管] 中。 因應措施，請在[sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx)檢視使用者。
  
### <a name="find-the-details"></a>尋找詳細資料
- 完成的詳細的步驟。 設定和使用 Azure Active Directory 驗證的詳細的步驟是 Azure SQL 資料庫和 Azure SQL Data Warehouse 幾乎相同。 請遵循在主題中[連線至 SQL 資料庫或 SQL 資料倉庫來使用 Azure Active Directory 驗證](../sql-database/sql-database-aad-authentication.md)的詳細的步驟。
- 建立自訂的資料庫角色，並將使用者新增至角色。 然後授與細微給角色的權限。 如需詳細資訊，請參閱[開始使用資料庫引擎權限](https://msdn.microsoft.com/library/mt667986.aspx)。

## <a name="next-steps"></a>後續步驟

若要開始查詢您的資料倉庫 Visual Studio 與其他應用程式，請參閱[使用 Visual Studio 的查詢][]。

<!-- Article references -->
[保護資料庫安全之 SQL Data Warehouse 中]: ./sql-data-warehouse-overview-manage-security.md
[使用 Visual Studio 的查詢]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]: ../active-directory/active-directory-whatis.md
[Azure AD 功能和限制]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
