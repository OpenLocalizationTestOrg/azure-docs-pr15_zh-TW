<properties
   pageTitle="MapReduce 和 Hadoop HDInsight 在使用遠端桌面 |Microsoft Azure"
   description="瞭解如何使用遠端桌面連線至 Hadoop HDInsight 上，並執行 MapReduce 工作。"
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
   ms.date="09/27/2016"
   ms.author="larryfr"/>

# <a name="use-mapreduce-in-hadoop-on-hdinsight-with-remote-desktop"></a>在使用遠端桌面 HDInsight Hadoop 中使用 MapReduce

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

在本文中，您將學習如何使用遠端桌面連線到 Hadoop HDInsight 叢集上並使用 [Hadoop] 命令，以執行 MapReduce 工作。

##<a id="prereq"></a>必要條件

若要完成此文件中的步驟進行，您將需要下列項目︰

* Windows 型 HDInsight (Hadoop 上 HDInsight) 叢集

* 執行 Windows 10、 Windows 8 或 Windows 7 的用戶端電腦

##<a id="connect"></a>使用遠端桌面連線

啟用遠端桌面 HDInsight 叢集，然後連線到其在[連線至 HDInsight 叢集使用 RDP](hdinsight-administer-use-management-portal.md#rdp)的指示執行。

##<a id="hadoop"></a>使用 [Hadoop] 命令

當您連線到 HDInsight 叢集桌面時，請使用下列步驟使用 [Hadoop] 命令來執行 MapReduce 工作︰

1. 從 HDInsight 桌面，開始**Hadoop 命令列**。 這會開啟新的 「 命令提示字元中**c:\apps\dist\hadoop-&lt;版本號碼 >**目錄。

    > [AZURE.NOTE] Hadoop 更新時，就會變更版本數目。 若要尋找路徑可**HADOOP_HOME**環境變數。 例如，`cd %HADOOP_HOME%`變更目錄 Hadoop 目錄，而不需要知道的版本號碼。

2. 若要使用 [ **Hadoop** ] 命令來執行範例 MapReduce 工作，使用下列命令︰

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/WordCountOutput

    此舉會啟動**wordcount**課程，其包含在目前的目錄**hadoop-mapreduce-examples.jar**檔案中。 使用**wasbs://example/data/gutenberg/davinci.txt**文件中，輸入與輸出儲存在︰ **wasbs: / 範例/資料/WordCountOutput**。

    > [AZURE.NOTE] 如需有關此 MapReduce 工作和範例資料的詳細資訊，請參閱<a href="hdinsight-use-mapreduce.md">使用 MapReduce HDInsight Hadoop 中</a>。

2. 工作處理程序，並會傳回資訊類似下面的工作完成時，會發出詳細資料︰

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. 在工作完成時，使用下列命令清單儲存在**wasbs://example/data/WordCountOutput**的輸出檔案︰

        hadoop fs -ls wasbs:///example/data/WordCountOutput

    這將會顯示兩個檔案、 **_SUCCESS**和**組件-r-00000**。 **組件-r-00000**檔案中包含此工作的輸出效果。

    > [AZURE.NOTE] 某些 MapReduce 工作可能分割跨多個**部分-r # # #**檔案的結果。 如果是這樣，使用 # # # 後置字元，表示檔案的順序。

4. 若要檢視輸出，請使用下列命令︰

        hadoop fs -cat wasbs:///example/data/WordCountOutput/part-r-00000

    隨後便會顯示一份**wasbs://example/data/gutenberg/davinci.txt**檔案，以及每個單字發生次數中所包含的文字。 將檔案中包含資料的範例如下︰

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

##<a id="summary"></a>摘要

如您所見，Hadoop] 命令會提供輕鬆 HDInsight 叢集上執行 MapReduce 工作，然後檢視 [工作成果。

##<a id="nextsteps"></a>後續步驟

如需在 HDInsight MapReduce 工作的一般資訊︰

* [使用 HDInsight Hadoop MapReduce](hdinsight-use-mapreduce.md)

有關的其他方法您可以使用 Hadoop HDInsight 上︰

* [使用上 HDInsight Hadoop 登錄區](hdinsight-use-hive.md)

* [Hadoop HDInsight 上搭配使用的豬](hdinsight-use-pig.md)
