<properties
    pageTitle="Hadoop 的可用性叢集中 HDInsight |Microsoft Azure"
    description="HDInsight 部署高可用及可靠叢集與其他的標頭節點。"
    services="hdinsight"
    tags="azure-portal"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="jgao"/>


#<a name="availability-and-reliability-of-windows-based-hadoop-clusters-in-hdinsight"></a>可用性及 HDInsight 中的 Windows 型 Hadoop 叢集可靠性


>[AZURE.NOTE] 使用此文件中的步驟是針對 Windows 型 HDInsight 叢集。 如果您使用的 Linux 叢集，請參閱[可用性及可靠性中 HDInsight 的 Linux 型 Hadoop 叢集](hdinsight-high-availability-linux.md)Linux 特定資訊。

HDInsight 允許部署叢集類型，不同的資料分析工作負載的各種不同的客戶。 今天提供叢集類型是查詢及分析負載、 HBase 叢集的 NoSQL 負載和大量叢集即時事件處理工作負載的 Hadoop 叢集。 在 [指定的叢集類型，有不同的節點的不同角色。 例如︰



- Hadoop 叢集 HDInsight 的部署使用兩種角色︰
    - 不對節點 （2 節點）
    - 資料節點 （至少 1 節點）

- HDInsight HBase 叢集部署與三個角色︰
    - 不對伺服器 （2 節點）
    - 地區伺服器 （至少 1 節點）
    - 母片/動物園管理員節點 （3 節點）

- HDInsight 大量叢集部署與三個角色︰
    - Nimbus 節點 （2 節點）
    - 主管伺服器 （至少 1 節點）
    - 動物園管理員節點 （3 節點）

Hadoop 叢集的標準實作通常會有單一主節點。 HDInsight 移除失敗的次要標頭節點 /head 搭配這一點來增加可用性和可靠性管理負載所需的服務的伺服器/Nimbus 節點。 這些標頭的節點不對伺服器/Nimbus 節點設計用來管理的工作者節點失敗，但在主節點上執行的主版服務的任何中斷會造成叢集停止運作。


[動物園管理員](http://zookeeper.apache.org/ )節點 (ZKs) 已新增的標頭節點的前置字元選舉所使用，並確保該工作者節點和閘道 (GWs) 知道作用中的主節點 (不對 Node0) 變成非作用中時，容錯移轉至第二個標頭節點 (不對 Node1)。

![在 HDInsight Hadoop 實作可靠的標頭節點的圖表。](./media/hdinsight-high-availability/hadoop.high.availability.architecture.diagram.png)




## <a name="check-active-head-node-service-status"></a>核取 [作用中的主節點服務狀態
若要判斷哪一個主節點是作用中，並查看該主節點上執行的服務的狀態，您必須連線至 Hadoop 叢集使用遠端桌面通訊協定 (RDP)。 如需 RDP 指示，請參閱[管理 Hadoop 叢集 HDInsight 使用 Azure 入口網站中](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp)。 一旦您已連線至叢集，位於 [桌面] 來取得狀態的相關的標頭的節點 Namenode **Hadoop 服務可用**圖示上按兩下，Jobtracker、 Templeton、 Oozieservice、 Metastore 和 Hiveserver2 服務會執行，或 HDI 3.0、 Namenode、 資源管理員、 歷程記錄伺服器、 Templeton、 Oozieservice、 Metastore 和 Hiveserver2 服務。

![](./media/hdinsight-high-availability/Hadoop.Service.Availability.Status.png)

螢幕擷取畫面，在作用中的主節點是*headnode0*。

## <a name="access-log-files-on-the-secondary-head-node"></a>在第二個存取記錄檔

若要存取工作記錄在次要讓它具有會變成作用中的主節點，瀏覽 JobTracker UI 仍能運作與主要作用中的節點。 若要存取 JobTracker，您必須連線到 Hadoop 叢集使用 RDP 前一節所述。 遠端到叢集之後，按兩下桌面上**Hadoop 名稱節點狀態**圖示上，，然後按一下 [另**NameNode 記錄檔**以取得在第二個記錄的目錄。

![](./media/hdinsight-high-availability/Hadoop.Head.Node.Log.Files.png)


## <a name="configure-head-node-size"></a>設定主節點大小
標頭節點會依預設配置為大虛擬機器 (Vm)。 此大小是適合叢集上執行的大部分 Hadoop 工作的管理。 不過也有可能會要求超大型 Vm 標頭節點的案例。 例如，當有管理大量的小型 Oozie 工作。

您可以使用 PowerShell 的 Azure cmdlet 或 HDInsight SDK 設定超大型 Vm。

建立和使用 PowerShell 的 Azure 佈建叢集的[管理 HDInsight 使用 PowerShell](hdinsight-administer-use-powershell.md)說明。 大型的標頭節點的設定，需要的`-HeadNodeVMSize ExtraLarge`參數`New-AzureRmHDInsightcluster`此程式碼中使用的指令程式。

    # Create a new HDInsight cluster in Azure PowerShell
    # Configured with an ExtraLarge head-node VM
    New-AzureRmHDInsightCluster `
                -ResourceGroupName $resourceGroupName `
                -ClusterName $clusterName ` 
                -Location $location `
                -HeadNodeVMSize ExtraLarge `
                -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $storageAccountKey `
                -DefaultStorageContainerName $containerName  `
                -ClusterSizeInNodes $clusterNodes

本文 sdk，您可以是類似。 建立和使用 SDK 佈建叢集的說明[使用 HDInsight.NET SDK](hdinsight-provision-clusters.md#sdk)資訊。 大型的標頭節點的設定，需要的`HeadNodeSize = NodeVMSize.ExtraLarge`參數`ClusterCreateParameters()`此程式碼中使用的方法。

    # Create a new HDInsight cluster with the HDInsight SDK
    # Configured with an ExtraLarge head-node VM
    ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
    {
        Name = clustername,
        Location = location,
        HeadNodeSize = NodeVMSize.ExtraLarge,
        DefaultStorageAccountName = storageaccountname,
        DefaultStorageAccountKey = storageaccountkey,
        DefaultStorageContainer = containername,
        UserName = username,
        Password = password,
        ClusterSizeInNodes = clustersize
    };


## <a name="next-steps"></a>後續步驟

- [Apache 動物園管理員](http://zookeeper.apache.org/ )
- [連線至 HDInsight 叢集使用 RDP](hdinsight-administer-use-management-portal.md#rdp)
- [使用 HDInsight.NET SDK](hdinsight-provision-clusters.md#sdk)
