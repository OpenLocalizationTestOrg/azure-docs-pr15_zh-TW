<properties
   pageTitle="使用 Beeline 處理 HDInsight (Hadoop) 上的登錄區 |Microsoft Azure"
   description="瞭解如何使用 SSH 連線到 HDInsight，Hadoop 叢集，然後互動的方式來使用 Beeline 提交登錄區查詢。 Beeline 是透過 JDBC 使用 HiveServer2 公用程式。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/10/2016"
   ms.author="larryfr"/>

#<a name="use-hive-with-hadoop-in-hdinsight-with-beeline"></a>使用中與 Beeline HDInsight Hadoop 登錄區

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

在本文中，您將學習如何使用安全命令介面 (SSH) 連線 Linux 型 HDInsight 叢集，再使用[Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell)命令列工具互動的方式提交登錄區查詢。

> [AZURE.NOTE] Beeline 使用 JDBC 連線到登錄區。 如需有關如何使用登錄區 JDBC 的詳細資訊，請參閱[連線至登錄區上使用登錄區 JDBC 驅動程式 Azure HDInsight](hdinsight-connect-hive-jdbc-driver.md)。

##<a id="prereq"></a>必要條件

若要完成此文件中的步驟進行，您將需要下列項目︰

* HDInsight 叢集上 Linux 型 Hadoop。

* SSH 用戶端。 Linux、 Unix 和 Mac OS 應該了 SSH 用戶端。 Windows 使用者必須下載的用戶端，例如[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

##<a id="ssh"></a>使用 SSH 連線

連線至的完整的網域名稱 (FQDN) 的 HDInsight 叢集使用 SSH] 命令。 FQDN 會您命名叢集，然後**。 azurehdinsight.net**。 例如，下列會連線到名稱為**myhdinsight**叢集︰

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**如果您提供 SSH 驗證憑證金鑰**建立 HDInsight 叢集時，您可能需要在用戶端系統指定私密金鑰的位置︰

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**如果您提供 SSH 驗證密碼，**您建立 HDInsight 叢集時，您必須提供出現提示時的密碼。

如需有關如何使用 HDInsight SSH 的詳細資訊，請參閱[使用 SSH Linux 為基礎的 Hadoop Linux 與 OS X Unix 從 HDInsight 上使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

###<a name="putty-windows-based-clients"></a>PuTTY （Windows 型的用戶端）

Windows 不提供的內建的 SSH 用戶端。 我們建議使用**PuTTY**，可以從[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)下載。

如需有關如何使用 PuTTY 的詳細資訊，請參閱[使用 SSH Linux 為基礎的 Hadoop HDInsight 從 Windows 上使用](hdinsight-hadoop-linux-use-ssh-windows.md)。

##<a id="beeline"></a>使用 [Beeline] 命令

1. 連線之後，請使用下列開始 Beeline:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

    將啟動 Beeline 用戶端，並連接至 JDBC url。 在這裡，`localhost`因為 HiveServer2 執行兩個標頭節點叢集，且我們執行的 Beeline 直接在主要 headnode 上使用。
    
    命令完成後，您將會送達在`jdbc:hive2://localhost:10001/>`提示。

3. Beeline 命令通常開頭`!`字元，例如`!help`顯示 [說明]。 不過`!`通常會省略。 例如，`help`也都將能夠運作。

    如果您檢視說明]，您會發現`!sql`，用來執行 HiveQL 陳述式。 不過，HiveQL，通常會使用您可以略過上述`!sql`。 下列兩個陳述式具有完全相同的結果。顯示目前可透過登錄區資料表︰
    
        !sql show tables;
        show tables;
    
    在新的叢集，應該執行的動作會列出一個資料表︰ __hivesampletable__。

4. 若要顯示 hivesampletable 的結構描述中使用下列︰

        describe hivesampletable;
        
    這會傳回下列資訊︰
    
        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    這會顯示在表格中的欄。 雖然我們無法執行某些對此資料的查詢，請改為建立全新的表格示範如何將資料載入至登錄區，並套用結構描述。
    
5. 輸入下列的陳述式，以建立新資料表命名**log4jLogs**使用 HDInsight 叢集所提供的範例資料︰

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    這些陳述式執行下列動作︰

    * **卸除表格**-會刪除表格和資料檔案，以防資料表已經存在。
    * **建立外部表格**-會建立新的 「 外部 」 資料表中登錄區。 外部資料表只會儲存在登錄區資料表定義。 資料會保留在原始位置。
    * **列格式**的資料設定格式的方式就是告訴登錄區。 在此案例中，以空格分隔每個記錄檔中的欄位。
    * **儲存另存新檔 TEXTFILE 位置**-會告訴登錄區資料的位置儲存 （範例/資料目錄），並確定它儲存為文字。
    * **選取**-選取資料行**t4**其中包含**[錯誤]**的值的所有資料列的計數。 如有三個資料列包含此值，此應傳回**3**的值。
    * **例如 '%.log' INPUT__FILE__NAME** -會告訴登錄區我們應該只傳回從檔案中的資料。 記錄。 一般而言，您將只會有資料結構描述的相同資料夾中時查詢與登錄區，不過此範例的記錄檔儲存與其他資料格式。

    > [AZURE.NOTE] 當您更新外部來源，例如自動化的資料上傳程序，或另一個 MapReduce 作業，但始終使用最新的資料的登錄區查詢的基礎資料，則應使用外部表格。
    >
    > 卸除外部表格會**無法**刪除資料，只有表格定義。
    
    這個命令的輸出應如下︰
    
        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :
        
        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)
        
        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

