<properties
    pageTitle="可用性功能 Linux HDInsight (Hadoop) |Microsoft Azure"
    description="瞭解如何 Linux 型 HDInsight 叢集改善可靠性和可用性使用其他的標頭節點。 您將學習如何影響，以及如何登錄區，Ambari 等的 Hadoop 服務個別連線到使用 SSH 每個主節點。"
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="Blackmist"
    documentationCenter=""
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="larryfr"/>

#<a name="availability-and-reliability-of-hadoop-clusters-in-hdinsight"></a>可用性及 Hadoop 叢集中 HDInsight 可靠性

Hadoop 達成高可用性及可靠性之間集中節點散發份 services 和資料。 但是 Hadoop 的標準散佈通常可以只有一個標頭節點。 任何單一主節點會中斷，可能會導致叢集停止運作。

若要解決此潛在問題，請在 Azure 的 Linux 型 HDInsight 叢集會提供來增加可用性和可靠性 Hadoop services 及執行工作的兩個標頭節點。

> [AZURE.NOTE] 使用此文件中的步驟會有特定 Linux 型 HDInsight 叢集。 如果您使用的 windows 叢集，請參閱[可用性及可靠性 HDInsight 中的 Windows 型 Hadoop 叢集](hdinsight-high-availability.md)Windows 特定資訊。

##<a name="understanding-the-nodes"></a>了解節點

使用 Azure 虛擬機器實作 HDInsight 叢集節點。 節點失敗，離線，而建立新的節點取代失敗的節點。 節點離線時，直到新節點上線，則會使用相同類型的另一個節點。

> [AZURE.NOTE] 如果節點分析資料，它失敗時，在工作進度會遺失。 失敗節點已處理的工作會重新提交到另一個節點。

