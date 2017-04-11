<properties
    pageTitle="SQL 資料庫教學課程︰ 建立 SQL 資料庫 |Microsoft Azure"
    description="瞭解如何為您設定 SQL 資料庫邏輯伺服器、 伺服器防火牆規則，SQL 資料庫及範例資料。 此外，瞭解如何與連線用戶端工具、 設定使用者，並設定資料庫防火牆規則。"
    keywords="sql 資料庫教學課程中，建立 sql 資料庫"
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
    ms.date="09/07/2016"
    ms.author="carlrab"/>


# <a name="sql-database-tutorial-create-a-sql-database-in-minutes-by-using-the-azure-portal"></a>SQL 資料庫教學課程︰ 使用 Azure 入口網站，以分鐘為單位建立 SQL 資料庫

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

在本教學課程中，您可以瞭解如何使用 Azure 入口網站︰

- 建立含有範例資料的 Azure SQL 資料庫。
- 建立單一 IP 位址或 IP 位址範圍的伺服器層級的防火牆規則。

您可以使用[C#](sql-database-get-started-csharp.md)或[PowerShell](sql-database-get-started-powershell.md)來執行這些相同的工作。

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

<a name="create-logical-server-bk"></a>

## <a name="create-your-first-azure-sql-database"></a>建立您的第一個 Azure SQL 資料庫 

1. 如果您目前並未連線，連線至[Azure 入口網站](http://portal.azure.com)。
2. 按一下 [**新增**]，按一下 [**資料 + 的儲存空間**]，然後尋找**SQL 資料庫**。

    ![新的 sql 資料庫 1](./media/sql-database-get-started/sql-database-new-database-1.png)

3. 按一下以開啟 [SQL 資料庫刀**SQL 資料庫**]。 根據您的訂閱與您現有的物件 （例如現有伺服器） 的數目，而異此刀上的內容。

    ![新的 sql 資料庫 2](./media/sql-database-get-started/sql-database-new-database-2.png)

4. 在**資料庫名稱**] 文字方塊中，提供您的第一個資料庫-例如 「 我資料庫 」 的名稱。 綠色的核取記號會指出您所提供的有效的名稱。

    ![新的 sql 資料庫 3](./media/sql-database-get-started/sql-database-new-database-3.png)

5. 如果您有多個訂閱，請選取 [訂閱]。
6. 在 [**資源群組**] 底下，按一下 [**建立新**並提供您的第一個資源群組-例如 「 我的資源-群組 」 的名稱。 綠色的核取記號會指出您所提供的有效的名稱。

    ![新的 sql 資料庫 4](./media/sql-database-get-started/sql-database-new-database-4.png)

7. 在 [**選取來源**] 按一下**範例**，然後**選取 [範例**] 下按一下**AdventureWorksLT [V12]**。

    ![新的 sql 資料庫 5](./media/sql-database-get-started/sql-database-new-database-5.png)

8. 在 [**伺服器**] 底下，按一下 [**設定所需的設定**]。

    ![新的 sql 資料庫 6](./media/sql-database-get-started/sql-database-new-database-6.png)

9. 在伺服器刀中，按一下 [**建立新的伺服器**]。 Azure SQL 資料庫伺服器的物件，可以是新的伺服器或現有的伺服器中建立。

    ![新的 sql 資料庫 7](./media/sql-database-get-started/sql-database-new-database-7.png)

10. 檢閱**新的伺服器**刀，若要瞭解以提供您的新伺服器所需的資訊。

    ![新的 sql 資料庫 8](./media/sql-database-get-started/sql-database-new-database-8.png)

11. 在 [**伺服器名稱**] 文字方塊中，提供您的第一個伺服器-例如 「 我的新-伺服器-物件 」 的名稱。 綠色的核取記號會指出您所提供的有效的名稱。

    ![新的 sql 資料庫 9](./media/sql-database-get-started/sql-database-new-database-9.png)
 
12. 在**伺服器管理員登入**，提供此伺服器-例如 「 我的管理員-帳戶 」 的管理員登入的使用者名稱。 此登入稱為伺服器本金登入。 綠色的核取記號會指出您所提供的有效的名稱。

    ![新的 sql 資料庫 10](./media/sql-database-get-started/sql-database-new-database-10.png)

13. 在 [**密碼**] 和 [**確認密碼**下, 提供的密碼伺服器本金登入帳戶-例如"p@ssw0rd1"。 綠色的核取記號，表示您已提供正確的密碼。

    ![新的 sql 資料庫 11](./media/sql-database-get-started/sql-database-new-database-11.png)
 
14. 在 [**位置**] 下，選取 [適用於您的位置-例如 「 澳大利亞東亞 」 的資料中心]。

    ![新的 sql 資料庫 12](./media/sql-database-get-started/sql-database-new-database-12.png)

15. 在下 * * 建立 V12 伺服器 （最新的更新），請注意您只需要建立新版 Azure SQL server 的選項。

    ![新的 sql 資料庫 13](./media/sql-database-get-started/sql-database-new-database-13.png)

16. 請注意，根據預設，**允許 azure**服務存取伺服器的核取方塊已選取且無法變更以下。 這是進階的選項。 雖然在大部分情況下不需要，您可以變更此設定，在此伺服器物件，伺服器防火牆設定中。

    ![新的 sql 資料庫 14](./media/sql-database-get-started/sql-database-new-database-14.png)

17. 在新的伺服器刀，檢閱您的選擇，然後按一下以選取這個新的伺服器，為新資料庫的 [**選取**。

    ![新的 sql 資料庫 15](./media/sql-database-get-started/sql-database-new-database-15.png)

18. 在 SQL 資料庫刀，在 [**價格層**] 底下，按一下 [**標準] S2** ，然後按一下**基本**，選擇您的第一個資料庫的成本最低價格層。 您可以稍後變更價格層。

    ![新的 sql 資料庫 16](./media/sql-database-get-started/sql-database-new-database-16.png)

19. 在 SQL 資料庫防禦，以檢視您的選項，然後按一下 [**建立**]，建立您的第一個伺服器及資料庫。 您所提供的值會進行驗證，並部署開始。

    ![新的 sql 資料庫 17](./media/sql-database-get-started/sql-database-new-database-17.png)

20. 入口網站工具列上，按一下要檢查您的部署中的狀態**通知**項目。

    ![新的 sql 資料庫 18](./media/sql-database-get-started/sql-database-new-database-18.png)

>[AZURE.IMPORTANT]部署完成後，您的新 Azure SQL server 和資料庫中建立的 Azure。 您無法連線到您的新伺服器或使用 SQL Server 工具，直到您建立伺服器防火牆規則，以開啟來自外部 Azure SQL 資料庫防火牆的資料庫。

[AZURE.INCLUDE [Create server firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>後續步驟
現在，當您完成本教學課程中 SQL 資料庫，並建立含有一些範例資料的資料庫，您已準備好探索使用您偏好使用的工具。

- 如果您熟悉 TRANSACT-SQL 和 SQL Server 管理 Studio (SSMS)，瞭解如何[連線] 與 [查詢 SSMS 的 SQL 資料庫](sql-database-connect-query-ssms.md)。

- 如果您知道 Excel，瞭解如何[在 excel 的 Azure SQL 資料庫連線](sql-database-connect-excel.md)。

- 如果您已準備好開始撰寫程式碼，選擇您在[SQL 資料庫與 SQL Server 的連線文件庫](sql-database-libraries.md)的程式設計語言。

- 如果您想要將您內部部署的 SQL Server 資料庫移至 Azure，請參閱[移轉的資料庫的 SQL 資料庫，](sql-database-cloud-migrate.md)若要瞭解更多。

- 如果您想要使用 BCP 命令列工具載入從 CSV 檔案的新表格的一些資料，請參閱[載入將資料從 CSV 檔案使用 BCP SQL 資料庫](sql-database-load-from-csv-with-bcp.md)。

- 如果您想要開始探索 Azure SQL 資料庫安全性，請參閱[快速入門安全性](sql-database-get-started-security.md)


## <a name="additional-resources"></a>其他資源

[什麼是 SQL 資料庫？](sql-database-technical-overview.md)
