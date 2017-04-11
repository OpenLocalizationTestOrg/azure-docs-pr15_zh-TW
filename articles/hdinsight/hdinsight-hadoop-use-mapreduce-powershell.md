<properties
   pageTitle="使用 Hadoop MapReduce 與 PowerShell |Microsoft Azure"
   description="瞭解如何使用 PowerShell 來遠端 MapReduce 工作 Hadoop 執行上執行 HDInsight。"
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
   ms.date="08/29/2016"
   ms.author="larryfr"/>

# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>MapReduce 工作 Hadoop 執行上執行 HDInsight 使用 PowerShell

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

這份文件提供使用 PowerShell 的 Azure HDInsight 叢集上執行 Hadoop MapReduce 工作的範例。

##<a id="prereq"></a>必要條件

若要完成此文件中的步驟進行，您將需要下列項目︰

- **Azure HDInsight (Hadoop 上 HDInsight) 叢集 （Windows 型或 Linux 型）**

- **使用 PowerShell 的 Azure 工作站**中。

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##<a id="powershell"></a>執行使用 PowerShell 的 Azure MapReduce 工作

Azure PowerShell 提供*cmdlet* ，可讓您從遠端執行 HDInsight MapReduce 工作。 在內部，這是使用 HDInsight 叢集上執行的其他來電至[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (先前稱為 Templeton)。

在遠端 HDInsight 叢集執行 MapReduce 工作時，會使用下列 cmdlet。

* **登入 AzureRmAccount**︰ 驗證 Azure PowerShell Azure 訂閱

* **新增 AzureRmHDInsightMapReduceJobDefinition**︰ 使用指定的 MapReduce 資訊來建立新的*工作定義*

* **開始 AzureRmHDInsightJob**︰ 將工作定義傳送至 HDInsight、 開始工作，並傳回*工作*物件，可以用來檢查作業的狀態

* **等待 AzureRmHDInsightJob**︰ 若要檢查的工作狀態會使用工作物件。 它會等到完成作業，或超出等待的時間。

* **取得 AzureRmHDInsightJobOutput**︰ 用來擷取之工作的成果

下列步驟將示範如何使用這些 cmdlet HDInsight 叢集中執行的工作。

1. 使用 [編輯者，請為**mapreducejob.ps1**儲存下列程式碼。 您必須將**CLUSTERNAME**取代 HDInsight 叢集的名稱。

        #Specify the values
        $clusterName = "CLUSTERNAME"
                
        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Login-AzureRmAccount
        }

        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        #Define the MapReduce job
        #NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
        # -JarFile = the JAR containing the MapReduce application
        # -ClassName = the class of the application
        # -Arguments = The input file, and the output directory
        $wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
            -ClassName "wordcount" `
            -Arguments `
                "wasbs:///example/data/gutenberg/davinci.txt", `
                "wasbs:///example/data/WordCountOutput"

        #Submit the job to the cluster
        Write-Host "Start the MapReduce job..." -ForegroundColor Green
        $wordCountJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $wordCountJobDefinition `
            -HttpCredential $creds

        #Wait for the job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $wordCountJob.JobId `
            -HttpCredential $creds
        # Download the output
        Get-AzureStorageBlobContent `
            -Blob 'example/data/WordCountOutput/part-r-00000' `
            -Container $container `
            -Destination output.txt `
            -Context $context
        # Print the output
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. 開啟新的**PowerShell 的 Azure**命令提示字元。 變更目錄**mapreducejob.ps1**檔案的位置，然後執行指令碼中使用下列命令︰

        .\mapreducejob.ps1
    
    當您執行指令碼時，可能會提示您驗證 Azure 訂閱。 您也會要求 HDInsight 叢集提供 HTTPS/管理員帳戶的名稱和密碼。

3. 工作完成時，您應該會收到下列類似的輸出︰

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    此輸出指出工作已順利完成。

    > [AZURE.NOTE] 如果**ExitCode** 0 以外的值，請參閱[疑難排解](#troubleshooting)。

    此範例中也會執行指令碼的目錄中儲存至**output.txt**檔案下載的檔案。

###<a name="view-output"></a>檢視輸出

若要查看的單字及計算所產生的工作文字編輯器中開啟**output.txt**檔案。

> [AZURE.NOTE] 輸出檔案 MapReduce 工作的是不變。 因此，如果您重新執行這個範例中，您需要變更輸出檔案的名稱。

##<a id="troubleshooting"></a>疑難排解

如果沒有任何資訊會傳回作業完成後，可能會有處理期間發生錯誤。 若要檢視此工作的錯誤資訊，將下列命令新增至**mapreducejob.ps1**檔案的結尾，儲存，並再執行一次。

    # Print the output of the WordCount job.
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

這會傳回寫入 STDERR 在伺服器執行工作時的資訊，並將其可能幫助您判斷工作失敗的原因。

##<a id="summary"></a>摘要

如您所見，PowerShell 的 Azure 提供輕鬆 HDInsight 叢集上執行 MapReduce 工作、 監視工作狀態，以及擷取成果。

##<a id="nextsteps"></a>後續步驟

如需在 HDInsight MapReduce 工作的一般資訊︰

* [使用 HDInsight Hadoop MapReduce](hdinsight-use-mapreduce.md)

有關的其他方法您可以使用 Hadoop HDInsight 上︰

* [使用上 HDInsight Hadoop 登錄區](hdinsight-use-hive.md)

* [Hadoop HDInsight 上搭配使用的豬](hdinsight-use-pig.md)