下列各節討論 HDInsight 搭配使用的個別節點類型。 並非所有節點類型適都用於叢集類型。 例如，Hadoop 叢集類型不會任何 Nimbus 節點。 如需有關使用 HDInsight 叢集類型的節點的詳細資訊，請參閱[建立 Linux 型 Hadoop 叢集 HDInsight 中](hdinsight-hadoop-provision-linux-clusters.md#cluster-types)的 [叢集類型] 區段。

###<a name="head-nodes"></a>不對節點

Hadoop 某些實作有單一的標頭節點裝載的服務和順暢地管理的工作者節點失敗的元件。 但任何中斷的標頭節點上執行的主版服務會造成叢集停止運作。

HDInsight 叢集提供次要的主節點，可讓主版服務和元件，以繼續在第二個主要失敗節點上執行。

> [AZURE.IMPORTANT] 兩個標頭的節點同時在 [作用中，執行叢集內。 某些服務，例如 HDFS 或 YARN，只是在任何指定的時間 （和 '備用' 在其他） 「 啟動 」 在一個主節點。 同時，HiveServer2 或登錄區 MetaStore 等其他服務是作用中的兩個標頭的節點。

不對節點 （及其他節點 HDInsight 中,） 有一個數字的值做為節點的主機名稱的一部分。 例如，`hn0-CLUSTERNAME`或`hn4-CLUSTERNAME`。 

> [AZURE.IMPORTANT] 不會與節點是主要或次要; 關聯的數值數值才有提供每個節點的唯一名稱。

###<a name="nimbus-nodes"></a>Nimbus 節點

大量叢集 Nimbus 節點會提供類似的功能，以 Hadoop JobTracker 發佈並監控處理工作者節點。 HDInsight 提供大量叢集類型 2 Nimbus 節點。

###<a name="zookeeper-nodes"></a>動物園管理員節點

[動物園管理員](http://zookeeper.apache.org/ )節點 (ZKs) 用於前置字元選舉母片上的服務標頭節點，並以確保服務、 資料 （工作） 節點及閘道器知道哪一個主節點的主機服務是在作用中。 根據預設，HDInsight 提供 3 動物園管理員節點。

###<a name="worker-nodes"></a>工作者節點

在工作送出到叢集時，工作者節點執行實際的資料分析。 如果工作者節點失敗，它所執行的工作會送出到另一個工作者節點。 根據預設，HDInsight 會建立 4 工作者節點。不過，您可以變更此號碼來叢集建立期間和叢集建立之後，符合您的需求。

###<a name="edge-node"></a>邊緣節點

邊緣節點主動參與叢集，內的資料分析，但改為使用開發人員或資料科學家使用 Hadoop 時。 邊緣節點放在相同 Azure 虛擬的網路中叢集，其他節點，並可直接存取所有其他節點。 因為它不涉及分析資料的叢集，可用於而不需要任何考慮的資源不重要的 Hadoop 服務或分析作業。

目前，HDInsight R 伺服器是唯一的叢集類型提供預設的邊緣節點。 R 伺服器上 HDInsight] 中，使用邊緣節點測試 R 在本機上送出以供分散式處理前節點的程式碼。

[建立在邊緣節點色調的 Linux 型 HDInsight 叢集](https://azure.microsoft.com/documentation/templates/hdinsight-linux-with-hue-on-edge-node/)是範例範本，可以用來建立含有邊緣的節點 Hadoop 叢集類型。


## <a name="accessing-the-nodes"></a>存取節點

透過網際網路叢集存取透過公用閘道，提供和限於連線到標頭節點 （如果 HDInsight 叢集上 R Server) 和邊緣節點。 存取服務上的標頭節點執行不受影響有多個標頭節點，為公用閘道器路由至裝載要求的服務標頭節點的要求。 例如，如果 Ambari 目前裝載在次要，閘道器會路由傳送傳入的邀請的 Ambari 到該節點。

存取時使用 SSH 叢集，透過連接埠 22 （SSH，預設值） 連線會連線至主要的主節點。連線到 23 的連接埠會連接至第二個標頭節點。 例如，`ssh username@mycluster-ssh.azurehdinsight.net`會連線到名稱為__mycluster__叢集主要主節點。

> [AZURE.NOTE] 這也適用於基礎 SSH，例如 SSH 檔案傳輸通訊協定 (SFTP) 通訊協定。

R Server 提供 HDInsight 叢集上邊緣節點直接亦可使用 SSH 透過連接埠 22。 例如，`ssh username@RServer.mycluster.ssh.azurehdinsight.net`會在名為__mycluster__HDInsight 叢集 R 伺服器將連線到邊緣節點。 

### <a name="internal-fully-qualified-domain-names-fqdn"></a>內部的完整的網域名稱 (FQDN)

HDInsight 叢集節點有內部的 IP 位址和 FQDN 僅可存取的 （例如 SSH 工作階段，主節點或叢集上執行的作業。）存取時使用的內部的 FQDN 或 IP 位址叢集上的服務，您應該使用 Ambari 驗證存取服務時要使用的 IP 或 FQDN。

Oozie 服務，例如，只能在一個主節點，並使用執行`oozie`SSH 工作階段] 命令需要服務的 URL。 這可以從擷取 Ambari 使用下列命令︰

    curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

這會傳回一個值類似下列，其中包含要使用的內部 URL`oozie`命令︰

    "oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

### <a name="accessing-other-node-types"></a>存取其他節點類型

您可以連線至不是直接存取網際網路上使用以下兩種方法的節點。

* __SSH__︰ 連線到使用 SSH 標頭節點後，您可以再 SSH 從使用標頭節點連線至其他叢集的節點。
* __SSH 通道__︰ 如果您需要存取 web 服務裝載於其中一個節點未公開至網際網路，您必須[使用 SSH 通道](hdinsight-linux-ambari-ssh-tunnel.md)。
* __Azure 虛擬網路__︰ HDInsight 叢集是 Azure 虛擬網路的一部分，如果在相同的虛擬網路上的任何資源可直接存取叢集內所有節點。

## <a name="how-to-check-on-a-service-status"></a>若要查看服務狀態的方式

Ambari Web UI 或 Ambari REST API 可用來檢查執行的標頭節點服務的狀態。

###<a name="ambari-web-ui"></a>Ambari 網頁 UI

Ambari Web UI 是在 https://CLUSTERNAME.azurehdinsight.net 可檢視。 **CLUSTERNAME**換成您叢集的名稱。 如果出現提示，輸入您的叢集 HTTP 使用者認證。 預設 HTTP 使用者名稱是**管理員**和密碼是建立叢集時，您所輸入的密碼。

當您到達 Ambari 頁面上時，已安裝的服務會列出頁面的左邊。

![已安裝的服務](./media/hdinsight-high-availability-linux/services.png)

有一系列的可能會出現，表示狀態某個服務旁邊的圖示。 使用頁面頂端的 [**通知**] 連結可檢視任何服務相關的警示。 您可以選取每一項服務，若要在其上檢視的詳細資訊。

[服務] 頁面上的狀態與每個服務設定提供資訊，它不提供的標頭節點執行服務的資訊。 若要檢視這項資訊，使用頁面頂端的**主辦城市**] 連結。 這會顯示叢集，包括標頭節點主機。

![主機清單](./media/hdinsight-high-availability-linux/hosts.png)

選取其中一個標頭節點的連結會顯示的服務與該節點上執行的元件。

![元件的狀態](./media/hdinsight-high-availability-linux/nodeservices.png)

###<a name="ambari-rest-api"></a>Ambari REST API

Ambari REST API 可透過網際網路、 同時公用閘道器處理路由要求目前主控 REST API 標頭節點。

若要檢查透過 Ambari REST API 服務的狀態，您可以使用下列命令︰

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* **密碼**取代 HTTP 使用者 （系統），帳戶密碼

* **CLUSTERNAME**取代叢集的名稱

* 若要檢查的狀態服務名稱取代**SERVICENAME**

例如，檢查在名為**mycluster**，使用的**密碼**，密碼叢集**HDFS**服務的狀態，您會使用下列︰

    curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

