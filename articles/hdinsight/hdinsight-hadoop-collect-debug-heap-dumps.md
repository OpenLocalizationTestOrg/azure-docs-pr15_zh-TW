<properties
    pageTitle="偵錯及分析 Hadoop 服務與堆積傾印 |Microsoft Azure"
    description="自動收集堆積傾印 Hadoop 服務並置於 Azure Blob 儲存體帳戶偵錯和分析。"
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
    ms.date="10/19/2016"
    ms.author="jgao"/>


# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>收集堆積傾印在偵錯與分析 Hadoop 服務 Blob 儲存體

[AZURE.INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

堆積傾印包含應用程式的記憶體，包括變數的值建立傾印的時間的快照。 因此，如果他們是很有用的診斷在執行階段所發生的問題。 堆積傾印可以自動 Hadoop 服務收集並放置於 Azure Blob 儲存體的使用者帳戶下 HDInsightHeapDumps /。 

您必須啟用堆積傾印各種服務的集合的個別叢集服務。 這項功能預設是關閉會叢集。 這些堆積傾印可能大，因此，建議您監控 Blob 儲存體帳戶他們所儲存的位置之後已啟用集合。

> [AZURE.NOTE] 本文中的資訊僅適用於 Windows 型 HDInsight。 Linux 型 HDInsight 資訊，請參閱[啟用的 Linux 型 HDInsight Hadoop 服務堆積傾印](hdinsight-hadoop-collect-debug-heap-dump-linux.md)

## <a name="eligible-services-for-heap-dumps"></a>符合資格服務堆積傾印

您可以啟用堆積傾印下列服務︰

*  **hcatalog** tempelton
*  **登錄區**-hiveserver2 metastore、 derbyserver
*  **mapreduce** jobhistoryserver
*  **yarn** -resourcemanager nodemanager、 timelineserver
*  **hdfs** -datanode secondarynamenode、 namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>設定項目，讓堆積傾印

若要開啟堆積傾印服務，您需要的服務，指定的**服務名稱**] 區段中設定適當的設定項目。

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

值的**服務名稱**可以是任何一個以上的服務︰ tempelton，hiveserver2，metastore，derbyserver，jobhistoryserver，resourcemanager，nodemanager，timelineserver，datanode，secondarynamenode，namenode 或。

## <a name="enable-using-azure-powershell"></a>啟用使用 PowerShell 的 Azure

例如，若要開啟的 jobhistoryserver 使用 PowerShell 的 Azure 堆積傾印，您想要執行下列動作︰

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>啟用使用.NET SDK

例如，若要開啟堆積傾印使用 jobhistoryserver Azure HDInsight.NET SDK，您會執行下列動作︰

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));
