<properties
   pageTitle="SSMS 支援的 SQL 資料庫與 SQL Data Warehouse Azure AD MFA |Microsoft Azure"
   description="SQL 資料庫及 SQL Data Warehouse 使用 SSMS 多重層層防護驗證。"
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
   ms.date="10/04/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="ssms-support-for-azure-ad-mfa-with-sql-database-and-sql-data-warehouse"></a>Azure AD MFA SQL 資料庫與 SQL Data Warehouse SSMS 支援

Azure SQL 資料庫和 Azure SQL Data Warehouse 現在支援連線從 SQL Server 管理 Studio (SSMS) 使用*Active Directory 通用驗證*。 Active Directory 通用驗證是互動式的工作流程的支援*Azure 多重因素驗證*(MFA)。 Azure MFA 可協助保護存取資料和會議的簡單登入程序的使用者需求時的應用程式。 它提供強式驗證於某範圍內的簡單的驗證選項 — 電話、 簡訊智慧卡 pin]，或行動應用程式通知，可讓使用者選擇方法他們想要。 如需多重因素驗證的說明，請參閱[多重因素驗證](../multi-factor-authentication/multi-factor-authentication.md)。

SSMS 現在支援︰

- 互動式的快顯對話方塊方塊驗證有可能的 Azure AD MFA。
- 非互動 Active Directory 密碼] 和 [作用中的目錄整合式驗證方法可在多種不同的應用程式 （ADO.NET、 JDBC、 ODBC 等）。 這兩種方法永遠不會導致快顯對話方塊。

當使用者帳戶設定為 MFA 互動式驗證工作流程需要透過快顯對話方塊、 智慧卡使用等其他使用者互動。當使用者帳戶設定為 MFA 時，使用者必須先選取 Azure 通用驗證連線。 如果使用者帳戶並不需要 MFA，使用者仍然可以使用其他兩個 Azure Active Directory 驗證選項。

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>SQL 資料庫及 SQL Data Warehouse 通用驗證限制

- SSMS 是目前已為 MFA 透過 Active Directory 通用驗證啟用工具。
- 使用通用驗證 SSMS 的執行個體，以單一 Azure Active Directory 帳戶可以登入。 若要為另一個 Azure AD 帳戶登入，您必須使用另一個 SSMS 執行個體。 （這項限制為限制在 Active Directory 通用驗證; 您可以登入使用 Active Directory 密碼驗證、 Active Directory 整合式驗證] 或 SQL Server 驗證的不同伺服器）。
- SSMS 支援 Active Directory 通用驗證物件總管]、 [查詢編輯器] 和 [查詢儲存的視覺效果。
- DacFx 和結構描述設計工具都不支援通用驗證。
- MSA 帳戶不支援 Active Directory 通用驗證。
- SSMS 不支援 active Directory 通用驗證的使用者匯入至目前的 Active Directory 來自其他 Azure 作用中的目錄。 不支援這些使用者，因為這需要的租用戶 ID 驗證帳戶，並沒有提供的機制。
- 除了，您必須使用受支援的版本的 SSMS 有 Active Directory 通用驗證沒有其他的軟體需求。

## <a name="configuration-steps"></a>設定步驟

實作多重因素驗證需要四個基本步驟。

1. **設定 Azure Active Directory** – 的詳細資訊，請參閱[整合您的內部部署識別與 Azure Active Directory](../active-directory/active-directory-aadconnect.md)、[新增您自己的網域名稱，以 Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)、 [Microsoft Azure 現在支援與 Windows Server 的 Active Directory federation](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)、[管理 Azure AD 目錄](https://msdn.microsoft.com/library/azure/hh967611.aspx)，以及[管理使用 Windows PowerShell 的 Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx)。

2. **設定 MFA** – 逐步指示，請參閱[設定 Azure 多重因素驗證](../multi-factor-authentication/multi-factor-authentication-whats-next.md)。 

3. **設定 SQL 資料庫或 SQL Azure AD 驗證的 Data Warehouse** – 的逐步指示，請參閱[連線至 SQL 資料庫或 SQL 資料倉庫來使用 Azure Active Directory 驗證](sql-database-aad-authentication.md)。

4. **下載 SSMS** – 在用戶端電腦上下載最新的 SSMS (至少年 8 月 2016年)，從[下載 SQL Server 管理 Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)。

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>使用通用驗證與 SSMS 連線

下列步驟說明如何使用最新的 SSMS 連線至 SQL 資料庫或 SQL Data Warehouse。

1. 若要連線使用通用驗證]，在 [**連線至伺服器**] 對話方塊上，選取 [**作用中的目錄通用驗證**]。
![1mfa 標準連線][1]

2. 平常的 SQL 資料庫及 SQL Data Warehouse，您必須按一下 [**選項]** ，並在 [**選項**] 對話方塊中指定的資料庫。 然後按一下 [**連線**。
3. **登入您的帳戶**] 對話方塊出現時，提供的帳戶和您的 Azure Active Directory 身分識別的密碼。
![2mfa 登入][2]

    > [AZURE.NOTE] 通用帳戶不需要 MFA 驗證，您連線到目前為止。 為需要 MFA 使用者，請繼續執行下列步驟。
 
4. 可能會出現兩個 MFA 設定] 對話方塊。 一次作業 MFA 系統管理員設定，而定，因此可能是選用。 啟用 MFA 網域中的此步驟會有時預先定義 （例如，網域需要使用者使用的智慧卡和 pin）。  
![3mfa 設定][3]

5. 第二個可能的一次] 對話方塊可讓您選取您的驗證方法的詳細資料。 設定您的系統管理員可能的選項。
![4mfa 驗證 1][4]
 
6. Azure Active Directory 會將確認資訊傳送給您。 當您收到的驗證碼時，輸入 [**輸入驗證碼**] 方塊，然後按一下 [**登入**。
![5mfa 驗證 2][5]

完成驗證後，SSMS 連線以正常方式假設有效的認證與防火牆存取權。

##<a name="next-steps"></a>後續步驟  

將您的資料庫的存取權授與其他人︰ [SQL 資料庫驗證與授權︰ 授與存取](sql-database-manage-logins.md)  
請確定其他人可以透過防火牆連接︰[使用 Azure 入口網站的 Azure SQL 資料庫伺服器層級的防火牆規則設定](sql-database-configure-firewall-settings.md)


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