回應會類似下列動作︰

    {
      "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
      "ServiceInfo" : {
        "cluster_name" : "mycluster",
        "service_name" : "HDFS",
        "state" : "STARTED"
      }
    }

URL 會告訴我們的標頭節點，名為__hn0 CLUSTERNAME__目前執行服務。

我們正在執行服務，或**已啟動**，就是告訴狀態。

如果您不知道叢集上已安裝的服務，您可以使用下列動作來擷取清單︰

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

####<a name="service-components"></a>服務元件

服務可能會包含您想要檢查的狀態個別的元件。 例如，HDFS 包含 NameNode 元件。 若要檢視資訊元件，就會命令︰

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

如果您不知道哪些元件所提供的服務，您可以使用下列動作來擷取清單︰

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
    
## <a name="how-to-access-log-files-on-the-head-nodes"></a>如何存取標頭節點的記錄檔

###<a name="ssh"></a>SSH

連線到透過 SSH 標頭節點，而記錄檔找下**/var/log**。 例如， **/var/log/hadoop-yarn/yarn**包含 YARN 記錄。

每個標頭節點可以有唯一的記錄項目，所以您要檢查兩者的記錄。

###<a name="sftp"></a>SFTP

您也可以連線至主節點使用 SSH 檔案傳輸通訊協定或保護檔案傳輸通訊協定 (SFTP)，並直接下載記錄檔。

類似的 SSH 用戶端，當您連線到叢集必須提供 SSH 使用者的客戶名稱] 和 [叢集 SSH 地址。 例如， `sftp username@mycluster-ssh.azurehdinsight.net`。 您也必須提供出現提示時，該帳戶的密碼，或提供公用索引鍵使用`-i`參數。

連線之後，您會看到`sftp>`提示。 在此提示中，您可以變更目錄、 上傳及下載檔案。 例如，下列命令**/var/log/hadoop/hdfs**目錄變更的目錄，然後下載目錄中的所有檔案。

    cd /var/log/hadoop/hdfs
    get *

如需可用命令的清單，請輸入`help`在`sftp>`提示。

> [AZURE.NOTE] 此外，還有可讓您以視覺化方式呈現檔案系統時使用 SFTP 連接的圖形化介面。 例如， [MobaXTerm](http://mobaxterm.mobatek.net/)可讓您瀏覽檔案系統的介面類似 Windows 檔案總管]。


###<a name="ambari"></a>Ambari

> [AZURE.NOTE] 透過 Ambari 存取記錄檔需要 SSH 通道，，當個別服務的網站不在網際網路上公開公開。 使用 SSH 通道資訊，請參閱[使用 SSH 通道存取 Ambari 網頁 UI、 ResourceManager、 JobHistory、 NameNode、 Oozie 及其他網站使用者介面的](hdinsight-linux-ambari-ssh-tunnel.md)。

從 Ambari 網路使用者介面中，選取您想要檢視 (例如，YARN，) 記錄的服務，然後使用**快速連結**選取的主節點，若要檢視的記錄。

![若要檢視記錄檔中使用快速連結](./media/hdinsight-high-availability-linux/viewlogs.png)

## <a name="how-to-configure-the-node-size"></a>如何設定節點大小 ##

大小僅在叢集建立期間選取節點。 您可以找到 HDInsight，包括核心、 記憶體和本機存放在[HDInsight 定價頁面](https://azure.microsoft.com/pricing/details/hdinsight/)上的每個不同的 VM 大小的清單。

在建立新的叢集時，您可以指定節點的大小。 以下提供如何指定大小使用[Azure 入口網站]的資訊[preview-portal]， [PowerShell 的 Azure][azure-powershell]，和[Azure CLI][azure-cli]:

* **Azure 入口網站**︰ 時建立新的叢集，您可以設定的標頭，（價格層） 大小的選擇工作者和 （如果使用叢集類型] 中，） 動物園管理員節點叢集︰

    ![叢集建立精靈節點大小的選取範圍的圖像](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Azure CLI**︰ 使用時`azure hdinsight cluster create`命令，您可以設定的大小不對、 工作，以及動物園管理員節點使用`--headNodeSize`， `--workerNodeSize`，及`--zookeeperNodeSize`參數。

* **PowerShell 的 azure**︰ 使用時`New-AzureRmHDInsightCluster`指令程式，您可以藉由設定不對、 工作人員及動物園管理員節點的大小`-HeadNodeVMSize`， `-WorkerNodeSize`，及`-ZookeeperNodeSize`參數。

##<a name="next-steps"></a>後續步驟

在這份文件中您已經學會如何 Azure HDInsight 提供高可用性 Hadoop。 您可以使用下列，瞭解更多關於此文件中所提及的事項。

- [Ambari 其餘參照](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

- [安裝和設定 Azure CLI](../xplat-cli-install.md)

- [安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)

- [管理使用 Ambari HDInsight](hdinsight-hadoop-manage-ambari.md)

- [佈建 Linux 型 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: ../powershell-install-configure.md
[azure-cli]: ../xplat-cli-install.md
