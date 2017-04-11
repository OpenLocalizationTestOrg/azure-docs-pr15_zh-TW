<properties
   pageTitle="使用 SQL Data Warehouse Azure 資料流分析 |Microsoft Azure"
   description="適用於開發解決方案中使用 Azure SQL Data Warehouse Azure 資料流分析的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>使用 SQL Data Warehouse Azure 資料流狀況分析

Azure 資料流分析是完全受管理的服務，提供低延遲可用性、 調整複雜的事件處理透過串流雲端中的資料。 您可以先閱讀[簡介 Azure 資料流分析][]瞭解基本概念。 然後，您可以瞭解如何建立具有資料流分析的端對端解決方案，按照[開始使用 Azure 資料流分析][]教學課程。

在本文中，您將學習如何使用 Azure SQL Data Warehouse 資料庫作為輸出接收，為您的資料流分析工作。

## <a name="prerequisites"></a>必要條件

首先，請執行下列步驟[開始使用 Azure 資料流分析資料][]的教學課程中。  

1. 建立事件中心輸入
2. 設定和啟動事件產生器應用程式
3. 佈建資料流分析工作
4. 指定工作輸入和查詢

然後，建立 Azure SQL Data Warehouse 資料庫

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>指定工作輸出︰ Azure SQL Data Warehouse 資料庫

### <a name="step-1"></a>步驟 1

在您的資料流分析工作按一下**輸出**頂端的頁面]，然後按一下**新增輸出**。

### <a name="step-2"></a>步驟 2

選取 [SQL 資料庫]，然後按一下 [下一步]。

![][add-output]

### <a name="step-3"></a>步驟 3
在下一個頁面上輸入下列值︰

- *輸出 alias （別名)*: 輸入此工作輸出易記的名稱。
- *訂閱*︰
    - 如果 SQL Data Warehouse 資料庫在同一份訂閱，以資料流分析工作，請選取 [從目前的訂閱的 [使用 SQL 資料庫]。
    - 如果資料庫在不同的訂閱，請選取 [從其他訂閱的 [使用 SQL 資料庫]。
- *資料庫*︰ 指定目的地資料庫的名稱。
- *伺服器名稱*︰ 指定您指定的資料庫的伺服器名稱。 若要尋找此，您可以使用 Azure 傳統入口網站。

![][server-name]

- *使用者名稱*︰ 指定具有資料庫的寫入權限的帳戶的使用者名稱。
- *密碼*︰ 提供指定的使用者帳戶的密碼。
- *資料表*︰ 在資料庫中指定目標資料表的名稱。

![][add-database]

### <a name="step-4"></a>步驟 4

按一下 [新增此工作輸出，並確認資料流分析可以順利連線至資料庫中的 [檢查] 按鈕。

![][test-connection]

當資料庫的連線，您會看到通知底部的入口網站。 您可以按一下 [測試連線]，測試連線至資料庫底部。

## <a name="next-steps"></a>後續步驟

如需整合的概觀，請參閱[SQL Data Warehouse 整合概觀][]。

如需開發秘訣，請參閱[SQL Data Warehouse 開發概觀][]。

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Azure 資料流分析簡介]: ../stream-analytics/stream-analytics-introduction.md
[開始使用 Azure 資料流狀況分析]: ../stream-analytics/stream-analytics-get-started.md
[SQL Data Warehouse 開發概觀]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse 整合概觀]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/
