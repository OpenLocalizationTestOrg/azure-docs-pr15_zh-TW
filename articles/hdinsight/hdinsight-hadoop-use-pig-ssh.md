<properties
   pageTitle="使用 SSH 使用 Hadoop Pig HDInsight 叢集上 |Microsoft Azure"
   description="了解如何連線至 Linux 型 Hadoop 叢集與 SSH，然後使用 Pig 命令 Pig 拉丁陳述式以互動方式執行，或以批次工作。"
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

#<a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Linux 基礎叢集 Pig 命令 (SSH) 上執行 Pig 工作

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

在本文件您會逐步完成連接至 Linux 式 Azure HDInsight 叢集使用安全殼層 (SSH)，然後使用 Pig 命令以 Pig 拉丁陳述式以互動方式執行，或以批次工作的程序。

Pig 拉丁程式設計語言可讓您以描述套用至輸入資料產生所需的輸出的轉換。

> [AZURE.NOTE] 如果您已熟悉使用 Linux 型 Hadoop 伺服器，但卻 HDInsight 的新功能，請參閱[Linux 型 HDInsight 秘訣](hdinsight-hadoop-linux-information.md)。

##<a id="prereq"></a>必要條件

若要完成本文中的步驟，您需要下列項目。

* Linux 型 HDInsight (Hadoop 上 HDInsight) 叢集。

* SSH 用戶端。 Linux 且 Unix、 Mac OS 應隨附的 SSH 用戶端。 Windows 使用者必須先下載的用戶端，例如[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

##<a id="ssh"></a>連線與 SSH

使用 SSH 命令的完整的網域名稱 (FQDN) 來 HDInsight 叢集中的連線。 FQDN 將是已授與該叢集，然後名稱**。 azurehdinsight.net**。 例如，下列會連線至名為**myhdinsight**叢集。

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**如果您提供 SSH 驗證憑證索引鍵**時所建立的 HDInsight 叢集，您可能需要用戶端系統上指定的私密金鑰的位置。

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**如果您提供 SSH 驗證的密碼**時建立 HDInsight 叢集中，您將需要提供當系統提示您輸入密碼。

如需使用 SSH 搭配 HDInsight 的詳細資訊，請參閱[使用 SSH Linux 型 Hadoop 從 Linux、 OS X 和 Unix HDInsight 上使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

###<a name="putty-windows-based-clients"></a>PuTTY （Windows 型用戶端）

Windows 不提供的內建的 SSH 用戶端。 我們建議使用**PuTTY**，可從[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)下載。

如需使用 PuTTY 的詳細資訊，請參閱[使用 SSH 與 Linux 式 Hadoop HDInsight 從 Windows 上](hdinsight-hadoop-linux-use-ssh-windows.md)。

##<a id="pig"></a>使用 Pig 命令

2. 一旦連線，請使用下列命令啟動 Pig 命令列介面 (CLI)。

        pig

    後可用，您應該會看見`grunt>`提示。

3. 輸入下列陳述式。

        LOGS = LOAD 'wasbs:///example/data/sample.log';

    此命令會將 sample.log 檔案的內容載入記錄。 您可以使用下列檢視檔案的內容。

        DUMP LOGS;

4. 下一步] 套用規則從每筆記錄擷取記錄層級使用下列運算式轉換的資料。

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    您可以使用**傾印**在轉換之後檢視資料。 在此例中，使用`DUMP LEVELS;`。

5. 繼續使用下列陳述式來套用轉換。 使用`DUMP`檢視轉換之後的每個步驟的結果。

    <table>
    <tr>
    <th>陳述式</th><th>及其作用</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = LOGLEVEL 來篩選層級不是 null;</td><td>會移除包含 null 值的記錄層級的資料列並將結果儲存至 FILTEREDLEVELS。</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = 群組 FILTEREDLEVELS 由 LOGLEVEL;</td><td>群組記錄層級所列並將結果儲存至 GROUPEDLEVELS。</td>
    </tr>
    <tr>
    <td>頻率 = foreach GROUPEDLEVELS 產生群組為 LOGLEVEL、 計數 (FILTEREDLEVELS。LOGLEVEL) 計算;</td><td>建立一組新資料包含每一個唯一的記錄檔的層級的值與多少時間它會發生。 這儲存的頻率。</td>
    </tr>
    <tr>
    <td>結果 = 訂單計數 desc; 的頻率</td><td>依計數 （遞減） 訂單的記錄等級，並將儲存到結果。</td>
    </tr>
    </table>

6. 您也可以使用儲存的轉換結果`STORE`陳述式。 例如，下列儲存`RESULT`您叢集預設儲存容器**/example/data/pigout**目錄。

        STORE RESULT into 'wasbs:///example/data/pigout';

    > [AZURE.NOTE] 資料會儲存在名為**部分 nnnnn**檔案中指定的目錄。 如果目錄已經存在，您會收到錯誤。

7. 若要結束步兵提示字元處，輸入下列陳述式。

        QUIT;

###<a name="pig-latin-batch-files"></a>Pig 拉丁批次檔案

您也可以使用 Pig 命令執行檔案中所包含的 Pig 拉丁文。

3. 之後結束步兵提示，使用下列命令以管道 STDIN 到名為**pigbatch.pig**csv 檔案。 此檔案都會在您已登入 SSH 工作階段之帳戶的主目錄中。

        cat > ~/pigbatch.pig

4. 輸入或貼上下列幾行，然後再使用 Ctrl + D 完成。

        LOGS = LOAD 'wasbs:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

5. 若要使用 Pig 命令執行**pigbatch.pig**檔案使用下列項目。

        pig ~/pigbatch.pig

    一旦批次工作完成後，您應該會看到下列輸出應該是當您使用相同`DUMP RESULT;`上一個步驟中。

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="summary"></a>摘要

如您所見，Pig 命令可讓您以互動方式執行 MapReduce 作業使用 Pig 拉丁以及執行陳述式儲存在批次檔中。

##<a id="nextsteps"></a>後續步驟

為一般 Pig HDInsight 中的詳細資訊。

* [使用在 HDInsight Hadoop Pig](hdinsight-use-pig.md)

如需其他方式您可以使用在 HDInsight Hadoop。

* [使用 Hadoop HDInsight 上的登錄區](hdinsight-use-hive.md)

* [使用在 HDInsight Hadoop MapReduce](hdinsight-use-mapreduce.md)
