<properties
    pageTitle="分析航班延遲情況資料增添與 Hadoop 中 HDInsight |Microsoft Azure"
    description="瞭解如何使用 Windows PowerShell 指令建立 HDInsight 叢集、 執行登錄區工作、 執行 Sqoop 工作，並刪除叢集。"
    services="hdinsight"
    documentationCenter=""
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

#<a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>使用登錄區中 HDInsight 分析航班延遲情況資料

登錄區提供一種透過稱為類似 SQL 指令碼語言執行 Hadoop MapReduce 工作*[HiveQL][hadoop-hiveql]*，可以套用根據摘要、 查詢，和分析大量資料。

> [AZURE.NOTE] 這份文件中的步驟需要 Windows 型 HDInsight 叢集。 使用 Linux 叢集的步驟，請參閱[使用 HDInsight (Linux) 中的登錄區分析航班延遲情況資料](hdinsight-analyze-flight-delay-data-linux.md)。

Azure HDInsight 的主要優點之一是區隔資料儲存區和運算。 HDInsight 的資料儲存區使用 Azure Blob 儲存體。 一般工作包含三個部分︰

1. **資料儲存在 Azure Blob 儲存體。**  例如應付資料、 感應器資料、 web 記錄，並在此情況下，航班延遲情況資料會儲存至 Azure Blob 儲存體。
2. **執行的作業。** 當處理資料時，您可以執行建立 HDInsight 叢集、 執行工作，並刪除叢集 Windows PowerShell 指令碼 （或用戶端應用程式）。 工作會將輸出資料儲存至 Azure Blob 儲存體。 輸出資料會保留，即使叢集會刪除。 如此一來，您是每年支付只什麼您有使用。
3. **擷取 Azure Blob 儲存體的輸出**，或在本教學課程中，將資料匯出至 Azure SQL 資料庫。

下圖示範的情況，而且此教學課程的結構︰

![HDI。FlightDelays.flow][img-hdi-flightdelays-flow]

**附註**︰ 在圖表中的數字會對應到章節標題。 **M**代表主要的程序。 **A**表示附錄中的內容。

教學課程的主要部分會顯示如何使用 Windows PowerShell 指令執行下列工作︰

- 建立 HDInsight 叢集。
- 計算平均延遲在機場叢集上執行登錄區工作。 航班延遲情況資料會儲存於 Azure Blob 儲存體帳戶。
- 執行匯出到 Azure SQL 資料庫的登錄區工作輸出 Sqoop 工作。
- 刪除 HDInsight 叢集。

另外，在您可以找到上傳航班延遲情況資料、 建立/上傳登錄區查詢字串，並備妥 Azure SQL 資料庫 Sqoop 工作的指示進行。

> [AZURE.NOTE] 這份文件中的步驟是針對 Windows 型 HDInsight 叢集。 使用 Linux 叢集的步驟，請參閱[使用登錄區 HDInsight (Linux) 中的分析航班延遲資料](hdinsight-analyze-flight-delay-data-linux.md)

###<a name="prerequisites"></a>必要條件

本教學課程之前，您必須具備下列項目︰

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- **使用 PowerShell 的 Azure 工作站**。

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

**在本教學課程中使用的檔案**

