<properties
    pageTitle="管理中使用 PowerShell HDInsight Hadoop 叢集 |Microsoft Azure"
    description="瞭解如何使用 PowerShell 的 Azure HDInsight 中 Hadoop 叢集執行系統管理工作。"
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    tags="azure-portal"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>使用 PowerShell 的 Azure 管理 Hadoop 叢集 HDInsight 中

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell 是功能強大的指令碼環境，您可以用來控制自動化的部署及管理您的工作量 Azure 中。 在本文中，您將學習如何使用本機 Azure PowerShell 主控台使用 Windows PowerShell 管理 Hadoop 叢集中 Azure HDInsight。 HDInsight PowerShell cmdlet 清單中，請參閱[HDInsight cmdlet 參考][hdinsight-powershell-reference]。



**必要條件**

這份文件之前，您必須具備下列項目︰

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

##<a name="install-azure-powershell"></a>安裝 Azure PowerShell

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

如果您已安裝版本 0.9 PowerShell 的 Azure x，您必須將它解除安裝再安裝較新的版本。

若要檢查已安裝的 PowerShell 的版本︰

    Get-Module *azure*
    
若要解除安裝較舊的版本，請執行 [控制台] 中的 [程式和功能。 


##<a name="create-clusters"></a>建立叢集

請參閱[使用 PowerShell 的 Azure HDInsight 中的 [建立 Linux 型叢集](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

##<a name="list-clusters"></a>清單叢集
若要在目前的訂閱中的所有叢集] 都清單中使用下列命令︰

    Get-AzureRmHDInsightCluster

##<a name="show-cluster"></a>顯示叢集

您可以使用下列命令，顯示目前的訂閱中的特定叢集的詳細資料︰

    Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>

##<a name="delete-clusters"></a>刪除叢集

若要刪除叢集使用下列命令︰

    Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>

您也可以刪除叢集移除包含叢集的資源群組。 請注意，這將會刪除包括預設儲存帳戶] 群組中的所有資源。

    Remove-AzureRmResourceGroup -Name <Resource Group Name>
            
##<a name="scale-clusters"></a>縮放比例叢集
叢集縮放功能可讓您變更正在執行中 Azure HDInsight，而不需要重新建立 [叢集叢集使用工作者節點數目。

>[AZURE.NOTE] 只使用 HDInsight 版本 3.1.3 叢集或更高支援。 如果您不確定您的叢集版本的您可以核取 [屬性] 頁面。  請參閱[叢集清單] 和 [顯示](hdinsight-administer-use-portal-linux.md#list-and-show-clusters)。

變更每種類型的支援 HDInsight 叢集資料節點數目的影響︰

- Hadoop

    您可以順暢完美地增加背景工作中的節點數目 Hadoop 叢集執行而不會影響任何擱置] 或 [執行的工作。 在進行作業時也能提交新的工作。 縮放比例作業失敗適當地處理以便叢集永遠左功能的狀態。

    當 Hadoop 叢集縮小來減少資料節點數目時，部分叢集服務會重新啟動。 這會使所有執行，並且擱置的工作，在 [縮放比例] 作業完成失敗。 您可以不過，重新送出工作一旦完成作業。

- HBase

    流暢地，您可以新增或移除節點] 在執行中時，HBase 叢集。 完成 [縮放比例] 作業的幾分鐘內自動對稱地區的伺服器。 不過，您可以登入的叢集 headnode，並從 [命令提示字元視窗中執行下列命令以手動平衡地區伺服器︰

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

- 大量

    流暢地，您可以新增或移除資料節點] 在執行中時，大量叢集。 但縮放作業成功完成之後，您將需要重新平衡拓撲。

    平衡可完成兩種方法︰

    * 大量網頁 UI
    * 命令列介面 (CLI) 工具

    請參閱[Apache 大量文件](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)更多。

    大量網頁 UI HDInsight 叢集上有︰

    ![HDInsight 大量比例 rebalance](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

    以下是如何使用 [CLI 命令來重新平衡大量拓撲範例︰

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors

        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

若要變更使用 PowerShell 的 Azure Hadoop 叢集大小，請執行下列命令從用戶端電腦︰

    Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
    

##<a name="grantrevoke-access"></a>授與/撤銷存取

HDInsight 叢集有下列 HTTP web 服務 （所有這些服務需要 RESTful 的端點）︰

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


根據預設，這些服務會授與存取。 您可以撤銷/授與存取權。 撤銷︰

    Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>

若要授與︰

    $clusterName = "<HDInsight Cluster Name>"

    # Credential option 1
    $hadoopUserName = "admin"
    $hadoopUserPassword = "<Enter the Password>"
    $hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

    # Credential option 2
    #$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"
    
    Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential

>[AZURE.NOTE] 授與/撤銷存取，您會重設叢集使用者名稱和密碼。

這也可以透過入口網站完成。 請參閱[使用 Azure 入口網站的管理 HDInsight][hdinsight-admin-portal]。

##<a name="update-http-user-credentials"></a>更新 HTTP 使用者認證

則為[HTTP/撤銷授與存取權](#grant/revoke-access)的相同程序。如果叢集已獲授予他人 HTTP 存取，您必須先撤銷。  然後授與存取權，以新的 HTTP 使用者認證。


##<a name="find-the-default-storage-account"></a>尋找預設儲存帳戶

下列 Powershell 指令碼示範如何取得預設儲存空間客戶名稱] 和 [叢集的預設儲存帳戶金鑰。

    $clusterName = "<HDInsight Cluster Name>"
    
    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup
    $defaultStorageAccountName = ($cluster.DefaultStorageAccount).Replace(".blob.core.windows.net", "")
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 

##<a name="find-the-resource-group"></a>尋找 [資源] 群組

在資源管理員模式中，每個 HDInsight 叢集屬於 Azure 資源群組。  若要尋找 [資源] 群組︰

    $clusterName = "<HDInsight Cluster Name>"
    
    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup


##<a name="submit-jobs"></a>送出工作

**提交 MapReduce 工作**

請參閱[在 Windows 型 HDInsight 執行 Hadoop MapReduce 範例](hdinsight-run-samples.md)。

**提交登錄區工作** 

請參閱[使用 PowerShell 執行登錄區查詢](hdinsight-hadoop-use-hive-powershell.md)。

**送出的豬工作**

請參閱[使用 PowerShell 執行豬工作](hdinsight-hadoop-use-pig-powershell.md)。

**提交 Sqoop 工作**

請參閱[使用 Sqoop 與 HDInsight](hdinsight-use-sqoop.md)。

**提交 Oozie 工作**

請參閱[使用 Oozie 與 Hadoop 來定義並執行 HDInsight 中的工作流程](hdinsight-use-oozie.md)。

##<a name="upload-data-to-azure-blob-storage"></a>將資料上傳至 Azure Blob 儲存體
請參閱[上傳至 HDInsight 的資料][hdinsight-upload-data]。


## <a name="see-also"></a>另請參閱
* [HDInsight cmdlet 參考文件][hdinsight-powershell-reference]
* [藉由使用 [Azure 入口網站管理 HDInsight][hdinsight-admin-portal]
* [管理 HDInsight 使用命令列介面][hdinsight-admin-cli]
* [建立 HDInsight 叢集][hdinsight-provision]
* [上傳至 HDInsight 的資料][hdinsight-upload-data]
* [以程式設計方式提交 Hadoop 工作][hdinsight-submit-jobs]
* [開始使用 Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: powershell-install-configure.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png
