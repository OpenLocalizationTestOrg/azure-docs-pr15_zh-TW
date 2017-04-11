<properties
   pageTitle="使用遠端桌面 HDInsight Hadoop 豬 |Microsoft Azure"
   description="瞭解如何使用 [豬命令來執行遠端桌面連線到 Windows 型 Hadoop HDInsight 豬拉丁陳述式。"
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

#<a name="run-pig-jobs-from-a-remote-desktop-connection"></a>執行遠端桌面連線的豬工作

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

這份文件會提供逐步解說，使用豬命令執行遠端桌面連線到 Windows 型 HDInsight 叢集豬拉丁陳述式。 豬拉丁可讓您建立 MapReduce 應用程式說明資料轉換，而不是對應，並減少函數。

在此文件，瞭解如何

##<a id="prereq"></a>必要條件

若要完成此文件中的步驟進行，您需要下列項目。

* Windows 型 HDInsight (Hadoop 上 HDInsight) 叢集

* 執行 Windows 10、 Windows 8 或 Windows 7 的用戶端電腦

##<a id="connect"></a>使用遠端桌面連線

啟用遠端桌面 HDInsight 叢集，然後連線到其在[連線至 HDInsight 叢集使用 RDP](hdinsight-administer-use-management-portal.md#rdp)的指示執行。

##<a id="pig"></a>使用 [豬] 命令

2. 遠端桌面連線之後，請在桌面上使用圖示開始**Hadoop 命令列**。

2. 使用下列命令以啟動豬] 命令︰

        %pig_home%\bin\pig

    您將會看到與`grunt>`提示。

3. 輸入下列陳述式︰

        LOGS = LOAD 'wasbs:///example/data/sample.log';

    這個命令，將記錄檔載入 sample.log 檔案的內容。 您可以使用下列命令，以檢視檔案的內容︰

        DUMP LOGS;

4. 套用規則運算式，每一筆記錄中擷取記錄層次轉換的資料︰

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    您可以使用**傾印**轉換之後，檢視資料。 在此情況下， `DUMP LEVELS;`。

5. 繼續使用下列陳述式中套用轉換。 使用`DUMP`若要檢視的每個步驟後轉換結果。

    <table>
    <tr>
    <th>陳述式</th><th>功能</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = 篩選層級的 LOGLEVEL 不是 null。</td><td>移除包含 null 值的記錄層次的列，並將結果儲存到 FILTEREDLEVELS。</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = 群組 FILTEREDLEVELS 以 LOGLEVEL;</td><td>分組的記錄層級的資料列，並將結果儲存到 GROUPEDLEVELS。</td>
    </tr>
    <tr>
    <td>頻率 = foreach GROUPEDLEVELS 產生群組為 LOGLEVEL，COUNT (FILTEREDLEVELS。LOGLEVEL) 計算。</td><td>建立一組新的資料包含每個唯一的記錄檔的層級的值] 和 [次數，就會發生。 這會儲存在頻率</td>
    </tr>
    <tr>
    <td>結果 = 順序來計算遞減; 的頻率</td><td>依計數 （遞減） 訂單的記錄層次，並將儲存成結果</td>
    </tr>
    </table>

6. 您也可以使用儲存的轉換結果`STORE`陳述式。 例如，下列命令儲存`RESULT` **/example/data/pigout**目錄叢集的預設存放容器中︰

        STORE RESULT into 'wasbs:///example/data/pigout'

    > [AZURE.NOTE] 資料會儲存於檔案命名**組件 nnnnn**中指定的目錄。 如果目錄已經存在，您會收到錯誤訊息。

7. 若要結束步兵提示時，輸入下列陳述式。

        QUIT;

###<a name="pig-latin-batch-files"></a>豬拉丁批次檔案

您也可以執行的豬拉丁檔案中所包含的使用豬] 命令。

3. 結束之後步兵提示時，開啟**[記事本]**並建立名為**pigbatch.pig** **%pig_home%**目錄中的新檔案。

4. 輸入或貼上下列幾行**pigbatch.pig**檔案，並將其儲存︰

        LOGS = LOAD 'wasbs:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

5. 使用下列命令以執行**pigbatch.pig**檔案時使用的豬] 命令。

        pig %PIG_HOME%\pigbatch.pig

    批次工作完成時，您應該會看到下列的輸出中，應使用相同`DUMP RESULT;`在先前的步驟︰

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="summary"></a>摘要

如您所見，豬命令可讓您互動的方式執行 MapReduce 作業，或執行批次檔案中儲存的豬拉丁工作。

##<a id="nextsteps"></a>後續步驟

如需豬 HDInsight 中的一般資訊︰

* [Hadoop HDInsight 上搭配使用的豬](hdinsight-use-pig.md)

有關的其他方法您可以使用 Hadoop HDInsight 上︰

* [使用上 HDInsight Hadoop 登錄區](hdinsight-use-hive.md)

* [使用上 HDInsight Hadoop MapReduce](hdinsight-use-mapreduce.md)
