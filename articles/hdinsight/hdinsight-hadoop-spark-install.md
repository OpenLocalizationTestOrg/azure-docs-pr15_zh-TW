<properties
    pageTitle="使用指令碼動作 Hadoop 叢集上安裝火花 |Microsoft Azure"
    description="瞭解如何自訂 HDInsight 叢集與火花使用指令碼動作。"
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/05/2016"
    ms.author="nitinme"/>

# <a name="install-and-use-spark-on-hdinsight-hadoop-clusters-using-script-action"></a>安裝並使用火花上 HDInsight Hadoop 叢集使用指令碼的巨集指令

> [AZURE.IMPORTANT] 本文現在已遭取代。 HDInsight 現在會提供為優先叢集類型火花為 windows 的叢集，這表示您現在可以直接建立火花叢集而不需要修改 Hadoop 叢集使用指令碼動作。 您可使用火花叢集類型，與火花版本 1.3.1 取得 HDInsight 版本 3.2 捨位叢集。  若要安裝不同版本的火花，您可以使用指令碼的巨集指令。 HDInsight 提供範例指令碼動作指令碼。

瞭解如何在 Windows 上安裝火花基礎 HDInsight 使用指令碼的巨集指令，以及如何在 HDInsight 上執行火花查詢叢集。


**相關的文章**

- [建立 Hadoop 叢集 HDInsight 中](hdinsight-provision-clusters.md)︰ 建立 HDInsight 叢集的一般資訊。

- [在 HDInsight Apache 火花快速入門](hdinsight-apache-spark-jupyter-spark-sql.md)︰ 建立 HDInsight 火花叢集。

- [自訂使用指令碼的巨集指令的 HDInsight 叢集][hdinsight-cluster-customize]︰ 自訂 HDInsight 叢集使用指令碼的巨集指令的一般資訊。

- [HDInsight 開發指令碼動作指令碼](hdinsight-hadoop-script-actions.md)。

## <a name="what-is-spark"></a>什麼是火花？

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache 火花</a>是處理架構，可支援提高大型資料分析應用程式的效能的記憶體內處理開啟來源平行。 火花的記憶體內的計算功能讓您更適合反覆運算演算法電腦學習及圖形計算中。

火花也可用於執行傳統磁碟處理資料。 火花避免磁碟寫入的中繼階段中，以改善傳統 MapReduce 架構。 此外，火花是相容於 Hadoop 分散式檔案系統 (HDFS) 和 Azure Blob 儲存體透過火花可以輕鬆地處理現有的資料。

本主題說明如何自訂安裝火花 HDInsight 叢集。

## <a name="install-spark-using-the-azure-portal"></a>安裝火花使用 Azure 入口網站

使用唯讀 Azure 儲存體 blob [https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1)在從 HDInsight 叢集上安裝火花範例指令碼。 這個指令碼安裝火花 1.2.0 或火花 1.0.2 取決於您所建立的 HDInsight 叢集版本。

- 如果您建立**HDInsight 3.2 捨位**叢集時使用指令碼，它會安裝**火花 1.2.0**。
- 如果您建立**HDInsight 3.1**叢集時使用指令碼，它會安裝**火花 1.0.2**。

您可以修改這個指令碼或建立您自己的指令碼來安裝其他版本的火花。

> [AZURE.NOTE] 範例指令碼只適用於 HDInsight 3.1 及 3.2 叢集。 如需有關 HDInsight 叢集版本的詳細資訊，請參閱[HDInsight 叢集版本](hdinsight-component-versioning.md)。