本教學課程使用-時間的效能與航班資料的[研究和創新技術管理、 交通美國統計]或 RITA [rita-website]。 複製的資料具有已上傳至公用 Blob 的存取權限與 Azure Blob 儲存容器。 PowerShell 指令碼的組件會複製預設 blob 的容器叢集公用 blob 容器中的資料。 HiveQL 指令碼也會一併複製到相同的 Blob 容器。 如果您想要瞭解如何將取得/上傳的資料儲存區帳戶，以及如何建立/上傳 HiveQL 指令碼檔，請參閱[附錄 A](#appendix-a)和[B 附錄](#appendix-b)。

下表列出此教學課程中所使用的檔案︰

<table border="1">
<tr><th>檔案</th><th>描述</th></tr>
<tr><td>wasbs://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>群組工作所使用的 HiveQL 指令碼檔。 這個指令碼具有已上傳至公用存取的 Azure Blob 儲存體帳戶。 <a href="#appendix-b">附錄 B</a>有準備，並將此檔案上傳到您自己 Azure Blob 儲存體帳戶的指示。</td></tr>
<tr><td>wasbs://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>輸入的群組工作的詳細資料。 資料已上傳到公用存取的 Azure Blob 儲存體帳戶。 <a href="#appendix-a">附錄 A</a>有取得資料，並將資料上傳到您自己 Azure Blob 儲存體帳戶的指示。</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>群組工作的輸出路徑。 預設的容器用來儲存的輸出資料。</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>預設的容器上的登錄區工作狀態資料夾。</td></tr>
</table>


##<a name="create-cluster-and-run-hivesqoop-jobs"></a>建立叢集，並執行登錄區/Sqoop 工作

Hadoop MapReduce 是批次處理。 執行登錄區工作最經濟方法是建立工作，叢集和工作完成之後，刪除作業。 下列指令碼涵蓋整個程序。 建立 HDInsight 叢集和執行登錄區工作的詳細資訊，請參閱 <<c0>在 HDInsight 建立 Hadoop 叢集<b1></b1>和<b2>使用登錄區與 HDInsight</b2><b3></b3>。

**若要執行 PowerShell 的 Azure 登錄區查詢**

1. 建立 Azure SQL 資料庫及表格 Sqoop 工作輸出使用[附錄 C](#appendix-c)中的指示。
3. 開啟 Windows PowerShell ise [以系統，並執行下列指令碼︰

        $subscriptionID = "<Azure Subscription ID>"
        $nameToken = "<Enter an Alias>" 
        
        ###########################################
        # You must configure the follwing variables
        # for an existing Azure SQL Database
        ###########################################
        $existingSqlDatabaseServerName = "<Azure SQL Database Server>"
        $existingSqlDatabaseLogin = "<Azure SQL Database Server Login>"
        $existingSqlDatabasePassword = "<Azure SQL Database Server login password>"
        $existingSqlDatabaseName = "<Azure SQL Database name>"
        
        $localFolder = "E:\Tutorials\Downloads\" # A temp location for copying files. 
        $azcopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy" # depends on the version, the folder can be different
        
        ###########################################
        # (Optional) configure the following variables
        ###########################################
        
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $location = "EAST US 2"
        
        $HDInsightClusterName = $namePrefix + "hdi"
        $httpUserName = "admin"
        $httpPassword = "<Enter the Password>"
        
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $HDInsightClusterName # use the cluster name
        
        $existingSqlDatabaseTableName = "AvgDelays"
        $sqlDatabaseConnectionString = "jdbc:sqlserver://$existingSqlDatabaseServerName.database.windows.net;user=$existingSqlDatabaseLogin@$existingSqlDatabaseServerName;password=$existingSqlDatabaseLogin;database=$existingSqlDatabaseName"
        
        $hqlScriptFile = "/tutorials/flightdelays/flightdelays.hql" 
        
        $jobStatusFolder = "/tutorials/flightdelays/jobstatus"
        
        ###########################################
        # Login
        ###########################################
        try{
            $acct = Get-AzureRmSubscription
        }
        catch{
            Login-AzureRmAccount
        }
        Select-AzureRmSubscription -SubscriptionID $subscriptionID
        
        ###########################################
        # Create a new HDInsight cluster
        ###########################################
        
        # Create ARM group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
        
        # Create the default storage account
        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS
        
        # Create the default Blob container
        $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
        $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 
        New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext 
        
        # Create the HDInsight cluster
        $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
        $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
        
        New-AzureRmHDInsightCluster `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $HDInsightClusterName `
            -Location $location `
            -ClusterType Hadoop `
            -OSType Windows `
            -ClusterSizeInNodes 2 `
            -HttpCredential $httpCredential `
            -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -DefaultStorageAccountKey $defaultStorageAccountKey `
            -DefaultStorageContainer $existingDefaultBlobContainerName 
        
        
        ###########################################
        # Prepare the HiveQL script and source data
        ###########################################
        
        # Create the temp location  
        New-Item -Path $localFolder -ItemType Directory -Force 
        
        # Download the sample file from Azure Blob storage
        $context = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
        $blobs = Get-AzureStorageBlob -Container "flightdelay" -Context $context
        #$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder
        
        # Upload data to default container
        
        $azcopycmd = "cmd.exe /C '$azcopyPath\azcopy.exe' /S /Source:'$localFolder' /Dest:'https://$defaultStorageAccountName.blob.core.windows.net/$defaultBlobContainerName/tutorials/flightdelays' /DestKey:$defaultStorageAccountKey"
        
        Invoke-Expression -Command:$azcopycmd
        
        ###########################################
        # Submit the Hive job
        ###########################################
        Use-AzureRmHDInsightCluster -ClusterName $HDInsightClusterName -HttpCredential $httpCredential
        $response = Invoke-AzureRmHDInsightHiveJob `
                        -Files $hqlScriptFile `
                        -DefaultContainer $defaultBlobContainerName `
                        -DefaultStorageAccountName $defaultStorageAccountName `
                        -DefaultStorageAccountKey $defaultStorageAccountKey `
                        -StatusFolder $jobStatusFolder 
        
        write-Host $response
        
        
        ###########################################
        # Submit the Sqoop job
        ###########################################
        $exportDir = "wasbs://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net/tutorials/flightdelays/output"
        
        $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
                        -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
        $sqoopJob = Start-AzureRmHDInsightJob `
                        -ResourceGroupName $resourceGroupName `
                        -ClusterName $hdinsightClusterName `
                        -HttpCredential $httpCredential `
                        -JobDefinition $sqoopDef #-Debug -Verbose
        
        Wait-AzureRmHDInsightJob `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $HDInsightClusterName `
            -HttpCredential $httpCredential `
            -WaitTimeoutInSeconds 3600 `
            -Job $sqoopJob.JobId
        
        
        Get-AzureRmHDInsightJobOutput `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -HttpCredential $httpCredential `
            -DefaultContainer $existingDefaultBlobContainerName `
            -DefaultStorageAccountName $defaultStorageAccountName `
            -DefaultStorageAccountKey $defaultStorageAccountKey `
            -JobId $sqoopJob.JobId `
            -DisplayOutputType StandardError
        
        ###########################################
        # Delete the cluster
        ###########################################
        Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName

6. 連線到您的 SQL 資料庫，請參閱依城市 AvgDelays 資料表中的平均航班延遲狀況︰

    ![HDI。FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]



---
##<a id="appendix-a"></a>附錄 A-上傳航班延遲情況資料至 Azure Blob 儲存體
上傳的資料檔案和 HiveQL 指令碼檔 （請參閱[附錄 B](#appendix-b)） 需要規劃。 是儲存的資料檔案和 HiveQL 檔案之前先建立 HDInsight 叢集和執行登錄區工作。 您有兩個選項︰

- **使用相同的 Azure 儲存體帳戶，則會使用 HDInsight 叢集作為預設檔案系統。** HDInsight 叢集會有儲存帳戶便捷鍵，因為您不需要進行任何其他變更。
- **使用不同的 Azure 儲存體帳戶從 HDInsight 叢集預設檔案系統。** 如果這種情況，您必須修改[建立 HDInsight 叢集和執行登錄區/Sqoop 工作](#runjob)連結的儲存空間帳戶為額外的儲存空間帳戶中找到的 Windows PowerShell 指令碼中建立的一部分。 如需相關指示，請參閱[建立 Hadoop 叢集中 HDInsight][hdinsight-provision]。 HDInsight 叢集就會知道儲存帳戶的便捷鍵。

>[AZURE.NOTE] 資料檔案的 Blob 儲存路徑很難編碼 HiveQL 指令碼檔中。 您必須適當地更新。

**若要下載航班資料的關聯**

1. 瀏覽至[研究及創新技術管理，美國交通統計局][rita-website]。
2. 在頁面上，選取下列值︰

    <table border="1">
    <tr><th>名稱</th><th>值</th></tr>
    <tr><td>篩選的年份</td><td>2013 </td></tr>
    <tr><td>篩選期間</td><td>年 1 月</td></tr>
    <tr><td>欄位</td><td>*年*、 *flightdate] 資料*、 *UniqueCarrier*、*公司*、 *FlightNum*、 *OriginAirportID*、*原點*、 *OriginCityName*、 *OriginState*、 *DestAirportID*、*目的地*、 *DestCityName*、 *DestState*、 *DepDelayMinutes*、 *ArrDelay*、 *ArrDelayMinutes*、 *CarrierDelay*、 *WeatherDelay*、 *NASDelay*、 *SecurityDelay*、 *LateAircraftDelay* （清除所有其他欄位）</td></tr>
    </table>

3. 按一下 [**下載**]。
4. 解壓縮檔案到**C:\Tutorials\FlightDelay\2013Data**資料夾。 每個檔案是以 CSV 檔案，大約 60 GB 大小。
5.  重新命名檔案，其中包含資料的月份的名稱。 例如，包含 1 月的資料檔案會命名為*January.csv*。
6. 重複步驟 2 和 5，12 月 2013年中的每一個下載的檔案。 您將需要執行教學課程的一個檔案的最小值。  

**若要將航班延遲情況資料增添上傳至 Azure Blob 儲存體**

1. 準備參數︰

    <table border="1">
    <tr><th>變數名稱</th><th>備忘稿</th></tr>
    <tr><td>$storageAccountName</td><td>您要上傳資料以 Azure 儲存體帳戶。</td></tr>
    <tr><td>$blobContainerName</td><td>您要上傳資料以 Blob 容器。</td></tr>
    </table>
2. 開啟 Azure PowerShell ise [以系統。
3. 下列指令碼貼到指令碼] 窗格中︰

        [CmdletBinding()]
        Param(

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #Region - Variables
        $localFolder = "C:\Tutorials\FlightDelay\2013Data"  # The source folder
        $destFolder = "tutorials/flightdelay/2013data"     #The blob name prefix for the files to be uploaded
        #EndRegion
        
        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #Region - Validate user input
        Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
        # Validate the Storage account
        if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }
        else{
            $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
        }
        
        # Validate the container
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion
        
        #Region - Copy the file from local workstation to Azure Blob storage  
        if (test-path -Path $localFolder)
        {
            foreach ($item in Get-ChildItem -Path $localFolder){
                $fileName = "$localFolder\$item"
                $blobName = "$destFolder/$item"
        
                Write-Host "Copying $fileName to $blobName" -ForegroundColor Green
        
                Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
            }
        }
        else
        {
            Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
        }
        
        # List the uploaded files on HDInsight
        Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
        #EndRegion




4. 按**F5**執行指令碼。

如果您選擇要上傳檔案時，用於不同的方法，請確定檔案路徑是教學課程/flightdelay 資料。 存取檔案的語法是︰

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelay/data

路徑教學課程/flightdelay/資料是您上傳檔案時所建立的虛擬資料夾。 確認有 12 個檔案，為每個月。

>[AZURE.NOTE] 您必須更新登錄區查詢讀取新的位置。

> 您必須設定為公用或將儲存空間帳戶繫結到 HDInsight 叢集容器存取權限。 否則，登錄區查詢字串會無法存取資料檔案。

---
##<a id="appendix-b"></a>附錄 B-建立並上傳 HiveQL 指令碼

使用 PowerShell 的 Azure，您可以執行多個 HiveQL 陳述式一次，或封裝成指令碼檔 HiveQL 陳述式。 本節說明如何建立 HiveQL 指令碼，並使用 PowerShell 的 Azure 至 Azure Blob 儲存體上傳指令碼。 登錄區需要 HiveQL 指令碼會儲存在 Azure Blob 儲存體。

HiveQL 指令碼會執行下列動作︰

1. **卸除 delays_raw 資料表**，大小寫資料表已經存在。
2. **建立 delays_raw 外部登錄區資料表**與航班延遲檔案指向 Blob 儲存位置。 此查詢會指定欄位，以分隔 「，」 和 「 \n 」 來結束的線條。 這會產生問題時欄位的值包含逗號，因為登錄區無法區分欄位分隔符號的逗號和一個是欄位值的一部分 (這是原始的欄位值的大小寫\_縣/市\_名稱] 和 [目的地\_縣/市\_名稱)。 若要解決這個問題，查詢會建立 TEMP 欄，按住不正確的方式分割成欄的資料。  
3. **卸除延遲的資料表**，大小寫資料表已經存在。
4. **建立 [延遲] 資料表**。 還是有幫助以進一步處理之前，先清理資料。 此查詢會建立新的資料表，*延遲*，delays_raw 資料表中。 請注意，不會複製 TEMP 欄 （如先前所述），而且**子字串**函數，用於從資料中移除括住。
5. **依縣/市名稱計算平均天氣延遲和群組的結果。** 它也會輸出結果 Blob 儲存體。 請注意，查詢會從資料中移除單引號會排除的列位置**weather_delay** ] 的值為 null。 因為 Sqoop，稍後再使用此教學課程中，無法順利處理這些值，根據預設，這是必要。

如需 HiveQL 命令的完整清單，請參閱[登錄區資料定義語言][hadoop-hiveql]。 每個 HiveQL 命令必須以分號終止。

**若要建立 HiveQL 指令碼檔**

1. 準備參數︰

    <table border="1">
    <tr><th>變數名稱</th><th>備忘稿</th></tr>
    <tr><td>$storageAccountName</td><td>Azure 儲存體帳戶，您要上傳 HiveQL 指令碼的位置。</td></tr>
    <tr><td>$blobContainerName</td><td>您要上傳 HiveQL 指令碼 Blob 容器。</td></tr>
    </table>
2. 開啟 Azure PowerShell ise [以系統。

3. 複製並貼上下列的指令碼至指令碼窗格︰

        [CmdletBinding()]
        Param(

            # Azure Blob storage variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #region - Define variables
        # Treat all errors as terminating
        $ErrorActionPreference = "Stop"
        
        # The HiveQL script file is exported as this file before it's uploaded to Blob storage
        $hqlLocalFileName = "e:\tutorials\flightdelay\flightdelays.hql"
        
        # The HiveQL script file will be uploaded to Blob storage as this blob name
        $hqlBlobName = "tutorials/flightdelay/flightdelays.hql"
        
        # These two constants are used by the HiveQL script file
        #$srcDataFolder = "tutorials/flightdelay/data"
        $dstDataFolder = "/tutorials/flightdelay/output"
        #endregion

        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #Region - Validate user input
        Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
        # Validate the Storage account
        if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }
        else{
            $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
        }
        
        # Validate the container
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion
        
        #region - Validate the file and file path
        
        # Check if a file with the same file name already exists on the workstation
        Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
        if (test-path $hqlLocalFileName){
        
            $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'
        
            if ($isDelete.ToLower() -ne "y")
            {
                Exit
            }
        }
        
        # Create the folder if it doesn't exist
        $folder = split-path $hqlLocalFileName
        if (-not (test-path $folder))
        {
            Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green
        
            new-item $folder -ItemType directory  
        }
        #end region
        
        #region - Write the Hive script into a local file
        Write-Host "`nWriting the Hive script into a file on your workstation ..." `
                    -ForegroundColor Green
        
        $hqlDropDelaysRaw = "DROP TABLE delays_raw;"
        
        $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
                "YEAR string, " +
                "FL_DATE string, " +
                "UNIQUE_CARRIER string, " +
                "CARRIER string, " +
                "FL_NUM string, " +
                "ORIGIN_AIRPORT_ID string, " +
                "ORIGIN string, " +
                "ORIGIN_CITY_NAME string, " +
                "ORIGIN_CITY_NAME_TEMP string, " +
                "ORIGIN_STATE_ABR string, " +
                "DEST_AIRPORT_ID string, " +
                "DEST string, " +
                "DEST_CITY_NAME string, " +
                "DEST_CITY_NAME_TEMP string, " +
                "DEST_STATE_ABR string, " +
                "DEP_DELAY_NEW float, " +
                "ARR_DELAY_NEW float, " +
                "CARRIER_DELAY float, " +
                "WEATHER_DELAY float, " +
                "NAS_DELAY float, " +
                "SECURITY_DELAY float, " +
                "LATE_AIRCRAFT_DELAY float) " +
            "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
            "LINES TERMINATED BY '\n' " +
            "STORED AS TEXTFILE " +
            "LOCATION 'wasbs://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';"
        
        $hqlDropDelays = "DROP TABLE delays;"
        
        $hqlCreateDelays = "CREATE TABLE delays AS " +
            "SELECT YEAR AS year, " +
                "FL_DATE AS flight_date, " +
                "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
                "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
                "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
                "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
                "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
                "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
                "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
                "DEST_AIRPORT_ID AS dest_airport_id, " +
                "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
                "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
                "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
                "DEP_DELAY_NEW AS dep_delay_new, " +
                "ARR_DELAY_NEW AS arr_delay_new, " +
                "CARRIER_DELAY AS carrier_delay, " +
                "WEATHER_DELAY AS weather_delay, " +
                "NAS_DELAY AS nas_delay, " +
                "SECURITY_DELAY AS security_delay, " +
                "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
            "FROM delays_raw;"
        
        $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
            "SELECT regexp_replace(origin_city_name, '''', ''), " +
                "avg(weather_delay) " +
            "FROM delays " +
            "WHERE weather_delay IS NOT NULL " +
            "GROUP BY origin_city_name;"
        
        $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal
        
        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force
        #endregion
        
        #region - Upload the Hive script to the default Blob container
        Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green
        
        # Create a storage context object
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        # Upload the file from local workstation to Blob storage
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext
        #endregion
        
        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    以下是用的指令碼的變數︰

    - **$hqlLocalFileName** -指令碼 HiveQL 指令碼會將檔案儲存至本機之前上傳至 Blob 儲存體。 這是檔案名稱。 預設值是<u>C:\tutorials\flightdelay\flightdelays.hql</u>。
    - **$hqlBlobName** -這是 Azure Blob 儲存體中使用的 HiveQL 指令碼檔 blob 名稱。 預設值是 tutorials/flightdelay/flightdelays.hql。 Azure Blob 儲存體會直接寫入檔案，因為沒有 」 / 「 blob 名稱的開頭。 如果您想要從 Blob 儲存體存取檔案，您必須新增 「 / 」 開頭的檔案名稱。
    - **$srcDataFolder**和**$dstDataFolder** -= 「 教學課程/flightdelay/資料 「 = 」 教學課程/flightdelay/輸出 」


---
##<a id="appendix-c"></a>附錄 C-準備 Sqoop 工作輸出 Azure SQL 資料庫
**若要準備 SQL 資料庫 （這與合併 Sqoop 指令碼）**

1. 準備參數︰

    <table border="1">
    <tr><th>變數名稱</th><th>備忘稿</th></tr>
    <tr><td>$sqlDatabaseServerName</td><td>Azure SQL 資料庫伺服器的名稱。 輸入任何動作，建立新的伺服器。</td></tr>
    <tr><td>$sqlDatabaseUsername</td><td>登入 Azure SQL 資料庫伺服器的名稱。 如果 $sqlDatabaseServerName 現有的伺服器，請登入並登入密碼會用於驗證與伺服器。 否則，用來建立新的伺服器。</td></tr>
    <tr><td>$sqlDatabasePassword</td><td>Azure SQL 資料庫伺服器的登入密碼。</td></tr>
    <tr><td>$sqlDatabaseLocation</td><td>只有在您要建立新的 Azure 資料庫伺服器時，會使用這個值。</td></tr>
    <tr><td>$sqlDatabaseName</td><td>用來建立 AvgDelays 表格 Sqoop 工作 SQL 資料庫。 保留空白，將會建立資料庫，稱為 HDISqoop。 Sqoop 工作輸出的資料表名稱是 AvgDelays。 </td></tr>
    </table>
2. 開啟 Azure PowerShell ise [以系統。
3. 複製並貼上下列的指令碼至指令碼窗格︰

        [CmdletBinding()]
        Param(
        
            # Azure Resource group variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure resource group name. It will be created if it doesn't exist.")]
            [String]$resourceGroupName,
        
            # SQL database server variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database Server Name. It will be created if it doesn't exist.")]
            [String]$sqlDatabaseServer, 
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database admin user.")]
            [String]$sqlDatabaseLogin,
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database admin user password.")]
            [String]$sqlDatabasePassword,
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the region to create the Database in.")]
            [String]$sqlDatabaseLocation,   #For example, West US.
        
            # SQL database variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the database name. It will be created if it doesn't exist.")]
            [String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
        )
        
        # Treat all errors as terminating
        $ErrorActionPreference = "Stop"
        
        #region - Constants and variables
        
        # IP address REST service used for retrieving external IP address and creating firewall rules
        [String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
        [String]$fireWallRuleName = "FlightDelay"
        
        # SQL database variables
        [String]$sqlDatabaseMaxSizeGB = 10
        
        #SQL query string for creating AvgDelays table
        [String]$sqlDatabaseTableName = "AvgDelays"
        [String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [origin_city_name] [nvarchar](50) NOT NULL,
                    [weather_delay] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
                (
                    [origin_city_name] ASC
                )
                )"
        #endregion
        
        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #region - Create and validate Azure resouce group
        try{
            Get-AzureRmResourceGroup -Name $resourceGroupName
        }
        catch{
            New-AzureRmResourceGroup -Name $resourceGroupName -Location $sqlDatabaseLocation
        }
        
        #EndRegion
        
        #region - Create and validate Azure SQL database server
        try{
            Get-AzureRmSqlServer -ServerName $sqlDatabaseServer -ResourceGroupName $resourceGroupName}
        catch{
            Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
        
            $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
            $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
        
            $sqlDatabaseServer = (New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -SqlAdministratorCredentials $credential -Location $sqlDatabaseLocation).ServerName
            Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan
        
            Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
            $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
            New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress
        
            #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. Note that this allows Azure traffic from any Azure subscription to access the server.
            New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-Azureservices" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
        }
        
        #endregion
        
        #region - Create and validate Azure SQL database
        
        try {
            Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
        }
        catch {
            Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
            New-AzureRMSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName -Edition "Standard" -RequestedServiceObjectiveName "S1"
        }
        
        #endregion
        
        #region -  Execute an SQL command to create the AvgDelays table
        
        Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()
        
        $conn.close()
        
        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    >[AZURE.NOTE] 指令碼使用代表性狀態傳輸 （剩餘） 服務，http://bot.whatismyipaddress.com，擷取您外部的 IP 位址。 IP 位址是用來建立 SQL 資料庫伺服器的防火牆規則。  

    以下是一些使用指令碼中的變數︰

    - **$ipAddressRestService**預設值是 http://bot.whatismyipaddress.com。 這是公用 IP 位址其餘服務取得您外部的 IP 位址。 如果您想要您可以使用其他服務。 外部的 IP 位址透過服務擷取會用於建立 Azure SQL 資料庫伺服器的防火牆規則，讓您可以從工作站存取資料庫 （藉由使用 Windows PowerShell 指令碼）。
    - **$fireWallRuleName** -這是 Azure SQL 資料庫伺服器的防火牆規則的名稱。 預設名稱是<u>FlightDelay</u>。 您可以重新命名該如果您想要。
    - 只有在您要建立新的 Azure SQL 資料庫伺服器時，會使用**$sqlDatabaseMaxSizeGB**這個值。 預設值為 10 GB。 10 GB 不足，此教學課程。
    - 只有在您要建立新的 Azure SQL 資料庫時，會使用**$sqlDatabaseName**這個值。 預設值是 HDISqoop。 如果您將其重新命名，您必須適當地更新 Sqoop Windows PowerShell 指令碼。

4. 按**F5**執行指令碼。
5. 驗證指令碼輸出。 請確定已順利執行指令碼。

##<a id="nextsteps"></a>後續步驟
現在您瞭解如何將檔案上傳至 Azure Blob 儲存體、 如何使用 Azure Blob 儲存體中的資料填入登錄區資料表、 如何執行登錄區查詢，以及如何使用 Sqoop HDFS 資料匯出至 Azure SQL 資料庫。 若要深入瞭解，請參閱下列文章︰

* [快速入門 HDInsight][hdinsight-get-started]
* [使用 HDInsight 的登錄區][hdinsight-use-hive]
* [使用 HDInsight Oozie][hdinsight-use-oozie]
* [使用 HDInsight Sqoop][hdinsight-use-sqoop]
* [使用 HDInsight 的豬][hdinsight-use-pig]
* [開發 HDInsight Java MapReduce 程式][hdinsight-develop-mapreduce]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[powershell-install-configure]: powershell-install-configure.md

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
