<properties
    pageTitle="記憶體用完 (OOM)-登錄區設定 |Microsoft Azure"
    description="修正不足的記憶體錯誤 (OOM) 從登錄區查詢中的 Hadoop HDInsight 中。 客戶案例是查詢跨多個大型的資料表。"
    keywords="登出記憶體錯誤，OOM，登錄區設定"
    services="hdinsight"
    documentationCenter=""
    authors="rashimg"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/02/2016"
    ms.author="rashimg;jgao"/>

# <a name="fix-an-out-of-memory-oom-error-with-hive-memory-settings-in-hadoop-in-azure-hdinsight"></a>修正在 Azure HDInsight Hadoop 登錄區記憶體設定的記憶體 (OOM) 錯誤

其中一個常見問題的解決辦法我們的客戶美元時發生的記憶體 (OOM) 錯誤使用登錄區。 本文將說明客戶案例，我們建議的修正問題的登錄區設定。

## <a name="scenario-hive-query-across-large-tables"></a>在大型的資料表的情況︰ 登錄區查詢

客戶執行下方使用登錄區查詢。

    SELECT
        COUNT (T1.COLUMN1) as DisplayColumn1,
        …
        …
        ….
    FROM
        TABLE1 T1,
        TABLE2 T2,
        TABLE3 T3,
        TABLE5 T4,
        TABLE6 T5,
        TABLE7 T6
    where (T1.KEY1 = T2.KEY1….
        …
        …

此查詢的一些細微差別︰

* T1 是顯示較大的表格，TABLE1，其中包含許多字串的欄類型的別名。
* 其他資料表是不大，但是有大量的資料行。
* 所有資料表會都加入彼此，在某些情況下使用多個資料行 TABLE1 和其他人。

當客戶執行查詢上 MapReduce 的登錄區使用 24 節點 A3 叢集上時，查詢執行關於 26 分鐘。 使用登錄區 MapReduce 上執行查詢時，客戶注意到以下的警告訊息︰

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

因為查詢完成關於 26 分鐘執行時，客戶略過這些警告，改為啟動聚焦於如何改善這一點查詢的效能。

客戶諮詢[最佳化登錄區 Hadoop HDInsight 中的查詢](hdinsight-hadoop-optimize-hive-query.md)，並決定要使用 Tez 執行引擎。 一旦啟用 Tez 設定執行相同的查詢的查詢執行 15 分鐘，然後發生下列錯誤︰

    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space

客戶再決定使用較大字型 VM (亦即 D12) 想放大 VM 有更多堆積空間。 即使客戶持續看到此錯誤。 客戶達到 HDInsight 小組中偵錯時，此問題的說明。

## <a name="debug-the-out-of-memory-oom-error"></a>偵錯的記憶體 (OOM) 錯誤

我們支援與工程團隊合作發現其中一個問題造成的記憶體 (OOM) 錯誤的[已知問題 Apache JIRA 所述](https://issues.apache.org/jira/browse/HIVE-8306)。 從 JIRA 中的說明︰

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

我們已確認該**hive.auto.convert.join.noconditionaltask**已確實設定為**true**底下登錄區 site.xml 檔案︰

    <property>
        <name>hive.auto.convert.join.noconditionaltask</name>
        <value>true</value>
        <description>
            Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
            If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
            specified size, the join is directly converted to a mapjoin (there is no conditional task).
        </description>
    </property>

根據警告和 JIRA，我們假設是地圖加入已 Java 堆積空間 OOM 錯誤的原因。 讓我們在研究瞭解此問題。

部落格文章[中 HDInsight Hadoop Yarn 記憶體設定](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx)所述，實際 Tez 執行引擎是使用時使用的堆積空間屬於 Tez 容器。 請參閱下面說明 Tez 容器記憶體圖像。

![Tez 容器記憶體圖表︰ 記憶體用完 OOM 登錄區](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)


下列兩個記憶體設定部落格文章建議，為定義堆積的容器記憶體︰ **hive.tez.container.size**和**hive.tez.java.opts**。 從我們的經驗，OOM 例外狀況不表示容器大小是太小。 表示 Java 堆積大小 (hive.tez.java.opts) 是太小。 當您看到 OOM 時，您可以嘗試增加**hive.tez.java.opts**。 如有需要增加**hive.tez.container.size**時，您可能需要。 **Java.opts**設定應措施**container.size**80%。

> [AZURE.NOTE]  設定**hive.tez.java.opts**都必須小於**hive.tez.container.size**。

因為 D12 電腦有 28 GB 的記憶體，我們會決定若要使用 [容器大小為 10 GB (10240 MB)，並指定 java.opts 80%。 這是在登錄區主控台中使用下列設定︰

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

根據這些設定，底下的十分鐘內中已順利執行查詢。

## <a name="conclusion-oom-errors-and-container-size"></a>結束︰ OOM 錯誤與容器的大小

發生 OOM 錯誤並不一定的容器大小是太小。 不過，您應設定的記憶體設定，使堆積大小會增加，並至少 80%的容器記憶體大小。
