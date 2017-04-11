<properties
   pageTitle="登錄查詢 Hadoop 工具區 Visual studio |Microsoft Azure"
   description="瞭解如何使用 Hadoop HDInsight 使用 Visual Studio Hadoop 工具中的群組。"
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

#<a name="run-hive-queries-using-the-hdinsight-tools-for-visual-studio"></a>使用 Visual Studio HDInsight 工具執行登錄區查詢

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

在本文中，您將學習如何送出到 HDInsight 叢集使用 Visual Studio HDInsight 工具的登錄區查詢。

> [AZURE.NOTE] 這份文件不提供範例中使用 HiveQL 陳述式功能的詳細的的描述。 在此範例中所 HiveQL 的相關資訊，請參閱[使用登錄區與 Hadoop HDInsight 上](hdinsight-use-hive.md)。

##<a id="prereq"></a>必要條件

若要完成此文件中的步驟進行，您需要下列項目。

* Azure HDInsight (Hadoop 上 HDInsight) 叢集 (Linux 或 windows)

* Visual Studio （與下列版本之一）︰

    Visual Studio 2013 社群/專業/進階/Ultimate 與[更新 4](https://www.microsoft.com/download/details.aspx?id=44921)

    Visual Studio 2015 年 （社群/企業版）

- Visual studio HDInsight 工具。 安裝和設定工具的資訊，請參閱[開始使用 Visual Studio Hadoop HDInsight 工具](hdinsight-hadoop-visual-studio-tools-get-started.md)。

##<a id="run"></a>使用 Visual Studio HDInsight 工具執行登錄區查詢

1. 開啟**Visual Studio** ，然後選取 [**新增** > **專案** > **HDInsight** > **登錄區應用程式**。 提供此專案的名稱。

2. 開啟此專案，以建立**Script.hql**檔案並貼上下列的 HiveQL 陳述式中︰

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    這些陳述式執行下列動作︰

    * **卸除表格**︰ 刪除資料表和資料檔案，如果資料表已經存在。
    * **建立外部表格**︰ 登錄區中建立新的 「 外部 」 資料表。 外部資料表只會儲存在登錄區 （的資料會保留在原始位置） 中的表格定義。

        > [AZURE.NOTE] 當您預期的基礎資料 （例如自動化的資料上傳程序） 的外部來源，或另一個 MapReduce 作業，更新時，應使用外部表格，但您會想要使用的最新資料的登錄區查詢。
        >
        > 卸除外部表格會**無法**刪除資料，只有表格定義。

    * **列格式**︰ 資料設定格式的方式就是告訴登錄區。 在此案例中，以空格分隔每個記錄檔中的欄位。
    * **儲存另存新檔 TEXTFILE 位置**︰ 會告訴登錄區資料的位置儲存 （範例/資料目錄），並將其儲存為文字。
    * **選取**︰ 選取資料行**t4**其中包含**[錯誤]**的值的所有資料列的計數。 這應該因為有三個資料列包含此值會傳回**3**的值。
    * **例如 '%.log' INPUT__FILE__NAME** -會告訴登錄區我們應該只傳回從檔案中的資料。 記錄。 這將搜尋限制 sample.log 檔案包含資料，並從 [從其他範例資料檔案不符合我們所定義的結構描述，傳回資料就會保存為。

3. 從工具列中，選取您想要使用這個查詢， **HDInsight 叢集**，然後選取**送出到 WebHCat**執行陳述式以使用 WebHCat 登錄區工作。 您也可以送出使用 [__透過 HiveServer2 執行__] 按鈕，如果 HiveServer2 才會提供您叢集版本的工作。 **登錄區工作摘要**會出現，並顯示執行工作的相關資訊。 **工作狀態**會變更為**完成**之前，請重新整理工作資訊，使用 [**重新整理**] 連結。

4. 若要檢視之工作的成果使用**工作輸出**連結。 應該顯示為其`[ERROR] 3`，這是 SELECT 陳述式所傳回的值。

5. 您也可以執行登錄區查詢，但不建立專案。 使用**伺服器總管**] 中，展開**Azure** > **HDInsight**，您 HDInsight 的伺服器，以滑鼠右鍵按一下，然後選取 [**撰寫登錄區查詢**。

6. 在出現**temp.hql**文件中，新增下列 HiveQL 陳述式︰

        set hive.execution.engine=tez;
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    這些陳述式執行下列動作︰

    * **建立資料表 IF 不存在**︰ 如果不存在，會建立資料表。 不使用**外部**關鍵字，因為這是內部的資料表，就會儲存在登錄區資料倉庫，並登錄區會完全管理。

        > [AZURE.NOTE] 與**外部**表格拖放在內部表格也會刪除基礎資料。

    * **儲存另存新檔 ORC**︰ 最佳化的資料列的欄 (ORC) 格式儲存資料。 這是高度最佳化，且有效率格式來儲存登錄區資料。
    * **插入覆寫...選取 [**: **log4jLogs**資料表中的選取列包含**[錯誤]**，然後將資料插入**種**表格。

7. 從工具列中，選取 [**送出**執行工作。 使用 [**工作狀態**，來判斷 [工作已順利完成。

8. 若要確認工作完成，建立新的資料表，請使用**伺服器總管**並展開**Azure** > **HDInsight** > HDInsight 叢集 >**登錄區資料庫**> 及**預設值**。 您應該會看到**種**和**log4jLogs**資料表。

##<a id="summary"></a>摘要

如您所見，Visual Studio 的 HDInsight 工具提供的登錄區查詢在上執行 HDInsight 叢集、 監視工作狀態，並擷取輸出簡單的方法。

##<a id="nextsteps"></a>後續步驟

如需登錄區 HDInsight 中的一般資訊︰

* [使用上 HDInsight Hadoop 登錄區](hdinsight-use-hive.md)

有關的其他方法您可以使用 Hadoop HDInsight 上︰

* [Hadoop HDInsight 上搭配使用的豬](hdinsight-use-pig.md)

* [使用上 HDInsight Hadoop MapReduce](hdinsight-use-mapreduce.md)

如需 Visual studio HDInsight 工具的相關資訊︰

* [快速入門的 Visual Studio 的 HDInsight 工具](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)


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

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
