 <properties
    pageTitle="Hadoop-什麼是上 HDInsight Hadoop 簡介嗎？ |Microsoft Azure"
    description="在雲端上 HDInsight 取得 Hadoop、 分散式的處理大型資料和分析及 Hadoop 生態系統元件的簡介。"
    keywords="顯示較大的資料分析、 hadoop，hadoop、 hadoop 技術堆疊、 hadoop 生態功能簡介"
    services="hdinsight"
    documentationCenter=""
    authors="cjgronlund"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="cgronlun"/>


# <a name="an-introduction-to-the-hadoop-ecosystem-on-azure-hdinsight"></a>在 Azure HDInsight Hadoop 生態簡介

 本文提供簡介 Hadoop Azure HDInsight、 生態，以及顯示較大的資料。 深入了解 Hadoop 元件、 常見的詞彙，並顯示較大的資料分析的案例。

## <a name="what-is-hadoop-on-hdinsight"></a>什麼是上 HDInsight Hadoop？

Hadoop 指的是分散式處理、 儲存，架構的開啟來源軟體生態和叢集的電腦上的大型資料集的分析。 Azure HDInsight 在雲端，讓**Hortonworks 資料平台 (HDP)**分配從 Hadoop 元件、 部署與佈建新高可靠性與可用的受管理的叢集。  

