<properties
    pageTitle="上傳的 HDInsight Hadoop 工作資料 |Microsoft Azure"
    description="瞭解如何上傳並存取中使用 Azure CLI、 Azure 儲存檔案總管、 PowerShell 的 Azure、 Hadoop 命令列或 Sqoop HDInsight Hadoop 工作的資料。"
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
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>



#<a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>上傳 Hadoop 工作 HDInsight 的資料

Azure HDInsight 透過 Azure Blob 儲存體提供完整功能 Hadoop 分散式檔案系統 (HDFS)。 它被為了 HDFS 副檔名為客戶提供順暢的使用體驗。 讓中直接在管理的資料上作業的 Hadoop 生態系統元件的完整。 Azure Blob 儲存體和 HDFS 是不同的檔案系統最佳化的資料並計算該資料的儲存空間。 使用 Azure Blob 儲存體的優點的相關資訊，請參閱[使用 Azure Blob 儲存體，與 HDInsight][hdinsight-storage]。

**必要條件**

開始之前，請注意下列需求︰

* Azure HDInsight 叢集。 如需相關指示，請參閱[開始使用 Azure HDInsight] [hdinsight-get-started]或[佈建 HDInsight 叢集][hdinsight-provision]。

##<a name="why-blob-storage"></a>為什麼 blob 儲存空間？

Azure HDInsight 叢集通常會執行 MapReduce 工作部署，這些工作完成後，會遭到捨棄叢集。 資料儲存於 HDFS 叢集完成計算後會耗費的方式來儲存此資料。 Azure Blob 儲存體是容量可用性、 彈性、 高、 最低的成本，並可共用的儲存空間選項處理使用 HDInsight 的資料。 將資料儲存在 blob 啟用 HDInsight 叢集，而不遺失資料安全地釋放用於計算。

###<a name="directories"></a>目錄

Azure Blob 儲存體容器會將資料儲存為金鑰/值組，而且沒有目錄階層。 不過 「 / 」 字元可用的機碼名稱中，使其顯示為目錄結構中儲存檔案。 HDInsight 都無法看到這些是實際的目錄。

例如，blob 的金鑰可能*input/log1.txt*。 沒有實際的 「 輸入 「 目錄存在，但由於 「 / 」 字元碼名稱中的目前狀態，因此會有檔案路徑的外觀。

因此，如果您使用 Azure Explorer 工具，您可能會發現有些 0 位元組檔案。 這些檔案有兩個功能︰

- 如果沒有空白的資料夾，他們會標示的資料夾存在。 Azure Blob 儲存體是聰明不夠知道稱為 foo/列 blob 存在，如果有一個名為**foo**資料夾。 但是，表示一個空的資料夾，稱為**foo**唯一方式是由特殊 0 位元組檔案中的位置。

- 他們按住特殊的中繼資料所需的 Hadoop 檔案系統，尤其是權限與資料夾的擁有者。

##<a name="command-line-utilities"></a>命令列公用程式

Microsoft 提供下列與 Azure Blob 儲存體搭配公用程式︰

| 工具 | Linux | OS X | Windows |
| ---- |:-----:|:----:|:-------:|
| [Azure 命令列介面][azurecli] | ✔ | ✔ | ✔ |
| [Azure PowerShell][azure-powershell] | | | ✔ |
| [AzCopy][azure-azcopy] | | | ✔ |
| [Hadoop] 命令](#commandline) | ✔ | ✔ | ✔ |

> [AZURE.NOTE] 雖然 Azure CLI PowerShell 的 Azure 與 AzCopy 都可以使用從外部 Azure Hadoop] 命令只適 HDInsight 叢集上，只允許 [本機檔案系統中的資料載入 Azure Blob 儲存體。

###<a id="xplatcli"></a>Azure CLI

Azure CLI 可跨平台工具，可讓您管理 Azure 服務。 若要上傳至 Azure Blob 儲存體的資料使用下列步驟︰

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [安裝及設定 Azure CLI for Mac、 Linux 和 Windows](../xplat-cli-install.md)。

2. 開啟命令提示字元、 艦隊或其他命令介面，並使用下列項目會驗證 Azure 訂閱。

        azure login

    出現提示時，輸入使用者名稱和密碼，您的訂閱。

3. 輸入下列命令，列出您的訂閱的儲存空間帳戶︰

        azure storage account list

4. 選取包含您想要使用，的 blob 儲存體帳戶，然後使用下列命令以擷取此帳戶金鑰︰

        azure storage account keys list <storage-account-name>

    這會傳回**主要**和**次要**鍵。 複製**主**索引鍵值，因為它將會用於接下來的步驟。

5. 您可以使用下列命令來擷取 blob 儲存體帳戶內的容器的清單︰

        azure storage container list -a <storage-account-name> -k <primary-key>

6. 若要上傳及下載檔案以 blob 使用下列命令︰

    * 若要上傳檔案︰

            azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

    * 若要下載檔案︰

            azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] 如果您隨時都使用相同的儲存空間帳戶，您可以設定下列環境變數，而不是指定的帳戶，而且每個指令的關鍵︰
