   <properties
   pageTitle="將資料載入至 Azure SQL Data Warehouse |Microsoft Azure"
   description="瞭解資料載入到 SQL Data Warehouse 常見的情況。 包括使用 PolyBase、 Azure blob 儲存體、 一般檔案，以及磁碟傳送。 您也可以使用協力廠商工具。"
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
   ms.date="07/12/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="load-data-into-azure-sql-data-warehouse"></a>將資料載入至 Azure SQL Data Warehouse

摘要的案例選項和 SQL Data Warehouse 將資料載入的建議。

載入資料的辛苦部分通常準備資料載入。 Azure 簡化載入使用 Azure blob 儲存體做常見的資料存放區的服務，然後使用 Azure 資料工廠協調 Azure 服務之間的通訊和資料移動即可。 這些處理程序會整合 PolyBase 技術平行資料從 Azure blob 儲存體載入 SQL Data Warehouse 使用人連線平行處理 (MPP)。 

載入範例資料庫的教學課程，請參閱[載入範例資料庫][]。

## <a name="load-from-azure-blob-storage"></a>從 Azure blob 儲存體載入
將資料匯入 SQL Data Warehouse 最快的方法是使用 PolyBase 從 Azure blob 儲存體載入資料。 PolyBase 使用 SQL Data Warehouse 的人連線平行處理 (MPP) 設計從 Azure blob 儲存體載入平行的資料。 若要使用 PolyBase，您可以使用 T SQL 命令或 Azure 資料工廠管道的郵件。

### <a name="1-use-polybase-and-t-sql"></a>1.使用 PolyBase 和 T SQL

載入程序的摘要︰

2. 將資料格式化為 utf-8 由於 PolyBase 目前不支援 utf-16。
2. 將資料移到 Azure blob 儲存體，並將其儲存在文字檔案。
3. 在 SQL Data Warehouse 定義的位置和資料的格式設定外部的物件
4. 執行 T SQL 命令平行將資料載入到新的資料庫資料表。

<!-- 5. Schedule and run a loading job. --> 

教學課程，請參閱[載入資料從 SQL Data Warehouse (PolyBase) 至 Azure blob 儲存體][]。

### <a name="2-use-azure-data-factory"></a>2.使用 Azure 資料工廠

若要使用 PolyBase 更簡單的方法，您可以建立使用 PolyBase 資料從 Azure blob 儲存體載入 SQL Data Warehouse Azure 資料工廠管線。 這是快速設定，因為您不需要定義 T SQL 物件。 如果您需要查詢外部資料，而不將其匯入，請使用 T SQL。 

載入程序的摘要︰

2. 將資料格式化為 utf-8 由於 PolyBase 目前不支援 utf-16。
2. 將資料移到 Azure blob 儲存體，並將其儲存在文字檔案。
3. 建立 Azure 資料工廠管線內嵌的資料。 使用 [PolyBase 選項。
4. 排程，並執行管道的郵件。

教學課程，請參閱[載入資料從 SQL Data Warehouse （Azure 資料工廠） 至 Azure blob 儲存體][]。


## <a name="load-from-sql-server"></a>載入從 SQL Server
若要從 SQL Server 資料載入 SQL Data Warehouse，您可以使用整合服務 (SSIS)、 傳輸一般檔案或運送磁碟至 Microsoft。 執行的不同摘要，請參閱閱讀載入程序和教學課程的連結。

若要規劃從 SQL Server 完整資料移轉到 SQL Data Warehouse，請參閱[移轉概觀][]。 

### <a name="use-integration-services-ssis"></a>使用整合服務 (SSIS)
如果您已使用載入到 SQL Server 整合服務 (SSIS) 套件，您可以更新為來源和 SQL Data Warehouse 的 SQL Server 作為目的地套件。 這是快速又簡單，而是很不錯的選擇，如果您不想要移轉雲端中已使用的資料載入程序。 缺點是載入會比使用 PolyBase，因為此 SSIS 並不會執行載入平行慢。

載入程序的摘要︰

1. 修改您的整合服務套件，以指向來源的 SQL Server 執行個體和 SQL Data Warehouse 資料庫的目的地。
2. 如果找不到已，請以 SQL Data Warehouse，移轉您的結構描述。
3. 變更您的套件中的對應使用只支援的 SQL Data Warehouse 的資料類型。
3. 排程與執行套件。

教學課程，請參閱[載入資料從 SQL Server 至 Azure SQL 資料倉庫 (SSIS)][]。

### <a name="use-azcopy-recommended-for--10-tb-data"></a>使用 AZCopy （< 10 TB 資料的建議使用）
如果您的資料大小 < 10 TB，您可以從 SQL Server 的資料匯出到一般檔案、 檔案複製到 Azure blob 儲存體，，然後，將資料載入 SQL Data Warehouse 使用 PolyBase

載入程序的摘要︰

1. 使用 SQL Server 資料匯出到一般檔案 bcp 命令列公用程式。
2. 使用 AZCopy 命令列公用程式，將資料複製到 Azure blob 儲存體一般檔案。
3. 若要將 SQL Data Warehouse 載入使用 PolyBase。

教學課程，請參閱[載入資料從 SQL Data Warehouse (PolyBase) 至 Azure blob 儲存體][]。

### <a name="use-bcp"></a>使用 bcp
如果您有少量的資料您可以使用 bcp 直接載入 Azure SQL Data Warehouse。

載入程序的摘要︰
1. 使用 SQL Server 資料匯出到一般檔案 bcp 命令列公用程式。
2. 使用 bcp 資料載入從一般檔案直接 SQL Data Warehouse。

教學課程，請參閱[載入資料從 SQL Server Azure SQL Data Warehouse (bcp)][]。


### <a name="use-importexport-recommended-for--10-tb-data"></a>使用 [匯入/匯出 （> 10 TB 資料的建議使用）
如果您的資料大小 > 10 TB，而您想要將其移至 Azure，我們建議您使用我們磁碟貨運服務[匯入/匯出]][]。 

