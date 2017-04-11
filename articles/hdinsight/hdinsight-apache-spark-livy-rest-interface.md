<properties
    pageTitle="提交火花工作使用遠端晚總 |Microsoft Azure"
    description="瞭解如何使用晚總 HDInsight 叢集遠端提交火花工作。"
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="nitinme"/>


# <a name="submit-spark-jobs-remotely-to-an-apache-spark-cluster-on-hdinsight-linux-using-livy"></a>提交遠端到 Apache 火花叢集 HDInsight linux 使用晚總火花工作

Azure HDInsight Apache 火花叢集包含晚總，REST 介面送出到火花叢集遠端工作。 如需詳細的文件，請參閱[晚總](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)。

您可以使用晚總執行互動式火花 shell 或提交批次火花上執行的工作。 本文討論如何使用晚總提交批次工作。 下列語法使用捲曲打電話晚總端點的其餘部分。

**先決條件︰**

您必須具備下列項目︰

- Azure 的訂閱。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- HDInsight linux Apache 火花叢集。 如需相關指示，請參閱[建立 Apache 火花叢集中 Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md)。

## <a name="submit-a-batch-job-the-cluster"></a>送出批次工作叢集

提交批次工作之前，您必須上傳應用程式糖叢集相關聯的叢集存放區。 您可以使用[**AzCopy**](../storage/storage-use-azcopy.md)，命令列公用程式中，執行此作業。 有許多其他用戶端，您可以使用上傳的資料。 您可以找到深入瞭解其在[上傳 Hadoop 工作 HDInsight 的資料](hdinsight-upload-data.md)。

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**範例**︰

* 如果 jar 檔案位於叢集儲存空間 (WASB)

        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"

* 如果您想要做為輸入檔案的一部分傳遞 jar 檔名和類別的名稱 (在此範例中，input.txt)

        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-batches-running-on-the-cluster"></a>取得有關叢集上執行的批次

    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**範例**︰

* 如果您想要擷取叢集上執行的所有批次︰

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

* 如果您想要擷取與指定的 batchId 特定批次

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"


## <a name="delete-a-batch-job"></a>刪除批次工作

    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**範例**︰

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-and-high-availability"></a>晚總和高可用性

晚總提供高可用性火花叢集上執行的工作。 以下是幾個範例。

* 如果您已送出到火花叢集遠端工作之後，晚總服務會當機，工作會繼續在背景中執行。 備份晚總時，它會還原工作及重新的報表的狀態。

* HDInsight 的 Jupyter 筆記本是在後端可晚總。 如果筆記本正在執行火花工作並重新啟動晚總服務取得，筆記本會繼續執行的程式碼儲存格。 

## <a name="show-me-an-example"></a>顯示範例

在此區段中，我們看看範例如何使用晚總提交火花應用程式，監控進度的應用程式，然後刪除作業。 我們在此範例中使用的應用程式是指開發，請參閱[建立獨立 Scala 應用程式，而且 HDInsight 火花叢集上執行](hdinsight-apache-spark-create-standalone-application.md)。 下列步驟假設下列動作︰

* 您已複製了應用程式 jar 叢集相關聯的儲存空間帳戶。
* 您必須捲曲嘗試這些步驟的電腦上安裝。

執行下列步驟。

1. 讓我們先確認晚總正在執行叢集上。 我們可以快速執行批次的清單執行。 如果這是您執行的使用晚總工作的第一次，這應該會傳回 0。

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

    您應該取得輸出如下︰

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    請注意輸出中的最後一行顯示**總計︰ 0**，建議沒有執行批次的方式。

2. 讓我們現在送出批次工作。 下列程式碼片段使用傳遞 jar 名稱的輸入的檔案 (input.txt) 和類別名稱做為參數。 如果您從 Windows 電腦執行這些步驟，這是建議的方法。

        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

    在檔案**input.txt**參數定義如下︰

        { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }

    您應該會看到類似以下的輸出︰

        < HTTP/1.1 201 Created
        < Content-Type: application/json; charset=UTF-8
        < Location: /0
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:51:30 GMT
        < Content-Length: 36
        <
        {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    請注意的輸出最後一列的顯示方式**狀態︰ 啟動**。 也顯示，**識別碼︰ 0**。 這是批次識別碼。

3. 您現在可以擷取使用批次識別碼此特定批次的狀態

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

    您應該會看到類似以下的輸出︰

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    輸出現在會顯示**狀態︰ 成功**，其建議工作已順利完成。

4. 如果您想，現在您可以刪除批次。

        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

    您應該會看到類似以下的輸出︰

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    輸出的最後一行會顯示已成功刪除批次。 如果您刪除工作，在執行中時，它會基本上刪除工作。 如果您刪除工作的完成時，成功或失敗，它會完全刪除工作資訊。

## <a name="seealso"></a>另請參閱


* [概觀︰ Apache 火花上 Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>案例

* [使用 BI 火花︰ 執行火花 HDInsight 中使用的 BI 工具的互動式的資料分析](hdinsight-apache-spark-use-bi-tools.md)

* [與電腦學習火花︰ 使用火花 HDInsight 分析建置溫度使用 HVAC 資料中](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [與電腦學習火花︰ 使用火花 HDInsight 預測食物檢查結果中](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [火花串流︰ 使用火花 HDInsight 建置即時串流應用程式中](hdinsight-apache-spark-eventhub-streaming.md)

* [HDInsight 中使用火花網站記錄分析](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>建立和執行應用程式

* [建立使用 Scala 獨立應用程式](hdinsight-apache-spark-create-standalone-application.md)

### <a name="tools-and-extensions"></a>工具和延伸模組

* [使用 HDInsight 工具增益集，如 IntelliJ 瞭解建立及提交火花 Scala 應用程式](hdinsight-apache-spark-intellij-tool-plugin.md)

* [使用 HDInsight 工具增益集，如 IntelliJ 瞭解遠端偵錯火花應用程式](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [使用上 HDInsight 火花叢集運貨用飛艇筆記本](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [核心適用於 Jupyter 火花叢集 HDInsight 的筆記本](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [使用外部封包 Jupyter 筆記本](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [在 [您的電腦上安裝 Jupyter 並連線到 HDInsight 火花叢集](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理資源

* [管理資源 Apache 火花叢集中 Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [追蹤和偵錯 Apache 火花中叢集 HDInsight 上執行的工作](hdinsight-apache-spark-job-debugging.md)