>
> * **AZURE\_儲存\_帳戶**︰ 儲存體帳戶名稱
>
> * **AZURE\_儲存\_存取\_鍵**︰ 儲存帳戶金鑰

###<a id="powershell"></a>Azure PowerShell

Azure PowerShell 是您可以使用來控制及自動化的部署及管理您的工作量 Azure 中的指令碼環境。 設定您的工作站執行 PowerShell 的 Azure 的相關資訊，請參閱[安裝及設定 PowerShell 的 Azure](../powershell-install-configure.md)。

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**若要將本機的檔案上傳至 Azure Blob 儲存體**

1. 中的指示，請開啟 Azure PowerShell 主控台[安裝及設定 PowerShell 的 Azure](../powershell-install-configure.md)。
2. 設定下列指令碼前五個變數的值︰

        $resourceGroupName = "<AzureResourceGroupName>"
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<ContainerName>"

        $fileName ="<LocalFileName>"
        $blobName = "<BlobName>"

        # Get the storage account key
        $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
        # Create the storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to the Blob container
        Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext

3. 指令碼貼到 PowerShell 的 Azure 主控台執行複製檔案。

例如 PowerShell 指令碼建立使用 HDInsight，請參閱[HDInsight 工具](https://github.com/blackmist/hdinsight-tools)。

###<a id="azcopy"></a>AzCopy

AzCopy 是設計用來簡化工作和 Azure 儲存體帳戶傳送資料的命令列工具。 您可以將其作為獨立工具或併入此工具中現有的應用程式。 [下載 AzCopy][azure-azcopy-download]。

AzCopy 語法是︰

    AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

如需詳細資訊，請參閱[AzCopy-上傳/下載檔案以供 Azure Blob][azure-azcopy]。


###<a id="commandline"></a>Hadoop 命令列

Hadoop 命令列才有幫助的資料已在叢集簡報時，將資料儲存到 blob 儲存體。

若要使用 [Hadoop] 命令，您必須先連線到使用以下兩種方法的其中一個 headnode:

* **Windows 型 HDInsight**︰[使用遠端桌面連線。](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp)

* **Linux 型 HDInsight**︰ 使用 SSH （[SSH 命令](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)或[PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)） 連線

連線之後，您可以使用下列語法，若要將檔案上傳至儲存體。

    hadoop -copyFromLocal <localFilePath> <storageFilePath>

例如，`hadoop fs -copyFromLocal data.txt /example/data/data.txt`

因為 HDInsight 的預設檔案系統中 Azure Blob 儲存體，/example/data.txt 實際上是 Azure Blob 儲存體中。 您也可以參考歸檔為]:

    wasbs:///example/data/data.txt

或

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

