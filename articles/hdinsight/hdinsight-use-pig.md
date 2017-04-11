<properties
   pageTitle="HDInsight 中使用 Hadoop 豬 |Microsoft Azure"
   description="瞭解如何使用 Hadoop 上 HDInsight 豬。"
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
   ms.date="09/14/2016"
   ms.author="larryfr"/>

# <a name="use-pig-with-hadoop-on-hdinsight"></a>Hadoop HDInsight 上搭配使用的豬

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

[Apache 豬](http://pig.apache.org/)是使用稱為*豬拉丁*程序語言建立 Hadoop 程式的平台。 豬 Java 另一種建立*MapReduce*解決方案的和皆隨附於 Azure HDInsight。

本文中，您將學習如何使用豬與 HDInsight。

##<a id="why"></a>為什麼要使用的豬？

使用 MapReduce Hadoop 中處理資料的挑戰實作您處理邏輯使用只地圖和縮減函數。 進行複雜的處理，您通常需要處理分成多個會鏈結的 MapReduce 作業以達到所要的結果。

而非強制您使用只對應，並減少函數，豬可讓您定義為一系列資料來產生所需的輸出流向的轉換的處理。

豬拉丁語言可讓您用來說明資料流的原始輸入，透過一或多個轉換，以產生所需的輸出。 豬拉丁程式，請遵循此一般模式︰

- **載入**︰ 讀取操作檔案系統中的資料
- **轉換**︰ 操作的資料
- **傾印] 或 [市集**︰ 輸出至畫面的資料，或將其儲存為處理

豬拉丁也支援使用者定義函數 (UDF)，可讓您叫用實作難以模型中的豬拉丁邏輯的外部元件。

如需有關豬拉丁的詳細資訊，請參閱[豬拉丁參照手動 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html)和[豬拉丁參照手動 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html)。

如需使用的豬 Udf 的範例，請參閱下列文件︰

* [使用 DataFu 與 HDInsight 的豬](hdinsight-hadoop-use-pig-datafu-udf.md)DataFu 是由 Apache 維護的實用 Udf 的集合

* [使用 Python 豬與中 HDInsight 的登錄區](hdinsight-python.md)

* [使用 C# 登錄區與 HDInsight 的豬](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

##<a id="data"></a>範例資料的相關資訊

此範例使用儲存在您 blob 儲存體容器中的**/example/data/sample.log** *log4j*範例檔案。 在檔案的每個記錄檔組成一行] 欄位包含`[LOG LEVEL]`欄位，以顯示類型及嚴重性，例如︰

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

在上一個範例中，記錄層級會是錯誤。

> [AZURE.NOTE] 可以也使用[Apache Log4j](http://en.wikipedia.org/wiki/Log4j)記錄工具會產生 log4j 檔案，然後將該檔案上傳到您 blob。 如需相關指示，請參閱[上傳至 HDInsight 的資料](hdinsight-upload-data.md)。 如需有關如何 blob Azure 儲存體中的搭配使用的是 HDInsight 的詳細資訊，請參閱[使用與 HDInsight Azure Blob 儲存體](hdinsight-hadoop-use-blob-storage.md)。

範例資料會儲存於 Azure Blob 儲存體，即為預設檔案系統使用 Hadoop 叢集的 HDInsight。 HDInsight 都可以存取儲存在 blob 中使用**wasb**前置字元的檔案。 例如，若要存取 sample.log 檔案，您會使用下列語法︰

    wasbs:///example/data/sample.log

因為 WASB HDInsight 的預設儲存空間，您也可以使用從豬拉丁**/example/data/sample.log**存取檔案。

> [AZURE.NOTE] 語法， **wasbs: / /**，會用來存取儲存在您的 HDInsight 叢集的預設儲存容器中的檔案。 如果您指定額外儲存空間帳戶，當您佈建叢集，而且您想要存取儲存在這些帳戶中的檔案時，您可以存取資料指定的容器名稱和儲存帳戶地址，例如︰ **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**。


##<a id="job"></a>範例工作

下列豬拉丁工作載入**sample.log**檔案從 HDInsight 叢集預設儲存空間。 再執行一系列中的每個記錄層級發生輸入資料的次數計數結果的轉換。 結果會將 STDOUT 傾印。

    LOGS = LOAD 'wasbs:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

下圖顯示每個轉換什麼資料的分析。

![轉換的圖形化表示][image-hdi-pig-data-transformation]

##<a id="run"></a>執行豬拉丁工作

HDInsight 可以執行的豬拉丁工作使用各種不同的方法。 決定哪一種方法是最適合您，請使用下表，然後依照逐步解說中的連結]。

| 如果您要，**使用此**...                                   | ....an**互動式**命令介面 | ...**批次處理** | ....with**叢集作業系統** | ....from**用戶端作業系統** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-pig-ssh.md)                        |              ✔              |            ✔            | Linux                                     | Linux、 Unix、 Mac OS X 或 Windows        |
| [捲曲](hdinsight-hadoop-use-pig-curl.md)                      |           &nbsp;            |            ✔            | Linux 或 Windows                          | Linux、 Unix、 Mac OS X 或 Windows        |
| [Hadoop.NET SDK](hdinsight-hadoop-use-pig-dotnet-sdk.md) |           &nbsp;            |            ✔            | Linux 或 Windows                          | Windows （適用於現在）                        |
| [Windows PowerShell](hdinsight-hadoop-use-pig-powershell.md)  |           &nbsp;            |            ✔            | Linux 或 Windows                          | Windows                                  |
| [遠端桌面](hdinsight-hadoop-use-pig-remote-desktop.md)  |              ✔              |            ✔            | Windows                                   | Windows                                  |


## <a name="running-pig-jobs-on-azure-hdinsight-using-on-premises-sql-server-integration-services"></a>使用內部部署的 SQL Server 整合服務 Azure HDInsight 上執行的豬工作

您也可以執行的豬工作使用 SQL Server 整合服務 (SSIS)。 Ssis Azure Feature Pack 提供下列元件的豬工作處理 HDInsight。


- [Azure HDInsight 豬工作][pigtask]
- [Azure 訂閱連線管理員][connectionmanager]


深入瞭解 Azure Feature Pack ssis[以下][ssispack]。


##<a id="nextsteps"></a>後續步驟

現在您已經學會如何使用 HDInsight 豬，使用下列連結以探索 Azure HDInsight 所使用的其他方法。

* [上傳至 HDInsight 的資料][hdinsight-upload-data]
* [使用 HDInsight 的登錄區][hdinsight-use-hive]
* [使用 HDInsight Sqoop](hdinsight-use-sqoop.md)
* [使用 HDInsight Oozie](hdinsight-use-oozie.md)
* [使用 HDInsight MapReduce 工作][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-pig/hdi.checkmark.png

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: ../powershell-install-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png
