<properties
    pageTitle="什麼是 HBase HDInsight 中？ |Microsoft Azure"
    description="在 HDInsight Apache HBase 簡介，NoSQL 資料庫建立 Hadoop 上。 瞭解如何使用案例並比較 HBase 其他 Hadoop 叢集。"
    keywords="bigtable，nosql，hbase 功能"
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
    ms.topic="get-started-article"
    ms.date="09/14/2016"
    ms.author="jgao"/>



# <a name="what-is-hbase-in-hdinsight-a-nosql-database-that-provides-bigtable-like-capabilities-for-hadoop"></a>什麼是在 HDInsight HBase: NoSQL 資料庫，提供 Hadoop BigTable 類似的功能

Apache HBase 是開啟來源 NoSQL 資料庫的內建在 Hadoop，並以 Google BigTable 模型。 HBase 提供隨機存取和加強一致性的大量的非結構化及 semistructured schemaless 資料庫依欄系列中的資料。

資料會儲存在資料表的資料列和資料列中的已分組的資料行系列。 HBase 是在欄或儲存在進行中的資料類型都不需要加以定義，再使用其意義 schemaless 資料庫。 開啟來源代碼等差比例來處理 petabytes 上數以千計的節點的資料。 它可以依賴資料重複、 批次處理及其他功能所提供的 Hadoop 生態的分散式應用程式。

## <a name="how-is-hbase-implemented-in-azure-hdinsight"></a>HBase 實作中 Azure HDInsight 的方式是？

HDInsight HBase 提供做為受管理的叢集整合在 Azure 環境。 將資料儲存直接在 Azure Blob 儲存體，提供低延遲和提高的 elasticity 效能與成本選項中設定叢集。 這可讓客戶建立使用大型資料集，來建立數百萬結束點、 感應器與遙測資料儲存的服務以及分析此資料與 Hadoop 工作的互動式網站。 HBase 和 Hadoop 是絕佳起點大型資料專案中 Azure;特別是，他們可以啟用即時應用程式，以使用大型資料集。

HDInsight 實作運用提供自動 sharding 表格的讀取和寫入，和自動容錯移轉強式一致性 HBase 擴充架構。 效能增強記憶體內快取的讀取和寫入的串流的最高-處理量。 虛擬網路佈建的時，也可使用 HDInsight HBase。 如需詳細資訊，請參閱[Azure 虛擬網路上的佈建 HDInsight 叢集] [hbase-provision-vnet]。

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>資料管理中 HDInsight HBase 的方式是？

可使用 HBase 在管理資料`create`， `get`， `put`，及`scan`HBase 命令介面從命令。 使用資料寫入到資料庫`put`並閱讀使用`get`。 `scan`命令用來取得從表格中的多個資料列的資料。 資料也可以使用 HBase C# API，提供用戶端文件庫的上方 HBase REST API 來管理。 也可以使用登錄區查詢 HBase 資料庫。 這些程式設計模型簡介資訊，請參閱[開始使用與中 HDInsight Hadoop HBase][hbase-get-started]。 共同處理器，也會出現，讓資料庫裝載的節點中處理資料。


## <a name="scenarios-use-cases-for-hbase"></a>案例︰ HBase 的使用案例
標準的使用案例的哪些 BigTable （以及由分機]，[HBase） 所建立的網站搜尋。 搜尋引擎建立索引，對應到包含網頁的字詞。 但還有許多 HBase 適用於其他用途情況下，有幾個的分項此區段中。

- 關鍵值存放區

    HBase 可做為索引鍵值存放區，並很適合用來管理郵件系統。 Facebook HBase 用於他們的郵件系統，而且適合用來儲存和管理網際網路通訊。 WebTable 使用 HBase 搜尋和管理的從網頁擷取資料表。

- 感應器資料

    HBase 適合用來擷取從屬參照收集來自各種來源的資料。 這包含社交分析，時間序列互動式儀表板保持在最新版本的趨勢與計數器，及稽核記錄系統管理。 範例包括 Bloomberg 商人終端機和開啟時間數列資料庫 (OpenTSDB)，儲存，並提供存取收集伺服器系統的狀況相關的指標。

- 即時查詢

    [鳳凰](http://phoenix.apache.org/)是 Apache HBase 的 SQL 查詢引擎。 存取成 JDBC 驅動程式，可讓查詢，並使用 SQL 管理 HBase 資料表。

- HBase 做為平台

    應用程式可以使用資料存放區為執行 HBase 的上方。 範例包括鳳凰、 OpenTSDB、 Kiji 及坦。 應用程式也可以使用 HBase 整合。 範例包括登錄區、 豬、 Solr、 大量、 Flume、 Impala、 火花、 Ganglia 和向下切入。


##<a name="next-steps"></a>後續步驟

- [使用 HBase Hadoop HDInsight 中使用快速入門][hbase-get-started]
- [佈建 HDInsight 叢集 Azure 虛擬網路上] [hbase-provision-vnet]
- [設定 HDInsight HBase 複寫](hdinsight-hbase-geo-replication.md)
- [分析 HBase HDInsight 中使用 Twitter 舉動][hbase-twitter-sentiment]
- [使用 Maven 建置 HDInsight (Hadoop) 搭配使用 HBase Java 應用程式][hbase-build-java-maven]

##<a name="see-also"></a>另請參閱

- [Apache HBase](https://hbase.apache.org/)
- [Bigtable: 分散式的儲存系統結構化資料](http://research.google.com/archive/bigtable.html)




[hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hbase-build-java-maven]: hdinsight-hbase-build-java-maven.md

[hdinsight-use-hive]: hdinsight-use-hive.md

[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md

[hbase-get-started]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
