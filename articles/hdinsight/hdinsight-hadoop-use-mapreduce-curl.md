<properties
   pageTitle="使用 MapReduce 與使用中 HDInsight Hadoop 捲曲 |Microsoft Azure"
   description="瞭解如何從遠端 MapReduce 工作 Hadoop 執行上執行使用捲曲 HDInsight。"
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

#<a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-curl"></a>MapReduce 工作 Hadoop 執行上執行使用捲曲 HDInsight

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

在此文件中，您將學習如何使用捲曲 HDInsight 叢集上 Hadoop 上執行 MapReduce 工作。

捲曲用來說明您可以與互動的方式 HDInsight 執行 MapReduce 工作使用原始的 HTTP 要求。 此作業使用 WebHCat REST API （先前稱為 Templeton） HDInsight 叢集所提供的運作。

> [AZURE.NOTE] 如果您已熟悉使用 Linux 型 Hadoop 伺服器，但您不熟悉 HDInsight，請參閱[必須知道 Linux 為基礎的 Hadoop HDInsight 上](hdinsight-hadoop-linux-information.md)。

##<a id="prereq"></a>必要條件

若要完成此文件中的步驟進行，您將需要下列項目︰

* Hadoop HDInsight 叢集上的 (Linux 或 windows)

* [捲曲](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>執行使用捲曲 MapReduce 工作

> [AZURE.NOTE] 當您 WebHCat 的情況下，使用捲曲或任何其他的其餘部分通訊時，您必須驗證要求提供的 HDInsight 叢集管理員的使用者名稱和密碼。 您也必須使用叢集名稱做為 URI 用來將邀請傳送至伺服器的一部分。
>
> 本節中的命令，取代叢集和**密碼**驗證的使用者帳戶的密碼與使用者**的使用者名稱**。 **CLUSTERNAME**換成您叢集的名稱。
>
> REST API 受到保護使用[基本存取驗證](http://en.wikipedia.org/wiki/Basic_access_authentication)。 您隨時都應該要要求使用 HTTPS，以確保您的認證安全地傳送至伺服器。

1. 從命令列，請確認您可以連線至您的 HDInsight 叢集使用下列命令︰

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    您應該會收到下列類似的回應︰

        {"status":"ok","version":"v1"}

    使用這個命令中的參數如下所示︰

    * **-u**︰ 表示的使用者名稱和密碼來進行驗證要求
    * **-G**︰ 指出這是 GET 要求

    URI **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**，開頭是相同的所有要求。

2. 若要送出 MapReduce 工作，請使用下列命令︰

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasbs:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasbs:///example/data/gutenberg/davinci.txt -d arg=wasbs:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar

    結束 URI (/ mapreduce/jar) 會告訴 WebHCat 此要求會開始 MapReduce 工作，從 jar 檔案中的類別。 使用這個命令中的參數如下所示︰

    * **-d**:`-G`不是，因此要求預設文章方法。 `-d`指定資料值，會傳送要求。

        * **user.name**︰ 執行命令的使用者
        * **jar**︰ 執行之類別的 jar 檔案的位置
        * **課程**︰ 包含 MapReduce 邏輯的類別
        * **引數**︰ 引數傳遞給 MapReduce 工作。在這種情況下，輸入的文字檔案和目錄中所使用的輸出

    這個命令應傳回作業識別碼，可以用來檢查作業的狀態︰

        {"id":"job_1415651640909_0026"}

3. 若要檢查的工作狀態，請使用下列命令。 上一個步驟中傳回的值取代**JOBID** 。 例如，如果傳回的值是`{"id":"job_1415651640909_0026"}`，然後就會 JOBID `job_1415651640909_0026`。

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    如果已完成的工作，狀態會是 「 成功 」。

    > [AZURE.NOTE] 此捲曲要求傳回 JSON 文件的工作; 的相關資訊jq 用來擷取狀態值。

4. 當工作狀態已變更為**成功**時，您可以從 Azure Blob 儲存體來擷取工作的結果。 `statusdir`與查詢傳遞的參數會包含輸出檔案; 的位置在此情況下， **wasbs: / 範例/捲曲**。 此位址 HDInsight 叢集所使用的預設儲存容器中的**範例/捲曲**目錄中儲存工作的成果。

您可以清單，然後使用[Azure CLI](../xplat-cli-install.md)下載這些檔案。 例如，如果清單檔案中的**範例/捲曲**中，請使用下列命令︰

    azure storage blob list <container-name> example/curl

若要下載的檔案，請使用下列步驟︰

    azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] 您必須指定使用包含 blob 儲存體帳戶名稱`-a`和`-k`的參數或設定**AZURE\_儲存\_帳戶**和**AZURE\_儲存\_存取\_鍵**環境變數。 瞭解[如何上傳至 HDInsight 的資料](hdinsight-upload-data.md)，如需詳細資訊。

##<a id="summary"></a>摘要

在這份文件中所示，您可以使用原始的 HTTP 要求執行、 監控及檢視 HDInsight 叢集登錄區工作的結果。

如需有關使用本文中其餘介面的詳細資訊，請參閱[WebHCat 參照](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)。

##<a id="nextsteps"></a>後續步驟

如需在 HDInsight MapReduce 工作的一般資訊︰

* [使用上 HDInsight Hadoop MapReduce](hdinsight-use-mapreduce.md)

有關的其他方法您可以使用 Hadoop HDInsight 上︰

* [使用上 HDInsight Hadoop 登錄區](hdinsight-use-hive.md)

* [Hadoop HDInsight 上搭配使用的豬](hdinsight-use-pig.md)
