<properties
   pageTitle="使用 SQL Data Warehouse Azure 資料工廠 |Microsoft Azure"
   description="Azure SQL Data Warehouse Azure 資料工廠 (ADF) 使用開發解決方案的秘訣。"
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
   ms.date="08/08/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>使用 SQL Data Warehouse Azure 資料工廠

Azure 資料工廠提供完整的管理方法，來協調資料的傳送和 SQL Data Warehouse 預存程序執行。  這可讓您更輕鬆地安排與排程複雜擷取轉換]，並且載入 (ETL) 程序與 SQL Data Warehouse。 如 Azure 資料工廠更完整的概觀，請參閱[Azure 資料工廠文件][]。

## <a name="data-movement"></a>移動資料

Azure 資料工廠可讓內部部署和不同 Azure 服務之間移動資料。  Azure 資料工廠整體、 目前整合支援資料移動至並從下列位置︰

+ Azure blob 儲存體
+ Azure SQL 資料庫
+ 內部部署的 SQL Server
+ SQL Server，內部 IaaS

如需如何設定資料複製活動請參閱[複製與 Azure 資料工廠的資料][]

## <a name="stored-procedures"></a>預存程序
 以相同的方式，可用於排程的資料傳輸，Azure 資料工廠也可以用來協調的預存程序執行。  這會建立更複雜的管線並延伸利用計算 SQL Data Warehouse Azure 資料工廠的能力。

## <a name="next-steps"></a>後續步驟
如需整合的概觀，請參閱[SQL Data Warehouse 整合概觀][]。
如需開發秘訣，請參閱[SQL Data Warehouse 開發概觀][]。

<!--Image references-->

<!--Article references-->

[Azure 資料工廠複製資料]: ../data-factory/data-factory-data-movement-activities.md
[SQL Data Warehouse 開發概觀]: ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse 整合概觀]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure 資料工廠文件]:https://azure.microsoft.com/documentation/services/data-factory/