使用檔案的其他 Hadoop] 命令的清單，請參閱[http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [AZURE.WARNING] 在 HBase 叢集，預設會封鎖時，使用撰寫資料 256 KB 的大小。 而正常運作時使用 HBase Api 或 REST Api，使用`hadoop`或`hdfs dfs`撰寫大於 ~ 12 GB 的資料會導致錯誤的命令。 請參閱下方[上 blob 寫入的儲存空間例外狀況](#storageexception)區段，如需詳細資訊。

##<a name="graphical-clients"></a>圖形的用戶端

此外，還有幾個應用程式提供的圖形化介面使用 Azure 儲存體。 以下是幾個這些應用程式的清單︰

| 用戶端 | Linux | OS X | Windows |
| ------ |:-----:|:----:|:-------:|
| [Microsoft Visual Studio Tools for HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources) | ✔ | ✔ | ✔ |
| [Azure 儲存檔案總管](http://storageexplorer.com/) | ✔ | ✔ | ✔ |
| [雲端儲存空間 Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | | ✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | | ✔ |
| [Azure 總管](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | | ✔ |
| [Cyberduck](https://cyberduck.io/) |  | ✔ | ✔ |

###<a name="visual-studio-tools-for-hdinsight"></a>Visual Studio HDInsight 工具

如需詳細資訊，請參閱[瀏覽連結的資源](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources)。

###<a id="storageexplorer"></a>Azure 儲存檔案總管

*Azure 儲存體 Explorer*是實用的工具，檢查和更改二進位大型物件中的資料。 這是您可以從[http://storageexplorer.com/](http://storageexplorer.com/)下載免費、 開啟來源工具。 使用此連結，以及從來源程式碼。

使用工具之前，您必須知道您 Azure 儲存體帳戶名稱和帳戶金鑰]。 這項資訊的相關指示，請參閱 「 如何︰ 檢視、 複製及重新產生的儲存空間的便捷鍵 」 章節的[建立、 管理，或刪除儲存帳戶][azure-create-storage-account]。  

1. 執行 Azure 儲存檔案總管。 如果這是您第一次儲存檔案總管，您會提示您的 ___儲存體帳戶名稱__和__儲存帳戶金鑰]__。 如果您執行的之前，使用 [__新增__] 按鈕，將新的儲存空間客戶名稱] 和 [索引鍵。

    輸入名稱，然後使用 HDinsight 叢集儲存帳戶金鑰]，然後選取 [__儲存及開啟__。

    ![HDI。AzureStorageExplorer][image-azure-storage-explorer]

5. 在左邊的介面容器的清單中，按一下 [容器 HDInsight 叢集與相關聯的名稱。 根據預設，這是 HDInsight 叢集的名稱，但可能不同，如果您在建立叢集時輸入特定的名稱。

6. 從 [工具] 列中，選取 [上傳] 圖示。

    ![醒目提示的上傳圖示的工具列](./media/hdinsight-upload-data/toolbar.png)

7. 指定要上傳的檔案，然後按一下 [**開啟**。 出現提示時，請選取 [__上傳__至的檔案上傳至存放容器的根目錄]。 如果您想要的檔案上傳至特定的路徑，__目的__欄位中輸入路徑，然後選取 [__上傳__。

    ![檔案上傳] 對話方塊](./media/hdinsight-upload-data/fileupload.png)
    
    上傳完成檔案之後，您可以使用它，從 HDInsight 叢集上的工作。

##<a name="mount-azure-blob-storage-as-local-drive"></a>本機磁碟機裝載 Azure Blob 儲存體

請參閱[為本機磁碟機裝載 Azure Blob 儲存體](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx)。

##<a name="services"></a>服務

###<a name="azure-data-factory"></a>Azure 資料工廠

Azure 資料工廠服務會將精簡、 可調整和可靠的資料生產管線撰寫資料儲存、 資料處理和資料移動服務完全受管理的服務。

Azure 資料工廠可用來將資料移到 Azure Blob 儲存體，或建立資料管線，直接使用 HDInsight 功能，例如登錄區豬。

如需詳細資訊，請參閱[Azure 資料工廠文件](https://azure.microsoft.com/documentation/services/data-factory/)。

###<a id="sqoop"></a>Apache Sqoop

Sqoop 是設計用來將資料傳送 Hadoop 和關聯式資料庫之間的工具。 您可以使用它從關聯式資料庫管理系統 (RDBMS)，匯入資料，例如 SQL Server]、 [MySQL 或 [Oracle Hadoop 分散式檔案系統 (HDFS)，將轉換的資料中 Hadoop MapReduce 或登錄區，然後將資料匯出至 RDBMS。

如需詳細資訊，請參閱[使用 Sqoop 與 HDInsight][hdinsight-use-sqoop]。

##<a name="development-sdks"></a>開發 Sdk

Azure Blob 儲存體也可以使用下列的程式設計語言的 Azure SDK 來存取︰

* .NET
* Java
* Node.js
* PHP
* Python
* [注音標示

如需有關如何安裝 Azure Sdk 的詳細資訊，請參閱[Azure 下載](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>疑難排解

### <a id="storageexception"></a>Blob 上寫入的儲存空間例外狀況

__發生上述任何症狀__︰ 使用時`hadoop`或`hdfs dfs`命令來撰寫 ~ 12 GB 的檔案或更大在 HBase 叢集上，您可能會遇到下列錯誤︰ 

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

__原因__︰ 在 HDInsight HBase 叢集，預設 256 KB 的區塊大小寫入 Azure 儲存時。 雖然這適用於 HBase Api 或 REST Api，將會導致錯誤時使用`hadoop`或`hdfs dfs`命令列公用程式。

__解決方案__︰ 使用`fs.azure.write.request.size`指定較大的區塊。 您可以在每次使用使用`-D`參數。 以下是範例使用此參數`hadoop`命令︰

    hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data

您也可以增加的值`fs.azure.write.request.size`全面使用 Ambari。 若要變更 Ambari 網路使用者介面中的值可下列步驟︰

1. 在瀏覽器中，移至 [叢集 Ambari 網路使用者介面。 這是 https://CLUSTERNAME.azurehdinsight.net，其中__CLUSTERNAME__是叢集的名稱。

    出現提示時，輸入叢集管理員名稱和密碼。

2. 從畫面的左側，選取__HDFS__，然後按一下 [__設定__] 索引標籤。

3. 在 [__篩選...__ ] 欄位中，輸入`fs.azure.write.request.size`。 這會顯示欄位並在頁面中間的目前值。

4. 從 262144 (256 KB) 的值變更為新的值。 例如，4194304 (4 MB)。

![變更值，透過 Ambari Web UI 的圖像](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

如需有關如何使用 Ambari 的詳細資訊，請參閱[管理 HDInsight 叢集使用 Ambari Web UI](hdinsight-hadoop-manage-ambari.md)。

## <a name="next-steps"></a>後續步驟

瞭解如何將 HDInsight 取得資料，請閱讀下列文章，了解如何執行分析︰

* [開始使用 Azure HDInsight][hdinsight-get-started]
* [以程式設計方式提交 Hadoop 工作][hdinsight-submit-jobs]
* [使用 HDInsight 的登錄區][hdinsight-use-hive]
* [使用 HDInsight 的豬][hdinsight-use-pig]




[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage/storage-create-storage-account.md
[azure-azcopy-download]: ../storage/storage-use-azcopy.md
[azure-azcopy]: ../storage/storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../powershell-install-configure.md

[azurecli]: ../xplat-cli-install.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
