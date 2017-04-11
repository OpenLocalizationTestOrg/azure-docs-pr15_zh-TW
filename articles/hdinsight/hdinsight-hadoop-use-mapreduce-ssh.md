<properties
   pageTitle="Hadoop 中 HDInsight MapReduce 和 SSH 連線 |Microsoft Azure"
   description="瞭解如何使用 SSH 執行 HDInsight 上使用 Hadoop MapReduce 工作。"
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

# <a name="use-mapreduce-with-hadoop-on-hdinsight-with-ssh"></a>使用上與 SSH HDInsight Hadoop MapReduce

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

在本文中，您將學習如何使用安全命令介面 (SSH) 連線到 Hadoop HDInsight 叢集，然後使用 Hadoop 命令提交 MapReduce 工作。

> [AZURE.NOTE] 如果您已熟悉使用 Linux 型 Hadoop 伺服器，但您不熟悉 HDInsight，請參閱[Linux 型 HDInsight 的秘訣](hdinsight-hadoop-linux-information.md)。

##<a id="prereq"></a>必要條件

若要完成此文件中的步驟進行，您將需要下列項目︰

* Linux HDInsight (Hadoop 上 HDInsight) 叢集

* SSH 用戶端。 Linux、 Unix 及 Mac 作業系統應該了 SSH 用戶端。 Windows 使用者必須下載的用戶端，例如[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)」。

##<a id="ssh"></a>使用 SSH 連線

連線至的完整的網域名稱 (FQDN) 的 HDInsight 叢集使用 SSH] 命令。 FQDN 會叢集，後面再加上的名稱**。 azurehdinsight.net**。 例如，下列會連線到名稱為**myhdinsight**叢集︰

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**如果您提供 SSH 驗證憑證金鑰**建立 HDInsight 叢集時，您可能需要在用戶端系統，指定私密金鑰的位置，例如︰

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**如果您提供 SSH 驗證密碼，**您建立 HDInsight 叢集時，您必須提供出現提示時的密碼。

如需有關如何使用 HDInsight SSH 的詳細資訊，請參閱[使用 SSH Linux 為基礎的 Hadoop Linux 與 OS X Unix 從 HDInsight 上使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

###<a name="putty-windows-clients"></a>PuTTY （Windows 用戶端）

Windows 不提供的內建的 SSH 用戶端。 我們建議使用**PuTTY**，可以從[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)下載。

如需有關如何使用 PuTTY 的詳細資訊，請參閱[使用 SSH Linux 為基礎的 Hadoop HDInsight 從 Windows 上使用](hdinsight-hadoop-linux-use-ssh-windows.md)。

##<a id="hadoop"></a>使用 Hadoop 命令

1. 您連線到 HDInsight 叢集之後，請使用下列**Hadoop**命令來啟動 MapReduce 工作︰

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

    此舉會啟動**wordcount**課程，其包含**hadoop-mapreduce-examples.jar**檔案中。 使用**wasbs://example/data/gutenberg/davinci.txt**文件中，輸入與輸出儲存在**wasbs: / 範例/資料/WordCountOutput**。

    > [AZURE.NOTE] 如需有關此 MapReduce 工作和範例資料的詳細資訊，請參閱[使用 MapReduce 上 HDInsight Hadoop 中](hdinsight-use-mapreduce.md)。

2. 處理，並會傳回資訊類似下列作業完成後，該工作會發出詳細資料︰

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. 工作完成後，請使用下列命令] 清單中的儲存於**wasbs://example/data/WordCountOutput**輸出檔案︰

        hdfs dfs -ls wasbs:///example/data/WordCountOutput

    這將會顯示兩個檔案、 **_SUCCESS**和**組件-r-00000**。 **組件-r-00000**檔案中包含此工作的輸出效果。

    > [AZURE.NOTE] 某些 MapReduce 工作可能分割跨多個**部分-r # # #**檔案的結果。 如果是這樣，使用 # # # 後置字元，表示檔案的順序。

4. 若要檢視輸出，請使用下列命令︰

        hdfs dfs -cat wasbs:///example/data/WordCountOutput/part-r-00000

    隨後便會顯示一份**wasbs://example/data/gutenberg/davinci.txt**檔案與每個單字發生次數中所含的文字。 將檔案中包含資料的範例如下︰

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

##<a id="summary"></a>摘要

如您所見，Hadoop 命令會提供輕鬆 HDInsight 叢集執行 MapReduce 工作，然後檢視 [工作成果。

##<a id="nextsteps"></a>後續步驟

如需在 HDInsight MapReduce 工作的一般資訊︰

* [使用 HDInsight Hadoop MapReduce](hdinsight-use-mapreduce.md)

有關的其他方法您可以使用 Hadoop HDInsight 上︰

* [使用上 HDInsight Hadoop 登錄區](hdinsight-use-hive.md)

* [Hadoop HDInsight 上搭配使用的豬](hdinsight-use-pig.md)
