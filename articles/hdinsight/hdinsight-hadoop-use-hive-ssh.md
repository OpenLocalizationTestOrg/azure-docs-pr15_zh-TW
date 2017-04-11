<properties
   pageTitle="HDInsight (Hadoop) 中使用登錄區命令介面 |Microsoft Azure"
   description="瞭解如何使用 Linux 型 HDInsight 叢集登錄區命令介面。 您將瞭解如何連線到使用 SSh，HDInsight 叢集，然後使用登錄區命令介面互動的方式執行查詢。"
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
   ms.date="10/04/2016"
   ms.author="larryfr"/>

# <a name="use-hive-with-hadoop-in-hdinsight-with-ssh"></a>使用中與 SSH HDInsight Hadoop 登錄區

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

在本文中，您將學習如何使用安全命令介面 (SSH) 連線到 Hadoop Azure HDInsight 叢集上，然後使用登錄區的命令列介面 (CLI) 互動的方式提交登錄區查詢。

> [AZURE.IMPORTANT] [群組] 命令可用 Linux 型 HDInsight 叢集上時，您應該考慮使用 Beeline。 Beeline 更新的用戶端使用登錄區，而隨附於 HDInsight 叢集。 如需有關如何使用此的詳細資訊，請參閱[使用登錄區中使用 Beeline HDInsight Hadoop 使用](hdinsight-hadoop-use-hive-beeline.md)。

##<a id="prereq"></a>必要條件

若要完成此文件中的步驟進行，您將需要下列項目︰

* HDInsight 叢集上 Linux 型 Hadoop。

* SSH 用戶端。 Linux、 Unix 和 Mac OS 應該了 SSH 用戶端。 Windows 使用者必須下載的用戶端，例如[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

##<a id="ssh"></a>使用 SSH 連線

連線至的完整的網域名稱 (FQDN) 的 HDInsight 叢集使用 SSH] 命令。 FQDN 會您命名叢集，然後**。 azurehdinsight.net**。 例如，下列會連線到名稱為**myhdinsight**叢集︰

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**如果您提供 SSH 驗證憑證金鑰**建立 HDInsight 叢集時，您可能需要在用戶端系統指定私密金鑰的位置︰

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**如果您提供 SSH 驗證密碼，**您建立 HDInsight 叢集時，您必須提供出現提示時的密碼。

如需有關如何使用 HDInsight SSH 的詳細資訊，請參閱[使用 SSH Linux 為基礎的 Hadoop Linux 與 OS X Unix 從 HDInsight 上使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

###<a name="putty-windows-based-clients"></a>PuTTY （Windows 型的用戶端）

Windows 不提供的內建的 SSH 用戶端。 我們建議使用**PuTTY**，可以從[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)下載。

如需有關如何使用 PuTTY 的詳細資訊，請參閱[使用 SSH Linux 為基礎的 Hadoop HDInsight 從 Windows 上使用](hdinsight-hadoop-linux-use-ssh-windows.md)。

##<a id="hive"></a>使用 [群組] 命令

2. 連線之後，請使用下列命令以啟動登錄區 CLI:

        hive

3. 使用 CLI，輸入要建立新資料表命名**log4jLogs**使用的範例資料的下列陳述式︰

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
    * **選取**-選取資料行**t4**其中包含**[錯誤]**的值的所有資料列的計數。 這應該有三個資料列包含此值會傳回**3**的值。
    * **例如 '%.log' INPUT__FILE__NAME** -會告訴登錄區我們應該只傳回從檔案中的資料。 記錄。 這將搜尋限制 sample.log 檔案包含資料，並從 [從其他範例資料檔案不符合我們所定義的結構描述，傳回資料就會保存為。

    > [AZURE.NOTE] 當您更新外部來源，例如自動化的資料上傳程序，或另一個 MapReduce 作業，但始終使用最新的資料的登錄區查詢的基礎資料，則應使用外部表格。
    >
    > 卸除外部表格會**無法**刪除資料，只有表格定義。

4. 您可以使用下列陳述式，建立新的 「 內部 」 資料表命名**種**︰

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    這些陳述式執行下列動作︰

    * **建立資料表 IF 不存在**-會建立一個表格，如果不存在。 不使用**外部**關鍵字，因為這是內部的資料表，就會儲存在登錄區資料倉庫，並登錄區會完全管理。
    * **儲存另存新檔 ORC** -最佳化列欄 (ORC) 格式儲存的資料。 這是強烈最佳化，且有效率格式來儲存登錄區資料。
    * **插入覆寫...選取 [** -從**log4jLogs**資料表包含**[錯誤]**，選取列，然後**種**表格中插入資料。

    若要確認列包含欄 t4 **[錯誤]**已儲存至**種**資料表，請使用下列陳述式從**種**傳回的所有資料列︰

        SELECT * from errorLogs;

    三個資料列應該會傳回，所有包含欄 t4 **[錯誤]** 。

    > [AZURE.NOTE] 與外部表格不同卸除內部的資料表將會刪除基礎資料。

##<a id="summary"></a>摘要

如您所見，[群組] 命令會提供輕鬆互動的方式執行登錄區查詢 HDInsight 叢集上、 監視工作狀態，以及擷取輸出。

##<a id="nextsteps"></a>後續步驟

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


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png

