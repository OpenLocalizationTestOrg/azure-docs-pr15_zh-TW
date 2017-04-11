<properties
   pageTitle="查詢 Azure SQL Data Warehouse (Visual Studio) |Microsoft Azure"
   description="使用 Visual Studio SQL Data Warehouse 查詢。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="sonyama;barbkess"/>

# <a name="query-azure-sql-data-warehouse-visual-studio"></a>查詢 Azure SQL Data Warehouse (Visual Studio)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Azure 機器學習](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

使用查詢 Azure SQL Data Warehouse Visual Studio 中只需幾分鐘。 此方法在 Visual Studio 中使用 SQL Server Data Tools (SSDT) 副檔名。 

## <a name="prerequisites"></a>必要條件

若要使用此教學課程中，您需要︰

+ 現有的 SQL 資料倉庫。 若要建立一個，請參閱[建立 SQL Data Warehouse][]。
+ Visual studio SSDT。 如果您有 Visual Studio 時，您可能已經擁有此。 如需安裝指示與選項，請參閱[安裝的 Visual Studio 與 SSDT][]。
+ 完整的 SQL server 名稱。 若要尋找此問題，請參閱[連線至 SQL Data Warehouse][]。

## <a name="1-connect-to-your-sql-data-warehouse"></a>1.連線至您的 SQL Data Warehouse

1. 開啟 Visual Studio 2013 或 2015年。
2. 開啟 [SQL Server 物件總管]。 若要這麼做，請選取 [**檢視** > **SQL Server 物件總管]**。

    ![SQL Server 物件總管][1]

3. 按一下 [**新增 SQL Server** ] 圖示。

    ![新增 SQL Server][2]

4. 填寫 [連線到伺服器] 視窗中的欄位。

    ![連線到伺服器][3]

    - **伺服器名稱**。 輸入先前識別的**伺服器名稱**。
    - **驗證**。 選取 [ **SQL Server 驗證**或**Active Directory 整合式的驗證**。
    - **使用者名稱**和**密碼**。 如果已選取 [上方的 [SQL Server 驗證，請輸入使用者名稱和密碼。
    - 按一下 [**連線**]。

5. 若要瀏覽展開 Azure SQL server。 您可以檢視伺服器相關聯的資料庫。 展開 AdventureWorksDW 若要查看您的範例資料庫中的資料表。

    ![探索 AdventureWorksDW][4]

## <a name="2-run-a-sample-query"></a>2.執行範例查詢

現在，您的資料庫中建立連線，讓我們來撰寫的查詢。

1. 以滑鼠右鍵按一下您在 SQL Server 物件總管] 中的資料庫。

2. 選取**新的查詢**。 新的 [查詢] 視窗隨即開啟。

    ![新的查詢][5]

3. 將這個 TSQL 查詢複製到 [查詢] 視窗中︰

    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```

4. 執行查詢。 若要這麼做，請按一下綠色箭頭，或使用下列快速鍵︰ `CTRL` + `SHIFT` + `E`。

    ![執行查詢][6]

5. 查看查詢結果。 在此範例中，FactInternetSales 表格中有 60398 的列。

    ![查詢結果][7]

## <a name="next-steps"></a>後續步驟

現在，您可以連線並查詢，請嘗試[視覺化使用中的資料][]。

若要設定您的環境的 Azure Active Directory 驗證，請參閱[以 SQL Data Warehouse 驗證][]。

<!--Arcticles-->
[連線至 SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[建立 SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[安裝的 Visual Studio 與 SSDT]: sql-data-warehouse-install-visual-studio.md
[SQL Data Warehouse 驗證]: sql-data-warehouse-authentication.md
[視覺化使用中的資料]: sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-query-visual-studio/open-ssdt.png
[2]: media/sql-data-warehouse-query-visual-studio/add-server.png
[3]: media/sql-data-warehouse-query-visual-studio/connection-dialog.png
[4]: media/sql-data-warehouse-query-visual-studio/explore-sample.png
[5]: media/sql-data-warehouse-query-visual-studio/new-query2.png
[6]: media/sql-data-warehouse-query-visual-studio/run-query.png
[7]: media/sql-data-warehouse-query-visual-studio/query-results.png
