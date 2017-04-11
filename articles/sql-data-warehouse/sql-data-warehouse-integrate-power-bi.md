<properties
   pageTitle="使用 Power BI 與 SQL Data Warehouse |Microsoft Azure"
   description="使用 Power BI Azure SQL Data Warehouse 具有開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/31/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="use-power-bi-with-sql-data-warehouse"></a>使用 SQL Data Warehouse Power BI
為 Azure SQL 資料庫]，與 SQL 資料倉庫直接將連線允許使用者運用功能強大的邏輯 pushdown 同時分析的 Power BI 功能。  以直接連線時，查詢會傳送回即時您 Azure SQL Data Warehouse 探索資料。  這項目，結合使用 SQL Data Warehouse，可讓使用者在分鐘 tb 資料中建立動態報表的小數位數。  此外，在 Power BI] 按鈕開啟的簡介可讓使用者直接連接到其 SQL Data Warehouse Power BI Azure 的其他部分從收集資訊。

使用時直接連線請注意︰

+ 連線 （請參閱下方更多詳細資料） 時，指定的完整的伺服器名稱
+ 確定資料庫的防火牆規則會設定為 「 Azure 服務允許存取]。
+ 每一個動作，例如選取欄] 或 [新增篩選會直接查詢資料倉庫
+ 並排會重新整理每 15 分鐘 （重新整理不需要排程）
+ 問與答不適用於直接連接的資料集
+ 結構描述的變更會不挑選自動
+ 所有直接連接的查詢 2 分鐘後會逾時

隨著我們持續改進的體驗，可能會變更這些限制和備忘稿。 下列被詳細連線的步驟。  

## <a name="using-the-open-in-power-bi-button"></a>使用 「 Power BI 中開啟] 按鈕
SQL Data Warehouse 和 Power BI 之間移動最簡單的方法是在 Power BI] 按鈕開啟。 此按鈕可讓您完美，開始建立新的儀表板 Power BI 中。  

1.  若要開始瀏覽至您的 SQL Data Warehouse 執行個體 Azure 傳統入口網站中。
2.  按一下 「 Power BI 中開啟] 按鈕。
3.  如果我們無法直接登入，或如果您沒有 Power BI 帳戶，您必須登入。  
4.  您會導向至 SQL Data Warehouse 連線頁面上，從預先填入您 SQL Data Warehouse 的資訊。
5.  輸入您的認證後您會完全連線到您的 SQL Data Warehouse。

## <a name="connecting-through-the-power-bi-portal"></a>透過 Power BI 網站的連線
除了使用在 Power BI] 按鈕中的開啟，使用者也可以連線到其 SQL Data Warehouse，透過 Power BI 網站。

1.  按一下功能窗格底部的 [取得資料]。
2.  選取 「 資料庫 」。
3.  一次在資料庫頁面上，選取 [' Azure SQL Data Warehouse 」，然後按一下 [連線]。
4.  輸入所需的連線資訊。  Azure 入口網站中可以找到您的伺服器名稱和資料庫名稱。
5.  您會導向至 Power bi 的主頁面，而且您連線 '資料集] 底下的新項目後會顯示您的執行個體的名稱。  
6.   您可以按一下要探索的資料表和檢視資料庫中的所有新的資料集。 選取欄會將查詢傳回的來源，以動態方式建立您的視覺效果。 這些視覺效果儲存在新的報表，然後重新固定至您的儀表板。

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
