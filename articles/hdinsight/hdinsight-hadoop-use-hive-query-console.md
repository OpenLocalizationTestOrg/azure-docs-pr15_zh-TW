<properties
   pageTitle="在查詢中的主控台 HDInsight 使用 Hadoop 登錄區 |Microsoft Azure"
   description="瞭解如何使用 web 查詢主控台執行 HDInsight Hadoop 叢集登錄區查詢從瀏覽器。"
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
   ms.date="09/20/2016"
   ms.author="larryfr"/>

# <a name="run-hive-queries-using-the-query-console"></a>使用查詢主控台執行登錄區查詢

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

在本文中，您將學習如何使用 HDInsight 查詢主控台 HDInsight Hadoop 叢集上執行登錄區查詢，從瀏覽器。

> [AZURE.IMPORTANT] HDInsight 查詢主控台僅適用於 Windows 型 HDInsight 叢集上的。 如果您使用的 Linux 型 HDInsight 叢集，請參閱[使用登錄區檢視執行登錄區查詢](hdinsight-hadoop-use-hive-ambari-view.md)。


##<a id="prereq"></a>必要條件

若要完成此文件中的步驟進行，您需要下列項目。

* Windows 型 HDInsight Hadoop 叢集

* 新的網頁瀏覽器

##<a id="run"></a>使用查詢主控台執行登錄區查詢

1. 開啟網頁瀏覽器，然後瀏覽至__https://CLUSTERNAME.azurehdinsight.net__，其中__CLUSTERNAME__是 HDInsight 叢集的名稱。 如果出現提示，請輸入的使用者名稱與建立叢集時，使用的密碼。


2. 從頁面頂端的連結，請選取 [**登錄區編輯器**]。 隨後便會顯示的表單，可用於輸入您想要執行 HDInsight 叢集 HiveQL 陳述式。

    ![登錄區編輯器](./media/hdinsight-hadoop-use-hive-query-console/queryconsole.png)

    取代文字`Select * from hivesampletable`下列 HiveQL 陳述式︰

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    這些陳述式執行下列動作︰

    * **卸除表格**︰ 刪除資料表和資料檔案，如果資料表已經存在。
    * **建立外部表格**︰ 登錄區中建立新的 「 外部 」 資料表。 外部資料表儲存在登錄區; 中的 [表格定義資料會保留在原始位置。

    > [AZURE.NOTE] 當您預期的基礎資料 （例如自動化的資料上傳程序） 的外部來源，或另一個 MapReduce 作業，更新時，應使用外部表格，但您會想要使用的最新資料的登錄區查詢。
    >
    > 卸除外部表格會**無法**刪除資料，只有表格定義。

    * **列格式**︰ 資料設定格式的方式就是告訴登錄區。 在此案例中，以空格分隔每個記錄檔中的欄位。
    * **儲存另存新檔 TEXTFILE 位置**︰ 會告訴登錄區資料的位置儲存 （範例/資料目錄），並將其儲存為文字
    * **選取**︰ 選取資料行**t4**其中包含**[錯誤]**的值的所有資料列的計數。 這應該因為有三個資料列包含此值會傳回**3**的值。
    * **例如 '%.log' INPUT__FILE__NAME** -會告訴登錄區我們應該只傳回從檔案中的資料。 記錄。 這將搜尋限制 sample.log 檔案包含資料，並從 [從其他範例資料檔案不符合我們所定義的結構描述，傳回資料就會保存為。

2. 按一下 [**送出**]。 在頁面底部的 [**工作階段**應該顯示為工作的詳細資料。

3. 當 [**狀態**] 欄位變更為 [**已完成**時，請選取 [**檢視詳細資料**的工作。 在 [詳細資料] 頁面包含**工作輸出** `[ERROR]   3`。 您可以使用此欄位之下的 [**下載**] 按鈕，若要下載的檔案包含之工作的成果。


##<a id="summary"></a>摘要

如您所見，查詢主控台讓您輕鬆執行登錄區查詢 HDInsight 叢集、 監視工作狀態，以及擷取輸出。

若要進一步瞭解如何使用登錄區查詢主控台執行的工作區中，選取 [頂端的 [查詢] 主控台中，[**快速入門**，然後使用所提供的範例。 每個範例會引導您完成程序，使用登錄區來分析資料，包括範例中所 HiveQL 陳述式的相關說明。

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



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
