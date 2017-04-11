<properties
    pageTitle="查詢資料 HDFS 相容 Blob 儲存體 |Microsoft Azure"
    description="HDInsight 為 HDFS 的大型資料存放區的使用 Azure Blob 儲存體。 瞭解如何查詢從 Blob 儲存體的資料，並儲存您的分析結果。"
    keywords="blob 儲存體、 hdfs、 結構化的資料、 非結構化的資料"
    services="hdinsight,storage"
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
    ms.topic="get-started-article"
    ms.date="09/06/2016"
    ms.author="jgao"/>


# <a name="use-hdfs-compatible-azure-blob-storage-with-hadoop-in-hdinsight"></a>使用中 HDInsight Hadoop HDFS 相容 Azure Blob 儲存體

瞭解如何使用 HDInsight 低成本 Azure Blob 儲存體與建立 Azure 儲存體帳戶和 Blob 儲存容器，然後地址內的資料。

Azure Blob 儲存體是完美整合 HDInsight 健全、 用途的儲存空間方案。 透過 Hadoop 分散式的檔案系統 (HDFS) 介面中 HDInsight, 元件的完整可以直接在 Blob 儲存體中的結構化或非結構化資料上作業。

將資料儲存在 Blob 儲存體可讓您安全地刪除 HDInsight 叢集所使用的計算，而不遺失使用者資料。

> [AZURE.IMPORTANT] HDInsight 只支援區塊二進位大型物件。 它不支援頁面或附加二進位大型物件。

建立 HDInsight 叢集的相關資訊，請參閱[開始使用 HDInsight] [hdinsight-get-started]或[建立 HDInsight 叢集][hdinsight-creation]。


## <a name="hdinsight-storage-architecture"></a>HDInsight 儲存架構
下圖會提供 HDInsight 儲存架構抽象的檢視︰

![Hadoop 叢集使用 HDFS API 存取，並將結構化及非結構化資料儲存在 Blob 儲存體。](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "HDInsight 儲存架構")

HDInsight 提供分散式的檔案系統的本機附加至運算節點的存取權。 可存取這個檔案系統使用完整的 URI，例如︰

    hdfs://<namenodehost>/<path>

此外，HDInsight 能夠存取儲存在 Azure Blob 儲存體中的資料。 語法如下︰

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

> [AZURE.NOTE] 在 HDInsight 的版本早於 3.0`asv://`而不是使用`wasb://`。 `asv://`不應使用 HDInsight 叢集 3.0 或更新版本，這樣會造成錯誤。

Hadoop 支援預設檔案系統的的概念。 預設檔案系統表示的預設配置和授權。 也會用於解決相對路徑。 期間 HDInsight 建立程序、 Azure 儲存體帳戶與特定的 Azure Blob 儲存體容器從該帳戶會指定為預設檔案系統中。

除了此儲存帳戶，您可以從同一份 Azure 訂閱或不同的 Azure 訂閱新增額外的儲存空間帳戶建立程序期間，或建立叢集之後。 如需新增額外的儲存空間帳戶的相關指示，請參閱[建立 HDInsight 叢集][hdinsight-creation]。

