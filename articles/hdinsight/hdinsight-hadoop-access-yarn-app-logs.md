<properties
    pageTitle="Access Hadoop YARN 應用程式以程式設計方式記錄 |Microsoft Azure"
    description="Access 應用程式以程式設計方式記錄中 HDInsight Hadoop 叢集上。"
    services="hdinsight"
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
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Access YARN 應用程式登入 Windows 型 HDInsight

本主題說明如何存取 YARN （尚未另一個資源談判者） 應用程式中 Azure HDInsight Hadoop 叢集上完成的記錄

> [AZURE.NOTE] 這份文件中的資訊僅適用於 Windows 型 HDInsight 叢集。 如需存取 YARN 登入 Linux 型 HDInsight 叢集，請參閱[存取 YARN 應用程式登入 Linux 為基礎的 Hadoop HDInsight 上](hdinsight-hadoop-access-yarn-app-logs-linux.md)

### <a name="prerequisites"></a>必要條件

- Windows 型 HDInsight 叢集。  請參閱[建立 Windows 型 Hadoop 叢集 HDInsight 中](hdinsight-provision-clusters.md)。


## <a name="yarn-timeline-server"></a>YARN 時間表伺服器

<a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN 時間表伺服器</a>提供的一般資訊已完成的應用程式，以及透過兩種不同的介面架構特定應用程式的資訊。 特別是︰

* 儲存與一般的應用程式上的資訊 HDInsight 叢集擷取已啟用版本 3.1.1.374 或更新版本。
* 時間表伺服器的架構特定應用程式資訊元件不適目前 HDInsight 叢集上。


應用程式的一般資訊包含下列排序的資料︰

* 應用程式識別碼，應用程式的唯一識別碼
* 啟動應用程式的使用者
* 若要完成應用程式，嘗試的詳細資訊
* 使用任何指定的應用程式嘗試容器

在您的 HDInsight 叢集，這項資訊會儲存由 Azure 資源管理員歷程記錄存放區中的預設 Azure 儲存體帳戶預設容器。 透過 REST API 擷取此已完成的應用程式的一般資料︰

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>YARN 應用程式和記錄

YARN 支援耦合從應用程式排程/監視資源管理多個程式模型 (MapReduce 正在其中)。 這是透過全域*ResourceManager* (RM)、 每個工作者節點*NodeManagers* (NMs)，以及每個應用程式*ApplicationMasters*反飛彈 （系統）。 每個應用程式 AM 交涉資源 （CPU、 記憶體、 磁碟、 網路） RM.執行您的應用程式 RM 搭配 NMs 授與這些資源，取得做為*容器*。 上午負責追蹤由 RM.分派給該容器的進度 應用程式，可能需要根據性質的應用程式的許多容器。

此外，每個應用程式可能會包含多個*應用程式嘗試*才能完成時當機或遺失的 AM 之間的通訊和 RM. 因此，容器會授與應用程式的特定嘗試。 在意義上，容器提供基本的 YARN 應用程式所執行的工時單位的內容，並在單一工作節點的配置容器上執行完成容器的內容中的所有工作。 請參閱[YARN 概念][YARN-concepts]進一步參照。

應用程式記錄 （和相關聯的容器記錄） 非常重要偵錯問題 Hadoop 應用程式。 提供用於收集與彙總，儲存應用程式與[記錄彙總]的記錄檔的部份架構，YARN[log-aggregation]功能。 記錄檔彙總功能讓您存取應用程式記錄更重要，跨工作者節點上的所有容器會彙總記錄並將它儲存為一個彙總記錄檔每個工作者節點預設檔案系統上，在應用程式完成之後。 您的應用程式可能會使用數百或數以千計的容器，但在單一工作節點上執行的所有容器的記錄一律會彙總至單一檔案]，產生一個記錄檔，每個應用程式所使用的工作者節點。 HDInsight 叢集上的預設會啟用記錄彙總 (3.0 版及以上)，及彙總的記錄可以在下列位置叢集的預設容器中找到︰

    wasbs:///app-logs/<user>/logs/<applicationId>

位置，*使用者*的 [啟動應用程式的使用者名稱，而*applicationId*會 YARN RM.由指定的應用程式的唯一識別碼

彙總的記錄不是直接讀取，他們會以[TFile]撰寫[T-file]，[二進位格式][binary-format]編製索引容器。 YARN 提供 CLI 工具傾印這些記錄，以純文字形式的應用程式或感興趣的容器。 執行下列 YARN 其中一項以純文字上的 [命令直接叢集節點 （後透過 RDP，以連線至該） 時，您可以檢視這些記錄︰

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI

YARN ResourceManager UI 上叢集 headnode 中，執行，並可存取透過 Azure 入口網站的儀表板︰ 

1. [Azure](https://portal.azure.com/)入口網站登入。 
2. 在左側功能表中，按一下 [**瀏覽**、 按一下**HDInsight 叢集**，按一下您要存取 YARN 應用程式記錄檔的 windows 叢集。
3. 在頂端的功能表中，按一下 [**儀表板**。 您會看到新的瀏覽器上開啟的頁面，稱為的**HDInsight 查詢主控台**] 索引標籤。
4. 從**HDInsight 查詢主控台**中，按一下 [ **Yarn UI**]。




[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
