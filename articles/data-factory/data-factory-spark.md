<properties 
    pageTitle="呼叫 [從 Azure 資料工廠火花程式" 
    description="瞭解如何叫用從使用 MapReduce 活動 Azure 資料工廠火花程式。" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="spelluru"/>

# <a name="invoke-spark-programs-from-data-factory"></a>呼叫 [從資料工廠火花程式
## <a name="introduction"></a>簡介
您可以使用資料工廠管道 MapReduce 活動 HDInsight 火花叢集上執行火花程式。 閱讀本文前使用活動的詳細資訊，請參閱[MapReduce 活動](data-factory-map-reduce.md)文件。 

## <a name="spark-sample-on-github"></a>在 GitHub 火花範例
[火花-GitHub 資料工廠範例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark)會示範如何使用 MapReduce 活動叫用火花程式。 火花程式只將資料從一個 Azure Blob 容器到另一個。 

## <a name="data-factory-entities"></a>資料工廠實體
**火花-ADF/src/ADFJsons**資料夾包含的資料工廠實體 （連結的服務、 資料集，管道的郵件） 的檔案。  

在這個範例中有兩個**連結的服務**︰ Azure 儲存體和 Azure HDInsight。 指定 [ **StorageLinkedService.json**和 clusterUri、 使用者名稱和密碼在**HDInsightLinkedService.json**中的 [Azure 儲存體名稱和關鍵值]。

在這個範例中有兩個**資料集**︰ **input.json**和**output.json**。 這些檔案位於 [**資料集**] 資料夾中。  這些檔案代表 MapReduce 活動的輸入與輸出資料集

您可以找到範例管線**ADFJsons/管道的郵件**] 資料夾中。 檢閱了解如何使用 MapReduce 活動叫用火花程式管線。 

叫用**com.adf.sparklauncher.jar** **adflibs**容器中 Azure 儲存空間 （指定的 StorageLinkedService.json） 中設定 MapReduce 活動。 此程式的程式碼位於火花-ADF/src/主要/java/com/adf/資料夾和其通話火花送出，並執行火花工作。 

> [AZURE.IMPORTANT] 
> 閱讀[讀我檔案。TXT](https://github.com/Azure/Azure-DataFactory/blob/master/Samples/Spark/README.txt)最新和其他資訊，再使用範例。 
>  
> 使用此方法的 HDInsight 火花叢集叫用火花程式使用 MapReduce 活動。 不支援使用視 HDInsight 叢集。   


## <a name="see-also"></a>另請參閱
- [登錄區活動](data-factory-hive-activity.md)
- [豬活動](data-factory-pig-activity.md)
- [MapReduce 活動](data-factory-map-reduce.md)
- [Hadoop 串流活動](data-factory-hadoop-streaming-activity.md)
- [呼叫 R 指令碼](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
