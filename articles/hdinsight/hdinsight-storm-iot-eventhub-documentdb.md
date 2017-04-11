<properties
 pageTitle="車輛感應器資料 Apache 大量 HDInsight 上的 [處理程序 |Microsoft Azure"
 description="瞭解如何從 HDInsight 上使用 Apache 大量的事件集線器車輛感應器資料 [處理程序。 新增模型資料從 DocumentDB，以及儲存輸出至儲存空間。"
 services="hdinsight,documentdb,notification-hubs"
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
ms.date="08/23/2016"
ms.author="larryfr"/>

#<a name="process-vehicle-sensor-data-from-azure-event-hubs-using-apache-storm-on-hdinsight"></a>從 Azure HDInsight 上使用 Apache 大量的事件集線器車輛感應器資料 [處理程序

瞭解如何從 HDInsight 上使用 Apache 大量 Azure 事件集線器車輛感應器資料 [處理程序。 本範例會從 Azure 事件集線器讀取感應器資料、 豐富的資料來參照 Azure DocumentDB 中儲存的資料，最後將資料儲存成 Azure 儲存體使用 Hadoop 檔案系統 (HDFS)。

![HDInsight] 和 [網際網路的項目 (IoT) 架構圖表](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

##<a name="overview"></a>概觀

新增車輛感應器可讓您預測根據歷史資料趨勢設備問題，以及改善使用圖樣分析所根據的未來版本。 傳統 MapReduce 批次處理可以使用這項分析，您必須能夠快速且有效率地將資料載入所有車輛從 Hadoop MapReduce 處理發生之前。 此外，您可以執行的要徑失敗路徑 （引擎溫度、 煞車等） 的即時分析。

Azure 事件集線器內建處理大量感應器所產生的資料量，在 HDInsight Apache 大量可以載入並處理資料之前將其儲存到 HDFS （Azure 儲存備份） 用於其他 MapReduce 處理程序。

##<a name="solution"></a>解決方案

遙測資料引擎溫度、 環境溫度和車輛速度記錄感應器，然後傳送給事件集線器以及汽車的車輛識別號碼輛和時間戳記。 從這裡 HDInsight 叢集上 Apache 大量上執行大量拓撲讀取資料、 程序，並將它儲存到 HDFS。

在處理期間，VIN 用來擷取中 Azure DocumentDB 的模型資訊。 這會新增至資料流，再將其儲存。

使用大量拓撲元件如下︰

* **EventHubSpout** -從 Azure 事件集線器讀取資料

* **TypeConversionBolt** -字串轉換成 JSON 從事件集線器包含引擎溫度、 環境的溫度、 速度、 VIN 和時間戳記的個別資料值，表示 tuple

* **DataReferencBolt** -從 DocumentDB 使用 VIN 查閱車輛模型

* **WasbStoreBolt** -儲存資料以 HDFS （Azure 儲存體）

以下是本解決方案的圖表︰

![大量拓撲](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

> [AZURE.NOTE] 這是一個簡單的圖表，並在方案中的每個元件都可能有多個執行個體。 例如，拓撲中每個元件的多個執行個體分佈 HDInsight 叢集上大量的節點。

##<a name="implementation"></a>實作

完成，自動解決方案，這種情況下是可用上 GitHub [HDInsight-大量-範例](https://github.com/hdinsight/hdinsight-storm-examples)存放庫的一部分。 若要使用此範例中，請遵循[IoTExample 讀我檔案中的步驟進行。會員](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md)。

## <a name="next-steps"></a>後續步驟

更多範例大量拓撲，請參閱[在 HDInsight 大量的範例拓撲](hdinsight-storm-example-topology.md)。
