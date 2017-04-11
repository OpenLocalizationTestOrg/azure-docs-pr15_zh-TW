<properties
    pageTitle="瞭解什麼是登錄區並瞭解如何使用 HiveQL |Microsoft Azure"
    description="深入了解 Apache 登錄區並瞭解如何使用 Hadoop HDInsight 中。 選擇如何執行您的登錄區工作，並使用 HiveQL 分析的範例 Apache log4j 檔案。"
    keywords="hiveql，功能區"
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
    ms.date="09/19/2016"
    ms.author="larryfr"/>

# <a name="use-hive-and-hiveql-with-hadoop-in-hdinsight-to-analyze-a-sample-apache-log4j-file"></a>使用登錄區及 HiveQL Hadoop HDInsight 中要分析的範例 Apache log4j 檔案

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]


在本教學課程中，您會學習如何使用 [HDInsight 中的 [Apache 登錄區 Hadoop 中，選擇 [群組工作的執行方式。 您也可以瞭解 HiveQL 和分析的範例 Apache log4j 檔案。

##<a id="why"></a>什麼是登錄區及為何使用它？
[Apache 登錄區](http://hive.apache.org/)是 Hadoop，讓使用 HiveQL （SQL 類似的查詢語言） 的資料摘要、 查詢與資料分析資料倉庫系統。 若要以互動方式探索資料，或建立可重複使用的批次處理工作，可登錄區。

登錄區可讓您大致非結構化資料上的專案結構。 定義的結構之後，您可以使用登錄區來查詢沒有瞭解 Java 或 MapReduce 該資料。 **HiveQL**（登錄區查詢語言） 可讓您撰寫類似 T SQL 陳述式的查詢。

登錄區瞭解如何使用結構化及半結構化資料，例如欄位，以特定字元分隔的文字檔案。 登錄區也支援複雜或不規則結構化資料的自訂**序列化 deserializers (SerDe)** 。 如需詳細資訊，請參閱[如何使用自訂的 JSON SerDe 與 HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx)。

## <a name="user-defined-functions-udf"></a>使用者定義函數 (UDF)

登錄區也可以延伸透過**使用者定義函數 (UDF)**。 UDF 可讓您在 HiveQL 實作功能或不輕鬆地建立模型的邏輯。 使用登錄區 Udf 的範例，請參閱下列內容︰

* [使用 Java 使用者定義函數登錄區](hdinsight-hadoop-hive-java-udf.md)

* [使用 Python 登錄區與 HDInsight 的豬](hdinsight-python.md)

* [使用 C# 登錄區與 HDInsight 的豬](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [如何將自訂的登錄區 UDF 新增至 HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [若要將日期/時間格式轉換登錄區加上時間戳記的自訂登錄區 UDF 範例](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="hive-internal-tables-vs-external-tables"></a>登錄區內部資料表與外部資料表

有幾個必須知道的內部的登錄區資料表及外部表格的項目︰

- [**建立表格**] 命令會建立內部的資料表。 預設容器中必須位於資料檔案。
- [**建立表格**] 命令將資料檔案移至 /hive/倉庫/<TableName>資料夾。
- **建立外部表格**] 命令會建立外部的資料表。 資料檔可以位於預設容器之外。
- **建立外部表格**命令不會移動的資料檔案。
- **建立外部表格**命令並不允許位置中的任何資料夾。 這是為什麼教學課程可讓您 sample.log 檔案的複本的原因。

如需詳細資訊，請參閱[HDInsight︰ 登錄區內部和外部表格簡介][cindygross-hive-tables]。


##<a id="data"></a>範例資料，而 Apache log4j 檔案的相關資訊

此範例使用儲存在您 blob 儲存體容器中的**/example/data/sample.log** *log4j*範例檔案。 在檔案的每個記錄檔組成一行] 欄位包含`[LOG LEVEL]`欄位，以顯示類型及嚴重性，例如︰

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

在上一個範例中，記錄層級會是錯誤。

> [AZURE.NOTE] 可以也使用[Apache Log4j](http://en.wikipedia.org/wiki/Log4j)記錄工具會產生 log4j 檔案，然後將該檔案上傳到 blob 容器。 如需相關指示，請參閱[上傳至 HDInsight 的資料](hdinsight-upload-data.md)。 如需有關如何 Azure Blob 儲存體使用 HDInsight 的詳細資訊，請參閱[使用與 HDInsight Azure Blob 儲存體](hdinsight-hadoop-use-blob-storage.md)。

範例資料會儲存於 Azure Blob 儲存體，即為預設檔案系統的 HDInsight 使用。 HDInsight 都可以存取儲存在 blob 中使用**wasb**前置字元的檔案。 例如，若要存取 sample.log 檔案，您會使用下列語法︰

    wasbs:///example/data/sample.log

因為 Azure Blob 儲存體 HDInsight 的預設儲存空間，您也可以使用從 HiveQL **/example/data/sample.log**存取檔案。

> [AZURE.NOTE] 語法， **wasbs: / /**，會用來存取儲存在您的 HDInsight 叢集的預設儲存容器中的檔案。 如果您指定額外儲存空間帳戶，當您佈建叢集，而且您想要存取儲存在這些帳戶中的檔案時，您可以指定的容器名稱和儲存帳戶地址，例如，來存取資料**wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**。

##<a id="job"></a>範例工作︰ 專案的資料行拖曳至分隔的資料

下列 HiveQL 陳述式會到分隔的資料中所儲存的專案資料行**wasbs: / 範例/資料**目錄︰

    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

在上一個範例中，HiveQL 陳述式，請執行下列動作︰

* __設定 hive.execution.engine=tez;__: 設定為使用 Tez 執行引擎。 使用 Tez，而不 MapReduce 可以提供增加查詢效能。 如需有關 Tez 的詳細資訊，請參閱[使用 Apache Tez 以改善效能](#usetez)] 區段。

    > [AZURE.NOTE] 此陳述式，才需使用 Windows 型 HDInsight 叢集; 時Tez 是 Linux 型 HDInsight 的預設執行引擎。

* **卸除表格**︰ 刪除資料表和資料檔案，如果資料表已經存在。
* **建立外部表格**︰ 登錄區中建立新的**外部**資料表。 外部資料表只會將表格定義儲存在登錄區;資料會保留在原始位置，然後以原始格式。
* **列格式**︰ 資料設定格式的方式就是告訴登錄區。 在此案例中，以空格分隔每個記錄檔中的欄位。
* **儲存另存新檔 TEXTFILE 位置**︰ 會告訴登錄區資料的位置儲存 （範例/資料目錄），並將其儲存為文字。 可以是一個檔案中的資料，或分散到目錄內的多個檔案。
* **選取**︰ 選取資料行**t4**其中包含**[錯誤]**的值的所有資料列的計數。 有三個資料列包含此值，因為這應該會傳回**3**的值。
* **例如 '%.log' INPUT__FILE__NAME** -會告訴登錄區我們應該只傳回從檔案中的資料。 記錄。 這將搜尋限制 sample.log 檔案包含資料，並從 [從其他範例資料檔案不符合我們所定義的結構描述，傳回資料就會保存為。

> [AZURE.NOTE] 當您預期的基礎資料更新外部來源，例如自動化的資料上傳程序，或另一個 MapReduce 作業，而您會想要使用的最新資料的登錄區查詢時，應使用外部表格。
>
> 卸除外部表格會**無法**刪除的資料，它只會刪除表格定義。

建立外部表格之後, 下列陳述式用來建立**內部**表格。

    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs
    SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

這些陳述式執行下列動作︰

* **建立資料表 IF 不存在**︰ 建立表格，如果不存在。 不使用**外部**關鍵字，因為這是內部的資料表，就會儲存在登錄區資料倉庫，並登錄區會完全管理。
* **儲存另存新檔 ORC**︰ 最佳化列欄 (ORC) 格式儲存資料。 這是高度最佳化，且有效率格式來儲存登錄區資料。
* **插入覆寫...選取 [**︰ **[錯誤]**，然後再**種**表格中插入資料**log4jLogs**表格中選取的資料列。

> [AZURE.NOTE] 與外部表格拖放在內部表格也會刪除基礎資料。

##<a id="usetez"></a>使用 Apache Tez 以改善效能

[Apache Tez](http://tez.apache.org)是架構，讓資料密集應用程式，例如登錄區，在 [縮放比例更有效率地執行。 HDInsight 的最新版本，登錄區支援 Tez 上執行。 Tez Linux 型 HDInsight 叢集預設為啟用。

> [AZURE.NOTE] Tez 目前 」 預設為關閉 Windows 型 HDInsight 叢集，必須先啟用。 若要利用 Tez，以下的值必須為登錄區查詢︰
>
> ```set hive.execution.engine=tez;```
>
>這可以提交每個查詢為基礎的將它放在您的查詢的開頭。 您也可以設定為在叢集上的預設設定的設定值，當您建立叢集。 您可以在[佈建 HDInsight 叢集](hdinsight-provision-clusters.md)，以找到更多詳細資料。

[登錄區 Tez 設計文件](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez)包含數字的實作選項和調整設定的詳細資訊。

若要協助偵錯工作使用 Tez，HDInsight 提供下列網站使用者介面，可讓您檢視 Tez 工作的詳細資料︰

* [使用 Windows 型 HDInsight Tez UI](hdinsight-debug-tez-ui.md)

* [使用 Linux 型 HDInsight Ambari Tez 檢視](hdinsight-debug-ambari-tez-view.md)

##<a id="run"></a>選擇如何執行 HiveQL 工作

HDInsight 都可以執行 HiveQL 工作使用各種不同的方法。 決定哪一種方法是最適合您，請使用下表，然後依照逐步解說中的連結]。

| 如果您要，**使用此**...                                                     | ....an**互動式**命令介面 | ...**批次處理** | ....with**叢集作業系統** | ....from**用戶端作業系統** |
|:--------------------------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [登錄區檢視](hdinsight-hadoop-use-hive-ambari-view.md) | ✔ | ✔ | Linux | 任何 （瀏覽器為基礎） |
| [Beeline] 命令 （SSH 工作階段）](hdinsight-hadoop-use-hive-beeline.md)                                         |              ✔              |            ✔            | Linux                                     | Linux、 Unix、 Mac OS X 或 Windows        |
| [登錄區] 命令 （SSH 工作階段）](hdinsight-hadoop-use-hive-ssh.md)                                         |              ✔              |            ✔            | Linux                                     | Linux、 Unix、 Mac OS X 或 Windows        |
| [捲曲](hdinsight-hadoop-use-hive-curl.md)                                       |           &nbsp;            |            ✔            | Linux 或 Windows                          | Linux、 Unix、 Mac OS X 或 Windows        |
| [查詢主控台](hdinsight-hadoop-use-hive-query-console.md)                     |           &nbsp;            |            ✔            | Windows                                   | 任何 （瀏覽器為基礎）                            |
| [Visual Studio HDInsight 工具](hdinsight-hadoop-use-hive-visual-studio.md) |           &nbsp;            |            ✔            | Linux 或 Windows                          | Windows                                  |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md)                   |           &nbsp;            |            ✔            | Linux 或 Windows                          | Windows                                  |
| [遠端桌面](hdinsight-hadoop-use-hive-remote-desktop.md)                   |              ✔              |            ✔            | Windows                                   | Windows                                  |

## <a name="running-hive-jobs-on-azure-hdinsight-using-on-premises-sql-server-integration-services"></a>使用內部部署的 SQL Server 整合服務 Azure HDInsight 上執行的工作區

您也可以使用 SQL Server 整合服務 (SSIS) 執行登錄區工作。 Ssis Azure Feature Pack 提供下列元件的登錄區工作處理 HDInsight。


- [Azure HDInsight 登錄區工作][hivetask]
- [Azure 訂閱連線管理員][connectionmanager]


深入瞭解 Azure Feature Pack ssis[以下][ssispack]。


##<a id="nextsteps"></a>後續步驟

現在您已經學會登錄區的功能，以及如何使用 Hadoop HDInsight 中，使用下列連結探索 Azure HDInsight 所使用的其他方法。


- [上傳至 HDInsight 的資料][hdinsight-upload-data]
- [使用 HDInsight 的豬][hdinsight-use-pig]
- [使用 HDInsight Sqoop](hdinsight-use-sqoop.md)
- [使用 HDInsight Oozie](hdinsight-use-oozie.md)
- [使用 HDInsight MapReduce 工作][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-hive/hdi.checkmark.png

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-get-started.md

[Powershell-install-configure]: ../powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
