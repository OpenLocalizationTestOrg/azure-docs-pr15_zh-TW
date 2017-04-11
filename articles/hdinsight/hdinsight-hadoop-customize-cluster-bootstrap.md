<properties
    pageTitle="自訂使用啟動安裝程式的 HDInsight 叢集 |Microsoft Azure"
    description="瞭解如何自訂 HDInsight 叢集使用啟動安裝程式。"
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="customize-hdinsight-clusters-using-bootstrap"></a>自訂 HDInsight 叢集使用啟動安裝程式

有時候，您要設定的設定檔，包括︰

- clusterIdentity.xml
- 核心 site.xml
- gateway.xml
- hbase env.xml
- hbase site.xml
- hdfs site.xml
- 登錄區 env.xml
- 登錄區 site.xml
- mapred 網站
- oozie site.xml
- oozie env.xml
- 大量 site.xml
- tez site.xml
- webhcat site.xml
- yarn site.xml

叢集無法保留因為重新所做的變更。 如需有關重新影像的詳細資訊，請參閱[角色執行個體重新啟動到期 OS 升級](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)。 若要保留的變更，透過叢集的存留時間，您可以使用 HDInsight 叢集自訂期間建立程序。 這是變更叢集的設定，以及跨這些 Azure reimage 重開機重新啟動事件保留的建議的方式。 變更這些設定會套用服務開始之前，因此不需要重新啟動服務。 

有 3 使用啟動安裝程式的方法︰

- 使用 PowerShell 的 Azure

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
    
- 使用.NET SDK
- 使用 Azure 資源管理員範本

安裝 HDInsight 叢集上的其他元件，建立時間期間的詳細資訊，請參閱︰

- [自訂 HDInsight 叢集使用指令碼動作 (Linux)](hdinsight-hadoop-customize-cluster-linux.md)
- [自訂 HDInsight 叢集使用指令碼動作 (Windows)](hdinsight-hadoop-customize-cluster.md)

## <a name="use-azure-powershell"></a>使用 PowerShell 的 Azure

下列 PowerShell 程式碼自訂登錄區設定︰

    # hive-site.xml configuration
    $hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
    
    $config = New-AzureRmHDInsightClusterConfig `
        | Set-AzureRmHDInsightDefaultStorage `
            -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -StorageAccountKey $defaultStorageAccountKey `
        | Add-AzureRmHDInsightConfigValues `
            -HiveSite $hiveConfigValues 
    
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $existingResourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType Hadoop `
        -OSType Windows `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -Config $config 

完成的工作 PowerShell 指令碼，請參閱[附錄 A](#hdinsight-hadoop-customize-cluster-bootstrap.md/appx-a:-powershell-sample)。

**若要確認變更︰**

1. [Azure 入口網站](https://portal.azure.com)登入。
2. 在左窗格中，按一下 [**瀏覽]**，然後再按一下**HDInsight 叢集**。
3. 按一下 [的叢集您剛建立使用 PowerShell 指令碼]。
4. 按一下 [**儀表板**從刀頂端開啟 Ambari UI]。
5. 按一下左側功能表上的 [**登錄區**]。
6. 按一下 [從**摘要** **HiveServer2** ]。
7. 按一下 [**設定**] 索引標籤。
8. 按一下左側功能表上的 [**登錄區**]。
9. 按一下 [**進階**] 索引標籤。
10. 向下捲動，然後再展開 [**進階的登錄區網站**。
11. 尋找**hive.metastore.client.socket.timeout** ] 區段中。

自訂其他設定檔的一些其他範例︰

    # hdfs-site.xml configuration
    $HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

    # core-site.xml configuration
    $CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

    # mapred-site.xml configuration
    $MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

    # oozie-site.xml configuration
    $OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

如需詳細資訊，請參閱標題為[自訂 HDInsight 叢集建立](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx)Azim Uddin 部落格。

## <a name="use-net-sdk"></a>使用.NET SDK

請參閱[使用.NET SDK 的 HDInsight 中的 [建立 Linux 型叢集](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap)。

## <a name="use-resource-manager-template"></a>使用資源管理員範本

您可以使用 [啟動安裝程式資源管理員範本中︰

    "configurations": {
        …
        "hive-site": {
            "hive.metastore.client.connect.retry.delay": "5",
            "hive.execution.engine": "mr",
            "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
        }
    }


![hdinsight hadoop 自訂叢集啟動安裝 azure 資源管理員範本](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)



## <a name="see-also"></a>另請參閱

- [建立 HDInsight Hadoop 叢集][hdinsight-provision-cluster]說明如何建立 HDInsight 叢集，您可以使用其他自訂選項。
- [開發 HDInsight 指令碼動作指令碼][hdinsight-write-script]
- [安裝並使用火花 HDInsight 叢集上][hdinsight-install-spark]
- [安裝並使用 R HDInsight 叢集上][hdinsight-install-r]
- [在 HDInsight Solr 叢集，安裝並使用](hdinsight-hadoop-solr-install.md)。
- [安裝並使用 Giraph 上 HDInsight 叢集](hdinsight-hadoop-giraph-install.md)。

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: powershell-install-configure.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "在叢集建立期間的階段"

## <a name="appx-a-powershell-sample"></a>Appx a: PowerShell 範例

這個 PowerShell 指令碼會建立 HDInsight 叢集，並自訂登錄區設定︰

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<ENTER AN ALIAS>" 
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"

    $sshUserName = "sshuser" #HDInsight ssh user name
    $sshPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"
    #endregion

    ####################################
    # Service names and varialbes
    ####################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an HDInsight cluster
    ####################################
    # Create dependent components
    ####################################
    Write-Host "Creating a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageContext #use the cluster name as the container name

    ####################################
    # Create a configuration object
    ####################################
    $hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
        
    $config = New-AzureRmHDInsightClusterConfig `
        | Set-AzureRmHDInsightDefaultStorage `
            -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -StorageAccountKey $defaultStorageAccountKey `
        | Add-AzureRmHDInsightConfigValues `
            -HiveSite $hiveConfigValues 

    ####################################
    # Create an HDInsight cluster
    ####################################
    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    $sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
    $sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes 1 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -SshCredential $sshCredential `
        -Config $config

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

    #endregion
