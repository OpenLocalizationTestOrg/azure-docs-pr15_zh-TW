<properties 
    pageTitle="資料轉換︰ 程序及轉換資料 |Microsoft Azure" 
    description="瞭解如何轉換資料或程序中的資料使用 Hadoop、 電腦學習或 Azure 資料湖分析 Azure 資料工廠。" 
    keywords="資料轉換的程序資料轉換轉換活動的資料"
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/23/2016" 
    ms.author="shlo"/>

# <a name="transform-data-in-azure-data-factory"></a>轉換 Azure 資料工廠中的資料
> [AZURE.SELECTOR]
[登錄區](data-factory-hive-activity.md)  
[豬](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop 串流](data-factory-hadoop-streaming-activity.md)
[電腦學習](data-factory-azure-ml-batch-execution-activity.md) 
[預存程序](data-factory-stored-proc-activity.md)
[資料湖分析 U SQL](data-factory-usql-activity.md)
[.NET 自訂](data-factory-use-custom-activities.md)
   

## <a name="overview"></a>概觀 
本文說明您可以使用轉換，並將預測和深入資訊處理您的原始資料 Azure 資料工廠資料轉換活動。 轉換活動執行中的計算的環境，例如 Azure HDInsight 叢集或 Azure 批次。 它會將文件的連結提供每個轉換活動的詳細資訊。
 
資料工廠支援下列資料轉換的活動可以新增至[管線](data-factory-create-pipelines.md)可以個別或鏈結與另一個活動。

> [AZURE.NOTE] 如需逐步解說的逐步指示，請參閱[建立管線與登錄區轉換](data-factory-build-your-first-pipeline.md)文件。  

## <a name="hdinsight-hive-activity"></a>HDInsight 登錄區活動
HDInsight 登錄區活動資料工廠管道跟您自己的登錄區查詢或視/Linux windows HDInsight 叢集。 如需此活動詳細資訊，請參閱[登錄區活動](data-factory-hive-activity.md)文件。 

## <a name="hdinsight-pig-activity"></a>HDInsight 豬活動
HDInsight 豬活動資料工廠管道跟您自己的豬查詢或視/Linux windows HDInsight 叢集。 如需此活動詳細資訊，請參閱[豬活動](data-factory-pig-activity.md)文件。 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce 活動
在資料工廠管線 HDInsight MapReduce 活動執行上您自己的 MapReduce 程式或視/Linux windows HDInsight 叢集。 如需此活動詳細資訊，請參閱[MapReduce 活動](data-factory-map-reduce.md)文件。

您可以使用 MapReduce 活動 HDInsight 火花叢集上執行火花程式。 如需詳細資訊，請參閱[從 Azure 資料工廠叫用火花程式](data-factory-spark.md)。

## <a name="hdinsight-streaming-activity"></a>HDInsight 串流活動
HDInsight 串流活動資料工廠管道執行 Hadoop 串流您自己的程式或視/Linux windows HDInsight 叢集。 如需此活動詳細資訊，請參閱[HDInsight 串流活動](data-factory-hadoop-streaming-activity.md)。

## <a name="machine-learning-activities"></a>電腦學習活動
Azure 資料工廠可讓您輕鬆建立使用已發佈的 Azure 電腦學習 web 服務的預測狀況分析的管線。 Azure 資料工廠管道使用[批次執行的活動](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity)，您可以叫用電腦學習 web 服務進行預測批次中的資料。

一段時間，需要使用新的輸入資料集重新培訓計分實驗電腦學習預測的模型。 您使用重新訓練完之後，要 retrained 電腦學習模型更新計分的 web 服務。 您可以使用[更新的資源活動](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity)新訓練模型更新 web 服務。  

如需這些電腦學習活動詳細資訊，請參閱[使用電腦學習活動](data-factory-azure-ml-batch-execution-activity.md)。 

## <a name="stored-procedure-activity"></a>預存程序活動
您可以使用 SQL Server 預存程序活動中的資料工廠管線叫用其中下列資料存放區中的預存程序︰ Azure SQL 資料庫，Azure SQL Data Warehouse，在您的企業版中的 SQL Server 資料庫或 Azure VM。 如需詳細資訊，請參閱[預存程序活動](data-factory-stored-proc-activity.md)文件。  

## <a name="data-lake-analytics-u-sql-activity"></a>資料湖分析 U SQL 活動
資料湖分析 U SQL 活動 Azure 資料湖分析叢集上執行 U SQL 指令碼。 如需詳細資訊，請參閱[資料分析 U SQL 活動](data-factory-usql-activity.md)文件。 

## <a name="net-custom-activity"></a>.NET 自訂活動
如果您要轉換的方式，不支援的資料工廠的資料，您可以使用您自己的資料處理邏輯建立自訂的活動，並使用管道的活動。 您可以設定為使用 Azure 批次服務或 Azure HDInsight 叢集來執行自訂的.NET 活動。 如需詳細資訊，請參閱[使用自訂活動](data-factory-use-custom-activities.md)文件。 

您可以建立自訂的活動，以執行安裝 R HDInsight 叢集 R 指令碼。 請參閱[執行 R 指令碼使用 Azure 資料工廠](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)。 

## <a name="compute-environments"></a>計算環境
您建立的計算環境的連結的服務，並定義轉換活動時，然後使用連結的服務。 有兩種類型的受支援的資料工廠計算環境。 

1. **視**︰ 在此情況下，計算環境完全管理資料工廠。 它會自動建立資料工廠服務之前工作程序資料送出，而工作完成時，移除。 您可以設定，並控制細微視需要計算環境設定作業執行、 叢集管理及啟動動作。 
2. **將您自己**︰ 在此情況下，您可以註冊您自己的電腦環境 （例如 HDInsight 叢集） 作為連結資料工廠服務。 計算環境由您管理及資料工廠服務所使用的是它執行的活動。 

若要深入瞭解，請參閱[計算連結服務](data-factory-compute-linked-services.md)文章計算資料工廠支援服務。 


## <a name="summary"></a>摘要
Azure 資料工廠支援下列資料轉換活動和計算環境的活動。 轉換活動可以新增管線可以個別或鏈結與另一個活動。

資料轉換活動 |  計算環境 
:----------------------- | :--------------------
[登錄區](data-factory-hive-activity.md) | HDInsight [Hadoop] 
[豬](data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop 串流](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[電腦學習活動︰ 批次執行和更新資源](data-factory-azure-ml-batch-execution-activity.md) | Azure VM 
[預存程序](data-factory-stored-proc-activity.md) | Azure SQL、 Azure SQL Data Warehouse 或 SQL Server |
[資料湖分析 U SQL](data-factory-usql-activity.md) | Azure 資料湖狀況分析 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] 或 Azure 批次
   

