<properties 
    pageTitle="分析航班延遲情況資料的登錄區 Linux 型 HDInsight 上 |Microsoft Azure" 
    description="瞭解如何使用登錄區分析航班資料的上 Linux 型 HDInsight，然後將資料匯出至 Sqoop SQL 資料庫。" 
    services="hdinsight" 
    documentationCenter="" 
    authors="Blackmist" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016" 
    ms.author="larryfr"/>

#<a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>使用登錄區 HDInsight 中的分析航班延遲情況資料

瞭解如何分析航班延遲情況資料增添 Linux 型 HDInsight 上使用登錄區，然後將資料匯出至使用 Sqoop Azure SQL 資料庫]。

> [AZURE.NOTE] 可以使用這份文件的各個部分 （Python，例如登錄區） 的 Windows 型 HDInsight 叢集，許多步驟特有 Linux 型叢集。 適用於 Windows 型叢集的步驟，請參閱[使用登錄區中 HDInsight 分析航班延遲資料](hdinsight-analyze-flight-delay-data.md)

###<a name="prerequisites"></a>必要條件

本教學課程之前，您必須具備下列項目︰

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- __HDInsight 叢集__。 如需建立新的 Linux 型 HDInsight 叢集的步驟，請參閱[開始使用 Hadoop 登錄區 linux HDInsight 中使用](hdinsight-hadoop-linux-tutorial-get-started.md)。

- __Azure SQL 資料庫__。 您會使用 Azure SQL 資料庫做為目的地資料存放區。 如果您沒有 SQL 資料庫已，請參閱[SQL 資料庫教學課程︰ 建立以分鐘為單位的 SQL 資料庫](../sql-database/sql-database-get-started.md)。

- __Azure CLI__。 如果您沒有安裝 Azure CLI，請參閱[安裝及設定 Azure CLI](../xplat-cli-install.md)更多的步驟。

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]


##<a name="download-the-flight-data"></a>下載航班資料的關聯

1. 瀏覽至[研究及創新技術管理，美國交通統計局][rita-website]。
2. 在頁面上，選取下列值︰

  	| 名稱 | 值 |
  	| ---- | ---- |
  	| 篩選的年份 | 2013 |
  	| 篩選期間 | 年 1 月 |
  	| 欄位 | 年份，flightdate] 資料、 UniqueCarrier，航空公司，FlightNum，OriginAirportID，原點，OriginCityName，OriginState，DestAirportID，目的地，DestCityName，DestState，DepDelayMinutes，ArrDelay，ArrDelayMinutes，CarrierDelay，WeatherDelay，NASDelay，SecurityDelay，LateAircraftDelay。 清除所有其他欄位 |

3. 按一下 [**下載**]。 

##<a name="upload-the-data"></a>上傳的資料

1. 上傳至 HDInsight 叢集主節點的 zip 檔案中使用下列命令︰

        scp FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    取代 zip 檔案的名稱中的__檔案名稱__。 取代 HDInsight 叢集 SSH 登入的__使用者名稱__。 取代 CLUSTERNAME HDInsight 叢集的名稱。
    
    > [AZURE.NOTE] 如果您是使用密碼驗證 SSH 登入，系統會提示您輸入密碼。 如果您是使用公開金鑰，您可能需要使用`-i`參數指定相符的私密金鑰的路徑。 例如`scp -i ~/.ssh/id_rsa FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`。