Apache Hadoop 已處理大型資料的原始開啟來源專案。 下列是開發的相關的軟體和公用程式 Hadoop 技術堆疊，包括 Apache 登錄區、 Apache HBase、 Apache 火花和許多其他的一部分。 如需詳細資訊，請參閱[在 HDInsight Hadoop 生態系統的概觀](#overview)。

## <a name="what-is-big-data"></a>什麼是顯示較大的資料？

大型資料說明數位資訊，摘要，資訊網站上瀏覽客戶和購買工業設備，從感應器資訊 Twitter 中之文字的任何大型本文。 顯示較大的資料可以歷史 （亦即儲存的資料） 或即時 （亦即直接從來源串流）。 顯示較大收集的資料和展開中也能擴大區越來越 velocities，在各種不同的格式。

顯示較大提供當中智慧或充分的詳細資料，您必須收集相關的資料，並提出問題。 您也必須確定已清除與分析，然後實用的方式呈現資料是可供存取。 這是有助於 Hadoop HDInsight 中的大型資料分析。

## <a name="overview"></a>在 HDInsight Hadoop 生態的概觀

HDInsight 是 Microsoft Azure 上顯示較大的資料分析快速展開 Apache Hadoop 技術堆疊的雲端通訊群組。 其包含實作，Apache 火花、 HBase、 大量、 豬、 登錄區、 Sqoop、 Oozie、 Ambari，以及等等。 HDInsight 也整合了例如 Power BI 與 Excel、 SQL Server Analysis Services，SQL Server Reporting Services 的商業智慧 (BI) 工具。

### <a name="hadoop-hbase-spark-storm-and-customized-clusters"></a>Hadoop、 HBase、 火花、 大量及自訂的叢集

HDInsight 提供 Apache Hadoop、 火花、 HBase，或大量叢集設定。 或者，您可以[自訂指令碼動作叢集](hdinsight-hadoop-customize-cluster-linux.md)。

* **Hadoop**︰ 提供[HDFS](#hdfs)和簡單的[MapReduce](#mapreduce)程式設計模式處理程序及分析資料平行可靠的資料儲存區。

* **<a target="_blank" href="http://spark.apache.org/">Apache 火花</a>**︰ 平行處理架構，可支援提高大資料分析應用程式，效能的記憶體內處理激起運作 sql，串流資料，及電腦學習。 請參閱[概觀︰ 什麼是在 HDInsight Apache 火花？](hdinsight-apache-spark-overview.md)

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>**︰ 內建在提供隨機存取及強式一致性大量的非結構化及半結構化資料-潛在數十億的資料列的 Hadoop NoSQL 資料庫時間數百萬個資料行。 請參閱[在 HDInsight HBase 概觀](hdinsight-hbase-overview.md)。

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache 大量</a>**︰ 快速處理資料的大量資料流分散式、 即時計算系統。 大量做為受管理的叢集 HDInsight 中提供。 請參閱[使用大量和 Hadoop 分析感應器即時資料](hdinsight-storm-sensor-data-analysis.md)。

### <a name="example-customization-scripts"></a>範例自訂指令碼

指令碼動作會執行期間叢集佈建，並可用於安裝叢集上的 [附加元件的指令碼。 針對 Linux 型叢集，這些是艦隊指令碼。

下列範例指令碼所提供的 HDInsight 小組︰

* [色調](hdinsight-hadoop-hue-linux.md)︰ 一組用來與叢集互動的 web 應用程式。 僅限 Linux 叢集。

* [Giraph](hdinsight-hadoop-giraph-install-linux.md)︰ 處理模型項目或人員之間的關聯的圖表。

* [R](hdinsight-hadoop-r-scripts-linux.md): 開啟來源語言] 和 [環境中電腦學習使用統計計算。

* [Solr](hdinsight-hadoop-solr-install-linux.md): 企業級搜尋平台上的資料，全文檢索搜尋。

如需開發您自己的指令碼的動作，請參閱[使用 HDInsight 的指令碼動作開發](hdinsight-hadoop-script-actions-linux.md)。


## <a name="what-are-the-hadoop-components-and-utilities"></a>Hadoop 元件和公用程式是什麼？

下列元件和公用程式會包含在 HDInsight 叢集上。

* **[Ambari](#ambari)**︰ 叢集佈建、 管理、 監控及公用程式。

* **[Avro](#avro)**（Microsoft.NET 文件庫的 Avro）︰ 資料序列化 Microsoft.NET 環境。

* **[登錄區與 HCatalog](#hive)**︰ 結構化查詢語言 (SQL)-等查詢與資料表和儲存空間管理圖層。

* **[Mahout](#mahout)**︰ 調整機器學習應用程式。

* **[MapReduce](#mapreduce)**︰ 舊版架構 Hadoop 分散式處理和資源管理。 請參閱[YARN](#yarn)下, 一步] 產生資源架構。

* **[Oozie](#oozie)**︰ 工作流程管理。

* **[鳳凰](#phoenix)**︰ 透過 HBase 關聯式資料庫圖層。

* **[豬](#pig)**︰ 簡單 MapReduce 轉換指令碼。

* **[Sqoop](#sqoop)**︰ 資料匯入及匯出。

* **[Tez](#tez)**︰ 可讓在有效率地執行需要大量資料的程序。

* **[YARN](#yarn)**: Hadoop 核心文件庫與下一個產生 MapReduce 軟體架構的一部分。

* **[動物園管理員](#zookeeper)**︰ 一起使用的分散式系統中的程序。

> [AZURE.NOTE] 在特定的元件上的資訊及版本資訊，請參閱[Hadoop 元件、 版本設定，並在 HDInsight 的服務方案][component-versioning]

### <a name="ambari"></a>Ambari

Apache Ambari 適用於佈建、 管理及監視 Apache Hadoop 叢集。 包括運算子工具直覺式集合及隱藏複雜的 Hadoop，簡化的叢集作業的 api 組。 Linux 型的 HDInsight 叢集提供兩 Ambari web UI 和 Ambari REST API 時 Windows 型叢集提供 REST API 的子集合。 HDInsight 叢集上 Ambari 檢視可以讓外掛程式 UI 功能。

請參閱[使用 Ambari 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)(僅限 Linux)[中使用 Ambari API HDInsight 的監視器 Hadoop 叢集](hdinsight-monitor-use-ambari-api.md)，與<a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Apache Ambari API 參考</a>。

### <a name="avro"></a>Avro （Microsoft.NET 文件庫的 Avro）

Microsoft.NET 文件庫的 Avro 實作序列化 Microsoft.NET 環境的 Apache Avro 精簡的二進位資料交換格式。 它會使用<a target="_blank" href="http://www.json.org/">JavaScript 物件標記法 (JSON)</a>來定義 underwrites 語言互通性適用於多種語言的結構描述，可以在另一個讀取序列化一種語言的意義資料。 詳細的資訊，在格式] 可以在中找到 < 目標 = _ 「 空白 」 href = 「 http://avro.apache.org/docs/current/spec.html 」 > Apache Avro 規格</a>。
Avro 檔案的格式支援分散式的 MapReduce 程式設計模型。 檔案是 「 可分割的圖形]，表示您可以搜尋檔案中的任何點並開始的特定區塊讀取。 若要瞭解如何，請參閱[Serialize 資料與 Avro 的 Microsoft.NET 文件庫](hdinsight-dotnet-avro-serialization.md)。


### <a name="hdfs"></a>HDFS

Hadoop 分散式檔案系統 (HDFS) 是一種分散式的檔案系統的 MapReduce 與 YARN，是 Hadoop 生態系統的核心。 HDFS 是 Hadoop 叢集 HDInsight 的標準檔案系統。

### <a name="hive"></a>登錄區與 HCatalog

<a target="_blank" href="http://hive.apache.org/">Apache 登錄區</a>」 是內建在可讓您的查詢，然後使用稱為 HiveQL 的類似 SQL 語言管理分散式儲存區中的大型資料集的 Hadoop 資料倉庫軟體。 登錄區，例如豬，是抽象 MapReduce 上方。 執行時，登錄區會轉換成一系列的 MapReduce 工作的查詢。 登錄區概念接近關聯式資料庫管理系統的豬，比，因此不適用於其他結構化資料。 非結構化資料，豬是較佳的選擇。 請參閱[使用登錄區 Hadoop HDInsight 中使用](hdinsight-use-hive.md)。

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a>是表格，儲存空間管理圖層的關聯式資料檢視會提供使用者的 Hadoop。 在 HCatalog，您可以讀取及撰寫登錄區 SerDe （序列化還原序列化） 可寫入的任何格式的檔案。

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a>是電腦的可調整文件庫學習演算法的 Hadoop 上執行。 電腦學習應用程式使用統計資料的準則，教系統，若要了解從資料，以及使用過去的結果決定未來的行為。 請參閱[使用 Mahout 上 Hadoop 產生影片建議](hdinsight-mahout.md)。

### <a name="mapreduce"></a>MapReduce
MapReduce 是 Hadoop 的舊版軟體架構撰寫批次平行程序大型資料集的應用程式。 MapReduce 工作分割大型資料集，並將資料組織到關鍵值組進行處理。

[YARN](#yarn) Hadoop 下一步] 產生資源管理員和應用程式架構，而指 MapReduce 2.0。 在 YARN 上，執行 MapReduce 工作。

如需有關 MapReduce 的詳細資訊，請參閱<a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> Hadoop Wiki 中。

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a>是工作流程協調系統管理 Hadoop 工作。 它與 Hadoop 堆疊整合，並用於 MapReduce 與豬、 登錄區，Sqoop 支援 Hadoop 工作。 也可用於特定系統，例如 Java 程式或殼層指令碼的工作排程。 請參閱[使用 Oozie 與 Hadoop](hdinsight-use-oozie.md)。

### <a name="phoenix"></a>鳳凰
<a  target="_blank" href="http://phoenix.apache.org/">Apache 鳳凰</a>是 HBase 關聯式資料庫圖層。 鳳凰包含可讓使用者的查詢，並直接管理 SQL 表格 JDBC 驅動程式。 鳳凰轉譯查詢和其他陳述式原生 NoSQL API 呼叫-，而不是使用 MapReduce-以便更快速地 NoSQL 儲存應用程式。 請參閱[使用 Apache 鳳凰和松鼠 HBase 叢集](hdinsight-hbase-phoenix-squirrel.md)。


### <a name="pig"></a>豬
<a  target="_blank" href="http://pig.apache.org/">Apache 豬</a>是整體的平台，可讓您可使用簡單的指令碼語言一部分的豬拉丁大型資料集執行複雜的 MapReduce 轉換。 豬翻譯豬拉丁指令碼，使其會執行 Hadoop 內。 您可以建立使用者定義函數 (Udf)，將延伸豬拉丁文。 請參閱[使用豬與 Hadoop](hdinsight-use-pig.md)。

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a>是傳輸大量 Hadoop 和關聯式資料庫的 SQL，之間的資料或其他結構化的資料儲存區中，盡可能地有效的工具。 請參閱[使用 Sqoop 與 Hadoop](hdinsight-use-sqoop.md)。

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a>是在 Hadoop YARN 執行複雜的、 非循環圖形的一般資料處理的內建應用程式架構。 這是更有彈性且強大的後續 MapReduce 架構，讓大量資料處理程序，例如登錄區，在 [縮放比例更有效率地執行。 請參閱[「 使用 Apache Tez 以改善效能 「 使用登錄區和 HiveQL 中](hdinsight-use-hive.md#usetez)。

### <a name="yarn"></a>YARN
Apache YARN MapReduce （MapReduce 2.0 或 MRv2） 的下一個產生，而支援除了 MapReduce 批次處理延展性與即時處理資料處理案例。 YARN 提供資源管理和分散式應用程式架構。 在 YARN 上，執行 MapReduce 工作。

若要瞭解 YARN，請參閱<a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>。


### <a name="zookeeper"></a>動物園管理員
<a  target="_blank" href="http://zookeeper.apache.org/">Apache 動物園管理員</a>大型分散式系統中協調程序，以資料登錄 (znodes) 的階層式共用命名空間。 Znodes 包含少量中繼程序進行共同作業所需的資訊︰ 狀態、 位置、 設定及等等。

## <a name="programming-languages-on-hdinsight"></a>在 HDInsight 的程式設計語言

HDInsight 叢集-Hadoop，HBase 大量，與火花叢集-支援數字的程式設計語言，但部分未安裝預設。 文件庫、 模組或預設未安裝套件，請安裝元件使用指令碼的巨集指令。 請參閱[使用 HDInsight 的指令碼動作開發](hdinsight-hadoop-script-actions-linux.md)。

### <a name="default-programming-language-support"></a>預設程式設計語言支援

根據預設，HDInsight 叢集支援︰

* Java

* Python

使用指令碼動作可以安裝其他語言︰[使用 HDInsight 的指令碼動作開發](hdinsight-hadoop-script-actions-linux.md)。

### <a name="java-virtual-machine-jvm-languages"></a>Java 虛擬機器 (JVM) 語言

可以使用 Java 虛擬機器 (JVM); 執行 Java 以外的多種語言不過，執行這些語言的部分可能會要求叢集上所安裝的其他元件。

這些 JVM 為主的語言支援 HDInsight 叢集上︰

* Clojure

* Jython (Python java)

* Scala

### <a name="hadoop-specific-languages"></a>Hadoop 特定語言

HDInsight 叢集支援下列語言的特定 Hadoop 生態︰

* 豬拉丁豬工作

* 群組工作和 SparkSQL HiveQL


## <a name="advantage"></a>Hadoop 在雲端的優點

Azure 雲端生態的一部分，Hadoop HDInsight 中的提供的福利，它們之間的數字︰

* 自動佈建的 Hadoop 叢集。 HDInsight 叢集會更方便比手動設定 Hadoop 叢集建立。 如需詳細資訊，請參閱[佈建 Hadoop 叢集 HDInsight 中](hdinsight-hadoop-provision-linux-clusters.md)。

* 圖案狀態 Hadoop 元件。 如需詳細資訊，請參閱[Hadoop 元件、 版本設定，並在 HDInsight 的服務方案][component-versioning]。

* 可用性及可靠性叢集。 如需詳細資訊，請參閱[可用性和可靠性 Hadoop 叢集 HDInsight 中](hdinsight-high-availability-linux.md)。

* Azure Blob 儲存體，Hadoop 相容選項與兼具效率與經濟資料儲存區。 如需詳細資訊，請參閱[使用 Hadoop HDInsight 中的使用 Azure Blob 儲存體](hdinsight-hadoop-use-blob-storage.md)。

* 與其他 Azure 服務，包括[Web 應用程式](https://azure.microsoft.com/documentation/services/app-service/web/)與[SQL 資料庫](https://azure.microsoft.com/documentation/services/sql-database/)整合。

* 其他 VM 大小，並執行 HDInsight 叢集類型。 [Hadoop 元件、 版本設定，並在 HDInsight 的服務方案]，請參閱[component-versioning]如需詳細資訊。

* 叢集縮放比例。 叢集縮放比例可讓您變更執行 HDInsight 叢集的節點數目，而不需要刪除或重新建立。

* 虛擬網路支援。 HDInsight 叢集可以使用支援雲端資源的連結雲端資源，與您的資料中心中的混合式案例隔離 Azure 虛擬網路。

* 成本低的項目。 開始[免費試用版](https://azure.microsoft.com/free/)，或[HDInsight 定價詳細資料](https://azure.microsoft.com/pricing/details/hdinsight/)，請參閱。

若要閱讀更多關於在中 HDInsight Hadoop 優點，請參閱[Azure 功能] 頁面上的 HDInsight][marketing-page]。

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight 標準和 HDInsight 進階版

HDInsight 提供標準和進階版的兩個類別中的大型資料雲端服務。 HDInsight 標準提供企業級叢集組織可以用來執行他們大型資料的工作量。 根據的 HDInsight 進階版，並提供進階分析及 HDInsight 叢集安全性功能。 如需詳細資訊，請參閱[Azure HDInsight 進階版](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)


## <a id="resources"></a>進一步了解大資料分析與 Hadoop，HDInsight 的資源

建立在此簡介 Hadoop 雲端和大型資料分析與下列資源。


### <a name="hadoop-documentation-for-hdinsight"></a>HDInsight Hadoop 文件

* [HDInsight 文件](https://azure.microsoft.com/documentation/services/hdinsight/)︰ 文件的頁面上 Azure HDInsight Hadoop 文章、 影片與更多資源的連結。

* [Hadoop HDInsight 中快速入門](hdinsight-hadoop-linux-tutorial-get-started.md)︰ 佈建 HDInsight Hadoop 叢集及執行查詢範例登錄區的快速入門教學課程。

* [在 HDInsight 火花快速入門](hdinsight-apache-spark-jupyter-spark-sql.md)︰ 建立火花叢集及執行互動式火花 SQL 查詢的快速入門教學課程。

* [使用 R 伺服器上 HDInsight](hdinsight-hadoop-r-server-get-started.md): HDInsight 進階版中使用 R 伺服器。

* [佈建 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)︰ 了解如何佈建透過 Azure 入口網站、 Azure CLI 或 PowerShell 的 Azure HDInsight Hadoop 叢集。


### <a name="apache-hadoop"></a>Apache Hadoop

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>︰ 深入了解 Apache Hadoop 軟體文件庫，一個架構，允許分散式處理大型資料集的跨叢集的電腦。

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>︰ 深入了解架構及 Hadoop 分散式檔案系統、 Hadoop 應用程式所用的主要儲存系統的設計。

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce 教學課程</a>︰ 深入瞭解程式設計的架構撰寫 Hadoop 快速程序的大量資料平行運算節點的大型叢集上的應用程式。


### <a name="microsoft-business-intelligence"></a>Microsoft 商務智慧功能

熟悉商務智慧 (BI) 工具-例如 Excel 與 PowerPivot、 SQL Server Analysis Services，SQL Server Reporting Services-擷取、 分析和報表使用 [Power Query 增益集或 Microsoft 登錄區 ODBC 驅動程式整合 HDInsight 的資料。

這些的 BI 工具可協助您大型資料分析中︰

* [將 Excel 連接至 Hadoop 使用 Power Query](hdinsight-connect-excel-power-query.md)︰ 深入瞭解如何連線至 Azure 儲存體帳戶儲存聯 HDInsight 叢集使用 Microsoft Power Query for Excel 資料的 Excel。 所需的 Windows 工作站。 適用於 Windows 或 Linux 的叢集。

* [將 Excel 連接至 Microsoft 登錄區 ODBC 驅動程式 Hadoop](hdinsight-connect-excel-hive-odbc-driver.md)︰ 了解如何從 HDInsight Microsoft 登錄區 ODBC 驅動程式匯入資料。 所需的 Windows 工作站。 適用於 Windows 或 Linux 的叢集。

* [Microsoft 雲端平台](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx)︰ 進一步瞭解 Power BI for Office 365、 下載 SQL Server 試用版和設定 SharePoint Server 2013 和 SQL Server BI。

* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx)。

* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx)。




[marketing-page]: https://azure.microsoft.com/services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/
