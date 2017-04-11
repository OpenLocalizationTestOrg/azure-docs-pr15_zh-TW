<properties
   pageTitle="使用中 HDInsight Hadoop 群組及遠端桌面 |Microsoft Azure"
   description="瞭解如何使用遠端桌面連線到 Hadoop 叢集 HDInsight 中，使用登錄區命令列介面，以執行登錄區查詢。"
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
   ms.date="09/06/2016"
   ms.author="larryfr"/>

# <a name="use-hive-with-hadoop-on-hdinsight-with-remote-desktop"></a>在使用遠端桌面 HDInsight Hadoop 搭配使用登錄區

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

本文中，您將瞭解如何使用遠端桌面連線到 HDInsight 叢集，並使用登錄區命令列介面 (CLI)，以執行登錄區查詢。

> [AZURE.NOTE] 這份文件不提供範例中所使用的 HiveQL 陳述式功能的詳細的的描述。 在此範例中所 HiveQL 的相關資訊，請參閱[使用登錄區與 Hadoop HDInsight 上](hdinsight-use-hive.md)。

##<a id="prereq"></a>必要條件

若要完成此文件中的步驟進行，您將需要下列項目︰

* Windows 型 HDInsight (Hadoop 上 HDInsight) 叢集

* 執行 Windows 10、 視窗 8 或 Windows 7 的用戶端電腦

##<a id="connect"></a>使用遠端桌面連線

啟用遠端桌面 HDInsight 叢集，然後連線到其在[連線至 HDInsight 叢集使用 RDP](hdinsight-administer-use-management-portal.md#rdp)的指示執行。

##<a id="hive"></a>使用 [群組] 命令

當您已連線至 HDInsight 叢集的桌面時，請使用登錄區中使用下列步驟︰

1. 從 HDInsight 桌面，開始**Hadoop 命令列**。

2. 輸入下列命令以開始登錄區 CLI:

        %hive_home%\bin\hive

    啟動 CLI 之後，您會看到的登錄區 CLI 提示︰ `hive>`。

3. 使用 CLI，輸入要建立新資料表命名**log4jLogs**使用範例資料的下列陳述式︰

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    這些陳述式執行下列動作︰

    * **卸除表格**︰ 刪除資料表和資料檔案，如果資料表已經存在。

    * **建立外部表格**︰ 登錄區中建立新的 「 外部 」 資料表。 外部資料表儲存 （的資料會保留在原始位置） 的登錄區資料表定義。

        > [AZURE.NOTE] 當您預期的基礎資料 （例如自動化的資料上傳程序） 的外部來源，或另一個 MapReduce 作業，更新時，應使用外部表格，但您會想要使用的最新資料的登錄區查詢。
        >
        > 卸除外部表格會**無法**刪除資料，只有表格定義。

    * **列格式**︰ 資料設定格式的方式就是告訴登錄區。 在此案例中，以空格分隔每個記錄檔中的欄位。

    * **儲存另存新檔 TEXTFILE 位置**︰ 會告訴登錄區資料的位置儲存 （範例/資料目錄），並將其儲存為文字。

    * **選取**︰ 選取資料行**t4**其中包含**[錯誤]**的值的所有資料列的計數。 這應該因為有三個資料列包含此值會傳回**3**的值。

    * **例如 '%.log' INPUT__FILE__NAME** -會告訴登錄區我們應該只傳回從檔案中的資料。 記錄。 這將搜尋限制 sample.log 檔案包含資料，並從 [從其他範例資料檔案不符合我們所定義的結構描述，傳回資料就會保存為。


4. 您可以使用下列陳述式，建立新的 「 內部 」 資料表命名**種**︰

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    這些陳述式執行下列動作︰

    * **建立資料表 IF 不存在**︰ 如果不存在，會建立資料表。 不使用**外部**關鍵字，因為這是內部的資料表，就會儲存在登錄區資料倉庫，並登錄區會完全管理。

        > [AZURE.NOTE] 與**外部**表格拖放在內部表格也會刪除基礎資料。

    * **儲存另存新檔 ORC**︰ 最佳化的資料列的欄 (ORC) 格式儲存資料。 這是高度最佳化，且有效率格式來儲存登錄區資料。

    * **插入覆寫...選取 [**: **log4jLogs**資料表中的選取列包含**[錯誤]**，然後將資料插入**種**表格。

    若要確認欄 t4 中包含**[錯誤]**資料列已儲存至**種**資料表，請使用下列陳述式從**種**傳回的所有資料列︰

        SELECT * from errorLogs;

    三個資料列應該會傳回，所有包含欄 t4 **[錯誤]** 。

##<a id="summary"></a>摘要

如您所見，讓您輕鬆互動的方式 HDInsight 叢集上執行登錄區查詢、 監視工作狀態，並可擷取輸出登錄區] 命令。

##<a id="nextsteps"></a>後續步驟

如需登錄區 HDInsight 中的一般資訊︰

* [使用上 HDInsight Hadoop 登錄區](hdinsight-use-hive.md)

有關的其他方法您可以使用 Hadoop HDInsight 上︰

* [Hadoop HDInsight 上搭配使用的豬](hdinsight-use-pig.md)

* [使用上 HDInsight Hadoop MapReduce](hdinsight-use-mapreduce.md)

如果您使用的 Tez 與登錄區，請參閱下列偵錯資訊的文件︰

* [使用 Windows 型 HDInsight Tez UI](hdinsight-debug-tez-ui.md)

* [使用 Linux 型 HDInsight Ambari Tez 檢視](hdinsight-debug-ambari-tez-view.md)

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

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





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: ../powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