- **容器中的儲存空間帳戶連線至叢集之︰**因為 [帳戶名稱] 和 [索引鍵關聯叢集建立期間，您會有這些容器中 blob 的完整功能。

- **公用容器或在未連線到叢集的儲存空間帳戶公用 blob:**您有 blob 容器中的唯讀權限。

    > [AZURE.NOTE]
        > 公用容器可讓您取得所有 blob 的可在該容器，並取得容器中繼資料的清單。 公用 blob 可讓您存取 blob，只有當您知道的確切的 URL。 如需詳細資訊，請參閱<a href="http://msdn.microsoft.com/library/windowsazure/dd179354.aspx">限制存取容器及二進位大型物件</a>。

- **私人容器在未連線到叢集的儲存空間帳戶︰**您無法存取容器中的 blob，除非您定義的儲存空間帳戶，當您送出 WebHCat 工作。 這是本文稍後的說明。


建立程序與其金鑰中定義的儲存空間帳戶會儲存在 %HADOOP_HOME%/conf/core-site.xml 叢集節點。 HDInsight 的預設行為是使用核心 site.xml 檔案中所定義的儲存空間帳戶。 不建議使用編輯核心 site.xml 檔案，因為叢集標頭 node(master) 可能會建立映像，或在任何時候移轉，這些檔案的任何變更將會遺失。

多個 WebHCat 工作，包括登錄區、 MapReduce、 Hadoop 串流，以及豬，可以執行儲存的帳戶和中繼資料，與他們的描述。 （此目前有效的中繼資料叨著儲存帳戶，但不是。）在 [此文件的 [[使用 PowerShell 的 Azure 存取 blob](#powershell) ] 區段中，有這項功能的範例。 如需詳細資訊，請參閱[使用替代的儲存空間帳戶與 Metastores HDInsight 叢集](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)。

Blob 儲存體可以用於結構化及非結構化資料。 Blob 儲存體容器會將資料儲存為金鑰/值組，且沒有目錄階層。 不過斜線字元 （/） 可用的機碼名稱中，使其顯示為目錄結構中儲存檔案。 例如，blob 的金鑰可能*input/log1.txt*。 沒有實際*輸入*目錄存在，但由於斜線字元碼名稱中的目前狀態，因此會有檔案路徑的外觀。

###<a id="benefits"></a>Blob 儲存體的優點
不共同找出的隱含的效能成本計算叢集並降低儲存資源運算叢集建立接近儲存空間帳戶資源 Azure 地區，高速網路位置可有效率運算節點存取 Azure Blob 儲存體內的資料了。

有相關聯，而不是 HDFS 的 Azure Blob 儲存體中儲存資料的幾項優點︰

* **資料重複使用及共用︰**HDFS 中的資料位於包裝盒內叢集。 有權存取叢集的應用程式可以使用 HDFS Api 使用的資料。 可存取 Azure Blob 儲存體中的資料，可以透過 HDFS Api 或[Blob 儲存體 REST Api][blob-storage-restAPI]。 因此，較大組的應用程式 （包括其他 HDInsight 叢集） 和工具可以用來產生與使用資料。
* **資料封存︰**將資料儲存在 Azure Blob 儲存體可用於計算，而不遺失使用者資料安全地刪除 HDInsight 叢集。
* **資料的儲存空間成本︰**資料儲存長期 DFS 是其他成本 Azure Blob 儲存體中儲存的資料，因為叢集的成本大於 Azure Blob 儲存容器的成本。 此外，因為您沒有每個運算叢集產生重新載入資料，您也要儲存資料載入成本。
* **彈性的擴充︰**雖然 HDFS 可讓您以橫向擴充的檔案系統，刻度取決於您建立的叢集節點數目。 變更小數位數，會變得更為複雜的程序比依賴彈性的縮放比例自動取得 Azure Blob 儲存體中的功能。
* **地理複寫︰**您 Azure Blob 儲存體容器可地理複寫。 雖然這可讓您地理復原及資料重複，容錯移轉到地理複寫的位置會嚴重影響您的效能，它可能會造成成本。 因此我們建議您妥善選擇地理複寫，只有資料的值是其他成本量。

某些 MapReduce 工作和封裝，可能會建立中繼不是真正要儲存 Azure Blob 儲存體中的結果。 在此情況下，您可以選擇將資料儲存在本機 HDFS。 事實上，HDInsight 會使用這些中繼結果登錄區工作及其他處理程序中的數個 DFS。

> [AZURE.NOTE] 大部分的 HDFS 命令 （例如<b>1 ！</b>、 <b>copyFromLocal</b>和<b>mkdir</b>） 仍正常運作。 原生 HDFS 實作 （這指 DFS），例如<b>fschk</b>和<b>dfsadmin</b>，特定的命令會 Azure Blob 儲存體中顯示不同的行為。

## <a name="create-blob-containers"></a>建立 Blob 容器

若要使用二進位大型物件，因此您先建立[Azure 儲存體帳戶][azure-storage-create]。 屬於此，您可以指定 Azure 的區域，以儲存您建立使用此帳戶的物件。 叢集和儲存帳戶必須裝載於相同的區域。 登錄區 metastore SQL Server 資料庫及 Oozie metastore SQL Server 資料庫也必須位於相同的區域。

無論它存在，您建立的每個 blob 所屬的容器中 Azure 儲存體帳戶。 此容器可能會建立外部 HDInsight，現有 blob 或它可能所建立的 HDInsight 叢集容器。


預設 Blob 容器儲存叢集等作業歷程記錄的特定資訊。 不使用多個 HDInsight 叢集共用預設 Blob 容器。 這可能會損毀工作歷程記錄，並將上線叢集。 建議您為每個叢集使用不同的容器，並將共用的資料放在指定的所有相關叢集，而不是預設儲存帳戶的部署中的連結的儲存空間帳戶。 如需有關如何設定連結的儲存空間帳戶的詳細資訊，請參閱[建立 HDInsight 叢集][hdinsight-creation]。 不過您可以重複使用預設存放容器在刪除原始的 HDInsight 叢集之後。 針對 HBase 叢集，您可以實際保留 HBase 資料表結構描述，以及資料來建立新的 HBase 叢集使用由 HBase 叢集已刪除的預設 blob 儲存容器。


### <a name="using-the-azure-portal"></a>使用 [Azure 入口網站

建立 HDInsight 叢集從入口網站，必須使用現有的儲存空間帳戶或建立新的儲存空間帳戶的選項︰

![hdinsight hadoop 建立資料來源](./media/hdinsight-hadoop-use-blob-storage/hdinsight.provision.data.source.png)

###<a name="using-azure-cli"></a>使用 Azure CLI

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

如果您有[安裝並設定 Azure CLI](../xplat-cli-install.md)，下列命令可用來儲存帳戶與容器。

    azure storage account create <storageaccountname> --type LRS

> [AZURE.NOTE] `--type`參數指出將複寫儲存帳戶的方式。 如需詳細資訊，請參閱[Azure 儲存複寫](../storage/storage-redundancy.md)。 不使用 ZRS ZRS 不支援頁面 blob、 檔案、 表格或佇列。

會提示您指定的儲存空間帳戶會位於地理區域。 您應該您計劃要建立 HDInsight 叢集之相同區域中建立的儲存空間的帳戶。

儲存帳戶建立後，請使用下列命令來擷取儲存帳戶金鑰︰

    azure storage account keys list <storageaccountname>

若要建立容器，請使用下列命令︰

    azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>

### <a name="using-azure-powershell"></a>使用 PowerShell 的 Azure

如果您[安裝並設定 PowerShell 的 Azure][powershell-install]，您可以使用下列 PowerShell 的 Azure 提示字元中，建立儲存帳戶與容器︰

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"
    
    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID
    
    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location
    
    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS 
    
    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

## <a name="address-files-in-blob-storage"></a>Blob 儲存體中的地址檔案

從 HDInsight 存取 Blob 儲存體中的檔案的 URI 配置是︰

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


URI 配置提供加密的存取 (與*wasb:*前置字元)，SSL 加密存取 （ *wasbs*)。 我們建議使用*wasbs*可能的話，即使存取 Azure 中相同的區域內的資料。

&lt;BlobStorageContainerName&gt;識別 Azure Blob 儲存體中的容器的名稱。
&lt;StorageAccountName&gt;識別 Azure 儲存體帳戶名稱。 需要的完整的網域名稱 (FQDN)。

如果有任何&lt;BlobStorageContainerName&gt;或&lt;StorageAccountName&gt;已指定，使用預設檔案系統。 在 [預設檔案系統上的檔案，您可以使用的相對路徑或絕對路徑。 例如，隨附 HDInsight 叢集*hadoop-mapreduce-examples.jar*檔案可以參考使用下列其中一項︰

    wasbs://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasbs:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE] 檔案名稱是<i>hadoop examples.jar</i> HDInsight 版本 2.1 和 1.6 叢集。


&lt;路徑&gt;是檔案或目錄 HDFS 路徑名稱。 因為容器中 Azure Blob 儲存體只要關鍵值存放區，沒有，則為 true 的階層式檔案系統。 Blob 鍵內的斜線字元 （/） 會解譯為目錄分隔符號。 *Hadoop-mapreduce-examples.jar* blob 名稱是的例如︰

    example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE] 使用外部 HDInsight blob 工作時，大部分的公用程式不要辨識 WASB 格式和改為預期基本路徑格式，例如`example/jars/hadoop-mapreduce-examples.jar`。

## <a name="access-blobs-using-azure-cli"></a>使用 Azure CLI 存取 blob

您可以使用下列命令，列出 blob 相關的命令︰

    azure storage blob

**若要將檔案上傳使用 Azure CLI 的範例**

    azure storage blob upload <sourcefilename> <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**使用 Azure CLI 下載檔案的範例**

    azure storage blob download <containername> <blobname> <destinationfilename> --account-name <storageaccountname> --account-key <storageaccountkey>

**若要刪除檔案使用 Azure CLI 的範例**

    azure storage blob delete <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**使用 Azure CLI 清單檔案的範例**

    azure storage blob list <containername> <blobname|prefix> --account-name <storageaccountname> --account-key <storageaccountkey>

## <a name="access-blobs-using-azure-powershell"></a>使用 PowerShell 的 Azure 存取 blob

> [AZURE.NOTE] 本節中的命令會提供基本範例使用 PowerShell 來存取儲存在二進位大型物件中的資料。 更多的完整功能的範例使用 HDInsight 的自訂，請參閱[HDInsight 工具](https://github.com/Blackmist/hdinsight-tools)。

您可以使用下列命令，列出 blob 相關的 cmdlet:

    Get-Command *blob*

![Blob 相關的 PowerShell 指令程式的清單。][img-hdi-powershell-blobcommands]

###<a name="upload-files"></a>上傳檔案

請參閱[上傳至 HDInsight 的資料][hdinsight-upload-data]。

###<a name="download-files"></a>下載檔案

下列指令碼會封鎖 blob 下載到目前的資料夾。 之前執行指令碼，請在您擁有寫入權限的資料夾變更的目錄。

    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at creation.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.
    
    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    Login-AzureRmAccount 
    Select-AzureRmSubscription -SubscriptionID "<Your Azure Subscription ID>"
    
    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    
    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
    
    Write-Host "List the downloaded file ..." -ForegroundColor Green
    cat "./$blob"

提供資源群組名稱] 和 [叢集名稱，您可以使用下列程式碼︰

    $resourceGroupName = "<AzureResourceGroupName>"
    $clusterName = "<HDInsightClusterName>"
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.
    
    $cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
    $defaultStorageContainer = $cluster.DefaultStorageContainer
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
    
    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob $blob -Context $storageContext -Force

###<a name="delete-files"></a>刪除檔案


    Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob

###<a name="list-files"></a>列出的檔案

    Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix "example/data/"

###<a name="run-hive-queries-using-an-undefined-storage-account"></a>執行使用未定義的儲存空間帳戶登錄區查詢

此範例會示範如何清單未定義建立程序期間的儲存空間帳戶的資料夾。
$clusterName = 」<HDInsightClusterName>」

    $undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
    $undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

    $undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

    Use-AzureRmHDInsightCluster $clusterName

    $defines = @{}
    $defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

    Invoke-AzureRmHDInsightHiveJob -Defines $defines -Query "dfs -ls wasbs://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"

## <a name="next-steps"></a>後續步驟

在本文中，您已經學會如何使用 HDInsight，HDFS 相容 Azure Blob 儲存體，您學到 Azure Blob 儲存體是 HDInsight 的基本元件。 這個選項可讓您建立可調整、 長期封存資料擷取 Azure Blob 儲存體解決方案，並使用 HDInsight 解除鎖定的儲存的結構化及非結構化資料內的資訊。

如需詳細資訊，請參閱︰

* [開始使用 Azure HDInsight][hdinsight-get-started]
* [上傳至 HDInsight 的資料][hdinsight-upload-data]
* [使用 HDInsight 的登錄區][hdinsight-use-hive]
* [使用 HDInsight 的豬][hdinsight-use-pig]
* [若要限制存取與 HDInsight 的資料使用 Azure 儲存共用 Access 簽章][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: ../powershell-install-configure.md
[hdinsight-creation]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
