<properties
   pageTitle="使用 PowerShell 中 HDInsight Hadoop 登錄區 |Microsoft Azure"
   description="使用 PowerShell 來執行登錄區查詢中 Hadoop HDInsight。"
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
   ms.date="09/07/2016"
   ms.author="larryfr"/>

#<a name="run-hive-queries-using-powershell"></a>使用 PowerShell 執行登錄區查詢

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

這份文件提供使用 Azure 資源群組模式中的 PowerShell 的 Azure HDInsight 叢集上將登錄區查詢執行 Hadoop 中的範例。

> [AZURE.NOTE] 這份文件不提供範例中所使用的 HiveQL 陳述式功能的詳細的的描述。 在此範例中使用 HiveQL 上的資訊，請參閱[使用登錄區與 Hadoop HDInsight 上](hdinsight-use-hive.md)。


**必要條件**

若要完成此文件中的步驟進行，您需要下列項目。

- **Azure HDInsight (Hadoop 上 HDInsight) 叢集 （Windows 型或 Linux 型）**
- **使用 PowerShell 的 Azure 工作站**中。

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##<a name="run-hive-queries-using-azure-powershell"></a>使用 PowerShell 的 Azure 執行登錄區查詢

Azure PowerShell 提供*cmdlet* ，可讓您從遠端執行 HDInsight 上的 [登錄區查詢。 在內部，這是使用 HDInsight 叢集上執行的其他來電至[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (先前稱為 Templeton)。

在 [遠端 HDInsight 叢集中執行登錄區查詢時，會使用下列 cmdlet:

* **新增 AzureRmAccount**︰ 驗證 Azure PowerShell Azure 訂閱

* **新增 AzureRmHDInsightHiveJobDefinition**︰ 使用指定的 HiveQL 陳述式中建立新的*工作定義*

* **開始 AzureRmHDInsightJob**︰ 將工作定義傳送至 HDInsight、 開始工作，並傳回*工作*物件，可以用來檢查作業的狀態

* **等待 AzureRmHDInsightJob**︰ 若要檢查的工作狀態會使用工作物件。 它會等到完成作業，或超出等待的時間。

* **取得 AzureRmHDInsightJobOutput**︰ 用來擷取之工作的成果

* **叫用 AzureRmHDInsightHiveJob**︰ 用來執行 HiveQL 陳述式。 這會封鎖查詢完成之後，再傳回結果

* **使用 AzureRmHDInsightCluster**︰ 設定目前叢集使用**叫用 AzureRmHDInsightHiveJob**命令

下列步驟說明如何使用這些 cmdlet HDInsight 叢集中執行的工作︰

1. 使用編輯器，將下列程式碼儲存為**hivejob.ps1**。 您必須將**CLUSTERNAME**取代 HDInsight 叢集的名稱。

        #Specify the values
        $clusterName = "CLUSTERNAME"
        $creds=Get-Credential

        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Add-AzureRmAccount
        }

        #HiveQL
        #Note: set hive.execution.engine=tez; is not required for
        #      Linux-based HDInsight
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

        #Create an HDInsight Hive job definition
        $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 

        #Submit the job to the cluster
        Write-Host "Start the Hive job..." -ForegroundColor Green

        $hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds

        #Wait for the Hive job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Print the output
        Write-Host "Display the standard output..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $hiveJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. 開啟新的**PowerShell 的 Azure**命令提示字元。 變更目錄**hivejob.ps1**檔案的位置，然後執行指令碼中使用下列命令︰

        .\hivejob.ps1

    指令碼執行時，系統會提示您輸入的 HTTPS/管理員帳戶認證叢集。 您可能也會提示您登入您的 Azure 訂閱。
    
7. 工作完成時，它應傳回類似下列資訊︰

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. 如先前所述，**叫用登錄區**可用來執行查詢，並等待回應。 使用下列命令，然後**CLUSTERNAME**換成您叢集的名稱︰

        Use-AzureRmHDInsightCluster -ClusterName $clusterName -HttpCredential $creds
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"
        Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query $queryString

    輸出看起來如下所示︰

        2012-02-03  18:35:34    SampleClass0    [ERROR] incorrect   id
        2012-02-03  18:55:54    SampleClass1    [ERROR] incorrect   id
        2012-02-03  19:25:27    SampleClass4    [ERROR] incorrect   id

    > [AZURE.NOTE] 對於較長的 HiveQL 查詢，您可以使用 PowerShell 的 Azure**以下字串**cmdlet 或 HiveQL 指令碼檔。 下列程式碼片段示範如何使用**叫用登錄區**cmdlet 來執行 HiveQL 指令碼檔。 HiveQL 指令碼檔必須上傳至 wasbs: / /。
    >
    > `Invoke-AzureRmHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
    >
    > 如需**以下字串**的詳細資訊，請參閱<a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">使用 Windows PowerShell 以下-字串</a>。

##<a name="troubleshooting"></a>疑難排解

如果沒有任何資訊會傳回作業完成後，可能會有處理期間發生錯誤。 若要檢視此工作的錯誤資訊，將以下**hivejob.ps1**檔案的結尾，儲存，並再執行一次。

    # Print the output of the Hive job.
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

這會傳回寫入 STDERR 在伺服器執行工作時的資訊，並將其可能幫助您判斷工作失敗的原因。

##<a name="summary"></a>摘要

如您所見，PowerShell 的 Azure 讓您輕鬆執行登錄區查詢 HDInsight 叢集、 監視工作狀態，以及擷取輸出。

##<a name="next-steps"></a>後續步驟

如需登錄區 HDInsight 中的一般資訊︰

* [使用上 HDInsight Hadoop 登錄區](hdinsight-use-hive.md)

有關的其他方法您可以使用 Hadoop HDInsight 上︰

* [Hadoop HDInsight 上搭配使用的豬](hdinsight-use-pig.md)

* [使用上 HDInsight Hadoop MapReduce](hdinsight-use-mapreduce.md)
