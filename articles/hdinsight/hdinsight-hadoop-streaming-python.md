<properties
   pageTitle="開發 HDInsight Python MapReduce 工作 |Microsoft Azure"
   description="瞭解如何建立和執行 Python MapReduce 工作 Linux 型 HDInsight 叢集上。"
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
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="develop-python-streaming-programs-for-hdinsight"></a>開發 Python 的 HDInsight 串流程式

Hadoop 提供串流的 API MapReduce 可讓您撰寫地圖，並減少 Java 以外的語言功能。 在本文中，您將學習如何使用 Python 執行 MapReduce 作業。

> [AZURE.NOTE] 而 Python 程式碼，在這份文件中的使用與 Windows 型 HDInsight 叢集，這份文件中的步驟會有特定 Linux 型叢集。

本文根據資訊與範例發佈 Michael Noll 撰寫[Python Hadoop MapReduce 程式](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/)而定。

##<a name="prerequisites"></a>必要條件

若要完成此文件中的步驟進行，您將需要下列項目︰

* HDInsight 叢集上 Linux 型 Hadoop

* 在文字編輯器

    > [AZURE.IMPORTANT] 在文字編輯器必須使用 LF 作為結尾行。 若使用 CRLF，會導致錯誤 Linux 型 HDInsight 叢集上執行 MapReduce 工作時。 如果您不確定，請[執行 MapReduce](#run-mapreduce)區段中使用選擇性的步驟，將任何 CRLF 轉換 LF。

* 適用於 Windows 用戶端，PuTTY 和 PSCP。 這些公用程式，可從<a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">PuTTY 下載頁面</a>。

##<a name="word-count"></a>字數統計

例如，您將使用的對應程式和減壓器實作基本字數統計。 對應句子分成個別的單字，減壓器彙總字數，並產生輸出計算。

以下流程圖顯示發生地圖和減少階段的項目。

![減少對應圖例](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

##<a name="why-python"></a>為什麼 Python 嗎？

Python 是用途的高層級的程式設計語言，可讓您快速較少的幾行程式碼比許多其他語言的概念。 具有最近已成為常用資料科學家原型語言與因為其解譯的自然動態輸入，請與典雅語法使其適合快速開發應用程式。

Python 已安裝所有 HDInsight 叢集上。

##<a name="streaming-mapreduce"></a>串流 MapReduce

Hadoop 可讓您可以指定包含地圖的檔案，並減少邏輯所使用的工作。 地圖的特定需求，並減少邏輯是︰

* **輸入**︰ 地圖，並減少元件必須從 STDIN 讀取輸入的資料。

* **成果**︰ 地圖，並減少元件必須寫入 STDOUT 的輸出資料。

* **資料格式**︰ consumed 和所產生的資料必須是金鑰/值組，並以 tab 字元分隔。

Python 讀取 STDIN 使用**系統**模組，並使用來列印 STDOUT 的 [**列印**，可以輕鬆地處理這些需求。 剩餘任務只要設定格式的資料] 索引標籤 (`\t`) 字元之間索引鍵和值。

##<a name="create-the-mapper-and-reducer"></a>建立的對應程式及減壓器

對應程式及減壓器會在此案例**mapper.py**和**reducer.py**文字檔案 （為了方便的在做什麼）。 您可以建立這些使用您所選擇的編輯器。

###<a name="mapperpy"></a>Mapper.py

建立新的檔名為**mapper.py**並使用下列程式碼做為內容︰

    #!/usr/bin/env python

    # Use the sys module
    import sys

    # 'file' in this case is STDIN
    def read_input(file):
        # Split each line into words
        for line in file:
            yield line.split()

    def main(separator='\t'):
        # Read the data using read_input
        data = read_input(sys.stdin)
        # Process each words returned from read_input
        for words in data:
            # Process each word
            for word in words:
                # Write to STDOUT
                print '%s%s%d' % (word, separator, 1)

    if __name__ == "__main__":
        main()

花點時間閱讀程式碼，以便瞭解功能。

###<a name="reducerpy"></a>Reducer.py

建立新的檔名為**reducer.py**並使用下列程式碼做為內容︰

    #!/usr/bin/env python

    # import modules
    from itertools import groupby
    from operator import itemgetter
    import sys

    # 'file' in this case is STDIN
    def read_mapper_output(file, separator='\t'):
        # Go through each line
        for line in file:
            # Strip out the separator character
            yield line.rstrip().split(separator, 1)

    def main(separator='\t'):
        # Read the data using read_mapper_output
        data = read_mapper_output(sys.stdin, separator=separator)
        # Group words and counts into 'group'
        #   Since MapReduce is a distributed process, each word
        #   may have multiple counts. 'group' will have all counts
        #   which can be retrieved using the word as the key.
        for current_word, group in groupby(data, itemgetter(0)):
            try:
                # For each word, pull the count(s) for the word
                #   from 'group' and create a total count
                total_count = sum(int(count) for current_word, count in group)
                # Write to stdout
                print "%s%s%d" % (current_word, separator, total_count)
            except ValueError:
                # Count was not a number, so do nothing
                pass

    if __name__ == "__main__":
        main()

##<a name="upload-the-files"></a>上傳檔案

**Mapper.py**和**reducer.py**都必須先在叢集的我們可以執行。 以上傳這些檔案的最簡單方法是使用**scp** (**pscp**如果您使用的 Windows 用戶端)。

從用戶端，在相同的目錄**mapper.py**和**reducer.py**，使用下列命令。 **使用者名稱**取代 SSH 使用者和**clustername**叢集的名稱。

    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:

這會從本機系統複製檔案到主節點。

> [AZURE.NOTE] 如果您是使用密碼保護 SSH 帳號，系統會提示您輸入密碼。 如果您使用 SSH 鍵，您可能必須使用`-i`參數和私密金鑰，例如，路徑`scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`。

##<a name="run-mapreduce"></a>執行 MapReduce

1. 連線到叢集使用 SSH:

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] 如果您是使用密碼保護 SSH 帳號，系統會提示您輸入密碼。 如果您使用 SSH 鍵，您可能必須使用`-i`參數和私密金鑰，例如，路徑`ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`。

2. （選用）如果您使用的文字編輯器使用 CRLF 作為結束時建立的 mapper.py 和 reducer.py 檔案，或執行不知道什麼行尾編輯器使用，請使用下列，命令以轉換 LF 的 CRLF mapper.py 和 reducer.py 中的項目。

        perl -pi -e 's/\r\n/\n/g' mappery.py
        perl -pi -e 's/\r\n/\n/g' reducer.py

2. 您可以使用下列命令來啟動 MapReduce 工作。

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input wasbs:///example/data/gutenberg/davinci.txt -output wasbs:///example/wordcountout

    這個命令包含下列部分︰

    * **hadoop streaming.jar**︰ 執行串流 MapReduce 作業時使用。 它所提供的外部 MapReduce 程式碼介面 Hadoop。

    * **-檔案**︰ 指定的檔案所需的 MapReduce 工作，及他們應該複製到所有的工作者節點的就是告訴 Hadoop。

    * **的對應程式**︰ 說明 Hadoop 用來作為的對應程式的檔案。

    * **-減壓器**︰ 告訴 Hadoop 檔案作為減壓器。

    * **-輸入**︰ 從文字以我們應該計算輸入的檔案。

    * **-輸出**︰ 輸出寫入的目錄。

        > [AZURE.NOTE] 此目錄會建立工作。

您應該看到一堆**資訊**陳述式工作一開始，以與最後的**地圖**和**減少**操作以百分比顯示。

    15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

完成時，您會收到工作的狀態資訊。

##<a name="view-the-output"></a>檢視輸出

在工作完成時，使用下列命令來檢視輸出︰

    hdfs dfs -text /example/wordcountout/part-00000

這將會顯示清單的文字和數字發生。 以下是範例的輸出資料︰

    wrenching       1
    wretched        6
    wriggling       1
    wrinkled,       1
    wrinkles        2
    wrinkling       2

##<a name="next-steps"></a>後續步驟

現在您已經學會如何使用 HDInsight 串流 MapRedcue 工作，使用下列連結以探索 Azure HDInsight 所使用的其他方法。

* [使用 HDInsight 的登錄區](hdinsight-use-hive.md)
* [使用 HDInsight 的豬](hdinsight-use-pig.md)
* [使用 HDInsight MapReduce 工作](hdinsight-use-mapreduce.md)
