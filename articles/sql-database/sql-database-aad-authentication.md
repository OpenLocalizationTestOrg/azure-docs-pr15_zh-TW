<properties
   pageTitle="使用 Azure Active Directory 驗證連線至 SQL 資料庫或 SQL Data Warehouse |Microsoft Azure"
   description="瞭解如何使用 Azure Active Directory 驗證連線至 SQL 資料庫。"
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
   ms.date="09/16/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="connecting-to-sql-database-or-sql-data-warehouse-by-using-azure-active-directory-authentication"></a>使用 Azure Active Directory 驗證連線到 SQL 資料庫或 SQL Data Warehouse

Azure Active Directory 驗證是連線到 Microsoft Azure SQL 資料庫及[SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) Azure Active Directory (Azure AD) 中使用身分識別的機制。 Azure Active Directory 驗證，您可以集中管理資料庫使用者的身分識別及其他 Microsoft 服務在一個中央位置。 中央識別碼管理提供單一位置管理資料庫的使用者，並簡化權限管理。 下列優點︰

- 它會提供 SQL Server 驗證的替代方案。
- 可協助會停止到資料庫伺服器的使用者身分識別。
- 允許在單一位置中的密碼旋轉
- 客戶管理資料庫使用外部 (AAD) 群組的權限。
- 它可以啟用整合式的 Windows 驗證和其他形式的受支援的 Azure Active Directory 驗證，以利消除儲存密碼。
- Azure Active Directory 驗證會使用包含資料庫使用者進行驗證身分識別資料庫層級。
- Azure Active Directory 支援權杖型驗證的應用程式連線至 SQL 資料庫。
- Azure Active Directory 驗證本機 Azure Active Directory 網域同步處理不支援 ADFS （網域同盟） 或原生的使用者密碼驗證。  
- Azure Active Directory 支援從 SQL Server Management Studio 中使用 Active Directory 通用驗證，其中包含多重因素驗證 (MFA) 的連線。  MFA 包含強式驗證於某範圍內的簡單的驗證選項 — 電話、 簡訊智慧卡 pin]，或行動應用程式通知。 如需詳細資訊，請參閱[Azure AD MFA SQL 資料庫與 SQL Data Warehouse SSMS 支援](sql-database-ssms-mfa-authentication.md)。

設定步驟會包含下列程序來設定和使用 Azure Active Directory 驗證。