2. 完成上傳後，連線到使用 SSH 叢集︰

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    如需有關使用 Linux 型 HDInsight SSH 的詳細資訊，請參閱下列文章︰
    
    * [使用上 HDInsight Linux、 Unix，或 OS X 的 Linux 型 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [使用上從 Windows HDInsight Linux 為基礎的 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-windows.md)
    
3. 連線之後，請使用下列解壓縮.zip 檔︰

        unzip FILENAME.zip
    
    這將擷取.csv 檔案的大小是大約 60 MB。
    
4. 使用下列命令以在 WASB （使用 HDInsight，到分散式的資料儲存區） 上建立新的目錄，然後複製檔案︰

    hdfs dfs-mkdir-p /tutorials/flightdelays/data hdfs dfs-放置 FILENAME.csv/教學課程/flightdelays/資料 /
    
##<a name="create-and-run-the-hiveql"></a>建立和執行 HiveQL

您可以使用下列步驟，將資料匯入 CSV 檔案中名為__延遲__的登錄區資料表。

1. 建立及編輯命名__flightdelays.hql__的新檔案使用下列動作︰

        nano flightdelays.hql
        
    使用下列做為此檔案的內容︰
    
        DROP TABLE delays_raw;
        -- Creates an external table over the csv file
        CREATE EXTERNAL TABLE delays_raw (
            YEAR string,
            FL_DATE string,
            UNIQUE_CARRIER string,
            CARRIER string,
            FL_NUM string,
            ORIGIN_AIRPORT_ID string,
            ORIGIN string,
            ORIGIN_CITY_NAME string,
            ORIGIN_CITY_NAME_TEMP string,
            ORIGIN_STATE_ABR string,
            DEST_AIRPORT_ID string,
            DEST string,
            DEST_CITY_NAME string,
            DEST_CITY_NAME_TEMP string,
            DEST_STATE_ABR string,
            DEP_DELAY_NEW float,
            ARR_DELAY_NEW float,
            CARRIER_DELAY float,
            WEATHER_DELAY float,
            NAS_DELAY float,
            SECURITY_DELAY float,
            LATE_AIRCRAFT_DELAY float)
        -- The following lines describe the format and location of the file
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE
        LOCATION '/tutorials/flightdelays/data';
        
        -- Drop the delays table if it exists
        DROP TABLE delays;
        -- Create the delays table and populate it with data
        -- pulled in from the CSV file (via the external table defined previously)
        CREATE TABLE delays AS
        SELECT YEAR AS year,
            FL_DATE AS flight_date,
            substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
            substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
            substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
            ORIGIN_AIRPORT_ID AS origin_airport_id,
            substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
            substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
            substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
            DEST_AIRPORT_ID AS dest_airport_id,
            substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
            substring(DEST_CITY_NAME,2) AS dest_city_name,
            substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
            DEP_DELAY_NEW AS dep_delay_new,
            ARR_DELAY_NEW AS arr_delay_new,
            CARRIER_DELAY AS carrier_delay,
            WEATHER_DELAY AS weather_delay,
            NAS_DELAY AS nas_delay,
            SECURITY_DELAY AS security_delay,
            LATE_AIRCRAFT_DELAY AS late_aircraft_delay
        FROM delays_raw;
        
2. 使用__Ctrl + X__，然後__Y__來儲存檔案。

3. 使用下列開始登錄區，並執行__flightdelays.hql__檔案︰

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -f flightdelays.hql
        
    > [AZURE.NOTE] 在此範例中，`localhost`是因為您已連線到 HDInsight 叢集，也就是正在 HiveServer2 主節點。

4. 您可以使用下列命令來開啟互動式 Beeline 工作階段︰

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

5. 當您收到`jdbc:hive2://localhost:10001/>`提示，請使用下列擷取匯入的航班延遲情況資料中的資料。

        INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        SELECT regexp_replace(origin_city_name, '''', ''),
            avg(weather_delay)
        FROM delays
        WHERE weather_delay IS NOT NULL
        GROUP BY origin_city_name;

    這會擷取資深天氣延遲，以及平均延遲的時間的城市清單，並將它儲存在`/tutorials/flightdelays/output`。 之後，Sqoop 會從這個位置讀取資料，然後將其匯出到 Azure SQL 資料庫。

6. 若要結束 Beeline，請輸入`!quit`出現提示時。

## <a name="create-a-sql-database"></a>建立 SQL 資料庫

如果您已經有 SQL 資料庫，您必須取得的伺服器名稱。 您可以在[Azure 入口網站](https://portal.azure.com)中找到這可以選取 [ __SQL 資料庫__]，然後篩選您想要使用的資料庫名稱。 伺服器名稱會列在 [__伺服器__] 欄。

如果您還沒有 SQL 資料庫，使用中的資訊[SQL 資料庫教學課程︰ 建立以分鐘為單位的 SQL 資料庫](../sql-database/sql-database-get-started.md)來建立一個。 您必須儲存所用資料庫的伺服器名稱。

##<a name="create-a-sql-database-table"></a>建立 SQL 資料庫表格

> [AZURE.NOTE] 有許多方法可以連線至 SQL 資料庫建立資料表。 下列步驟使用[FreeTDS](http://www.freetds.org/)的 HDInsight。

1. 使用 SSH 連線至 Linux 為基礎的 HDInsight 叢集，然後從 SSH 工作階段中執行下列步驟。

3. 若要安裝 FreeTDS 使用下列命令︰

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. FreeTDS 具有已安裝後，請使用下列命令以連線至 SQL 資料庫伺服器。 取代 SQL 資料庫伺服器的名稱__伺服器名稱__。 取代__adminLogin__和__adminPassword__與登入 SQL 資料庫。 __資料庫名稱__取代資料庫名稱。

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>

    您會收到下列類似的輸出︰

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. 在`1>`提示中，輸入下列行︰

        CREATE TABLE [dbo].[delays](
        [origin_city_name] [nvarchar](50) NOT NULL,
        [weather_delay] float,
        CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
        ([origin_city_name] ASC))
        GO

    當`GO`輸入陳述式時，將會評估前一個陳述式。 這會建立新資料表命名__延遲__，以群組的索引 （需要 SQL 資料庫）。

    您可以使用下列，確認已建立資料表︰

        SELECT * FROM information_schema.tables
        GO

    您應該會看到類似以下的輸出︰

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        databaseName       dbo     delays      BASE TABLE

8. 輸入`exit`在`1>`提示結束 tsql 公用程式。
    
##<a name="export-data-with-sqoop"></a>使用 Sqoop 匯出資料

2. 使用下列命令確認 Sqoop 可以看到您的 SQL 資料庫︰

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    這應該會傳回資料庫，包括您先前建立的延遲資料表中的資料庫的清單。

3. 若要從 hivesampletable 匯出資料至 mobiledata 表格中使用下列命令︰

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir 'wasbs:///tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1

    這會指示連線至 SQL 資料庫，以包含 [延遲] 資料表中的資料庫，並從 wasbs 匯出資料的 Sqoop: / / 教學課程/flightdelays/輸出 （我們儲存先前的登錄區查詢輸出） 延遲表格。

4. 命令完成後，請使用下列連線到使用 TSQL 資料庫︰

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    連線後，使用下列陳述式驗證的資料匯出至 mobiledata 資料表︰
    
        SELECT * FROM delays
        GO

    您應該會看到資料表中的資料的清單。 輸入`exit`結束 tsql 公用程式。

##<a id="nextsteps"></a>後續步驟

現在您瞭解如何將檔案上傳至 Azure Blob 儲存體、 如何使用 Azure Blob 儲存體中的資料填入登錄區資料表、 如何執行登錄區查詢，以及如何使用 Sqoop HDFS 資料匯出至 Azure SQL 資料庫。 若要深入瞭解，請參閱下列文章︰

* [快速入門 HDInsight][hdinsight-get-started]
* [使用 HDInsight 的登錄區][hdinsight-use-hive]
* [使用 HDInsight Oozie][hdinsight-use-oozie]
* [使用 HDInsight Sqoop][hdinsight-use-sqoop]
* [使用 HDInsight 的豬][hdinsight-use-pig]
* [開發 HDInsight Java MapReduce 程式][hdinsight-develop-mapreduce]
* [開發 Python Hadoop 的 HDInsight 串流程式][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx


 
