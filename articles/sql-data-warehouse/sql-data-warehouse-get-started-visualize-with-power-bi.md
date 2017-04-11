<properties
   pageTitle="使用 Power BI Microsoft Azure SQL Data Warehouse 資料視覺化"
   description="使用 Power BI 的 SQL Data Warehouse 資料視覺化"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor="" />

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="lodipalm;barbkess;sonyama" />

# <a name="visualize-data-with-power-bi"></a>使用 Power BI 的資料視覺化

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Azure 機器學習](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

本教學課程教您如何使用 Power BI 連線至 SQL Data Warehouse 並建立幾個基本的視覺效果。

> [AZURE.VIDEO azure-sql-data-warehouse-sample-data-and-powerbi]

## <a name="prerequisites"></a>必要條件

若要在逐步執行本教學課程中，您需要︰

- SQL Data Warehouse 預先載入與 AdventureWorksDW 資料庫。 佈建這種情況，請參閱[建立 SQL Data Warehouse][]並選擇載入範例資料。 如果您已有資料倉庫，但不是會有範例資料，您可以[手動載入範例資料][]。


## <a name="1-connect-to-your-database"></a>1.連線至您的資料庫

若要開啟 Power BI，並連線到您 AdventureWorksDW 資料庫︰

1. 登入[Azure 入口網站][]。
2. 按一下 [ **SQL 資料庫]** ，然後選擇您 AdventureWorks SQL Data Warehouse 的資料庫。

    ![尋找您的資料庫][1]

3. 按一下 「 Power BI 中開啟] 按鈕。

    ![Power BI] 按鈕][2]

4. 您現在應該會看到 [SQL Data Warehouse 連線] 頁面顯示您的資料庫的網址。 按一下 [下一步]。

    ![Power BI 連線][3]

6. 輸入您的 Azure SQL server 使用者名稱和密碼，並將完整連線至 SQL Data Warehouse 資料庫。

    ![Power BI 登入][4]

7. 一旦您登入 Power BI，按一下 [上左刀 AdventureWorksDW 資料集]。 這會開啟資料庫。

    ![Power BI 開啟 AdventureWorksDW][5]



## <a name="2-create-a-report"></a>2.建立報表

現在就可以開始使用 Power BI 分析 AdventureWorksDW 範例資料。 若要執行分析，AdventureWorksDW 會有一個名為 AggregateSales 的檢視。 此檢視包含多個主要的度量，用以分析銷售量的公司。

1. 若要建立的地圖根據郵遞區號，銷售金額右側的欄位] 窗格中，按一下 [AggregateSales 以將其展開。 按一下 [郵遞區號] 和 [SalesAmount 欄加以選取。

    ![Power BI 選取 AggregateSales][6]

    Power BI 自動辨識這是地理資料，您將其放在地圖上。

    ![Power BI 地圖][7]

2. 此步驟建立長條圖顯示每個客戶收入銷售金額。 若要建立此移至 [展開] AggregateSales 檢視。 按一下 [SalesAmount] 欄位。 向左拖曳 [客戶收入欄位拖放到座標軸。

    ![Power BI 選取座標軸][8]

    我們在向左移動橫條圖。

    ![列的 power BI][9]

3. 此步驟建立折線圖顯示每個訂單日期的銷售金額。 若要建立此移至 [展開] AggregateSales 檢視。 按一下 [SalesAmount 和 [訂單日期]。 視覺效果中的資料行按一下 [折線圖] 圖示。這是在第二行底下視覺效果中的第一個圖示。

    ![Power BI 選取折線圖][10]

    現在您已經有的報表，顯示資料的三個不同的視覺效果。

    ![Power BI 折線圖][11]

按一下 [**檔案**，然後選取 [**儲存**，您可以隨時儲存您的進度。

## <a name="next-steps"></a>後續步驟
現在，我們已提供您一些時間準備範例資料，請參閱[開發][]、[載入][]時，或[移轉][]的方式。 或參閱[Power BI 網站][]。

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[移轉]: sql-data-warehouse-overview-migrate.md
[開發]: sql-data-warehouse-overview-develop.md
[載入]: sql-data-warehouse-overview-load.md
[手動載入範例資料]: sql-data-warehouse-load-sample-databases.md
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[建立 SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!--Other-->
[Azure 入口網站]: https://portal.azure.com/
[Power BI 網站]: http://www.powerbi.com/
