<properties
    pageTitle="產生建議使用 Mahout 和 WIndows 型 HDInsight |Microsoft Azure"
    description="瞭解如何使用文件庫的學習 Apache Mahout 機器產生影片建議使用 Windows 型 HDInsight (Hadoop)。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

#<a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight"></a>使用中 HDInsight Hadoop Apache Mahout 來產生影片建議

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

瞭解如何使用學習文件庫與 Azure HDInsight [Apache Mahout](http://mahout.apache.org)機器產生影片建議。

> [AZURE.NOTE] 這份文件中的步驟需要 Windows 用戶端和 Windows 型 HDInsight 叢集。 使用 Mahout Linux、 OS X 或 Unix 用戶端，與 Linux 型 HDInsight 叢集的資訊，請參閱[產生影片建議使用 Apache Mahout Linux 為基礎的 Hadoop HDInsight 中使用](hdinsight-hadoop-mahout-linux-mac.md)


##<a name="learn"></a>您將學習

Mahout 是[電腦學習][ ml] Apache Hadoop 的文件庫。 Mahout 包含演算法來處理資料，例如篩選、 分類和叢集。 本文中，您會使用建議引擎產生影片建議，為您的朋友過的影片。 您也會學習如何執行與決策樹系分類。 這將會教您下列動作︰

* 如何使用 Windows PowerShell 來執行 Mahout 工作

* 如何從 Hadoop 命令列執行 Mahout 工作

* 如何安裝 Mahout HDInsight 3.0 和 HDInsight 2.0 叢集上

    > [AZURE.NOTE] Mahout 隨附的叢集 HDInsight 3.1 的版本。 如果您使用的舊版的 HDInsight，請參閱[安裝 Mahout](#install)之前繼續。

##<a name="prerequisites"></a>必要條件

- **Windows 型 Hadoop 叢集 HDInsight 中**。 建立一個的相關資訊，請參閱[使用 Hadoop HDInsight 中的快速入門][getstarted]
- **使用 PowerShell 的 Azure 工作站**。

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


##<a name="recommendations"></a>使用 Windows PowerShell 產生建議

> [AZURE.NOTE] 雖然工作中使用此區段中的運作方式使用 Windows PowerShell 許多提供 Mahout 類別目前不適用於 Windows PowerShell 與他們必須使用 Hadoop 命令列執行。 如要使用 Windows PowerShell 的類別清單，請參閱[疑難排解](#troubleshooting)一節。
>
> 如需使用 Hadoop 命令列執行 Mahout 工作的範例，請參閱[使用 Hadoop 命令列分類資料](#classify)。

其中一個函數所提供的 Mahout 是建議引擎。 這個引擎接受的格式中的資料`userID`， `itemId`，及`prefValue`（項目使用者喜好設定）。 Mahout 可以再執行共同所分析，以決定︰_使用者擁有的項目喜好設定也有的偏好設定，這些項目_。 Mahout 再決定類似項目喜好設定，可以用來進行建議的使用者。

以下是使用影片非常簡單範例︰

* __共同所__︰ 王 Alice，與百勝所有按讚_星星戰爭_、_帝國它回_，並_傳回的 Jedi_。 Mahout 會決定也喜歡這些影片的任何一個使用者等其他兩個。

* __共同所__︰ 百勝並 Alice 也按讚_造成虛設威脅__的複製攻擊_，與_復仇的 Sith_。 Mahout 會決定使用者對按讚先前的三個影片也想這三個。

* __相似性建議__︰ 因為王按讚的前三個影片，Mahout 查看影片的其他具有類似的喜好設定按讚，但王不監視 （按讚/評等）。 在此情況下，Mahout 建議_造成虛設威脅__的複製攻擊_，與_復仇的 Sith_。

###<a name="understanding-the-data"></a>了解資料

在此便利地， [GroupLens 研究][movielens]提供影片與 Mahout 相容的格式的評等資料。 此資料才會提供您的叢集預設儲存空間`/HdiSamples/MahoutMovieData`。

有兩個檔案， `moviedb.txt` （影片，瞭解） 和`user-ratings.txt`。 在分析期間，使用使用者 ratings.txt 檔案，而 moviedb.txt 用來顯示分析的結果時提供方便使用的文字資訊。

在 [使用者 ratings.txt 中所包含的資料結構的`userID`， `movieID`， `userRating`，和`timestamp`，它會告訴我們如何高度每位使用者評等影片。 以下是資料的範例︰


    196 242 3   881250949
    186 302 3   891717742
    22  377 1   878887116
    244 51  2   880606923
    166 346 1   886397596

###<a name="run-the-job"></a>執行工作

您可以使用下列 Windows PowerShell 指令碼來執行 Mahout 建議引擎使用影片資料的工作︰

    # The HDInsight cluster name.
    $clusterName = "the cluster name"
    
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
            
    # NOTE: The version number in the file path
    # may change in future versions of HDInsight.
    $jarFile =  "file:///C:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar"
    #
    # If you are using an earlier version of HDInsight,
    # set $jarFile to the jar file you
    # uploaded.
    # For example,
    # $jarFile = "wasbs:///example/jars/mahout-core-0.9-job.jar"

    # The arguments for this job
    # * input - the path to the data uploaded to HDInsight
    # * output - the path to store output data
    # * tempDir - the directory for temp files
    $jobArguments = "--similarityClassname", "recommenditembased", `
                    "-s", "SIMILARITY_COOCCURRENCE", `
                    "--input", "wasbs:///HdiSamples/MahoutMovieData/user-ratings.txt",
                    "--output", "wasbs:///example/out",
                    "--tempDir", "wasbs:///example/temp"

    # Create the job definition
    $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
      -JarFile $jarFile `
      -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
      -Arguments $jobArguments

    # Start the job
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

    # Wait on the job to complete
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
            -Blob example/out/part-r-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
            
    # Write out any error information
    Write-Host "STDERR"
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

> [AZURE.NOTE] Mahout 工作不會移除暫存處理工作時所建立的資料。 `--tempDir`範例工作隔離暫存檔案到特定的目錄中指定參數。

Mahout 工作不會傳回輸出到 STDOUT。 不過，它將它儲存在指定的輸出目錄為__組件-r-00000__。 指令碼會下載至__output.txt__您工作站上目前目錄中的檔案。

此檔案的內容的範例如下︰

    1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

第一欄是`userID`。 中包含的值 」 [「 及 」] 」 會`movieId`:`recommendationScore`。

###<a name="view-the-output"></a>檢視輸出

雖然所產生的輸出可能使用應用程式中的 [確定]，並不容易閱讀。 `moviedb.txt`從伺服器可以用來解決`movieId`至影片名稱，但您必須先它和評等的檔案從伺服器下載使用下列指令碼︰

    # The HDInsight cluster name.
    $clusterName = "the cluster name"
    
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
    #Download the files
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/moviedb.txt" `
    -Container $container `
    -Destination moviedb.txt `
    -Context $context
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/user-ratings.txt" `
    -Container $container `
    -Destination user-ratings.txt `
    -Context $context

在您下載檔案後，使用下列 PowerShell 指令碼來顯示影片名稱的建議︰

    <#
    .SYNOPSIS
        Displays recommendations for movies.
    .DESCRIPTION
        Displays recommendations generated by Mahout
        with HDInsight example in a human readable format.
    .EXAMPLE
        .\Show-Recommendation -userId 4
            -userDataFile "user-ratings.txt"
            -movieFile "moviedb.txt"
            -recommendationFile "output.txt"
    #>

    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
        #The user ID
        [Parameter(Mandatory = $true)]
        [String]$userId,

        [Parameter(Mandatory = $true)]
        [String]$userDataFile,

        [Parameter(Mandatory = $true)]
        [String]$movieFile,

        [Parameter(Mandatory = $true)]
        [String]$recommendationFile
    )
    # Read movie ID & description into hash table
    Write-Host "Reading movies descriptions" -ForegroundColor Green
    $movieById = @{}
    foreach($line in Get-Content $movieFile)
    {
        $tokens = $line.Split("|")
        $movieById[$tokens[0]] = $tokens[1]
    }
    # Load movies user has already seen (rated)
    # into a hash table
    Write-Host "Reading rated movies" -ForegroundColor Green
    $ratedMovieIds = @{}
    foreach($line in Get-Content $userDataFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            # Resolve the ID to the movie name
            $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
        }
    }
    # Read recommendations generated by Mahout
    Write-Host "Reading recommendations" -ForegroundColor Green
    $recommendations = @{}
    foreach($line in get-content $recommendationFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            #Trim leading/treailing [] and split at ,
            $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
            foreach($movieIdAndScore in $movieIdAndScores)
            {
                #Split at : and store title and score in a hash table
                $idAndScore = $movieIdAndScore.Split(":")
                $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
            }
            break
        }
    }

    Write-Host "Rated movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                   @{Expression={$_.Value};Label="Rating"}
    $ratedMovieIds | format-table $ratedFormat
    Write-Host "---------------------------" -ForegroundColor Green

    write-host "Recommended movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                            @{Expression={$_.Value};Label="Score"}
    $recommendations | format-table $recommendationFormat

以下是範例執行指令碼︰

    PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt

輸出應如下︰

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237  

##<a name="classify"></a>使用 Hadoop 命令列分類資料

用於 Mahout 分類兩種方法之一是建立[隨機樹系][forest]。 這是多個步驟的程序，包括使用訓練資料產生決策樹，然後使用分類資料。 這是使用 Mahout 所提供的__org.apache.mahout.classifier.df.tools.Describe__類別。 目前必須執行使用 Hadoop 命令列。

###<a name="load-the-data"></a>將資料載入

1. 下載下列檔案從[NSL KDD 資料集](http://nsl.cs.unb.ca/NSL-KDD/)。

  * [KDDTrain +。ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff)︰ 訓練檔案

  * [KDDTest +。ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff)︰ 測試資料

2. 開啟每個檔案，然後移除線條的頂端'@',，然後儲存檔案。 如果這些不會移除，您會收到錯誤訊息時使用 Mahout 此資料。

2. 上傳檔案到__範例/資料__。 您可以使用下列指令碼。 取代__CLUSTERNAME__ HDInsight 叢集的名稱。 取代為名稱的檔案名稱上傳之檔案。

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName="CLUSTERNAME"
        $fileToUpload="FILENAME"
        $blobPath="example/data/FILENAME"
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
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context

###<a name="run-the-job"></a>執行工作

1. 此工作需要 Hadoop 命令列。 啟用遠端桌面 HDInsight 叢集，然後連線到其在[連線至 HDInsight 叢集使用 RDP](hdinsight-administer-use-management-portal.md#rdp)的指示執行。

3. 連線之後，使用 [ __Hadoop 命令列__] 圖示來開啟 Hadoop 命令列︰

    ![hadoop cli][hadoopcli]

3. 您可以使用下列命令，產生的檔案描述項 （__KDDTrain +.info__），使用 Mahout。

        hadoop jar "c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasbs:///example/data/KDDTrain+.arff" -f "wasbs:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

    `N 3 C 2 N C 4 N C 8 N 2 C 19 N L`說明檔案中的資料的屬性。 例如，L 會指出標籤。

4. 建立決策樹的樹系使用下列命令︰

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasbs:///example/data/KDDTrain+.arff -ds wasbs:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    這項作業的輸出會儲存在__nsl 樹系__目錄中，位於 HDInsight 叢集在 __ wasbs://user/ 儲存空間&lt;使用者名稱 > / nsl 樹系/nsl forest.seq。 &lt;使用者名稱 > 是用於您的遠端桌面工作階段的使用者名稱。 此檔案不太懂容易閱讀。

5. 測試樹系分類__KDDTest +.arff__資料集。 使用下列命令︰

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasbs:///example/data/KDDTest+.arff -ds wasbs:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasbs:///example/data/predictions

    這個命令會傳回摘要資訊分類程序如下︰

        14/07/02 14:29:28 INFO mapreduce.TestForest:

        =======================================================
        Summary
        -------------------------------------------------------
        Correctly Classified Instances          :      17560       77.8921%
        Incorrectly Classified Instances        :       4984       22.1079%
        Total Classified Instances              :      22544

        =======================================================
        Confusion Matrix
        -------------------------------------------------------
        a       b       <--Classified as
        9437    274      |  9711        a     = normal
        4710    8123     |  12833       b     = anomaly

        =======================================================
        Statistics
        -------------------------------------------------------
        Kappa                                       0.5728
        Accuracy                                   77.8921%
        Reliability                                53.4921%
        Reliability (standard deviation)            0.4933

  這項作業也會位於__wasbs:///example/data/predictions/KDDTest+.arff.out__檔案。 不過，此檔案不太懂容易閱讀。

> [AZURE.NOTE] Mahout 工作不會覆寫檔案。 如果您想要一次執行這些工作，您必須刪除前一個工作所建立的檔案。

##<a name="troubleshooting"></a>疑難排解

###<a name="install"></a>安裝 Mahout

Mahout 已經安裝在 HDInsight 3.1 叢集，而且它可以手動安裝 HDInsight 3.0 或 HDInsight 2.1 叢集上使用下列步驟︰

1. 若要使用的 Mahout 版本取決於叢集 HDInsight 版本。 您可以找到叢集版本 Azure 入口網站中檢視叢集的內容。

  * __HDInsight 2.1 版__，您可以下載含有[Mahout 0.9](http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar)Java 封存 (JAR) 檔案。

  * 您__的 HDInsight 3.0__，必須[建立從來源 Mahout] [build]指定 HDInsight 所提供的 Hadoop 版本。 安裝在 [建立] 頁面上所列的先決條件、 下載來源]，然後使用下列命令以建立 Mahout jar 檔案︰

            mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

        After the build completes, you can find the JAR file at __mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar__.

        > [AZURE.NOTE] 釋放 Mahout 1.0 後，您可以使用的預先建立的套件與 HDInsight 3.0。

2. 檔案上傳 jar 至叢集的預設儲存區中的__範例/（每瓶)__ 。 CLUSTERNAME 下列指令碼中的 HDInsight 叢集，名稱並取代檔名__mahout coure-0.9 job.jar__檔案的路徑。

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName = "CLUSTERNAME"
        $fileToUpload = "FILENAME"
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
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob "example/jars/mahout-core-0.9-job.jar" `
            -Container $container `
            -Context $context

###<a name="cannot-overwrite-files"></a>無法覆寫檔案

清理暫存檔案還是處理期間所建立的 mahout 工作執行。 此外，工作不會覆寫現有的輸出檔案。

若要執行 Mahout 工作時，避免發生錯誤，刪除暫存與輸出檔案之間便會執行，或使用唯一的暫時與輸出目錄名稱。

###<a name="cannot-find-the-jar-file"></a>找不到 JAR 檔案

HDInsight 3.1 叢集包含 Mahout。 路徑和檔名包含 Mahout 叢集上已安裝的版本號碼。 Windows PowerShell 指令碼在本教學課程中的使用年 11 月 2015年有效的路徑，但版本號碼會在未來變更更新至 HDInsight。 若要判斷目前叢集 Mahout JAR 檔案路徑，請使用下列 Windows PowerShell 命令，並修改參考檔案路徑會傳回的指令碼，然後︰

    Use-AzureRmHDInsightCluster -ClusterName $clusterName
    #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "wasbs:///example/statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'

###<a name="nopowershell"></a>要使用 Windows PowerShell 的類別

如果使用 Windows PowerShell 中使用下列類別的 mahout 工作會傳回各種不同的錯誤訊息︰

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

若要執行使用這些類別的工作，連線到 HDInsight 叢集，並使用 Hadoop 命令列執行的工作。 如需範例，請參閱[使用 Hadoop 命令列分類資料](#classify)。

##<a name="next-steps"></a>後續步驟

現在，您已經學會如何使用 Mahout，探索 HDInsight 的資料使用的其他的方法︰

* [使用 HDInsight 登錄區](hdinsight-use-hive.md)
* [HDInsight 的豬](hdinsight-use-pig.md)
* [使用 HDInsight MapReduce](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: ../powershell-install-configure.md
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 