1. 開始使用 [**建立自訂**] 選項中，建立叢集，所述，[建立 Hadoop 叢集 HDInsight 中](hdinsight-provision-clusters.md#portal)。 挑選叢集版本根據下列動作︰

    - 如果您想要安裝**火花 1.2.0**，建立 HDInsight 3.2 捨位叢集。
    - 如果您想要安裝**火花 1.0.2**，建立 HDInsight 3.1 叢集。


2. 在精靈的**指令碼動作**頁面上，按一下 [**新增指令碼動作**提供詳細資料的指令碼動作，如下所示︰

    ![若要自訂叢集使用指令碼動作](./media/hdinsight-hadoop-spark-install/HDI.CustomProvision.Page6.png "若要自訂叢集使用指令碼動作")

    <table border='1'>
        <tr><th>屬性</th><th>值</th></tr>
        <tr><td>名稱</td>
            <td>指定指令碼動作的名稱。 例如，<b>安裝火花</b>。</td></tr>
        <tr><td>指令碼 URI</td>
            <td>指定自訂叢集叫用的指令碼統一資源識別元 (URI)。 例如， <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1</i></td></tr>
        <tr><td>節點類型</td>
            <td>指定要在其執行的自訂指令碼的節點。 您可以選擇<b>所有節點</b>、<b>僅不對節點</b>或<b>只工作者節點</b>。
        <tr><td>參數</td>
            <td>如果所需的指令碼，請指定參數。 安裝火花的指令碼時，不需要任何參數，讓您可以在此保留空白。</td></tr>
    </table>

    您可以新增一個以上的指令碼動作，以叢集上安裝多個元件。 您已新增的指令碼之後，請按一下 [開始建立叢集核取記號。

您也可以使用 PowerShell 的 Azure 或 HDInsight.NET SDK 安裝火花 HDInsight 上使用指令碼。 本主題稍後的提供這些程序的相關指示。

## <a name="use-spark-in-hdinsight"></a>HDInsight 中使用火花
火花提供 Scala、 Python 和 Java Api。 您也可以執行火花查詢中使用互動式火花命令介面。 本節提供如何使用不同的方式處理火花指示︰

- [若要執行互動式查詢中使用火花命令介面](#sparkshell)
- [若要執行火花 SQL 查詢中使用火花命令介面](#sparksql)
- [使用獨立 Scala 程式](#standalone)

###<a name="sparkshell"></a>若要執行互動式查詢中使用火花命令介面
執行下列步驟，以從互動式火花命令介面執行火花查詢。 在此區段中，執行火花查詢範例資料檔案 (/ example/data/gutenberg/davinci.txt) 這是預設的 HDInsight 叢集上使用。

1. 從 Azure 入口網站，請為您建立與火花安裝，然後遠端叢集叢集啟用遠端桌面。 如需相關指示，請參閱[連線至 HDInsight 叢集使用 RDP](hdinsight-administer-use-management-portal.md#rdp)。

2. 遠端桌面通訊協定 (RDP) 工作階段，從桌面，開啟 [Hadoop 命令列 （從桌面捷徑），然後瀏覽至火花安裝; 位置例如， **C:\apps\dist\spark-1.2.0**。


3. 執行下列命令以啟動火花殼層︰

         .\bin\spark-shell --master yarn

    指令執行之後，您應該取得 Scala 提示︰

         scala>

5. 在 Scala 提示時輸入火花查詢所示。 此查詢會計算每個單字的 davinci.txt 檔案的位置/範例/資料/約翰/叢集相關聯 Azure Blob 儲存體上提供的項目。

        val file = sc.textFile("/example/data/gutenberg/davinci.txt")
        val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
        counts.toArray().foreach(println)

6. 輸出應該如下所示︰

    ![從 HDInsight 叢集執行 Scala 互動式命令介面輸出](./media/hdinsight-hadoop-spark-install/hdi-scala-interactive.png)


7. 輸入︰ 若要結束 Scala 提示問。

        :q

###<a name="sparksql"></a>若要執行火花 SQL 查詢中使用火花命令介面

火花 SQL 可讓您用火花在結構化查詢語言 (SQL)、 HiveQL 或 Scala 關聯式查詢。 在此區段中，我們使用火花範例登錄區資料表上執行登錄區查詢。 建立叢集之後，就能使用預設 （稱為**hivesampletable**） 此區段中所用的登錄區資料表。

>[AZURE.NOTE] 下列範例是針對**激起 1.2.0**，如果您在執行時建立 HDInsight 3.2 捨位叢集指令碼動作安裝所建立。

1. 從 Azure 入口網站，請為您建立與火花安裝，然後遠端叢集叢集啟用遠端桌面。 如需相關指示，請參閱[連線至 HDInsight 叢集使用 RDP](hdinsight-administer-use-management-portal.md#rdp)。

2. RDP 工作階段，從桌面，開啟 [Hadoop 命令列 （從桌面捷徑），然後瀏覽至火花安裝; 位置例如， **C:\apps\dist\spark-1.2.0**。


3. 執行下列命令以啟動火花殼層︰

         .\bin\spark-shell --master yarn

    指令執行之後，您應該取得 Scala 提示︰

         scala>

4. 在 Scala 提示時，設定登錄區內容。 這是才能使用火花使用登錄區查詢。

        val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

    請注意， **sc**啟動火花命令介面時設定的預設火花內容。

5. 執行登錄區查詢使用的登錄區內容，列印輸出到主控台。 查詢擷取的特定產生的裝置上的資料，並限制為 20 擷取的記錄數目。

        hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. 您應該會看到如下所示的輸出︰

    ![從 HDInsight 叢集上執行火花 SQL 輸出](./media/hdinsight-hadoop-spark-install/hdi-spark-sql.png)

7. 輸入︰ 若要結束 Scala 提示問。

        :q

### <a name="standalone"></a>使用獨立 Scala 程式

在此區段中，我們撰寫 Scala 應用程式會計算包含字母的行數的 「 」 和 「 b 」 中的範例資料檔案 (/ example/data/gutenberg/davinci.txt) 這是預設的 HDInsight 叢集上使用。 若要撰寫，並使用自訂火花安裝叢集獨立 Scala 程式，您必須執行下列步驟︰

- 撰寫 Scala 程式
- 建立 Scala 程式，以取得.jar 檔案
- 在 [叢集上執行的工作

#### <a name="write-a-scala-program"></a>撰寫 Scala 程式
在此區段中，您撰寫 Scala 程式會計算包含的行數的 「 」 和 「 b 」 範例資料檔案中的。

1. 開啟文字編輯器，並貼上下列內容︰


        /* SimpleApp.scala */
        import org.apache.spark.SparkContext
        import org.apache.spark.SparkContext._
        import org.apache.spark.SparkConf

        object SimpleApp {
          def main(args: Array[String]) {
            val logFile = "/example/data/gutenberg/davinci.txt"         //Location of the sample data file on Azure Blob storage
            val conf = new SparkConf().setAppName("SimpleApplication")
            val sc = new SparkContext(conf)
            val logData = sc.textFile(logFile, 2).cache()
            val numAs = logData.filter(line => line.contains("a")).count()
            val numBs = logData.filter(line => line.contains("b")).count()
            println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
          }
        }

2. 將檔案儲存與**SimpleApp.scala**的名稱。

#### <a name="build-the-scala-program"></a>建立 Scala 程式
在此區段中，您使用<a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">簡單的建立工具</a>（或 sbt） 建立 Scala 程式。 sbt 需要 Java 1.6 或更新版本，因此請確定您有安裝再繼續本節 Java 的正確的版本。

1. 從 http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html 安裝 sbt。
2. 建立資料夾，稱為**SimpleScalaApp**，與此資料夾中建立名**simple.sbt**的檔案。 這是包含 Scala 版本、 文件庫的相依性、 等相關資訊的設定檔。貼上下列到 simple.sbt 檔案，並將其儲存︰


        name := "SimpleApp"

        version := "1.0"

        scalaVersion := "2.10.4"

        libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"



    >[AZURE.NOTE] 請確定保留檔案中的空白行。


3. 在 [ **SimpleScalaApp** ] 資料夾中，建立目錄結構**\src\main\scala** ，貼上您先前建立資料夾底下的 \src\main\scala Scala 程式 (**SimpleApp.scala**)。
4. 開啟命令提示字元中，瀏覽至 SimpleScalaApp 目錄，然後輸入下列命令︰


        sbt package


    一旦編譯應用程式時，您會看到**simpleapp_2.10 1.0.jar**所建立的檔案**\target\scala-2.10**目錄內的根 SimpleScalaApp 資料夾下。


#### <a name="run-the-job-on-the-cluster"></a>在 [叢集上執行的工作
在此區段中，您將含有火花叢集遠端安裝，，然後複製 [SimpleScalaApp 專案的目標資料夾。 然後，您會使用**火花送出**] 命令來送出叢集上的工作。

1. 將已安裝的火花叢集遠端。 從電腦撰寫及內建 SimpleApp.scala 程式的位置，將**SimpleScalaApp\target**資料夾複製並貼到叢集上的位置。
2. RDP 工作階段，從桌面，開啟 Hadoop 命令列，然後瀏覽到您貼上的**目標**資料夾的位置。
3. 輸入下列命令以執行 SimpleApp.scala 程式︰


        C:\apps\dist\spark-1.2.0\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. 程式結束時執行，在主控台上顯示的輸出。


        Lines with a: 21374, Lines with b: 11430

## <a name="install-spark-using-azure-powershell"></a>安裝火花使用 PowerShell 的 Azure

在此區段中，我們可以使用 [**<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">新增 AzureHDInsightScriptAction</a>** cmdlet 叫用來自訂叢集使用指令碼的巨集指令的指令碼。 在進行之前，請確定您有安裝並設定 PowerShell 的 Azure。 設定工作站執行 HDInsight Azure PowerShell cmdlet 的詳細資訊，請參閱[安裝及設定 PowerShell 的 Azure](../powershell-install-configure.md)。

執行下列步驟︰

1. 開啟 PowerShell 的 Azure 視窗，然後將下列變數︰

        # Provide values for these variables
        $subscriptionName = "<SubscriptionName>"        # Name of the Azure subscription
        $clusterName = "<HDInsightClusterName>"         # HDInsight cluster name
        $storageAccountName = "<StorageAccountName>"    # Azure Storage account that hosts the default container
        $storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
        $containerName = $clusterName
        $location = "<MicrosoftDataCenter>"             # Location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $clusterNodes = <ClusterSizeInNumbers>          # Number of nodes in the HDInsight cluster
        $version = "<HDInsightClusterVersion>"          # For example, "3.2"

2. 指定的設定值，例如節點叢集和要使用預設儲存空間。

        # Specify the configuration options
        Select-AzureSubscription $subscriptionName
        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
        $config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
        $config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
        $config.DefaultStorageAccount.StorageContainerName=$containerName

3. 您可以使用 [**新增 AzureHDInsightScriptAction** cmdlet 來將指令碼的巨集指令新增至叢集設定。 更新版本中，建立叢集時，取得執行指令碼動作。

        # Add a script action to the cluster configuration
        $config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1

    **新增 AzureHDInsightScriptAction** cmdlet 採用下列參數︰

    <table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
    <tr>
    <th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">參數</th>
    <th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">定義</th></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">設定</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">設定物件動作資訊會新增至哪些指令碼。</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名稱</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指令碼動作的名稱。</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指定要在其執行的自訂指令碼的節點。 有效的值是 HeadNode （若要安裝在） DataNode （若要在所有資料節點安裝）。 您可以使用其中一個或兩個值。</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">指定要執行的指令碼的 URI。</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">參數</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">所需的指令碼的參數。 使用本主題中的範例指令碼時，不需要任何參數，，因此您沒有看到此參數中上述的程式碼片段。
    </td></tr>
    </table>

4. 最後，開始建立自訂的叢集火花安裝。  

        # Start creating a cluster with Spark installed
        New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

出現提示時，請叢集輸入認證。 可能需要幾分鐘，才能建立叢集。

## <a name="install-spark-using-powershell"></a>安裝火花使用 PowerShell

請參閱[自訂 HDInsight 叢集使用指令碼動作](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell)。

## <a name="install-spark-using-net-sdk"></a>安裝火花使用.NET SDK

請參閱[自訂 HDInsight 叢集使用指令碼動作](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell)。


## <a name="see-also"></a>另請參閱

- [建立 Hadoop 叢集 HDInsight 中](hdinsight-provision-clusters.md)︰ 建立 HDInsight 叢集。
- [在 HDInsight Apache 火花快速入門](hdinsight-apache-spark-jupyter-spark-sql.md)︰ 在 HDInsight 火花快速入門。
- [自訂使用指令碼的巨集指令的 HDInsight 叢集][hdinsight-cluster-customize]︰ 自訂 HDInsight 叢集使用指令碼動作。
- [HDInsight 開發指令碼動作指令碼](hdinsight-hadoop-script-actions.md)︰ 開發指令碼動作指令碼。
- [HDInsight 叢集上安裝 R] [hdinsight-install-r]提供如何使用叢集自訂安裝並使用 R HDInsight Hadoop 叢集上的指示。 R 是開啟來源語言] 和 [統計計算環境。 它會提供數百個內建的統計函數和結合層面的功能和物件導向程式設計自己程式設計語言。 同時也會提供更多圖形功能。
- [安裝的 Giraph HDInsight 叢集上](hdinsight-hadoop-giraph-install.md)。 使用叢集自訂安裝 Giraph HDInsight Hadoop 叢集上。 Giraph 可讓您執行使用 Hadoop，處理的圖表，可以使用 Azure HDInsight。
- [安裝的 Solr HDInsight 叢集上](hdinsight-hadoop-solr-install.md)。 使用叢集自訂安裝 Solr HDInsight Hadoop 叢集上。 Solr 可讓您執行強大的搜尋作業，儲存資料。

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[powershell-install-configure]: powershell-install-configure.md
