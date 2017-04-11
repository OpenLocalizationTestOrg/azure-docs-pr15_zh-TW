<properties
   pageTitle="使用 PowerShell 的 Azure HDInsight 中建立 Windows 型 Hadoop 叢集 |Microsoft Azure"
    description="瞭解如何建立使用 PowerShell 的 Azure 叢集 Azure HDInsight。"
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/10/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-powershell"></a>建立 Windows 型 Hadoop 叢集中使用 PowerShell 的 Azure HDInsight

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

瞭解如何建立使用 PowerShell 的 Azure HDInsight 叢集。 Azure PowerShell 是一個模組，可提供 cmdlet 來管理使用 Windows PowerShell 的 Azure。 其他叢集建立的工具和功能按一下此頁面上方的索引標籤上選取，或查看[叢集建立方法](hdinsight-provision-clusters.md#cluster-creation-methods)。


##<a name="prerequisites"></a>先決條件︰

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

本文中的指示進行之前，您必須具備下列項目︰

- Azure 訂閱。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- Azure PowerShell。

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="access-control-requirements"></a>存取控制需求

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>建立叢集
Azure PowerShell 是功能強大的指令碼環境，您可以用來控制自動化的部署及管理您的工作量 Azure 中。 本節提供如何使用 PowerShell 的 Azure 建立 HDInsight 叢集指示。 設定工作站執行 HDInsight Windows PowerShell cmdlet 的詳細資訊，請參閱[安裝及設定 PowerShell 的 Azure](../powershell-install-configure.md)。 如需有關如何使用 PowerShell 的 Azure HDInsight 的詳細資訊，請參閱[管理 HDInsight 使用 PowerShell](hdinsight-administer-use-powershell.md)。 HDInsight Windows PowerShell cmdlet 的清單，請參閱[HDInsight cmdlet 參考](https://msdn.microsoft.com/library/azure/dn858087.aspx)。


若要建立使用 PowerShell 的 Azure HDInsight 叢集需要下列程序︰

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<Enter an Alias>" 
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<Enter a Password>"
    #endregion

    ###########################################
    # Service names and varialbes
    ###########################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    $clusterSizeInNodes = 1
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ###########################################
    # Connect to Azure
    ###########################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ###########################################
    # Create the resource group
    ###########################################
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    ###########################################
    # Preapre default storage account and container
    ###########################################
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Type Standard_GRS `
        -Location $location

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $hdinsightClusterName -Context $defaultStorageContext 

    ###########################################
    # Create the cluster
    ###########################################

    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType Hadoop `
        -OSType Windows `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $hdinsightClusterName 

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName 

## <a name="create-clusters-using-arm-template"></a>建立叢集使用手錶範本

您可以使用 PowerShell 的 Azure 部署建立 HDInsight 叢集手錶範本。  請參閱[撥打使用 PowerShell 的 Azure 的範本](hdinsight-hadoop-create-windows-clusters-arm-templates.md#call-templates-using-powershell)。

##<a name="customize-clusters"></a>自訂叢集

- 請參閱[自訂 HDInsight 叢集使用啟動安裝程式](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell)。
- 請參閱[自訂 Windows 型 HDInsight 叢集使用指令碼動作](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell)。


##<a name="next-steps"></a>後續步驟
本文中，您已經學會多種方式可以建立 HDInsight 叢集。 若要深入瞭解，請參閱下列文章︰

* [Azure HDInsight 快速入門](hdinsight-hadoop-linux-tutorial-get-started.md)-瞭解如何開始使用您的 HDInsight 叢集
* [送出 Hadoop 工作以程式設計方式](hdinsight-submit-hadoop-jobs-programmatically.md)-瞭解程式設計的方式將工作提交給 HDInsight
* [使用 PowerShell HDInsight 中的管理 Hadoop 叢集](hdinsight-administer-use-powershell.md)-瞭解如何使用使用 PowerShell 的 Azure HDInsight
* [Azure HDInsight SDK 文件] [ hdinsight-sdk-documentation] -探索 HDInsight SDK




[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/library/mt146778(v=sql.120).aspx