1. 建立並填入 Azure Active Directory。
2. 請確定您的資料庫中 Azure SQL 資料庫 V12。 （不需要 SQL Data Warehouse。）
3. 可省略︰ 建立關聯，或變更目前的 Azure 訂閱相關聯的 active directory。
4. 建立 Azure SQL Server] 或 [ [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/)的 Azure Active Directory 系統管理員。
5. 設定您的用戶端電腦。
6. 對應至 Azure AD 身分識別資料庫中建立包含的資料庫使用者。
7. 使用 Azure AD 身分識別，以連線至您的資料庫。


## <a name="trust-architecture"></a>信任架構

下列高層級圖表摘要列出 Azure AD 驗證使用 Azure SQL 資料庫的解決方案架構。 相同的概念適用於 SQL Data Warehouse。 若要支援 Azure Active Directory 原生使用者密碼，會被視為僅雲端部分和 Azure AD/Azure SQL 資料庫。 支援聯盟驗證 （或 [Windows 認證使用者/密碼），通訊與 ADFS 區塊資格。 箭號，指出通訊路徑。

![aad 驗證圖表][1]

下圖會指出同盟、 信任和允許送出權杖以連線至資料庫的用戶端的主機服務關聯性。 權杖 Azure AD，以進行驗證，然後信任的資料庫。 Azure Active Directory 與原生使用者或 Azure Active Directory 與同盟的使用者，可以代表客戶 1。 客戶 2 代表可行的解決方案，包括匯入的使用者。在此範例中使用 ADFS 與 Azure Active Directory 進行同步處理的同盟 Azure Active Directory 來自。 請務必瞭解使用 Azure AD 驗證資料庫的存取權，要求的主機服務的訂閱是與 Azure Active Directory。 若要建立 SQL Server Azure SQL 資料庫或 SQL Data Warehouse 主機服務必須使用同一份訂閱。

![訂閱的關聯][2]

## <a name="administrator-structure"></a>系統管理員結構

使用 Azure AD 驗證時，有兩個系統管理員帳戶的 SQL 資料庫伺服器。原始的 SQL Server 系統管理員和 Azure AD 系統管理員。 相同的概念適用於 SQL Data Warehouse。 Azure AD 客戶為基礎的系統管理員可以在使用者資料庫中建立的第一個包含 Azure AD 資料庫使用者。 Azure AD 使用者或 Azure AD 群組，可以是 Azure AD 管理員登入。 當系統管理員群組帳戶時，其可讓任何群組的成員，讓 SQL Server 執行個體的多個 Azure AD 系統管理員。 使用群組帳戶以系統管理員可以讓您集中新增和 Azure AD 中移除群組成員，而不變更 [SQL 資料庫中的權限的使用者增強管理能力。 您可以隨時設定只有一個 Azure AD 管理員 （使用者或群組）。

![系統結構][3]

## <a name="permissions"></a>權限

若要建立新的使用者，您必須`ALTER ANY USER`資料庫中的權限。 `ALTER ANY USER`任何資料庫使用者可以授與權限。 `ALTER ANY USER`權限也會保留伺服器管理員帳戶，並使用的資料庫使用者`CONTROL ON DATABASE`或`ALTER ON DATABASE`權限為該資料庫，以及成員`db_owner`資料庫角色。

若要建立包含的資料庫使用者 Azure SQL 資料庫或 SQL Data Warehouse 中，您必須連線至使用 Azure AD 身分識別的資料庫。 若要建立第一個含的資料庫使用者，您必須連線至資料庫使用的 Azure Active Directory 系統管理員 （為資料庫擁有者）。 步驟 4 和 5 以下所示。 任何 Azure Active Directory 驗證，才可以如果 Azure Active Directory 系統建立 Azure SQL 資料庫或 SQL Data Warehouse 伺服器。 Azure Active Directory 系統已從伺服器移除，如果現有的 Azure Active Directory 使用者建立先前 SQL Server 內可以不會再連線至資料庫中使用其 Azure Active Directory 認證。

## <a name="azure-ad-features-and-limitations"></a>Azure AD 功能和限制

下列 Azure Active Directory 中的成員可以在 Azure SQL Server 或 SQL Data Warehouse 佈建後︰

- 原生成員︰ 在管理網域]，或在客戶網域 Azure AD 中所建立的成員。 如需詳細資訊，請參閱[新增 Azure AD 您自己的網域名稱](../active-directory/active-directory-add-domain.md)。

- 同盟網域成員︰ Azure AD 與同盟網域中建立的成員。 如需詳細資訊，請參閱[Microsoft Azure 現在支援與 Windows Server 的 Active Directory federation](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)。

- 匯入的成員來自其他 Azure Active 目錄的原生或同盟網域成員。

- 建立安全性群組的 active Directory 群組。

Microsoft 帳戶 （例如 outlook.com、 hotmail.com、 live.com） 或其他來賓帳戶 （例如 gmail.com、 yahoo.com） 不受支援。 您可以登入[https://login.live.com](https://login.live.com)使用的帳戶與密碼，如果您使用 Azure AD 驗證 Azure SQL 資料庫或 Azure SQL Data Warehouse 不支援的 Microsoft 帳戶。

### <a name="additional-considerations"></a>其他考量

- 若要加強管理能力，建議您佈建專用的 Azure Active Directory 群組，以系統管理員身分。
- 只有一個 Azure AD 管理員 （使用者或群組） 可以隨時 Azure SQL Server 或 Azure SQL Data Warehouse 的設定。
- 僅用於 SQL Server Azure Active Directory 系統管理員可以最初連接到 Azure SQL Server 或 Azure SQL Data Warehouse 使用 Azure Active Directory 帳戶。 Active Directory 系統管理員可以設定後續的 Azure Active Directory 資料庫使用者。
- 我們建議您為 30 秒設定連線逾時。
- SQL Server 2016 Management Studio 和 SQL Server Data Tools for Visual Studio 2015 (版本 14.0.60311.1April 2016年或更新版本) 支援 Azure Active Directory 驗證。 (Azure Active Directory 驗證支援的**.NET Framework Data Provider for SqlServer**; 在最新版.NET Framework 4.6)。 因此這些工具和資料層應用程式 （DAC 和.bacpac） 的最新版本，可以使用 Azure Active Directory 驗證。
- [ODBC 版本 13.1](https://www.microsoft.com/download/details.aspx?id=53339)不過支援 Azure Active Directory 驗證`bcp.exe`無法使用 Azure Active Directory 驗證，因為其使用較舊的 ODBC 提供者連線。
- `sqlcmd`支援開始使用版本 13.1[下載中心](http://go.microsoft.com/fwlink/?LinkID=825643)提供的 Azure Active Directory 驗證。  
- SQL Server Data Tools for Visual Studio 2015 至少需要資料的工具 （版本 14.0.60311.1） 的年 4 月 2016年版本。 目前 Azure Active Directory 使用者不會顯示在 SSDT 物件總管] 中。 因應措施，請在[sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx)檢視使用者。
- [Microsoft JDBC 驅動程式 6.0 的 SQL Server](https://www.microsoft.com/en-us/download/details.aspx?id=11774)支援 Azure Active Directory 驗證。 此外，請參閱[設定連線內容](https://msdn.microsoft.com/library/ms378988.aspx)。
- PolyBase 無法透過 Azure Active Directory 驗證進行驗證。
- 不支援的 BI 與 Excel 等部分工具。
- Azure Active Directory 驗證 Azure 入口網站**資料庫匯入**及**匯出資料庫**刀支援的 SQL 資料庫。 匯入及匯出使用 Azure Active Directory 驗證，也支援從的 PowerShell 命令。


## <a name="1-create-and-populate-an-azure-ad"></a>1.建立並填入 Azure AD

建立 Azure Active Directory 並填入使用者和群組]。 Azure Active Directory 可以是初始網域 Azure AD 受管理的網域。 Azure Active Directory 也可以是內部部署 Active Directory 網域服務的同盟與 Azure Active Directory。

如需詳細資訊，請參閱[整合您的內部部署識別與 Azure Active Directory](../active-directory/active-directory-aadconnect.md)、[新增您自己的網域名稱，以 Azure AD](../active-directory/active-directory-add-domain.md)、 [Microsoft Azure 現在支援與 Windows Server 的 Active Directory federation](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)、[管理 Azure AD 目錄](https://msdn.microsoft.com/library/azure/hh967611.aspx)，以及[管理使用 Windows PowerShell 的 Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx)。

## <a name="2-ensure-your-sql-database-is-version-12"></a>2.請確定您 SQL 資料庫版本 12

最新的 SQL 資料庫 V12 支援 azure Active Directory 驗證。 有關 SQL 資料庫 V12，以及若要瞭解是否可以使用您的地區的資訊，請參閱[什麼是最新的 SQL 資料庫更新 V12 的新增功能](sql-database-v12-whats-new.md)。 此步驟中不需要的 Azure SQL Data Warehouse，因為 SQL Data Warehouse 只適用於 V12。

如果您有現有的資料庫，請確認其會裝載於 SQL 資料庫 V12 連接至資料庫 （例如使用 SQL Server Management Studio 中），以及執行`SELECT @@VERSION;`。 為資料庫的 SQL 資料庫 V12 預期的輸出至少是**Microsoft SQL Azure (RTM)-12.0**。 如果您的資料庫不裝載在 SQL 資料庫 V12，請參閱[計劃，並準備升級至 SQL 資料庫 V12](sql-database-v12-plan-prepare-upgrade.md)，然後造訪將資料庫移轉到 SQL 資料庫 V12 Azure 傳統入口網站。

或者，您也可以[建立您的第一個 Azure SQL 資料庫](sql-database-get-started.md)中所列的步驟進行，以建立新的資料庫中 SQL 資料庫 V12。 **提示**︰ 閱讀下一步，再選取 [訂閱] 新資料庫。

## <a name="3-optional-associate-or-change-the-active-directory-that-is-currently-associated-with-your-azure-subscription"></a>3.可省略︰ 建立關聯，或變更目前的 Azure 訂閱相關聯的 active directory

Azure AD 目錄，為您的組織中與您的資料庫，請目錄受信任的主機服務資料庫的 Azure 訂閱目錄。 如需詳細資訊，請參閱[如何 Azure 訂閱 Azure AD 與相關聯](https://msdn.microsoft.com/library/azure/dn629581.aspx)。

**的其他資訊︰**每個 Azure 的訂閱有 Azure AD 執行個體的信任關係。 這表示它信任驗證的使用者、 服務與裝置的目錄。 多個訂閱可以信任相同的目錄，但訂閱信任只有一個目錄。 您可以看到您的訂閱在[https://manage.windowsazure.com/](https://manage.windowsazure.com/)**設定**] 索引標籤下信任哪一個目錄。 此訂閱具有目錄的信任關係是與不同的訂閱有使用中的所有其他資源 Azure （網站、 資料庫等），也就是更像是訂閱的子資源的關聯性。 如果訂閱過期，然後存取這些與訂閱相關聯的資源也會停止。 目錄仍會保留在 Azure，但您可以與該目錄中的另一個訂閱，並繼續管理目錄使用者。 如需有關資源的詳細資訊，請參閱[瞭解資源存取 Azure 中](https://msdn.microsoft.com/library/azure/dn584083.aspx)。

下列程序如何變更指定訂閱相關聯的目錄提供的逐步指示。

1. 使用 Azure 訂閱管理員，以連線至您[Azure 傳統入口網站](https://manage.windowsazure.com/)。
2. 在左側的橫幅中，選取 [**設定**]。
3. 您的訂閱會出現在 [設定] 畫面。 如果不想要的訂閱，請按一下頂端的 [**訂閱****篩選以目錄**] 方塊的下拉式清單，選取包含您的訂閱的目錄，再按一下**套用**。

    ![選取 [訂閱]][4]
4. 在 [**設定**] 區域中，按一下您的訂閱，然後按一下頁面底部的**編輯目錄**。

    ![ad-設定-入口網站][5]
5. 在 [**編輯目錄**] 方塊中，選取與您的 SQL Server 或 SQL Data Warehouse，相關聯的 Azure Active Directory，然後按一下 [下一步] 箭號。

    ![編輯選取的目錄][6]
6. 在 [**確認**目錄對應] 對話方塊中，確認已選取 「**將會移除所有的共同管理員。**」

    ![編輯目錄確認][7]
7. 按一下檢查重新載入入口網站。

> [AZURE.NOTE] 當您變更的目錄，存取所有的共同管理員、 Azure AD 使用者和群組]，將會移除目錄備份資源的使用者，並他們不再擁有此訂閱或其資源的存取權。 僅，以服務系統管理員，可以設定存取原則根據新的目錄。 這項變更可能需要大量的時間，才會傳播到所有的資源。 變更目錄，也會 Azure AD 管理員變更 SQL 資料庫及 SQL Data Warehouse，而且不允許的任何現有的 Azure AD 使用者的資料庫存取權。 Azure AD 管理員必須重設 （如下所述），而且新 Azure AD 使用者必須建立。

## <a name="4-create-an-azure-ad-administrator-for-azure-sql-server"></a>4.建立 Azure SQL Server Azure AD 管理員

每個 Azure SQL Server （可主控 SQL 資料庫或 SQL Data Warehouse） 開頭的整個 Azure SQL Server 系統管理員的單一伺服器管理員帳戶。 第二個的 SQL Server 系統管理員必須建立，Azure AD 帳戶。 這個主體會建立包含的資料庫中的使用者主要資料庫。 以系統管理員，伺服器管理員帳戶是在每個使用者資料庫， **db_owner**角色的成員，然後**dbo**使用者輸入每個使用者的資料庫。 如需有關的伺服器管理員帳戶的詳細資訊，請參閱[管理資料庫和 Azure SQL 資料庫中的登入](sql-database-manage-logins.md)和[Azure SQL 資料庫的安全性指導方針和限制](sql-database-security-guidelines.md)的**登入與使用者**一節。

使用時 Azure Active Directory 地理複寫，Azure Active Directory 系統管理員必須設定主要和次要伺服器。 如果伺服器沒有 Azure Active Directory 系統管理員，然後 Azure Active Directory 登入與使用者收到 「 無法連線 」 至伺服器錯誤。

> [AZURE.NOTE] Azure AD 帳戶 （包括 Azure SQL Server 系統管理員帳戶），請在根據的使用者無法建立 Azure AD 為基礎的使用者，因為他們不需要驗證 Azure AD 建議的資料庫使用者的權限。

### <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-server-by-using-the-azure-portal"></a>使用 [Azure 入口網站佈建 Azure SQL server Azure Active Directory 系統管理員

1. 在[Azure 入口網站](https://portal.azure.com/)，在右上角，按一下下拉式清單的可能使用中的目錄的連線。 選擇正確的 Active Directory Azure AD 的預設值。 此步驟中的連結訂閱關聯與 Active Directory 確保同一份訂閱使用的兩個 Azure SQL server Azure AD 和 SQL Server。 （Azure SQL Server 可以主控 Azure SQL 資料庫或 Azure SQL Data Warehouse。）

    ![選擇 [ad][8]
2. 在左側的橫幅選取**SQL server**、 選取您的**SQL server**，然後**SQL Server**防禦，以在頂端按一下 [**設定**。

    ![ad 設定][9]
3. 在 [**設定**刀中，按一下 [* * Active Directory 管理員]。
4. 在**Active Directory 系統**刀中，按一下 [ **Active Directory 系統**，，然後在頂端，按一下**設定管理員**。
5. 在 [**新增管理員**防禦，以搜尋使用者，選取是系統管理員使用者或群組，然後按一下**選取**。 （Active Directory 系統刀會顯示所有成員與您的 Active Directory 群組。 無法選取使用者或群組，會呈現灰色，因為他們不支援 Azure AD 系統管理員的身分。 （請參閱支援管理員**Azure AD 功能和限制**以上的清單）。角色型存取控制 (RBAC) 僅適用於入口網站，並不會傳播到 SQL Server。
6. 在**Active Directory 系統**刀頂端，按一下 [**儲存**]。
    ![選擇 [管理員]][10]

    變更系統管理員的程序可能需要幾分鐘的時間。 隨後會出現新的系統管理員，在**Active Directory 管理員**] 方塊中。

> [AZURE.NOTE] Azure AD 管理員所設定，當新的系統管理員名稱 （使用者或群組） 不能出現在虛擬主要資料庫的 SQL Server 驗證使用者。 如果有的話，將會失敗 Azure AD 系統管理員設定。復原其建立，並指出該這類系統管理員 （名稱） 已經存在。 由於這類 SQL Server 驗證使用者不是 Azure AD 的一部分，連線到伺服器使用 Azure AD 驗證的任何項目將會失敗。

若要稍後移除為管理員，在**Active Directory 系統**刀頂端，按一下 [**移除管理員**]，再按一下 [**儲存**。

### <a name="provision-an-azure-ad-administrator-for-azure-sql-server-by-using-powershell"></a>佈建使用 PowerShell 的 Azure SQL Server Azure AD 管理員

若要執行 PowerShell cmdlet，您需要有 PowerShell 的 Azure 安裝及執行。 詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。

佈建 Azure AD 管理員，執行下列 PowerShell 的 Azure 命令︰

- 新增 AzureRmAccount
- 選取 AzureRmSubscription


Cmdlet 來佈建與管理 Azure AD 管理員︰

| Cmdlet 名稱                                       | 描述                                                                                                     |
|---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [設定 AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603544.aspx)    | 佈建新 Azure SQL Server 或 Azure SQL Data Warehouse 的 Azure Active Directory 系統管理員。 （必須是目前的訂閱。） |
| [移除 AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt619340.aspx) | 移除 Azure SQL Server] 或 [Azure SQL Data Warehouse 的 Azure Active Directory 系統管理員。 |
| [取得 AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603737.aspx)    | 傳回目前設定 Azure SQL Server 或 Azure SQL Data Warehouse Azure Active Directory 系統管理員的相關資訊。 |

若要查看更多詳細資料的命令，每個，例如使用 PowerShell 命令取得說明``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``。

下列指令碼條款 Azure AD 管理員群組的名稱為**DBA_Group** (物件識別碼`40b79501-b343-44ed-9ce7-da4c8cc7353f`) **demo_server**的資源群組中的伺服器名稱為**群組 23**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group"
```

Azure AD 顯示名稱或使用者主要名稱，可接受的**顯示名稱**輸入的參數。 例如，``DisplayName="John Smith"``和``DisplayName="johns@contoso.com"``。 Azure AD 群組只 Azure AD 支援顯示名稱。

> [AZURE.NOTE] PowerShell 的 Azure 命令```Set-AzureRmSqlServerActiveDirectoryAdministrator```不會防止您佈建 Azure AD 管理員不受支援的使用者。 不支援的使用者可以提供，但無法連線至資料庫。 （請參閱支援管理員**Azure AD 功能和限制**以上的清單）。

下列範例會使用選擇性**ObjectID**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [AZURE.NOTE] 不是唯一的**顯示名稱**時，需要 Azure AD **ObjectID** 。 若要擷取的**ObjectID**和**顯示名稱**值，使用 Active Directory 的區段 Azure 傳統入口網站，並檢視使用者或群組的屬性。

下列範例會傳回目前的相關資訊 Azure SQL Server Azure AD 管理員︰

```
Get-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" –ServerName "demo_server" | Format-List
```

下列範例會移除 Azure AD 管理員︰
```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" –ServerName "demo_server"
```

您也可以使用 REST Api 提供 Azure Active Directory 系統管理員。 如需詳細資訊，請參閱[服務管理 REST API 參考和 Azure SQL 資料庫作業 Azure SQL 資料庫的作業](https://msdn.microsoft.com/library/azure/dn505719.aspx)

## <a name="5-configure-your-client-computers"></a>5。 設定您的用戶端電腦

在所有的用戶端電腦從您的應用程式或使用者連線至 Azure SQL 資料庫或 Azure SQL Data Warehouse 使用 Azure AD 身分識別，您必須安裝下列軟體︰

- .NET framework 4.6 或從[https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx)更新版本。
- Azure Active Directory 驗證文件庫的 SQL Server (**ADALSQL。DLL**) 提供多種語言 （x86 和 amd64） 從下載中心在[Microsoft Active Directory 驗證庫的 Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742)。

### <a name="tools"></a>工具

- 安裝[SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)或[SQL Server Data Tools for Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx)符合.NET Framework 4.6 需求。
- **ADALSQL SSMS 安裝 x86 版本。DLL**。
- SSDT 安裝**ADALSQL amd64 版本。DLL**。
- 從[Visual Studio 下載](https://www.visualstudio.com/downloads/download-visual-studio-vs)最新的 Visual Studio 符合.NET Framework 4.6 需求，但不會安裝**ADALSQL 必要的 amd64 版本。DLL**。

## <a name="6-create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>6。 對應至 Azure AD 身分識別資料庫中建立包含的資料庫使用者

### <a name="about-contained-database-users"></a>含的資料庫使用者

Azure Active Directory 驗證需要資料庫使用者建立為包含的資料庫使用者。 根據 Azure AD 身分識別，含的資料庫使用者是在主要資料庫中，沒有登入資料庫使用者和其對應至資料庫與相關聯的 Azure AD 目錄中的身分識別。 Azure AD 身分識別可以是個別的使用者帳戶或群組。 含的資料庫使用者的詳細資訊，請參閱[包含資料庫使用者進行您資料庫可攜式](https://msdn.microsoft.com/library/ff929188.aspx)文件。

> [AZURE.NOTE] 無法使用] 入口網站建立資料庫使用者 （不含系統管理員）。 RBAC 角色不會傳送到 SQL Server、 SQL 資料庫或 SQL Data Warehouse。 Azure RBAC 角色用來管理 Azure 資源，並不會套用至資料庫的權限。 例如， **SQL Server 參與者**角色不授予存取以連線至 SQL 資料庫或 SQL Data Warehouse。 直接在使用 SQL 陳述式的資料庫中必須授與存取權限。

### <a name="connect-to-the-user-database-or-data-warehouse-by-using-sql-server-management-studio-or-sql-server-data-tools"></a>使用 SQL Server Management Studio 或 SQL Server Data Tools 連線到使用者資料庫或資料倉庫

若要確認 Azure AD 系統管理員已正確設定，連線到使用 Azure AD 管理員帳戶的**主要**資料庫。
佈建 Azure AD 包含資料庫使用者 （而非伺服器的系統管理員擁有資料庫），連線至資料庫的 Azure AD 身分含有資料庫的存取權。

> [AZURE.IMPORTANT] 使用[SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)與 Visual Studio 2015 中的[SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) Azure Active Directory 驗證支援。 2016 年 8 月發行的 SSMS 也包含 Active Directory 通用驗證]，可讓系統管理員參考，需要使用智慧卡 pin]，或行動應用程式通知的電話、 文字訊息的多重因素驗證的支援。

#### <a name="connect-using-active-directory-integrated-authentication"></a>使用 Active Directory 整合式的驗證連線

如果您登入 Windows 同盟網域中使用您的 Azure Active Directory 認證，請使用這個方法。

1. 啟動 Management Studio 中] 或 [資料工具，然後在**連線至伺服器**（或**連線到資料庫引擎**）] 對話方塊中，在 [**驗證**] 方塊中，選取 [**作用中的目錄整合式驗證**。 不使用密碼，需要或者輸入因為您現有的認證呈現連線。
    ![選取 [AD 整合式的驗證][11]

2. 按一下 [**選項**] 按鈕，然後在 [**連接屬性**] 頁面上的 [在**連線至資料庫**] 方塊中，輸入使用者資料庫連線到您想要的名稱。
    ![選取 [資料庫名稱][13]


#### <a name="connect-using-active-directory-password-authentication"></a>使用 Active Directory 密碼驗證連線

Azure AD 主體名稱使用 Azure AD 連線受管理的網域時，請使用這個方法。 您也可以使用同盟沒有網域，例如在遠端工作的存取權的帳戶。

如果您登入 Windows 不 Azure 與同盟網域中使用的認證，或使用 Azure AD 驗證使用 Azure AD 根據初始或用戶端網域時，請使用這個方法。

1. 啟動 Management Studio 中] 或 [資料工具，然後在 [**連線到伺服器**（或**連線到資料庫引擎**）] 對話方塊中，在 [**驗證**] 方塊中，選取 [ **Active Directory 密碼驗證**。
2. 在 [**使用者名稱**] 方塊中，輸入您的 Azure Active Directory 使用者名稱的格式**username@domain.com**。 這必須是從 Azure Active Directory 帳戶或帳戶的網域建立同盟與 Azure Active Directory。
3. 在 [**密碼**] 方塊中輸入您的使用者密碼的 Azure Active Directory 帳戶或同盟網域的帳戶。
    ![選取 [AD 密碼驗證][12]

4. 按一下 [**選項**] 按鈕，然後在 [**連接屬性**] 頁面上的 [在**連線至資料庫**] 方塊中，輸入使用者資料庫連線到您想要的名稱。 （請參閱中的上一個選項的圖像）。


### <a name="create-an-azure-ad-contained-database-user-in-a-user-database"></a>使用者資料庫中建立包含 Azure AD 資料庫使用者

若要建立 Azure AD 包含資料庫使用者 （而非伺服器的系統管理員擁有資料庫），使用連線至資料庫 Azure AD 身分識別為具有的使用者至少**更改任何使用者**的權限。 然後使用下列的 SQL 語法︰

    CREATE USER <Azure_AD_principal_name>
    FROM EXTERNAL PROVIDER;


*Azure_AD_principal_name*可以 Azure AD 使用者的使用者主要名稱或 Azure AD 群組的顯示名稱。

**範例︰**若要建立包含的資料庫使用者代表 Azure AD 同盟或管理網域的使用者︰

    CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
    CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;

若要建立包含的資料庫使用者 Azure AD，或聯盟網域] 群組中，提供安全性群組的顯示名稱︰

    CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;

若要建立包含的資料庫使用者代表使用 Azure AD 權杖連接的應用程式︰

    CREATE USER [appName] FROM EXTERNAL PROVIDER;

如需建立包含的資料庫使用者根據 Azure Active Directory 身分識別的詳細資訊，請參閱[建立使用者 (SQL)](http://msdn.microsoft.com/library/ms173463.aspx)。


> [AZURE.NOTE] 移除 Azure SQL Server 的 Azure Active Directory 系統管理員可防止任何 Azure AD 驗證使用者連線至伺服器。 如果有必要，無法使用 Azure AD 使用者可以手動刪除 SQL 資料庫系統管理員。

當您建立資料庫的使用者時，該使用者接收**連線**權限，並可以連線至該資料庫，為**公用**角色的成員。 一開始提供給使用者的權限的權限授與**公用**角色或授與他們所屬的任何 Windows 群組任何權限。 一旦您佈建 Azure AD 式包含資料庫的使用者，您可以授與使用者額外的權限，以相同的方式當您授與其他任何類型的使用者權限。 通常授與資料庫角色的權限，並將使用者新增至角色。 如需詳細資訊，請參閱[資料庫引擎權限的基本概念](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx)。 如需有關特殊 SQL 資料庫角色的詳細資訊，請參閱[管理資料庫及 Azure SQL 資料庫中的登入](sql-database-manage-logins.md)。
管理網域]，將匯入的同盟的網域使用者必須使用受管理的網域身分識別。

> [AZURE.NOTE] Azure AD 使用者會標示資料庫中繼資料中輸入 E (EXTERNAL_USER) 與群組類型 X (EXTERNAL_GROUPS)。 如需詳細資訊，請參閱[sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx)。


## <a name="7-connect-by-using-azure-ad-identities"></a>7。 使用連線 Azure AD 身分識別

Azure Active Directory 驗證支援下列連線到使用 Azure AD 身分識別資料庫的方法︰

- 使用整合式的 Windows 驗證
- 使用 Azure AD 主體名稱和密碼
- 使用應用程式的權杖驗證

### <a name="71-connecting-using-integrated-windows-authentication"></a>7.1。 使用整合式 (Windows) 驗證連線

若要使用整合式的 Windows 驗證，您必須與 Azure Active Directory，同盟您網域的 Active Directory。 在 [使用者的網域認證] 底下的網域的電腦上必須執行您的用戶端應用程式 （或服務） 連線至資料庫。

若要連線至資料庫，使用整合式的驗證和 Azure AD 身分識別，資料庫連線字串中的驗證關鍵字必須設定為 [作用中的目錄整合。 下列 C# 程式碼範例使用 ADO.NET。

    string ConnectionString =
    @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

注意連線字串關鍵字``Integrated Security=True``不支援連線到 Azure SQL 資料庫。
請注意，進行 ODBC 連線時，您需要移除空格，並設定 'ActiveDirectoryIntegrated' 驗證。

### <a name="72-connecting-with-an-azure-ad-principal-name-and-a-password"></a>7.2。 Azure AD 主體名稱和密碼連線
若要連線至資料庫，使用整合式的驗證和 Azure AD 身分識別，驗證關鍵字必須設定為 Active Directory 密碼。 使用者識別碼 UID 密碼/顯示密碼詢問關鍵字和值，則必須包含的連接字串。 下列 C# 程式碼範例使用 ADO.NET。

    string ConnectionString =
      @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

進一步瞭解可用的示範程式碼範例使用[Azure AD 驗證 GitHub 示範](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)Azure AD 驗證方法。


### <a name="73-connecting-with-an-azure-ad-token"></a>Azure AD 權杖 7.3 連線
這種驗證方法可讓您取得權杖從 Azure Active Directory (AAD) 連線到 Azure SQL 資料庫或 Azure SQL Data Warehouse 的中間層服務。 讓複雜的情況，包括憑證驗證。 您必須完成使用 Azure AD 權杖驗證的四個基本步驟︰

1. Azure Active Directory 註冊您的應用程式，並取得您的程式碼的用戶端識別碼。 
2. 建立資料庫使用者代表應用程式。 （完成之前在步驟 6 中）。
3. 在用戶端電腦執行應用程式建立的憑證。
4. 將憑證新增您的應用程式鍵。

範例連線字串︰

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

如需詳細資訊，請參閱[SQL Server 的安全性部落格](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)。

### <a name="connecting-with-sqlcmd"></a>與 sqlcmd 連線  
下列陳述式中，使用版本 13.1 sqlcmd，從[下載中心](http://go.microsoft.com/fwlink/?LinkID=825643)進行連線。

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="see-also"></a>另請參閱

[管理資料庫及 Azure SQL 資料庫中的登入](sql-database-manage-logins.md)

[含的資料庫使用者](https://msdn.microsoft.com/library/ff929071.aspx)

[建立使用者 (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)


<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png

