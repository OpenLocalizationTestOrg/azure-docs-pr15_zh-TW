<properties
   pageTitle="使用 PowerShell 中 HDInsight Hadoop 豬 |Microsoft Azure"
   description="瞭解如何將提交到 Hadoop 叢集上使用 PowerShell 的 Azure HDInsight 的豬工作。"
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
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-using-powershell"></a>執行使用 PowerShell 豬工作

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

這份文件提供使用 PowerShell 的 Azure 提交 HDInsight 叢集上 Hadoop 豬工作的範例。 豬可讓您使用 （豬拉丁） 的語言模型資料轉換，撰寫 MapReduce 工作，而不是對應並減少函數。

> [AZURE.NOTE] 這份文件不提供範例中使用的豬拉丁陳述式功能的詳細的的描述。 此範例中使用的豬拉丁的相關資訊，請參閱[使用豬 Hadoop HDInsight 上使用](hdinsight-use-pig.md)。

##<a id="prereq"></a>必要條件

若要完成此文件中的步驟進行，您需要下列項目。

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- **使用 PowerShell 的 Azure 工作站**中。

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


##<a id="powershell"></a>執行使用 PowerShell 豬工作

Azure PowerShell 提供*cmdlet* ，可讓您從遠端執行 HDInsight 豬工作。 在內部，這是使用 HDInsight 叢集上執行的其他來電至[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (先前稱為 Templeton)。

在遠端 HDInsight 叢集上執行的豬工作時，會使用下列 cmdlet:

* **登入 AzureRmAccount**︰ 驗證 Azure PowerShell Azure 訂閱

* **新增 AzureRmHDInsightPigJobDefinition**︰ 使用指定的豬拉丁陳述式中建立新的*工作定義*

* **開始 AzureRmHDInsightJob**︰ 將工作定義傳送至 HDInsight、 開始工作，並傳回*工作*物件，可以用來檢查作業的狀態

* **等待 AzureRmHDInsightJob**︰ 若要檢查的工作狀態會使用工作物件。 它會等到完成工作，或已超過等待的時間。

* **取得 AzureRmHDInsightJobOutput**︰ 用來擷取之工作的成果

下列步驟將示範如何使用這些 cmdlet HDInsight 叢集上執行作業。

1. 使用編輯器，將下列程式碼儲存為**pigjob.ps1**。 您必須將**CLUSTERNAME**取代 HDInsight 叢集的名稱。

        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get credentials for the admin/HTTPs account
        $creds = Get-Credential

        #Specify the cluster name
        $clusterName = "CLUSTERNAME"
        
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
        $container = $clusterInfo.DefaultStorageContainer
        $storageAccountKey = (Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Store the Pig Latin into $QueryString
        $QueryString =  "LOGS = LOAD 'wasbs:///example/data/sample.log';" +
        "LEVELS = foreach LOGS generate REGEX_EXTRACT(`$0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
        "RESULT = order FREQUENCIES by COUNT desc;" +
        "DUMP RESULT;"


        #Create a new HDInsight Pig Job definition
        $pigJobDefinition = New-AzureRmHDInsightPigJobDefinition `
            -Query $QueryString `
            -Arguments "-w"

        # Start the Pig job on the HDInsight cluster
        Write-Host "Start the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $pigJobDefinition `
            -HttpCredential $creds

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds

        # Display the output of the Pig job.
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds

2. 開啟新的 Windows PowerShell 命令提示字元。 變更目錄**pigjob.ps1**檔案的位置，然後執行指令碼中使用下列命令︰

        .\pigjob.ps1
        
    您第一次會提示您登入您的 Azure 訂閱。 接著，系統會詢問 HTTPs/管理員帳戶名稱和密碼 HDInsight 叢集。

7. 工作完成時，它應傳回類似下列資訊︰

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="troubleshooting"></a>疑難排解

如果沒有任何資訊會傳回作業完成後，可能會有處理期間發生錯誤。 若要檢視此工作的錯誤資訊，將下列命令新增至**pigjob.ps1**檔案的結尾，儲存，並再執行一次。

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

這會傳回的資訊，寫入 STDERR 在伺服器上執行工作時，它可能幫助您判斷工作失敗的原因。

##<a id="summary"></a>摘要

如您所見，PowerShell 的 Azure 提供輕鬆 HDInsight 叢集上執行的豬工作、 監視工作狀態，以及擷取成果。

##<a id="nextsteps"></a>後續步驟

如需豬 HDInsight 中的一般資訊︰

* [Hadoop HDInsight 上搭配使用的豬](hdinsight-use-pig.md)

有關的其他方法您可以使用 Hadoop HDInsight 上︰

* [使用上 HDInsight Hadoop 登錄區](hdinsight-use-hive.md)

* [使用上 HDInsight Hadoop MapReduce](hdinsight-use-mapreduce.md)
