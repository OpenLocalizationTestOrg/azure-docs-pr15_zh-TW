<properties
    pageTitle="使用指令碼動作 Hadoop 叢集上安裝 Solr |Microsoft Azure"
    description="瞭解如何自訂 HDInsight 叢集與 Solr 使用指令碼動作。"
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/05/2016"
    ms.author="nitinme"/>

# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>安裝並使用 Solr HDInsight Hadoop 叢集上

瞭解如何自訂 Windows 根據的 HDInsight 叢集與 Solr 使用指令碼的巨集指令，以及如何使用 Solr 搜尋資料。 使用 Solr 與 Linux 叢集的資訊，請參閱[安裝並使用 Solr HDinsight Hadoop 叢集 (Linux) 上](hdinsight-hadoop-solr-install-linux.md)。
 
使用*指令碼的巨集指令*，您可以在 Azure HDInsight 安裝 Solr 上任何類型的叢集 （Hadoop 大量、 HBase、 火花）。 使用唯讀 Azure 儲存體 blob [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1)在從 HDInsight 叢集上安裝 Solr 範例指令碼。 

範例指令碼只適用於 HDInsight 叢集版本 3.1。 如需有關 HDInsight 叢集版本的詳細資訊，請參閱[HDInsight 圖組版本](hdinsight-component-versioning.md)。

使用本主題中的範例指令碼會建立 Windows 型 Solr 叢集具有特定設定。 如果您想要使用不同的集合、 擊碎、 結構、 複本等設定 Solr 叢集，您必須修改的指令碼及 Solr 二進位檔案。

**相關的文章**

- [安裝並使用 Solr HDinsight Hadoop 叢集 (Linux) 上](hdinsight-hadoop-solr-install-linux.md)
- [建立 Hadoop 叢集 HDInsight 中](hdinsight-provision-clusters.md)︰ 建立 HDInsight 叢集的一般資訊。
- [自訂使用指令碼的巨集指令的 HDInsight 叢集][hdinsight-cluster-customize]︰ 自訂 HDInsight 叢集使用指令碼的巨集指令的一般資訊。
- [HDInsight 開發指令碼動作指令碼](hdinsight-hadoop-script-actions.md)。


## <a name="what-is-solr"></a>什麼是 Solr？

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a>是企業搜尋平台可從資料中的強大全文檢索搜尋。 Hadoop 啟用儲存和管理大量資料，請 Apache Solr 提供的搜尋功能來快速擷取資料。 

## <a name="install-solr-using-portal"></a>安裝 Solr 使用入口網站

