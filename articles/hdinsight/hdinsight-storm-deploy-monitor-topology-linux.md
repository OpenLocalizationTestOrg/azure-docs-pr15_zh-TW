<properties
   pageTitle="部署及管理 Apache 大量拓撲上 Linux 型 HDInsight |Microsoft Azure"
   description="瞭解如何部署、 監控和管理 Apache 大量拓撲 Linux 型 HDInsight 上使用大量儀表板。 使用 Hadoop tools for Visual Studio。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/07/2016"
   ms.author="larryfr"/>

# <a name="deploy-and-manage-apache-storm-topologies-on-linux-based-hdinsight"></a>部署及管理上 Linux 型 HDInsight Apache 大量拓撲

在此文件，瞭解管理及監視大量拓撲上 Linux 為基礎的大量 HDInsight 叢集上執行基本的概念。

> [AZURE.IMPORTANT] 本文中的步驟需要 Linux 為基礎的大量 HDInsight 叢集上。 部署和監視拓撲 Windows 型 HDInsight 上的資訊，請參閱[部署及管理 Apache 大量拓撲在 Windows 型 HDInsight](hdinsight-storm-deploy-monitor-topology.md)

## <a name="prerequisites"></a>必要條件

* **HDInsight 叢集上 Linux 為基礎的大量**︰ 請參閱[快速入門上 HDInsight Apache 大量](hdinsight-apache-storm-tutorial-get-started-linux.md)建立叢集的步驟

