<properties
 pageTitle="範例 Apache 大量拓撲上 HDInsight |Microsoft Azure"
 description="建立和測試上 HDInsight 包括基本 C# 和 Java 拓撲，並使用事件集線器 Apache 大量的範例大量拓撲的清單。"
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
 ms.date="08/23/2016"
 ms.author="larryfr"/>

# <a name="example-storm-toplogies-and-components-for-apache-storm-on-hdinsight"></a>範例大量 toplogies 和上 HDInsight Apache 大量元件

以下是範例建立和維護 microsoft 用於 Apache 大量 HDInsight 上的清單。 這些範例包含各種不同的主題，從建立基本 C# 和 Java 拓撲使用 Azure 服務，例如事件集線器、 DocumentDB，Power BI、 SQL 資料庫、 HBase HDInsight，和 Azure 儲存體。 部分範例也會示範如何使用非 Azure 或甚至非 Microsoft 技術，例如 SignalR 和 Socket.IO

| 描述                                                                                             | 示範                                         | 語言/架構         |
|:--------------------------------------------------------------------------------------------------------|:-----------------------------------------------------|:---------------------------|
| [從 Apache 大量寫 Azure 資料湖存放區](hdinsight-storm-write-data-lake-store.md) | 撰寫 Azure 資料湖存放區 | Java |
| [事件中心 Spout 和螺栓來源](https://github.com/apache/storm/tree/master/external/storm-eventhubs) | 事件中心 Spout 和螺栓來源 | Java |
| [開發上 HDInsight Apache 大量 java 拓撲][5797064f]                                 | Maven                                                | Java                       |
| [為 Apache 大量上 HDInsight 使用 Visual Studio 開發 C# 拓撲][16fce2d1]                     | Visual Studio HDInsight 工具                    | C# Java                   |
| [建立多個資料流在 C# 大量拓撲][ec5a4064]                                         | 多個資料流                                     | C#                         |
| [決定 Twitter 上 HDInsight 大量與趨勢的主題][3c86c7c8]                                   | 戟                                              | Java 戟              |
| [從 Azure 事件集線器大量 HDInsight (C#) 上與處理程序事件][844d1d81]                                | 事件集線器                                           | C# 和 Java                |
| [從 Azure 事件集線器大量 HDInsight (Java) 上與處理程序事件](hdinsight-storm-develop-java-event-hub-topology.md) | 事件集線器 | Java |
| [使用 Power Bi 以視覺化方式呈現資料的大量拓撲][94d15238]                              | Power BI                                             | C#                         |
| [分析大量與 HBase 中 HDInsight 感應器資料][ab894747]                                     | 事件集線器，HBase，Socket.IO Web 儀表板          | C#，Java，JavaScript] HTML |
| [處理從 HDInsight 上使用大量的事件集線器車輛感應器資料][246ee964]                        | 事件集線器，DocumentDb，儲存 Azure Blob (WASB)    | C# Java                   |
| [擷取、 轉換及載入 (ETL) 從 Azure 事件集線器至 HBase，HDInsight 上使用大量][b4b68194] | 事件集線器 HBase                                    | C#                         |
| [Azure HDInsight 上大量服務所使用的範本 C# 大量拓撲專案][ce0c02a2]  | 事件集線器，DocumentDb，SQL 資料庫]，[HBase，SignalR | C# Java                   |
| [從 Azure HDInsight 上使用大量的事件集線器閱讀延展性基準][d6c540e3]           | 郵件處理量，事件集線器 SQL 資料庫         | C# Java                   |
| [建立事件上 HDInsight 使用大量與 HBase 之間的關係](hdinsight-storm-correlation-topology.md) | HBase | C# |
| [使用大量上 HDInsight Python](hdinsight-storm-develop-python-topology.md) | Java 與 Clojure Python 元件以大量拓撲 | Python |

## <a name="next-steps"></a>後續步驟

* [在 HDInsight Apache 大量快速入門][2b8c3488]

* [瞭解如何部署及管理大量 HDInsight 上使用大量拓撲][6eb0d3b8]

  [2b8c3488]: hdinsight-apache-storm-tutorial-get-started-linux.md "瞭解如何建立大量 HDInsight 叢集上，並使用大量儀表板部署範例拓撲。"
  [6eb0d3b8]: hdinsight-storm-deploy-monitor-topology.md "瞭解如何部署及管理拓撲 Visual Studio 中用於網頁大量儀表板和大量使用者介面或 HDInsight 工具。"
  [16fce2d1]: hdinsight-storm-develop-csharp-visual-studio-topology.md "瞭解如何建立 C# 大量拓撲 Visual studio 使用 HDInsight 工具。"
  [5797064f]: hdinsight-storm-develop-java-topology.md "瞭解如何建立大量拓撲 Java，使用 Maven，藉由建立基本 wordcount 拓撲中。"
  [94d15238]: hdinsight-storm-power-bi-topology.md "示範如何寫入 Power BI 中的資料，從 C# 拓撲，然後建立資料的圖表和儀表板。"
  [ec5a4064]: https://github.com/Blackmist/csharp-storm-example "示範基本的大量拓撲執行 wordcount，實作 C#。這也會示範如何建立 C# 拓撲內的多個資料流。"
  [844d1d81]: hdinsight-storm-develop-csharp-event-hub-topology.md "瞭解如何讀取和寫入 HDInsight 從 Azure 事件集線器大量的資料。"
  [ab894747]: hdinsight-storm-sensor-data-analysis.md "瞭解如何使用 Apache 大量上 HDInsight 程序從 Azure 事件集線器感應器資料時，使用 D3.js，以視覺化方式呈現，以及 （選擇性） 將其儲存至 HBase。"
  [3c86c7c8]: hdinsight-storm-twitter-trending.md "瞭解如何建立大量拓撲決定趨勢主題 （根據 hashtags，） 使用戟 Twitter 上。"
  [246ee964]: hdinsight-storm-iot-eventhub-documentdb.md "從 Azure DocumentDB 瞭解如何使用大量拓撲讀取郵件從 Azure 事件集線器，閱讀的文件的參考資料，並將資料儲存至 Azure 儲存體。"
  [d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "若要從 Azure 事件集線器讀取並儲存到 SQL 資料庫 HDInsight 上使用 Apache 大量示範處理量的多個拓撲。"
  [b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "瞭解如何讀取 Azure 事件集線器，彙總的資料與轉換的資料，然後將其儲存至 HBase HDInsight 上。"
  [ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "此專案包含 spouts、 螺栓與拓撲互動各種 Azure 服務，例如事件集線器、 DocumentDB，與 SQL 資料庫範本。"
 
