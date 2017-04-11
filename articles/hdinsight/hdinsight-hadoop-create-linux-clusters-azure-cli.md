<properties
    pageTitle="使用跨平台 Azure CLI HDInsight 中 Linux 上建立 Hadoop、 HBase 或大量叢集 |Microsoft Azure"
    description="瞭解如何建立使用跨平台 Azure CLI、 Azure 資源管理員範本和 Azure REST API Linux 型 HDInsight 叢集。 您可以指定叢集類型 （Hadoop、 HBase 或大量，），或使用指令碼來安裝自訂元件。"
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
    ms.date="09/20/2016"
    ms.author="larryfr"/>

#<a name="create-linux-based-clusters-in-hdinsight-using-the-azure-cli"></a>建立 Linux 型叢集中使用 CLI Azure HDInsight

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Azure CLI 可跨平台的命令列公用程式可讓您管理 Azure 服務。 它可以用於，以及 Azure 資源管理範本，建立 HDInsight 叢集，以及相關聯的儲存帳戶及其他服務。

Azure 資源管理範本是以 JSON 文件描述__資源] 群組__中的所有資源 （例如 HDInsight。)此範本為基礎的方法可讓您定義您需要的 HDInsight 一個範本中的所有資源。 也可讓您透過__部署__，將變更套用至整個群組整個管理] 群組中的變更。

這份文件中的步驟逐步執行的程序建立新的 HDInsight 叢集使用 Azure CLI 和範本。

> [AZURE.IMPORTANT] 這份文件中的步驟 HDInsight 叢集使用預設的工作者節點數目 (4)。 如果您打算在 32 個以上的工作者節點 （在叢集建立期間或縮放叢集），您就必須選取至少 8 個核心與 14 GB ram 的標頭節點大小。
>
> 如需有關節點大小和相關聯的成本的詳細資訊，請參閱[HDInsight 價格](https://azure.microsoft.com/pricing/details/hdinsight/)。

##<a name="prerequisites"></a>必要條件

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- __Azure CLI__。 Azure CLI 版本 0.10.1 的最後一個測試此文件中的步驟。

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 


### <a name="access-control-requirements"></a>存取控制需求

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="log-in-to-your-azure-subscription"></a>登入您 Azure 的訂閱

請依照在[連線至 Azure 訂閱從 Azure 命令列介面 (Azure CLI)](../xplat-cli-connect.md)記錄的步驟，並連線到您使用的__登入__方法的訂閱。

##<a name="create-a-cluster"></a>建立叢集

安裝及設定 Azure CLI 之後，應該命令提示字元、 命令介面或終端機工作階段執行下列步驟。

1. 使用下列命令來驗證您 Azure 訂閱︰

        azure login

    系統會提示您提供您的名稱和密碼。 如果您有多個 Azure 訂閱，請使用`azure account set <subscriptionname>`設定 Azure CLI 命令使用訂閱。

3. 切換至 Azure 資源管理員模式使用下列命令︰

        azure config mode arm

4. 建立資源群組。 此資源群組會包含 HDInsight 叢集與儲存的帳戶。

        azure group create groupname location
        
    * __群組名稱__取代為] 群組中的唯一名稱。 
    * 取代您想要建立群組的地理區域中的__位置__。 
    
        有效的位置的清單，請使用`azure location list`命令，，，然後使用 [__名稱__] 欄的位置。

5. 建立儲存的帳戶。 此儲存帳戶會作為 HDInsight 叢集的預設儲存空間。

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename
        
     * 在上一個步驟所建立的群組名稱取代__群組名稱__。
     * 取代前一個步驟所用的同一個位置中的__位置__。 
     * 取代__storagename__儲存帳戶的唯一名稱。
     
     > [AZURE.NOTE] 如需有關使用這個命令中的參數的詳細資訊，請使用`azure storage account create -h`檢視此命令的說明。

5. 擷取用來存取儲存帳戶的金鑰。

        azure storage account keys list -g groupname storagename
        
    * 資源群組名稱取代__群組名稱__。
    * 取代__storagename__儲存體帳戶名稱。
    
    會傳回的資料，在要儲存____key1__索引鍵值__。

6. 建立 HDInsight 叢集。

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * 資源群組名稱取代__群組名稱__。

    * __Hadoop__換成您想要建立叢集類型。 例如， `Hadoop`， `HBase`，`Storm`或`Spark`。

        > [AZURE.IMPORTANT] HDInsight 叢集了各種不同的類型，對應到工作負載或叢集調整為技術。 沒有建立，結合多個類型，例如大量和上一個叢集 HBase 叢集支援的方法。 

    * 取代相同的位置使用前一個步驟中的__位置__。

    * 取代__storagename__儲存體帳戶名稱。

    * 取代__storagekey__與上一個步驟中取得索引鍵。 

    * 針對`--defaultStorageContainer`參數、 叢集使用相同的名稱，為您的使用。

    * __系統管理__] 及 [ __httppassword__取代的名稱與您想要透過 HTTPS 存取叢集時使用的密碼。

    * 使用使用者名稱和密碼，您想要使用時存取使用 SSH 叢集取代__sshuser__及__sshuserpassword__

    可能需要幾分鐘，完成叢集建立程序。 通常約 15。

##<a name="next-steps"></a>後續步驟

現在您已成功建立使用 Azure CLI HDInsight 叢集，使用下列若要瞭解如何使用叢集︰

###<a name="hadoop-clusters"></a>Hadoop 叢集

* [使用 HDInsight 的登錄區](hdinsight-use-hive.md)
* [使用 HDInsight 的豬](hdinsight-use-pig.md)
* [使用 HDInsight MapReduce](hdinsight-use-mapreduce.md)

###<a name="hbase-clusters"></a>HBase 叢集

* [在 HDInsight HBase 快速入門](hdinsight-hbase-tutorial-get-started-linux.md)
* [Java 為開發應用程式上 HDInsight HBase](hdinsight-hbase-build-java-maven-linux.md)

###<a name="storm-clusters"></a>大量叢集

* [開發上 HDInsight 大量 Java 拓撲](hdinsight-storm-develop-java-topology.md)
* [使用大量上 HDInsight Python 元件](hdinsight-storm-develop-python-topology.md)
* [部署，並監控與上 HDInsight 大量的拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)