載入程序的摘要
2. 若要從 SQL Server 匯出資料至 transferrable 磁碟上的一般檔案使用 bcp 命令列公用程式。
3. 傳送至 Microsoft 磁碟。
4. Microsoft 會將資料載入 SQL Data Warehouse

## <a name="load-from-hdinsight"></a>從 HDInsight 載入
SQL Data Warehouse 支援從 HDInsight 透過 PolyBase 載入資料。 有相同 Azure Blob 儲存體中的資料載入-使用 PolyBase 連線到資料載入 HDInsight 程序。 

### <a name="1-use-polybase-and-t-sql"></a>1.使用 PolyBase 和 T SQL

載入程序的摘要︰

2. 將資料格式化為 utf-8 由於 PolyBase 目前不支援 utf-16。
2. 將資料移到 HDInsight，將其儲存文字檔案中的 ORC 或 Parquet 格式。
3. 在 SQL Data Warehouse 定義的位置和資料的格式設定外部的物件。
4. 執行 T SQL 命令平行將資料載入到新的資料庫資料表。

教學課程，請參閱[載入資料從 SQL Data Warehouse (PolyBase) 至 Azure blob 儲存體][]。

## <a name="recommendations"></a>建議

我們的合作夥伴許多已載入解決方案。 若要找到其他資訊，請參閱我們的[合作夥伴解決方案][]清單。 

如果您的資料來自非關聯的來源，而且您想要載入您需要將其轉換為列與欄，再載入該 SQL Data Warehouse。 已轉換的資料不需要儲存資料庫中，它可以儲存在文字檔案。

建立新載入資料的統計資料。 Azure SQL Data Warehouse 尚未支援自動建立，或自動更新統計資料。  若要取得最佳效能，從您的查詢，請務必先載入之後，建立所有資料表的所有資料行的統計資料或資料發生任何大量的變更。  如需詳細資訊，請參閱[統計資料][]。


## <a name="next-steps"></a>後續步驟
如需開發秘訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[從 Azure blob 儲存體的資料載入到 SQL Data Warehouse (PolyBase)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[從 Azure blob 儲存體的資料載入到 SQL Data Warehouse （Azure 資料工廠）]: ./sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[若要 Azure SQL 資料倉庫 (SSIS) 載入從 SQL Server 資料]: ./sql-data-warehouse-load-from-sql-server-with-integration-services.md
[從 SQL Server 資料載入 Azure SQL Data Warehouse (bcp)]: ./sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: ./sql-data-warehouse-load-from-sql-server-with-azcopy.md

[載入範例資料庫]: ./sql-data-warehouse-load-sample-databases.md
[移轉概觀]: ./sql-data-warehouse-overview-migrate.md
[合作夥伴解決方案]: ./sql-data-warehouse-partner-business-intelligence.md
[開發概觀]: ./sql-data-warehouse-overview-develop.md
[統計資料]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[匯入/匯出]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/