4. 若要結束 Beeline，請使用`!quit`。

##<a id="file"></a>執行 HiveQL 檔案

Beeline 也可用於執行包含 HiveQL 陳述式的檔案。 若要建立的檔案，然後執行使用 Beeline 使用下列步驟。

1. 若要建立新的檔名為__query.hql__使用下列命令︰

        nano query.hql
        
2. 編輯器] 中開啟後，使用下列作為檔案的內容。 此查詢會建立新的 「 內部 」 資料表命名**種**︰

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    這些陳述式執行下列動作︰

    * **建立資料表 IF 不存在**-會建立一個表格，如果不存在。 不使用**外部**關鍵字，因為這是內部的資料表，就會儲存在登錄區資料倉庫，並登錄區會完全管理。
    * **儲存另存新檔 ORC** -最佳化列欄 (ORC) 格式儲存的資料。 這是高度最佳化，且有效率格式來儲存登錄區資料。
    * **插入覆寫...選取 [** -從**log4jLogs**資料表包含**[錯誤]**，選取列，然後**種**表格中插入資料。
    
    > [AZURE.NOTE] 與外部表格不同卸除內部的資料表將會刪除基礎資料。
    
3. 若要儲存檔案，使用__Ctrl__+ ___X__，然後輸入__Y__和最後__enter 鍵__。

4. 使用下列命令以執行使用 Beeline 檔案。 取得較舊版本的主節點和__密碼__管理員帳戶的密碼名稱取代__主機名稱__︰

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i query.hql

    > [AZURE.NOTE] `-i`參數啟動 Beeline query.hql 檔案中，執行陳述式，仍會保留在 Beeline`jdbc:hive2://localhost:10001/>`提示。 您也可以執行檔案使用`-f`會傳回艦隊處理檔案後的參數。

5. 若要確認建立**種**表格，請使用下列陳述式從**種**傳回的所有資料列︰

        SELECT * from errorLogs;

    三個資料列應該會傳回，所有包含**[錯誤]**資料行 t4 中︰
    
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a name="more-about-beeline-connectivity"></a>更多關於 Beeline 連線資訊

在此步驟的文件使用`localhost`連線至 HiveServer2 叢集 headnode 上執行。 您也可以使用主機名稱或完整的網域名稱的 headnode 那些需要額外步驟的程序 （步驟，找主機名稱或 FQDN）。 使用`localhost`不足時使用 headnode Beeline。

如果您有邊緣節點中叢集，Beeline 安裝，您必須使用主機名稱或 FQDN headnode 連線。

如果您有 Beeline 叢集以外的用戶端上已安裝，您可以使用下列命令連線。 __CLUSTERNAME__換成您 HDInsight 叢集的名稱。 取代__密碼__管理員 （HTTP 登入） 帳戶的密碼。

    beeline -u 'jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=hive2' -n admin -p PASSWORD

請注意，參數/URI 不同於時執行直接 headnode 或叢集內邊緣節點。 這是因為網際網路連線到叢集使用連接埠 443 上路由流量公用閘道器。 此外，許多其他服務是透過公開公用閘道器在連接埠 443、 URI 為不同於時直接連線。 從網際網路連線時您也必須提供密碼來驗證工作階段。

##<a id="summary"></a><a id="nextsteps"></a>後續步驟

如您所見，Beeline] 命令會提供互動的方式執行 HDInsight 叢集上的 [登錄區查詢簡單的方法。

如需登錄區 HDInsight 中的一般資訊︰

* [使用上 HDInsight Hadoop 登錄區](hdinsight-use-hive.md)

如需的資訊的其他方法您可以使用 Hadoop HDInsight 上︰

* [Hadoop HDInsight 上搭配使用的豬](hdinsight-use-pig.md)

* [使用上 HDInsight Hadoop MapReduce](hdinsight-use-mapreduce.md)

如果您使用的 Tez 與登錄區，請參閱下列偵錯資訊的文件︰

* [使用 Windows 型 HDInsight Tez UI](hdinsight-debug-tez-ui.md)

* [使用 Linux 型 HDInsight Ambari Tez 檢視](hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