* **熟悉 SSH 和 SCP**︰ 如需有關使用 SSH 和 SCP HDInsight 的詳細資訊，請參閱下列內容︰

    * **Linux、 Unix 或 OS X 的用戶端**︰ 請參閱[使用 SSH Linux 為基礎的 Hadoop HDInsight Linux、 OS X 或 Unix 上使用](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Windows 用戶端**︰ 請參閱[使用 SSH Linux 為基礎的 Hadoop HDInsight 從 Windows 上使用](hdinsight-hadoop-linux-use-ssh-windows.md)

* **SCP 用戶端**︰ 這提供所有 Linux Unix，與 OS X 系統。 適用於 Windows 用戶端，我們建議 PSCP，從[PuTTY 下載頁面](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

## <a name="start-a-storm-topology"></a>啟動大量拓撲

### <a name="using-ssh-and-the-storm-command"></a>使用 SSH 和大量] 命令

1. 使用 SSH HDInsight 叢集連線。 取代**使用者名稱**您 SSH 登入名稱。 **CLUSTERNAME**取代 HDInsight 叢集名稱︰

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    如需有關使用 SSH 連線到您的 HDInsight 叢集的詳細資訊，請參閱下列文件︰
    
    * **Linux、 Unix 或 OS X 的用戶端**︰ 請參閱[使用 SSH Linux 為基礎的 Hadoop HDInsight Linux、 OS X 或 Unix 上使用](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * **Windows 用戶端**︰ 請參閱[使用 SSH Linux 為基礎的 Hadoop HDInsight 從 Windows 上使用](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 您可以使用下列命令來啟動範例拓撲︰

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology WordCount

    這會啟動範例 WordCount 拓撲叢集上。 它會隨機產生句子，並計算的每個單字的句子中的項目。

    > [AZURE.NOTE] 送出到叢集拓撲時，您必須先複製 jar 檔案，再使用包含叢集`storm`] 命令。 這可以透過`scp`命令從檔案所在的用戶端。 例如，`scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > WordCount 範例中，與其他大量入門範例，已包含在叢集上`/usr/hdp/current/storm-client/contrib/storm-starter/`。

### <a name="programmatically"></a>以程式設計方式

您可以與 Nimbus 服務裝載於叢集通訊以程式設計方式部署上 HDInsight 大量的拓撲。 [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology)提供的範例將示範如何部署，並啟動拓撲 Nimbus 服務的 Java 應用程式。

## <a name="monitor-and-manage-using-the-storm-command"></a>監控和管理使用大量] 命令

`storm`公用程式可讓您可以使用從命令列執行拓撲。 以下是經常使用的命令清單。 使用`storm -h`命令的完整清單。

### <a name="list-topologies"></a>清單拓撲

使用下列命令以列出所有執行拓撲︰

    storm list
    
這會傳回類似下列資訊︰

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>停用，然後重新啟動

停用拓撲暫停將其刪除或重新啟動它。 使用下列命令以停用，然後重新啟動︰

    storm Deactivate TOPOLOGYNAME
    
    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>刪除的執行拓撲

大量拓撲啟動後，會繼續執行，直到停止。 若要停止拓撲，請使用下列命令︰

    storm stop TOPOLOGYNAME

### <a name="rebalance"></a>重新平衡

平衡拓撲讓修訂的拓撲平行系統。 例如，如果您已調整大小的叢集，若要新增更多的筆記，平衡可讓執行拓撲，讓使用新的節點。

> [AZURE.WARNING] 第一次重新拓撲停用拓撲，再重新工作者平均分配叢集，然後最後傳回拓撲平衡發生之前的狀態。 因此，如果拓撲是作用中時，它會變成作用中一次。 如果已停用，它仍會維持停用。

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-using-the-storm-ui"></a>監控和管理使用大量使用者介面

大量使用者介面提供 web 介面使用執行拓撲，並包含在 HDInsight 叢集。 若要檢視大量使用者介面，使用網頁瀏覽器開啟__https://CLUSTERNAME.azurehdinsight.net/stormui__，其中__CLUSTERNAME__是叢集的名稱。

> [AZURE.NOTE] 如果系統要求您提供使用者名稱和密碼，請輸入叢集系統管理員 （系統） 和密碼，您會使用何時建立叢集。


### <a name="main-page"></a>主頁面

大量使用者介面的主頁面會提供下列資訊︰

* **叢集摘要**︰ 有關大量叢集的基本資訊。

* **拓撲摘要**︰ 執行拓撲的清單。 使用此區段中的連結，若要檢視特定拓撲的詳細資訊。

* **主管摘要**︰ 大量主管的相關資訊。

* **Nimbus 設定**︰ 叢集 Nimbus 設定。

### <a name="topology-summary"></a>拓撲摘要

透過 [**拓撲摘要**] 區段中的連結會顯示拓撲的下列資訊︰

* **拓撲摘要**︰ 拓撲的基本資訊。

* **拓撲動作**︰ 您可以執行的拓撲管理動作。

  * **啟動**︰ 停用的拓撲履歷表處理。

  * **停用**︰ 暫停的執行拓撲。

  * **重新平衡**︰ 調整拓撲的平行。 您已變更的叢集的節點數目後，您應該重新平衡執行拓撲。 如此一來調整以增加或減少中的節點數目叢集賠償平行拓撲。

    如需詳細資訊，請參閱<a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">瞭解大量拓撲的平行</a>。

  * **刪除**︰ 結束大量拓撲後指定逾時。

* **拓撲 stats**︰ 以及拓撲相關統計資料。 若要在頁面上設定的時間範圍的其他項目中使用 [**視窗**] 欄中的連結。

* **Spouts**︰ 使用拓撲 spouts。 使用此區段中的連結，若要檢視特定 spouts 的詳細資訊。

* **螺栓**︰ 使用拓撲螺栓。 使用此區段中的連結，若要檢視特定螺栓的詳細資訊。

* **拓撲設定**︰ 選取拓撲的設定。

### <a name="spout-and-bolt-summary"></a>Spout 與螺栓摘要

從**Spouts**或**螺栓**區段選取 spout 會顯示選取的項目的下列資訊︰

* **元件摘要**︰ spout 或螺栓的基本資訊。

* **Spout/螺栓 stats**: spout 或螺栓相關統計資料。 若要在頁面上設定的時間範圍的其他項目中使用 [**視窗**] 欄中的連結。

* **輸入統計資料**（僅限閃電）︰ 輸入的資料流螺栓所使用的相關資訊。

* **輸出 stats**: spout 發出此資料流時的相關資訊，或閃電。

* **執行程式**︰ spout 或螺栓的執行個體的相關資訊。 選取特定的執行程式檢視針對此執行個體所產生的診斷資訊的記錄檔的**連接埠**項目。

* **錯誤**︰ 此任何錯誤資訊 spout 或閃電。

## <a name="rest-api"></a>REST API

大量使用者介面是內建上方 REST API，，所以您可以執行類似管理和使用 REST API 來監控功能。 您可以使用 REST API 來建立自訂的工具，管理及監視大量拓撲。

如需詳細資訊，請參閱[大量 UI REST API](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html)。 下列資訊是 REST API 使用 Apache 大量 HDInsight 上的特定項目。

> [AZURE.IMPORTANT] 大量 REST API 無法透過網際網路、 可公開使用，也必須使用 HDInsight 叢集主節點 SSH 通道存取。 建立和使用 SSH 通道資訊，請參閱[使用 SSH 通道存取 Ambari 網頁 UI、 ResourceManager、 JobHistory、 NameNode、 Oozie 及其他網站使用者介面的](hdinsight-linux-ambari-ssh-tunnel.md)。

### <a name="base-uri"></a>基底 URI

在主節點基底 URI REST api Linux 型 HDInsight 叢集上有**https://HEADNODEFQDN:8744/api/v1/**;不過，主節點的網域名稱時叢集建立產生，而且不會靜態。

您可以找到叢集標頭節點的完整的網域名稱 (FQDN)，以數種不同的方式︰

* __從 SSH 工作階段__︰ 使用命令`headnode -f`從叢集 SSH 工作階段。

* __從 Ambari Web__︰ 選取 [__服務__] 頁面的頂端，然後選取 [__大量__。 從 [__摘要__] 索引標籤中，選取 [__大量 UI 伺服器__]。 頁面頂端的將會執行大量使用者介面及 REST API 節點的 FQDN。

* __從 Ambari REST API__︰ 使用命令`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"`擷取執行大量 UI 和 REST API 節點的相關資訊。 取代叢集的管理員密碼的__密碼__。 取代__CLUSTERNAME__叢集名稱。 在回應中，「 host_name 」 的項目包含節點的 FQDN。

### <a name="authentication"></a>驗證

REST API 要求必須使用**基本驗證**]，讓您使用的 HDInsight 叢集系統管理員名稱和密碼。

> [AZURE.NOTE] 因為使用純文字傳送基本驗證時，您應該**一律**使用保護與叢集通訊的 HTTPS。

### <a name="return-values"></a>傳回值

傳回從 REST API 資訊僅能叢集或叢集為相同的 Azure 虛擬網路上的虛擬機器中可用。 例如，傳回動物園管理員伺服器的完整的網域名稱 (FQDN) 不會從網際網路存取。

## <a name="next-steps"></a>後續步驟

現在，您學到如何部署並監控拓撲使用大量儀表板，瞭解如何[使用 Maven 開發 java 拓撲](hdinsight-storm-develop-java-topology.md)。

如需更多範例拓撲清單，請參閱[在 HDInsight 大量的範例拓撲](hdinsight-storm-example-topology.md)。
