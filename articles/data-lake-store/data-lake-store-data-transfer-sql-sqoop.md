<properties 
   pageTitle="複製資料湖儲存和使用 Sqoop Azure SQL 資料庫之間的資料 |Microsoft Azure"
   description="使用 Sqoop Azure SQL 資料庫及資料湖存放區之間移動資料" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="copy-data-between-data-lake-store-and-azure-sql-database-using-sqoop"></a>複製資料湖儲存和使用 Sqoop Azure SQL 資料庫之間的資料

瞭解如何使用 Apache Sqoop 匯入及匯出資料湖存放 Azure SQL 資料庫之間的資料。
 

## <a name="what-is-sqoop"></a>什麼是 Sqoop？

大型資料應用程式是自然處理非結構化及半結構化資料，例如記錄檔和檔案。 不過，也可能需要處理儲存在關聯式資料庫的結構化的資料。

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html)是設計用來傳送資料關聯式資料庫之間的大型資料存放庫，例如資料湖存放區的工具。 您可以將資料匯入關聯式資料庫管理系統 (RDBMS) 等 Azure SQL 資料庫資料湖存放使用它。 然後轉換及分析使用大型資料的工作負載的資料，然後回 RDBMS 匯出資料。 在本教學課程中，您使用 Azure SQL 資料庫為您的關聯式資料庫匯入/匯出從。
 

## <a name="prerequisites"></a>必要條件

這份文件之前，您必須具備下列項目︰

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。
- **啟用 Azure 訂閱**的公用資料湖存放區的預覽。 請參閱[相關指示](data-lake-store-get-started-portal.md#signup)。 
- **Azure HDInsight 叢集**有權存取資料湖存放帳戶。 請參閱[建立資料湖存放 HDInsight 叢集](data-lake-store-hdinsight-hadoop-use-portal.md)。 本文假設您有資料湖存放存取 HDInsight Linux 叢集。
- **Azure SQL 資料庫**。 如需如何建立的指示，請參閱[建立 Azure SQL 資料庫](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>您學到快速與影片嗎？

[觀看這段影片](https://mix.office.com/watch/1butcdjxmu114)以 Azure 儲存體 Blob 和資料湖存放區之間移動資料的方式使用 DistCp。

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Azure SQL 資料庫中建立範例資料表

1. 開始使用，Azure SQL 資料庫中建立兩個範例資料表。 使用[SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md)或 Visual Studio 到 Azure SQL 資料庫連線，然後執行下列動作查詢。

    **建立 Table1**

        CREATE TABLE [dbo].[Table1]( 
        [ID] [int] NOT NULL, 
        [FName] [nvarchar](50) NOT NULL, 
        [LName] [nvarchar](50) NOT NULL, 
        CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
            ( 
                [ID] ASC 
            ) 
        ) ON [PRIMARY] 
        GO

    **建立 2**

        CREATE TABLE [dbo].[Table2]( 
        [ID] [int] NOT NULL, 
        [FName] [nvarchar](50) NOT NULL, 
        [LName] [nvarchar](50) NOT NULL, 
        CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
            ( 
                [ID] ASC 
            ) 
        ) ON [PRIMARY] 
        GO

2. 在**Table1**，新增一些範例資料。 將**2**保留空白。 我們會將資料從**Table1**匯入資料湖存放區。 然後，我們會將資料匯出資料湖存放到**2**。 執行下列程式碼片段。

         
        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson'); 
  

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-store"></a>使用 Sqoop 從 HDInsight 叢集有權存取資料湖存放區

HDInsight 叢集已有可用的 Sqoop 套件。 如果您已設定 HDInsight 叢集使用資料湖存放為額外的儲存空間，您可以使用 Sqoop （而不變更任何設定） 來匯入/匯出關聯式資料庫 （在此範例中，Azure SQL 資料庫） 之間的資料與資料湖存放帳戶。 

1. 在此教學課程中，我們假設您建立 Linux 叢集，因此您應該使用 SSH 來連線到叢集。 請參閱[連線至 Linux 型 HDInsight 叢集](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)。

2. 請確認您是否可以從叢集來存取資料湖存放帳戶。 從 SSH 提示，請執行下列命令︰

        
        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

    此應提供資料湖存放帳戶中的檔案或資料夾的清單。

### <a name="import-data-from-azure-sql-database-into-data-lake-store"></a>從 Azure SQL 資料庫匯入資料至資料湖存放區

3. 瀏覽至 Sqoop 封包可用的目錄。 一般而言，這是在`/usr/hdp/<version>/sqoop/bin`。 

4. 匯入資料從**Table1**資料湖存放帳戶。 使用下列語法︰

        
        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    請注意**sql 資料庫伺服器的名稱**預留位置表示正在 Azure SQL 資料庫伺服器的名稱。 **sql 資料庫名稱**的預留位置代表實際的資料庫名稱。

    例如，

        
        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

5. 確認已資料湖存放帳戶傳送的資料。 執行下列命令︰

        
        hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    您應該會看到下列輸出。

        
        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    每個**組件-m-** *檔案對應至 [來源] 資料表中的資料列* *Table1**。您可以檢視的部分內容-m-*檔案，以檢查。


### <a name="export-data-from-data-lake-store-into-azure-sql-database"></a>將資料湖存放資料匯出到 Azure SQL 資料庫

6. 將資料匯出資料湖存放帳戶至空白資料表，也就是**2**，在 Azure SQL 資料庫。 使用下列語法。

        
        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    例如，

        
        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

6. 驗證資料已上傳到 SQL 資料庫表格。 使用[SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md)或 Visual Studio 到 Azure SQL 資料庫連線，然後執行下列查詢。

        
        SELECT * FROM TABLE2

    此應具備下列輸出。

        ID  FName   LName
        ------------------
        1   Neal    Kell
        2   Lila    Fulton
        3   Erna    Myers
        4   Annette Simpson

## <a name="see-also"></a>另請參閱

- [Azure 儲存體 Blob 將資料複製到資料湖存放區](data-lake-store-copy-data-azure-storage-blob.md)
- [保護資料湖存放區中的資料](data-lake-store-secure-data.md)
- [使用資料湖存放 Azure 資料湖狀況分析](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [使用資料湖存放 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)
