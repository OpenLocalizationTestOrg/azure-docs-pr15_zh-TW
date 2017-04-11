<properties
    pageTitle="Excel 連線到 SQL 資料庫 |Microsoft Azure"
    description="瞭解如何在 Microsoft Excel 連線到雲端中的 Azure SQL 資料庫。 將 Excel 資料匯入的報告及資料探索。"
    services="sql-database"
    keywords="連線 sql 至 excel，匯入資料至 excel"
    documentationCenter=""
    authors="joseidz"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/05/2016"
    ms.author="joseidz"/>


# <a name="sql-database-tutorial-connect-excel-to-an-azure-sql-database-and-create-a-report"></a>SQL 資料庫教學課程︰ 將 Excel 連接至 Azure SQL 資料庫，並建立報告

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

瞭解如何連線至雲端中的 SQL 資料庫的 Excel，因此可以匯入資料，並建立表格和資料庫中的數值為根據的圖表。 在此教學課程中會設定 Excel 與資料庫資料表之間的連線，儲存資料和連線資訊的 Excel 中儲存的檔案，然後建立樞紐分析圖從資料庫中的值。

在開始之前，您將需要 Azure SQL 資料庫。 如果您沒有帳戶，請參閱[建立您的第一個 SQL 資料庫](sql-database-get-started.md)]，取得設定的範例資料，並在幾分鐘的時間中執行的資料庫。 在本文中，您將範例將資料匯入 Excel 的文章，但您可以依照您自己的資料類似的步驟。

您也必須份 Excel 複本。 本文所用的[Microsoft Excel 2016](https://products.office.com/en-US/)。

## <a name="connect-excel-to-a-sql-database-and-create-an-odc-file"></a>將 Excel 連接 SQL 資料庫，並建立 odc 檔

1.  若要將 Excel 連線至 SQL 資料庫中，開啟 Excel，然後建立新的活頁簿或開啟現有的 Excel 活頁簿。

2.  在頁面頂端的功能表列中按一下 [**資料**]，按一下 [**從其他來源**，然後按一下**從 SQL Server]**。

    ![選取資料來源︰ Excel 連線到 SQL 資料庫。](./media/sql-database-connect-excel/excel_data_source.png)

    隨後便會開啟 [資料連線精靈。

3.  在 [**資料庫伺服器的連線**] 對話方塊中，輸入您想要在表單中連線至 SQL 資料庫**伺服器名稱**<*伺服器名稱*>**。 database.windows.net**。 例如， **adworkserver.database.windows.net**。

4.  在 [**登入認證**] 底下按一下 [**使用下列的使用者名稱和密碼**，輸入**使用者名稱**和**密碼**您設定的 SQL 資料庫伺服器時，您所建立，然後按一下 [**下一步**]。

    ![輸入伺服器名稱與登入認證](./media/sql-database-connect-excel/connect-to-server.png)

    > [AZURE.TIP] 根據您的網路環境，您可能無法連線，或如果 SQL 資料庫伺服器不允許從您的用戶端 IP 位址，您可能會遺失連線。 移至[Azure 入口網站](https://portal.azure.com/)，按一下 SQL server，按一下您的伺服器、 按一下 [設定] 底下的 [防火牆，新增您的用戶端 IP 位址。 如需詳細資訊，請參閱[如何設定防火牆設定](sql-database-configure-firewall-settings.md)。

5. 在 [**選取資料庫及表格**] 對話方塊中，選取您想要從清單中，使用的資料庫，然後按一下 [表格或您想要使用的檢視 （我們之所以選擇這個**vGetAllCategories**），然後按一下 [**下一步**。

    ![選取資料庫及表格。](./media/sql-database-connect-excel/select-database-and-table.png)

    [**儲存資料連線檔案] 與 [完成**] 對話方塊隨即開啟，並提供 Excel 使用的 Office 資料庫連線 (query) 檔案的相關資訊的位置。 您可以保留預設值，或自訂您的選擇。

6. 您可以保留預設值，但請特別的**檔案名稱**。 **描述**、**好記的名稱**，然後**搜尋關鍵字**協助您與其他使用者記住什麼連線到並尋找連線。 如果您想要讓它可以更新當您連線，然後再按一下 [**完成]**儲存 odc 檔案中的連線資訊，請按一下 [**永遠嘗試使用此檔案，以重新整理資料**]。

    ![儲存 odc 檔案](./media/sql-database-connect-excel/save-odc-file.png)

    [**匯入資料**] 對話方塊隨即出現。

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>將 Excel 資料匯入及建立樞紐分析圖
現在，您所建立的連線，然後建立資料和連線資訊的檔案，您正在讀取匯入資料。

1. 在 [**匯入資料**] 對話方塊中，按一下您要呈現您的資料工作表中的選項，然後按一下**[確定]**。 我們之所以選擇這個**樞紐分析圖**。 您也可以選擇建立**新的工作表**或**新增此資料至資料模型**。 如需有關的資料模型的詳細資訊，請參閱[建立 Excel 中的資料模型](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B)。 按一下 [**屬性**探索您在上一個步驟中建立 odc 檔案的相關資訊，並選擇 [重新整理資料的選項。

    ![在 Excel 中選擇資料的格式](./media/sql-database-connect-excel/import-data.png)

    工作表現在有空白的樞紐分析表和圖表。

8. 在 [**樞紐分析表欄位**] 中，選取所有核取方塊您想要檢視的欄位。

    ![設定資料庫的報表。](./media/sql-database-connect-excel/power-pivot-results.png)

> [AZURE.TIP]如果您要連線至資料庫的其他 Excel 活頁簿和工作表時，按一下 [**資料****連線**，按一下 [**新增**]，選擇從清單中，您建立的連線，然後按一下然後按一下 [**開啟**。
> ![開啟從另一個活頁簿的連線](./media/sql-database-connect-excel/open-from-another-workbook.png)

## <a name="next-steps"></a>後續步驟

- 瞭解如何[連線至 SQL 資料庫的 SQL Server Management Studio 中](sql-database-connect-query-ssms.md)的進階查詢及分析。
- 深入了解[彈性的資料庫](sql-database-elastic-pool.md)的優點。
- 瞭解如何[建立 web 應用程式的連線至後端上的 SQL 資料庫](../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)。
