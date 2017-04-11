<properties
    pageTitle="使用互動式登錄區中 HDInsight |Microsoft Azure"
    description="HDInsight 中瞭解如何使用互動式的登錄區 （登錄區 LLAP 上）。"
    keywords=""
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="jgao"/>


# <a name="use-interactive-hive-in-hdinsight-preview"></a>使用互動式登錄區中 HDInsight （預覽版本）

互動式登錄區 （又名 [即時長與程序]( https://cwiki.apache.org/confluence/display/Hive/LLAP)）為新的 HDInsight[叢集類型]( hdinsight-hadoop-provision-linux-clusters.md#cluster-types)。  互動式登錄區可讓在記憶體快取中更互動式和更快速地使登錄區查詢。 這項新功能可讓您的世界一個 HDInsight 大部分的效能，彈性，並開啟上記憶體內的雲端的大型資料方案快取 （使用登錄區和火花） 進階分析透過深度整合與 R 服務。 

互動式登錄區叢集是從 Hadoop 叢集不同。 只包含登錄區服務。 

> [AZURE.NOTE] MapReduce 豬、 Sqoop、 Oozie，與其他服務將會移除此叢集類型推出。
僅透過 Ambari 登錄區檢視、 Beeline，以及登錄區 ODBC 存取互動式登錄區叢集登錄區服務。 無法透過登錄區主控台 Templeton、 Azure CLI 與 PowerShell 的 Azure 存取。 


 


## <a name="create-an-interactive-hive-cluster"></a>建立互動式登錄區叢集

互動式登錄區叢集只支援 Linux 型叢集上。 如需建立 HDInsight 叢集的資訊，請參閱[建立 Linux 型 Hadoop 叢集 HDInsight 中](hdinsight-hadoop-provision-linux-clusters.md)。


## <a name="execute-hive-from-interactive-hive"></a>從互動式登錄區執行登錄區

有不同選項如何您可以在其中執行登錄區查詢︰

- 執行登錄區使用 Ambari 登錄區檢視

    如需使用登錄區檢視資訊，請參閱[使用中 HDInsight Hadoop 登錄區檢視]( hdinsight-hadoop-use-hive-ambari-view.md)。

- 使用 Beeline 執行登錄區

    HDInsight 上使用 Beeline 資訊，請參閱[使用登錄區中使用 Beeline HDInsight Hadoop 與](hdinsight-hadoop-use-hive-beeline.md)。

    您可以使用 Beeline headnode 或空白的邊緣節點。  建議使用 Beeline 從空白邊緣節點。  建立空白的 edgenode HDInsight 叢集的詳細資訊，請參閱[使用空白的邊緣節點中 HDInsight](hdinsight-apps-use-edge-node.md)。

- 使用登錄區 ODBC 執行登錄區

    使用登錄區 ODBC 資訊，請參閱[將 Excel 連接至 Hadoop Microsoft 登錄區 ODBC 驅動程式](hdinsight-connect-excel-hive-odbc-driver.md)。

**若要尋找 JDBC 連線字串︰**

1.  登入 Ambari 使用以下的 URL: https://<ClusterName>。AzureHDInsight.net。
2.  按一下左側功能表上的 [**登錄區**]。
3.  按一下醒目提示的圖示，以複製的 URL:

    ![HDInsight Hadoop 互動式登錄區 LLAP JDBC](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="see-also"></a>另請參閱
-   [建立 Linux 型 Hadoop 叢集中 HDInsight](hdinsight-hadoop-provision-linux-clusters.md)︰ 了解如何建立互動式登錄區叢集 HDInsight。
-   [使用登錄區中使用 Beeline HDInsight Hadoop 與](hdinsight-hadoop-use-hive-beeline.md)︰ 了解如何使用 Beeline 提交登錄區查詢。
-   [將 Excel 連接至 Microsoft 登錄區 ODBC 驅動程式 Hadoop](hdinsight-connect-excel-hive-odbc-driver.md)︰ 了解如何將 Excel 連接至登錄區。
