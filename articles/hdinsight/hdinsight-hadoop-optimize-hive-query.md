<properties
   pageTitle="最佳化您的登錄區查詢中 HDInsight 更快速地執行 |Microsoft Azure"
   description="瞭解如何將登錄區查詢最佳化 Hadoop HDInsight 中。"
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/28/2015"
   ms.author="rashimg"/>


# <a name="optimize-hive-queries-for-hadoop-in-hdinsight"></a>最佳化中 HDInsight Hadoop 登錄區查詢

根據預設，Hadoop 叢集並未針對效能最佳化。 本文說明一些最常見登錄區效能最佳化的方法，您可以套用到我們的查詢。

##<a name="scale-out-worker-nodes"></a>不按比例縮放出工作者節點

自行和執行平行 reducers，可以利用增加叢集工作者節點數目。 有兩種方法可以增加延展 HDInsight 中︰

- 佈建時，您可以指定使用 Azure 入口網站、 PowerShell 的 Azure 或跨平台的命令列介面工作者節點數目。  如需詳細資訊，請參閱[佈建 HDInsight 叢集](hdinsight-provision-clusters.md)。 下列畫面顯示工作者節點設定 Azure 入口網站上︰

    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]

- 在執行階段中，您也可以擴充出叢集就必須重新建立一個。 此如下所示。
![scaleout_1][image-hdi-optimize-hive-scaleout_2]