1. 開始使用 [**建立自訂**] 選項中，建立叢集，所述，[建立 Hadoop 叢集 HDInsight 中](hdinsight-provision-clusters.md#portal)。
2. 在精靈的**指令碼動作**頁面上，按一下 [**新增指令碼動作**提供詳細資料的指令碼動作，如下所示︰

    ![若要自訂叢集使用指令碼動作](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "若要自訂叢集使用指令碼動作")

    <table border='1'>
        <tr><th>屬性</th><th>值</th></tr>
        <tr><td>名稱</td>
            <td>指定指令碼動作的名稱。 例如，<b>安裝 Solr</b>。</td></tr>
        <tr><td>指令碼 URI</td>
            <td>指定自訂叢集叫用的指令碼統一資源識別元 (URI)。 例如， <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
        <tr><td>節點類型</td>
            <td>指定要在其執行的自訂指令碼的節點。 您可以選擇<b>所有節點</b>，<b>只不對節點</b>或<b>只工作者節點</b>。
        <tr><td>參數</td>
            <td>如果所需的指令碼，請指定參數。 安裝 Solr 的指令碼不需要任何參數，因此您可以在此保留空白。</td></tr>
    </table>

    您可以新增一個以上的指令碼動作，以叢集上安裝多個元件。 您已新增的指令碼之後，請按一下 [開始建立叢集核取記號。


## <a name="use-solr"></a>使用 Solr

您必須開始編製索引作業的一些資料檔案的 Solr。 Solr 然後可用來執行搜尋查詢的索引的資料。 若要使用 Solr HDInsight 叢集下列步驟執行︰

1. **使用遠端桌面通訊協定 (RDP) 安裝的 Solr HDInsight 叢集到遠端**。 從 Azure 入口網站，請為您建立與 Solr 安裝，然後遠端叢集叢集啟用遠端桌面。 如需相關指示，請參閱[連線至 HDInsight 叢集使用 RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)。

2. **索引 Solr 資料檔案上傳**。 當您編製索引 Solr 時，您可以將文件中，您可能需要搜尋。 若要編製索引 Solr，使用 RDP 遠端至叢集、 瀏覽至桌面，開啟 Hadoop 命令列中，並瀏覽至**C:\apps\dist\solr-4.7.2\example\exampledocs**。 執行下列命令︰

        java -jar post.jar solr.xml monitor.xml

    您會看到下列輸出主控台上︰

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    Post.jar 公用程式的兩個樣本文件、 **solr.xml**與**monitor.xml**索引 Solr。 Post.jar 公用程式，並在 [文件範例可使用 Solr 安裝。

3. **使用 [Solr 儀表板，在索引的文件中進行搜尋**。 HDInsight 叢集 RDP 工作階段，請開啟 Internet Explorer，並啟動在 Solr 儀表板**http://headnodehost:8983/solr / #/**。 從左側的窗格，從**核心選取器**] 下拉式清單，選取**collection1**，然後中，按一下 [**查詢]**。 例如，若要選取並傳回 Solr 中的所有文件，提供下列的值︰

    * 在 [**問**文字] 方塊中輸入**\*:**\*。 這會傳回所有已編製索引的文件中 Solr。 如果您想要搜尋特定的文件中的字串，您可以輸入該的字串。
    
    * 在 [ **wt**文字] 方塊中選取的輸出格式。 預設值為**json**。 按一下 [**執行查詢**。

    ![若要自訂叢集使用指令碼巨集指令](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "執行 Solr 儀表板上查詢")
    
    輸出傳回兩個 Solr 編製索引所使用的文件。 輸出類似下列各項︰

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }


4. **建議︰ 備份 Solr 至 Azure Blob 儲存體 HDInsight 叢集相關聯的索引資料**。 好的做法是應該備份到 Azure Blob 儲存體 Solr 叢集節點索引的資料。 執行下列步驟來執行這項操作︰

    1. RDP 工作階段]，開啟 Internet Explorer 中，並指向 [將下列 URL:

            http://localhost:8983/solr/replication?command=backup

        您應該會看到的回應，像這樣︰

            <?xml version="1.0" encoding="UTF-8"?>
            <response>
              <lst name="responseHeader">
                <int name="status">0</int>
                <int name="QTime">9</int>
              </lst>
              <str name="status">OK</str>
            </response>

    2. 在遠端工作階段中，瀏覽到 {SOLR_HOME}\{集合} \data。 建立範例指令碼叢集，應該是**C:\apps\dist\solr-4.7.2\example\solr\collection1\data**。 在這個位置，您應該會看到類似*的名稱與建立的快照資料夾*快照。*時間戳記** *。

    3. Zip 快照集資料夾，然後上傳至 Azure Blob 儲存體。 從 Hadoop 命令列中，瀏覽至快照資料夾的位置使用下列命令︰

              hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

        這個命令複製快照，以 /example/data/在容器內的預設儲存空間叢集相關聯的帳戶。

## <a name="install-solr-using-aure-powershell"></a>安裝 Solr 使用 Aure PowerShell

請參閱[自訂 HDInsight 叢集使用指令碼動作](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell)。  範例會示範如何安裝火花使用 PowerShell 的 Azure。 您需要自訂使用[https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1)的指令碼。

## <a name="install-solr-using-net-sdk"></a>安裝 Solr 使用.NET SDK

請參閱[自訂 HDInsight 叢集使用指令碼動作](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell)。 範例會示範如何安裝火花使用.NET SDK。 您需要自訂使用[https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1)的指令碼。



## <a name="see-also"></a>另請參閱

- [安裝並使用 Solr HDinsight Hadoop 叢集 (Linux) 上](hdinsight-hadoop-solr-install-linux.md)
- [建立 Hadoop 叢集 HDInsight 中](hdinsight-provision-clusters.md)︰ 建立 HDInsight 叢集的一般資訊。
- [自訂使用指令碼的巨集指令的 HDInsight 叢集][hdinsight-cluster-customize]︰ 自訂 HDInsight 叢集使用指令碼的巨集指令的一般資訊。
- [HDInsight 開發指令碼動作指令碼](hdinsight-hadoop-script-actions.md)。
- [安裝並使用火花 HDInsight 叢集上][hdinsight-install-spark]︰ 了解如何安裝火花的指令碼動作範例。
- [HDInsight 叢集上安裝 R][hdinsight-install-r]︰ 指令碼動作範例，了解如何安裝。
- [HDInsight 叢集上的安裝 Giraph](hdinsight-hadoop-giraph-install.md)︰ 了解如何安裝 Giraph 的指令碼動作範例。


[powershell-install-configure]: powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
