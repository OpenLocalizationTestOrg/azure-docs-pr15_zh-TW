<properties
    pageTitle="在使用 PowerShell 的 Azure HDInsight Linux 上建立 Hadoop、 HBase、 或火花叢集 |Microsoft Azure"
    description="瞭解如何使用 PowerShell 的 Azure HDInsight 的建立 Hadoop、 HBase、 或火花叢集 Linux 上。"
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/05/2016"
    ms.author="nitinme"/>

# <a name="create-linux-based-clusters-in-hdinsight-by-using-azure-powershell"></a>建立 Linux 型叢集使用 PowerShell 的 Azure HDInsight

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Azure PowerShell 是功能強大的指令碼環境，您可以用來控制自動化的部署及管理您的工作量 Microsoft Azure 中。 這份文件提供如何使用 PowerShell 的 Azure 建立 Linux 型 HDInsight 叢集的相關資訊。 也包含一個範例指令碼。

> [AZURE.NOTE] Azure PowerShell 僅適用於 Windows 用戶端上的。 如果您使用的 Linux、 Unix 或 Mac OS X 的用戶端，請參閱[建立使用 Azure CLI Linux 型 HDInsight 叢集](hdinsight-hadoop-create-linux-clusters-azure-cli.md)建立叢集使用 Azure CLI 的相關資訊。

## <a name="prerequisites"></a>必要條件
您必須具備下列之前開始此程序︰

- Azure 的訂閱。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- Azure PowerShell。
    如需有關如何使用 PowerShell 的 Azure HDInsight 的詳細資訊，請參閱[管理 HDInsight 使用 PowerShell](hdinsight-administer-use-powershell.md)。 如 HDInsight Windows PowerShell 指令程式清單中，請參閱[HDInsight cmdlet 參考](https://msdn.microsoft.com/library/azure/dn858087.aspx)。

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="access-control-requirements"></a>存取控制需求

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>建立叢集

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

若要建立使用 PowerShell 的 Azure HDInsight 叢集，您必須完成下列程序︰

- 建立 Azure 資源群組
- 建立 Azure 儲存體帳戶
- 建立 Azure Blob 容器
- 建立 HDInsight 叢集

您必須設定為建立 Linux 叢集的兩個最重要參數會指定 OS 類型和 SSH 使用者詳細資料的項目︰

- 請確定您指定為**Linux**的**-OSType**參數。
- 若要使用 SSH 叢集上的遠端工作階段，您可以指定 SSH 使用者的密碼或 SSH 公用鍵。 如果您指定 SSH 使用者的密碼和 SSH 公開金鑰，也會忽略索引鍵。 如果您想要使用 SSH 鍵遠端工作階段，您必須指定空白 SSH 密碼的提示時。 如需有關如何使用 HDInsight SSH 的詳細資訊，請參閱下列文章︰

    * [使用上 HDInsight Linux、 Unix，或 OS X 的 Linux 型 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [使用上從 Windows HDInsight Linux 為基礎的 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-windows.md)

下列指令碼說明如何建立新的叢集︰

    $token ="<SpecifyAnUniqueString>"

    $resourceGroupName = $token + "rg"      # Provide a Resource Group name
    $clusterName = $token
    $defaultStorageAccountName = $token + "store"   # Provide a Storage account name
    $defaultStorageContainerName = $token + "container"
    $location = "East US 2"     # Change the location if needed
    $clusterNodes = 1           # The number of nodes in the HDInsight cluster

    # Sign in to Azure
    Login-AzureRmAccount

    # Select the subscription to use if you have multiple subscriptions
    #$subscriptionID = "<SubscriptionName>"        # Provide your Subscription Name
    #Select-AzureRmSubscription -SubscriptionId $subscriptionID

    # Create an Azure Resource Group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account and container used as the default storage
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $defaultStorageAccountName -ResourceGroupName $resourceGroupName)[0].Key1
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultStorageContainerName -Context $destContext

    # Create an HDInsight cluster
    $credentials = Get-Credential -Message "Enter Cluster user credentials" -UserName "admin"
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials"

    # The location of the HDInsight cluster must be in the same data center as the Storage account.
    $location = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $defaultStorageAccountName | %{$_.Location}

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials

**$ClusterCredentials**您指定的值用來建立叢集 Hadoop 使用者帳戶。 連線至叢集使用此帳戶。

**$SshCredentials**您指定的值用來建立叢集 SSH 使用者。 使用此帳戶叢集上啟動遠端 SSH 工作階段，並執行工作。

> [AZURE.IMPORTANT] 在這個指令碼，您必須指定就會在叢集的工作者節點數目。 如果您打算使用 32 個以上的工作者節點 （無論是在叢集建立，或藉由建立後縮放叢集），您也必須與至少 8 核心 14 GB 的 RAM 指定主節點大小。
>
> 如需有關節點大小和相關聯的成本的詳細資訊，請參閱[HDInsight 價格](https://azure.microsoft.com/pricing/details/hdinsight/)。

可能需要建立叢集最 20 分鐘。

下列範例會示範如何新增額外的儲存空間帳戶︰

    # Create another storage account used as additional storage account
    $additionalStorageAccountName = $token + "store2"
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $additionalStorageAccountName -Location $location -Type Standard_LRS
    $additionalStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $additionalStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value

    $config = New-AzureRmHDInsightClusterConfig
    Add-AzureRmHDInsightStorage -Config $config -StorageAccountName "$additionalStorageAccountName.blob.core.windows.net" -StorageAccountKey $additionalStorageAccountKey

    # Create a new HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials `
        -Config $config

## <a name="customize-clusters"></a>自訂叢集

- 請參閱[自訂 HDInsight 叢集使用啟動安裝程式](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell)。
- 請參閱[自訂 Windows 型 HDInsight 叢集使用指令碼動作](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell)。

## <a name="delete-the-cluster"></a>刪除叢集

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>後續步驟

現在您已成功建立 HDInsight 叢集，使用下列資源，瞭解如何使用叢集。

### <a name="hadoop-clusters"></a>Hadoop 叢集

* [使用 HDInsight 的登錄區](hdinsight-use-hive.md)
* [使用 HDInsight 的豬](hdinsight-use-pig.md)
* [使用 HDInsight MapReduce](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase 叢集

* [在 HDInsight HBase 快速入門](hdinsight-hbase-tutorial-get-started-linux.md)
* [Java 為開發應用程式上 HDInsight HBase](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>大量叢集

* [開發上 HDInsight 大量 Java 拓撲](hdinsight-storm-develop-java-topology.md)
* [使用大量上 HDInsight Python 元件](hdinsight-storm-develop-python-topology.md)
* [部署，並監控與上 HDInsight 大量的拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>火花叢集

* [建立使用 Scala 獨立應用程式](hdinsight-apache-spark-create-standalone-application.md)
* [在使用晚總火花叢集從遠端執行工作](hdinsight-apache-spark-livy-rest-interface.md)
* [使用 BI 火花︰ 執行火花 HDInsight 中使用的 BI 工具的互動式的資料分析](hdinsight-apache-spark-use-bi-tools.md)
* [與電腦學習火花︰ 使用火花 HDInsight 預測食物檢查結果中](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [火花串流︰ 使用火花 HDInsight 建置即時串流應用程式中](hdinsight-apache-spark-eventhub-streaming.md)