如需不同的虛擬機器 HDInsight 所支援的詳細資訊，請參閱[HDInsight 價格](https://azure.microsoft.com/pricing/details/hdinsight/)。

##<a name="enable-tez"></a>啟用 Tez

[Apache Tez](http://hortonworks.com/hadoop/tez/)為替代執行引擎 MapReduce 引擎︰

![tez_1][image-hdi-optimize-hive-tez_1]


Tez 是速度更快，因為︰

- 以單一 MapReduce 引擎中工作的方式執行導向非循環圖 (DAG)，表示 DAG 需要自行後面一組 reducers 每組。 如此會開始每個登錄區查詢多個 MapReduce 工作。 Tez 沒有這種限制式，並可以處理複雜 DAG，因此最小化工作啟動負荷的一項工作。
- **不必要的 Avoids 撰寫**正在開始為相同的登錄區查詢 MapReduce 引擎中的多個工作，因為每項工作的輸出寫入 HDFS 中繼資料。 由於 Tez 最小化的工作可避免不必要的寫入每個登錄區查詢的數目。
- **Minimizes 啟動延遲狀況**Tez 較佳來降低初次延遲，以減少的您需要的自行也改善整個最佳化。
- **重複使用容器**每當可能 Tez 是可以重複使用容器，以確保延遲，因為啟動容器會減少。
- **連續最佳化技巧**以往最佳化完成編譯階段。 更多有關輸入資訊，不過，允許在執行階段最佳化。 Tez 使用連續最佳化技巧，讓它最佳化計劃進一步將執行階段階段。

如需這些概念的詳細資訊，請按一下[這裡](http://hortonworks.com/hadoop/tez/)

您可以進行任何的登錄區查詢 Tez 啟用前面加上字元與下列設定值的查詢︰

    set hive.execution.engine=tez;

針對 Windows 型 HDInsight 叢集，必須啟用 Tez 佈建次。 以下是使用 Tez 啟用提供 Hadoop 叢集範例 Azure PowerShell 指令碼︰

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $clusterName = "[HDInsightClusterName]"
    $location = "[AzureDataCenter]" #i.e. West US
    $dataNodes = 32 # number of worker nodes in the cluster

    $defaultStorageAccountName = "[DefaultStorageAccountName]"
    $defaultStorageContainerName = "[DefaultBlobContainerName]"
    $defaultStorageAccountKey = $defaultStorageAccountKey = Get-AzureStorageKey $defaultStorageAccountName.ToLower() | %{ $_.Primary }

    $hdiUserName = "[HTTPUserName]"
    $hdiPassword = "[HTTPUserPassword]"

    $hdiSecurePassword = ConvertTo-SecureString $hdiPassword -AsPlainText -Force
    $hdiCredential = New-Object System.Management.Automation.PSCredential($hdiUserName, $hdiSecurePassword)

    $hiveConfig = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
    $hiveConfig.Configuration = @{ "hive.execution.engine"="tez" }

    New-AzureHDInsightClusterConfig -ClusterSizeInNodes $dataNodes -HeadNodeVMSize Standard_D14 -DataNodeVMSize Standard_D14 |
    Set-AzureHDInsightDefaultStorage -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" -StorageAccountKey $defaultStorageAccountKey -StorageContainerName $defaultStorageContainerName |
    Add-AzureHDInsightConfigValues -Hive $hiveConfig |
    New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $hdiCredential

    
> [AZURE.NOTE] Linux 型 HDInsight 叢集有 Tez 預設為啟用。
    

## <a name="hive-partitioning"></a>登錄區分割

I/O 作業是主要效能瓶頸執行登錄區查詢。 如果可以減少需要讀取的資料量，可改善效能。 根據預設，登錄區查詢掃描整個登錄區資料表。 等掃描資料表，但只需要掃描少量的資料 （例如篩選查詢） 的查詢，這會建立不必要的負荷，這是絕佳的查詢。 登錄區分割，可讓登錄區查詢存取只有必要的登錄區資料表中的資料量。

登錄區分割實作有自己的目錄的位置分割為使用者定義的每一個資料分割重新組織原始資料到新的目錄。 下圖顯示 [*年份*] 欄的分割登錄區資料表。 為每年建立新的目錄。

![分割][image-hdi-optimize-hive-partitioning_1]

分割事項︰

- **執行不置中的磁碟分割**-僅限幾個值的資料行分割可能會導致少數幾份磁碟分割區。 例如分割性別上會只建立 （公與母） 建立兩個磁碟分割，因此只有縮小延遲的下半部最大值。

- **過度不分割**-上時，具有唯一值 （例如使用者識別碼） 欄中建立的資料分割會造成壓力許多叢集 namenode 為處理的目錄大量有多個資料分割。

- **避免資料扭曲**-妥善選擇您分割的索引鍵，讓所有磁碟分割區的大小。 範例會在分割*狀態*可能會導致底下，幾乎 30 加州的記錄數目 x 的 Vermont 因為母體中的差異。

若要建立的磁碟分割表格，使用*分割 By*子句︰

    CREATE TABLE lineitem_part
        (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE        STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
         L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

分割的資料表建立後，您可以建立靜態分割或動態分割。

- **靜態分割**表示必須已經 sharded 資料在適當的目錄，您可以要求登錄區磁碟分割區以手動方式根據目錄的位置。 這會顯示在下列程式碼片段。

        INSERT OVERWRITE TABLE lineitem_part
        PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
        SELECT * FROM lineitem 
        WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’

        ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
        LOCATION ‘wasbs://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'

- **動態分割**，就表示您想要自動為您建立磁碟分割區的登錄區。 由於我們已經暫存的資料表中建立分割表格，我們只需要會插入到分割資料表的資料，如下所示︰

        SET hive.exec.dynamic.partition = true;
        SET hive.exec.dynamic.partition.mode = nonstrict;
        INSERT INTO TABLE lineitem_part
        PARTITION (L_SHIPDATE)
        SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
             L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
             L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
             L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as       L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as       L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as   L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as      L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

如需詳細資訊，請參閱[分割表格](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables)。

##<a name="use-the-orcfile-format"></a>使用 ORCFile 格式

登錄區支援不同的檔案格式。 例如︰

- **文字**︰ 這是預設檔案格式，適用於大部分的案例
- **Avro**︰ 適用於互通性案例
- **ORC/Parquet**︰ 最適合效能

ORC （最佳化列分欄） 格式是相當有效的方法來儲存登錄區資料。 相較於其他格式，ORC 具有下列優點︰

- 複雜的類型，包括 DateTime 和複雜及半結構化類型的支援
- 為 [70%壓縮
- 每個 10000 列可允許略過列建立索引
- 在執行階段執行中的重要首

若要啟用 ORC 格式，您第一次建立表格具有*預存為 ORC*:

    CREATE TABLE lineitem_orc_part
        (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
         L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT     STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

接下來，您將資料 ORC 表格暫存的資料表。 例如︰

    INSERT INTO TABLE lineitem_orc
    SELECT L_ORDERKEY as L_ORDERKEY, 
           L_PARTKEY as L_PARTKEY , 
           L_SUPPKEY as L_SUPPKEY,
           L_LINENUMBER as L_LINENUMBER,
           L_QUANTITY as L_QUANTITY, 
           L_EXTENDEDPRICE as L_EXTENDEDPRICE,
           L_DISCOUNT as L_DISCOUNT,
           L_TAX as L_TAX,
           L_RETURNFLAG as L_RETURNFLAG,
           L_LINESTATUS as L_LINESTATUS,
           L_SHIPDATE as L_SHIPDATE,
           L_COMMITDATE as L_COMMITDATE,
           L_RECEIPTDATE as L_RECEIPTDATE, 
           L_SHIPINSTRUCT as L_SHIPINSTRUCT,
           L_SHIPMODE as L_SHIPMODE,
           L_COMMENT as L_COMMENT
    FROM lineitem;

您可以閱讀更多 ORC 格式[以下](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC)。

##<a name="vectorization"></a>向量化

向量化可讓您以批次 1024年放在一起，而不是一次處理一列的資料列的 [處理程序的登錄區。 這表示，因為小於內部的程式碼，必須執行簡單的作業會更快完成。

若要啟用向量化前置詞登錄區查詢的下列設定︰

    set hive.vectorized.execution.enabled = true;

如需詳細資訊，請參閱[Vectorized 查詢執行](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution)。


##<a name="other-optimization-methods"></a>其他最佳化的方法

有多個最佳化方法，您可以考慮，例如︰

- **登錄區值區︰**技術，讓叢集或區段大型資料的設定來最佳化查詢效能。
- **加入最佳化︰**的計劃以提升效率的連接，減少需要使用者提示群組的查詢執行最佳化。 如需詳細資訊，請參閱[加入最佳化](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization)。
- **增加 Reducers**

##<a id="nextsteps"></a>後續步驟
本文中，您已經學會有數種常見的登錄區查詢最佳化方法。 若要深入瞭解，請參閱下列文章︰

- [使用中 HDInsight Apache 登錄區](hdinsight-use-hive.md)
- [使用登錄區中 HDInsight 分析航班延遲情況資料](hdinsight-analyze-flight-delay-data.md)
- [Twitter 使用分析資料的 HDInsight 的登錄區](hdinsight-analyze-twitter-data.md)
- [分析感應器資料中 HDInsight Hadoop 上使用登錄區查詢主控台](hdinsight-hive-analyze-sensor-data.md)
- [使用登錄區 HDInsight 分析從網站的記錄檔](hdinsight-hive-analyze-website-log.md)


[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
