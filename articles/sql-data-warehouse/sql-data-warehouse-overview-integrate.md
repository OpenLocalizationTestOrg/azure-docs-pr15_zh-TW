<properties
   pageTitle="建立 SQL Data Warehouse 使用整合式的解決方案 |Microsoft Azure"
   description="工具和合作夥伴使用與 SQL Data Warehouse 整合的解決方案。 "
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

#<a name="leverage-other-services-with-sql-data-warehouse"></a>運用 SQL Data Warehouse 與其他服務
核心功能，除了 SQL Data Warehouse 可讓使用者以運用許多其他服務中 Azure 旁邊。  具體來說，我們目前採取深層整合以下列步驟︰

+ Power BI
+ Azure 資料工廠
+ Azure 機器學習
+ Azure 資料流狀況分析

我們正在透過 Azure 生態連線與其他服務。

##<a name="power-bi"></a>Power BI
Power BI 整合功能可讓您運用計算的 SQL Data Warehouse 與動態報告和 Power bi 的視覺效果。 Power BI 整合目前包括︰

+ **直接連接**︰ 更多進階與 SQL Data Warehouse 針對邏輯 pushdown 連線。  這會提供更快速地分析更大。
+ **在 Power BI 中開啟**: 「 Power BI 中開啟] 按鈕傳送執行個體資訊 Power BI，以便更順暢的連線。

請參閱[使用 Power BI 的整合](./sql-data-warehouse-integrate-power-bi.md)或[Power BI 文件](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx)，詳細的資訊。

##<a name="azure-data-factory"></a>Azure 資料工廠
Azure 資料工廠可讓使用者建立複雜的解壓縮載入管線的受管理的平台。  Azure 資料工廠 SQL Data Warehouse 的整合，包括下列︰

+ **預存程序**︰ 協調的 SQL Data Warehouse 預存程序執行。
+ **複製**︰ 使用 ADF，將資料移到 SQL Data Warehouse。  在幕後可用 ADF 的標準資料移動機制或 PolyBase 這項作業。 

請參閱[Azure 資料工廠的整合](./sql-data-warehouse-integrate-azure-data-factory.md)或[Azure 資料工廠文件](https://azure.microsoft.com/documentation/services/data-factory/)，詳細的資訊。

##<a name="azure-machine-learning"></a>Azure 機器學習
Azure 電腦學習是完全受管理的分析服務可讓使用者建立複雜的模型運用大型的一組預測工具。  SQL Data Warehouse 為來源與目的地這些模型支援下列功能︰

+ **讀取資料︰**在針對 SQL Data Warehouse 使用 T SQL 驅動模型。
+ **寫入的資料︰**回到 SQL Data Warehouse，從任何模型認可變更。

請參閱[Azure 電腦學習的整合](./sql-data-warehouse-integrate-azure-machine-learning.md)或[Azure 電腦學習文件](https://azure.microsoft.com/services/machine-learning/)，詳細的資訊。

##<a name="azure-stream-analytics"></a>Azure 資料流狀況分析
Azure 資料流分析是複雜，完全受管理的基礎結構，來處理和使用從 Azure 事件中心產生的事件資料。  使用 SQL Data Warehouse 的整合功能可讓資料流有效率地處理和儲存同時啟用更深入、 更多進階分析的關聯式資料的資料。  

+ **工作成果︰**輸出資料流分析工作即可直接傳送給 SQL Data Warehouse。

請參閱[Azure 資料流分析的整合](./sql-data-warehouse-integrate-azure-stream-analytics.md)或[Azure 資料流分析文件](https://azure.microsoft.com/documentation/services/stream-analytics/)，詳細的資訊。

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-partner-business-intelligence.md

<!--MSDN references-->

<!--Other Web references-->
