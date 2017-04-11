<properties
    pageTitle="監視使用 Ambari API HDInsight 中的 Hadoop 叢集 |Microsoft Azure"
    description="用於建立、 管理及監視 Hadoop 叢集 Apache Ambari Api。 直覺式的運算子工具與 Api 隱藏 Hadoop 提高複雜度。"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    editor="cgronlun"
    manager="jhubbard"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="monitor-hadoop-clusters-in-hdinsight-using-the-ambari-api"></a>監視 Hadoop 叢集中使用 Ambari API HDInsight

瞭解如何使用 Ambari Api 監視 HDInsight 叢集。

> [AZURE.NOTE] 本文中的資訊是主要目的是為了提供 Ambari REST API 的唯讀版本的 Windows 型 HDInsight 叢集。 Linux 型叢集，請參閱 <<c0>管理 Hadoop 叢集使用 Ambari。

## <a name="what-is-ambari"></a>什麼是 Ambari？

[Apache Ambari] [ambari-home]用於佈建、 管理及監視 Apache Hadoop 叢集。 包括運算子工具直覺式集合及隱藏複雜的 Hadoop，簡化的叢集作業的 api 組。 如需有關 Api 的詳細資訊，請參閱[Ambari API 參考][ambari-api-reference]。 

HDInsight 目前支援 Ambari 監視功能。 HDInsight 版本 3.0 和 2.1 叢集支援 Ambari API 1.0。 本文涵蓋存取 Ambari Api HDInsight 版本 3.1 及 2.1 叢集上。 兩個之間的主要差異是，部分元件已變更 （例如工作歷程記錄伺服器） 的新功能的簡介。 

**必要條件**

本教學課程之前，您必須具備下列項目︰

- **使用 PowerShell 的 Azure 工作站**。

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- （選用）[cURL][curl]. 若要將其安裝，請參閱[cURL 版本與下載][curl-download]。

    >[AZURE.NOTE] 何時使用捲曲] 命令，在 Windows 中，使用雙引號，而不是單一-引號的選項值。

- **Azure HDInsight 叢集**。 如需有關叢集佈建的指示，請參閱[開始使用 HDInsight] [hdinsight-get-started]或[佈建 HDInsight 叢集][hdinsight-provision]。 您將需要教學課程的完整下列資料︰

    叢集屬性|Azure PowerShell 變數名稱|值|描述
    ---|---|---|---
    HDInsight 叢集名稱|$clusterName||HDInsight 叢集的名稱。
    叢集使用者名稱|$clusterUsername||當建立叢集指定叢集使用者名稱。
    叢集密碼|$clusterPassword||叢集使用者的密碼。

    >[AZURE.NOTE] 填入表格中的值。 這會很有幫助進行本教學課程中的項目。

## <a name="jump-start"></a>跳開始

有數種方式可使用 Ambari 監視 HDInsight 叢集。

**使用 PowerShell 的 Azure**

下列是取得 MapReduce 工作追蹤器資訊 Azure PowerShell 指令碼*HDInsight 3.1 叢集。*  主要差別我們提取 YARN 服務 （而非 MapReduce） 的下列詳細資料。

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

下列是取得 MapReduce 工作追蹤器資訊*HDInsight 2.1 叢集*Azure PowerShell 指令碼︰

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

輸出為︰

![Jobtracker 輸出][img-jobtracker-output]

**使用捲曲**

以下是使用捲曲開始叢集資訊的範例︰

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

輸出為︰

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

在**10/8/2014年的版本**︰

使用 Ambari 結束點 」 https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname} 」， *host_name*欄位會傳回的完整的網域名稱 (FQDN)，而不是主機名稱的節點。 2014/10/8 發行之前，此範例會傳回只要 「**headnode0**」。 在 2014/10/8 版本之後，您收到 FQDN 」**headnode0。 {ClusterDNS}.azurehdinsight.net**」，在先前範例所示。 以利於進行分析藍本位置 （例如 HBase 和 Hadoop） 的多個叢集類型可以部署虛擬網路 (VNET) 需要這項變更。 這種情況，例如，使用 Hadoop 做為後端平台 HBase 時。

## <a name="ambari-monitoring-apis"></a>Ambari 監控 Api

下表列出一些最常見的 Ambari 監控 API 呼叫。 如需有關 API 的詳細資訊，請參閱[Ambari API 參考][ambari-api-reference]。

通話監視器 API|URI|描述
---|---|---
取得叢集|`/api/v1/clusters`|
取得叢集資訊。|`/api/v1/clusters/<ClusterName>.azurehdinsight.net`|叢集，服務主機
取得服務|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services`|服務包括︰ hdfs，mapreduce
取得服務的資訊。|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>`|
取得服務元件|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components`|HDFS: namenode datanode<br/>MapReduce: jobtracker;tasktracker
取得元件的狀態。|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>`|ServiceComponentInfo 主機元件、 指標
取得主機|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts`|headnode0 workernode0
取得主機資訊。|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>`|
取得主機元件|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components`|namenode resourcemanager
取得主機元件的狀態。|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>`|HostRoles 元件、 主機指標
取得設定|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations`|設定類型︰ 核心網站、 hdfs 網站、 mapred 網站登錄區網站
取得設定資訊。|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>`|設定類型︰ 核心網站、 hdfs 網站、 mapred 網站登錄區網站


##<a name="next-steps"></a>後續步驟

現在您已經學會如何使用 Ambari 監控 API 呼叫。 若要深入瞭解，請參閱︰

- [管理 HDInsight 叢集使用 Azure 入口網站][hdinsight-admin-portal]
- [管理使用 PowerShell 的 Azure HDInsight 叢集][hdinsight-admin-powershell]
- [管理 HDInsight 叢集使用命令列介面][hdinsight-admin-cli]
- [HDInsight 文件][hdinsight-documentation]
- [快速入門 HDInsight][hdinsight-get-started]



[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: /documentation/services/hdinsight/
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
