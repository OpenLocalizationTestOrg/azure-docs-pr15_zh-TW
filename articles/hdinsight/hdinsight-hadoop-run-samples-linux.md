<properties
    pageTitle="執行 Hadoop MapReduce 範例 Linux 型 HDInsight |Microsoft Azure"
    description="開始使用 Linux 型 HDInsight MapReduce 範例。 使用 SSH 連線至叢集，然後使用 [Hadoop] 命令以執行範例工作。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="larryfr"/>




#<a name="run-the-hadoop-samples-in-hdinsight"></a>HDInsight 中執行 Hadoop 範例

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Linux 型 HDInsight 叢集提供一組可供您熟悉執行 Hadoop MapReduce 工作的 MapReduce 範例。 在此文件中，您可以進一步瞭解可用的範例，並逐步執行其中一些。

##<a name="prerequisites"></a>必要條件

- **Azure 訂閱**︰ 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

- **Linux 型 HDInsight 叢集**︰ 請參閱[使用 Hadoop 登錄區 linux HDInsight 中使用快速入門](hdinsight-hadoop-linux-tutorial-get-started.md)

- **SSH 用戶端**︰ SSH 使用 HDInsight 的詳細資訊，請參閱下列文章︰

    - [使用上 HDInsight Linux、 Unix，或 OS X 的 Linux 型 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [使用上從 Windows HDInsight Linux 為基礎的 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a name="the-samples"></a>範例 ##

**位置**︰ 範例位於**/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar** HDInsight 叢集

**內容**︰ 下列範例會包含在此封存中︰

- **aggregatewordcount**: 彙總基礎的計算中輸入檔案的單字地圖/減少程式
- **aggregatewordhist**: 彙總以減少地圖/程式計算中輸入檔案的文字的直方圖
- **bbp**︰ 地圖/減少使用的程式 Bailey-Borwein-Plouffe 來計算 Pi 的確切的位數
- **dbcount**︰ 計算 pageview 記錄儲存在範例工作資料庫
- **distbbp**︰ 使用 BBP 輸入公式來計算 Pi 的確切的位元的地圖/減少程式
- **grep**︰ 地圖/減少程式計算 regex 輸入中的相符項目
- **聯結**︰ 聯結會影響工作超過排序、 平均分割資料集
- **multifilewc**︰ 計算多個檔案中的字數的工作
- **pentomino**︰ 地圖/減少磚如此程式尋找 pentomino 問題的解決方案
- **pi**︰ 地圖/減少程式來估計 Pi 使用半票蒙地卡羅方法
- **randomtextwriter**︰ 撰寫 10 GB 每個節點的隨機文字資料的地圖/減少程式
- **randomwriter**︰ 地圖/減少程式撰寫 10 GB 的每個節點的隨機資料
- **secondarysort**︰ 定義縮減次要排序範例
- **排序**︰ 地圖/減少程式排序隨機作者所撰寫的資料
- **獨**︰ 獨規劃求解
- **teragen**︰ 產生 terasort 的資料
- **terasort**︰ 執行 terasort
- **teravalidate**︰ 檢查 terasort 的結果
- **wordcount**︰ 地圖/減少程式的計算中輸入檔案的單字
- **wordmean**︰ 計算平均中輸入檔案的文字長度地圖/減少程式
- **wordmedian**︰ 地圖/減少程式的計算中位數長度中輸入檔案的文字
- **wordstandarddeviation**︰ 計算標準差的輸入檔案中的文字長度地圖/減少程式

**程式碼**︰ **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples** HDInsight 叢集中包含這些範例原始程式碼

> [AZURE.NOTE] `2.2.4.9-1`路徑中 HDInsight 叢集，Hortonworks 資料平台的版本，並更新 HDInsight 時，可能會變更。

## <a name="how-to-run-the-samples"></a>如何執行範例 ##

1. 連線至 HDInsight 使用 SSH 下列文章中所述︰

    - [使用上 HDInsight Linux、 Unix，或 OS X 的 Linux 型 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [使用上從 Windows HDInsight Linux 為基礎的 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 從`username@#######:~$`提示，請使用下列命令] 清單中的範例︰

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

    這會產生範例的清單從這份文件的上一節。

3. 使用下列命令以取得說明在特定的範例。 在此例中**wordcount**範例︰

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount

    您應該會收到下列訊息︰

        Usage: wordcount <in> [<in>...] <out>

    這表示您可以提供的來源文件數個輸入的路徑。 最終路徑是位置儲存輸出 （來源文件中的字數計數）。

4. 您可以使用下列計算筆記本的達文西，以提供叢集做為範例資料中的所有文字︰

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount

    輸入此工作會從**wasbs:///example/data/gutenberg/davinci.txt**讀取。

    輸出的這個範例中就會儲存在**wasbs: / 範例/資料/davinciwordcount**。

    > [AZURE.NOTE] 如 wordcount 範例說明中所述，您也可以指定多個輸入的檔案。 例如，`hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount`會計算 davinci.txt 與 ulysses.txt 中的文字。

5. 工作完成後，使用下列命令來檢視輸出︰

        hdfs dfs -cat /example/data/davinciwordcount/*

    串連所有作業，產生的輸出檔案，並顯示它們。 此範例中基本有只有一個檔案，但如果沒有這個命令會逐一查看所有其他。

    輸出非常類似下列動作︰

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    每一列代表文字及輸入資料中發生的次數。

## <a name="sudoku"></a>獨

獨範例具有有點沒有幫助的使用方式的指示進行。「 包含在命令列拼圖 」。

[獨](https://en.wikipedia.org/wiki/Sudoku)是邏輯拼圖組成的九個 3x3 方格。 在格線中部分儲存格有數字，其他人是空白的而目標是要解決空白的儲存格。 上述的連結有更多有關拼圖，但這個範例的目的是要解決空白的儲存格。 因此我們輸入應該是以下列格式的檔案︰

- 九個資料列的九個資料行

- 每個資料行中包含數字或`?`（表示空白儲存格）

- 以空格分隔的儲存格

有特定的方式建構獨謎題;您無法重複欄或列中的數字。 已正確建立 HDInsight 叢集上有範例。 它位於**/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta** ，並包含下列項目︰

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

> [AZURE.NOTE] `2.2.4.9-1`路徑部分可能會變更為 HDInsight 叢集會更新。

若要執行此獨範例，請使用下列命令︰

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/2.2.9.1-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta

結果看起來應該類似下列動作︰

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi-"></a>Pi （π）

Pi 範例使用統計 (半票蒙地卡羅) 來估計 pi 的值的方法。 隨機放在為單位的點方形也落在該方塊中有與區域的圓形，等於機率圓形 pi/4。 Pi 的值可估計值的 4R，其中 R 是要在方塊中的點總數圓圈的點的數目的值。 使用點的樣本越大越估計值是。

此範例中的對應程式會產生隨機 eml 單位正方形點的數字，然後圓圈的這些點的個數。

減壓器然後累積點自行來計算，來估計從公式 4R，其中 R 是點到在方塊中的點總數圓圈計算的數字的比率 pi 的值。

若要執行此範例使用下列命令。 此使用 16 地圖 10000000 樣本來估計 pi 的值︰

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000

此所傳回的值應該類似**3.14159155000000000000**。 Pi 的前 10 個小數位數是 3.1415926535 供您參考。

##<a name="10gb-greysort"></a>10 GB Greysort

GraySort 是事先排序其度量是達成時排序有大量的資料，通常 100 TB 最小排序工資率 （TB/分鐘）。

這個範例使用簡單的 10 GB 的資料，使其可以相當快速地執行。 它會使用開發 Owen O'Malley 和 Arun Murthy 贏得 0.578 TB min (100 TB 173 分鐘) 的速度 2009 年的年度的一般用途 (「 daytona 」) tb 排序事先 MapReduce 應用程式。 如需有關這項問題和其他排序基準的詳細資訊，請參閱[Sortbenchmark](http://sortbenchmark.org/)網站。

此範例使用三組 MapReduce 程式︰

- **TeraGen**: MapReduce 程式產生要排序的資料列

- **TeraSort**︰ 範例輸入的資料，並使用 MapReduce 成總順序排序資料

    TeraSort 是標準的 MapReduce 函數，除了使用 N-1 取樣索引鍵的定義每個縮減的金鑰範圍排序的清單自訂 partitioner 排序。 特別是所有這類的按鍵範例 [i-1] < = 鍵 < 範例 [i] 會傳送給減少 i。 以下提供的輸出減少 i 是否都小於的輸出減少 i + 1。

- **TeraValidate**︰ 驗證輸出全域排序 MapReduce 程式

    在輸出目錄中，建立一個對應每個檔案，每一個對應可確保每個機碼小於或等於前一個。 地圖函數也會產生記錄的第一個和最後一個索引鍵的每個檔案，然後縮減函數可確保檔案 i 的第一個索引鍵大於檔案 i-1 的最後一個索引鍵。 任何問題與鍵順序的報告為縮減成果。

使用下列步驟來產生資料排序，然後驗證輸出︰

1. 產生的資料，儲存在 HDInsight 叢集的預設儲存空間的 10 GB **wasbs: / 範例/資料/10 GB 排序-輸入**:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input

    `-Dmapred.map.tasks`告訴 Hadoop 多少的地圖作業，才能使用這項作業。 最終的兩個參數指示建立值得 10 GB 的資料，並將它在儲存的工作**wasbs: / 範例/資料/10 GB 排序-輸入**。

2. 您可以使用下列命令來排序資料︰

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output

    `-Dmapred.reduce.tasks`會告訴 Hadoop 多少減少用於工作的工作。 最終的兩個參數是只輸入與輸出位置的資料。

3. 您可以使用下列驗證排序所產生的資料︰

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate

##<a name="next-steps"></a>後續步驟 ##

本文中，從您學到如何執行 Linux 型 HDInsight 叢集隨附的範例。 瞭解使用 HDInsight 豬登錄區，與 MapReduce 教學課程，請參閱下列主題︰

* [使用上 HDInsight Hadoop 的豬][hdinsight-use-pig]
* [使用上 HDInsight Hadoop 登錄區][hdinsight-use-hive]
* [使用上 HDInsight Hadoop MapReduce] [hdinsight-use-mapreduce]



[hdinsight-errors]: hdinsight-debug-jobs.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
