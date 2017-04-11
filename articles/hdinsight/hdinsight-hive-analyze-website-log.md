<properties 
    pageTitle="Hadoop 登錄區使用的網站記錄分析 |Microsoft Azure" 
    description="瞭解如何使用登錄區 HDInsight 分析網站記錄。 您會使用記錄檔做為輸入至 HDInsight 表格，但 HiveQL 查詢資料。" 
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
    ms.date="05/17/2016" 
    ms.author="nitinme"/>

# <a name="use-hive-with-hdinsight-to-analyze-logs-from-websites"></a>使用登錄區 HDInsight 分析從網站的記錄檔

瞭解如何使用 HiveQL HDInsight 分析從網站的記錄。 網站記錄分析可用區段觀眾根據類似的活動，請將網站訪客分類的統計資料，並找出內容他們檢視、 網站所需的工作，依此類推。

在此範例中，您會使用 HDInsight 叢集，來分析網站記錄檔以取得深入了解瀏覽至網站外部網站的一天的頻率。 您也會產生網站錯誤的使用者體驗的摘要。 您將學習如何︰

- 連線至 Azure Blob 儲存體，其中包含網站記錄檔。
- 建立查詢這些記錄的登錄區資料表。
- 建立區來分析資料的查詢。
- 使用 Microsoft Excel 連線至 HDInsight （藉由使用 [開放式資料庫連接 (ODBC) 來擷取分析的資料。

![HDI。Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##<a name="prerequisites"></a>必要條件

- 您必須有佈建 Hadoop 叢集上 Azure HDInsight。 如需相關指示，請參閱[佈建 HDInsight 叢集][hdinsight-provision]。 
- 您必須在 Microsoft Excel 2013 或 Excel 2010 安裝。
- 您必須將資料匯入登錄區從 Excel 的[Microsoft 登錄區 ODBC 驅動程式](http://www.microsoft.com/download/details.aspx?id=40886)。


##<a name="to-run-the-sample"></a>若要執行的範例

1. 從[Azure 入口網站](https://portal.azure.com/)，從 Startboard （如果您已釘選那里的叢集） 中，按一下您要執行範例 [叢集] 磚。

2. 從叢集刀，在 [**快速連結**] 底下，按一下 [**叢集儀表板**，，然後從**叢集儀表板**刀中，按一下**HDInsight 叢集儀表板**。 或者，您可以使用下列 URL 直接開啟儀表板︰

        https://<clustername>.azurehdinsight.net
    
    出現提示時，使用的系統管理員使用者名稱和密碼時佈建叢集使用驗證。
  
2. 從開啟的網頁上，按一下 [**快速入門庫**] 索引標籤，然後**解決方案範例資料**類別] 下方，按一下**網站記錄分析**範例。

3. 請遵循在網頁上提供的指示，完成的範例。

##<a name="next-steps"></a>後續步驟
請嘗試下列範例︰[使用登錄區與 HDInsight 分析感應器資料](hdinsight-hive-analyze-sensor-data.md)。


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png
 
