<properties
   pageTitle="使用中 HDInsight 捲曲 Hadoop 豬 |Microsoft Azure"
   description="瞭解如何使用捲曲 Azure HDInsight Hadoop 叢集上執行的豬拉丁工作。"
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

#<a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-curl"></a>使用捲曲 HDInsight 上執行的豬工作與 Hadoop

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

在此文件中，您將學習如何使用捲曲 Azure HDInsight 叢集上執行的豬拉丁工作。 豬拉丁程式設計語言可讓您描述會套用至輸入資料以產生所需的輸出的轉換功能。

捲曲用來說明您可以與互動的方式 HDInsight，請使用原始的 HTTP 要求執行、 監控及擷取豬作業的結果。 這樣的運作方式使用 WebHCat REST API （先前稱為 Templeton） 所提供的 HDInsight 叢集。

> [AZURE.NOTE] 如果您已熟悉使用 Linux 型 Hadoop 伺服器，但不熟悉 HDInsight，請參閱[Linux 型 HDInsight 秘訣](hdinsight-hadoop-linux-information.md)。

##<a id="prereq"></a>必要條件

若要完成此文件中的步驟進行，您將需要下列項目︰

* Azure HDInsight (Hadoop 上 HDInsight) 叢集 （Linux 型或 windows）

* [捲曲](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>使用捲曲執行豬工作

> [AZURE.NOTE] 當使用捲曲或任何其他的其餘部分通訊與 WebHCat，您必須驗證要求 HDInsight 叢集提供的系統管理員使用者名稱和密碼。 您也必須使用叢集名稱一部分的統一資源識別項 (URI)，用來將邀請傳送至伺服器。
>
> 本節中的命令，為**使用者名稱**與叢集，驗證使用者並**密碼**取代使用者帳戶的密碼。 **CLUSTERNAME**換成您叢集的名稱。
>
> REST API 受到保護透過[基本存取驗證](http://en.wikipedia.org/wiki/Basic_access_authentication)。 您隨時都應該要要求使用安全 HTTP (HTTPS)，以確保您的認證安全地傳送至伺服器。

1. 從命令列中，確認您可以連線到您的 HDInsight 叢集使用下列命令︰

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    您應該會收到下列類似的回應︰

        {"status":"ok","version":"v1"}

    使用這個命令中的參數如下所示︰

    * **-u**︰ 的使用者名稱與密碼驗證要求
    * **-G**︰ 指出這是 GET 要求

    開頭的 URL， **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**，將會相同的所有要求。 路徑， **/status**，表示要求傳回 WebHCat (也稱為 Templeton) 的狀態伺服器。

2. 您可以使用下列程式碼，提交到叢集豬拉丁工作︰

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'wasbs:///example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="wasbs:///example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    使用這個命令中的參數如下所示︰

    * **-d**︰ 因為`-G`不是，要求預設值為 [文章] 方法。 `-d`指定資料值，會傳送要求。

        * **user.name**︰ 執行命令的使用者
        * **執行**︰ 執行豬拉丁陳述式
        * **statusdir**︰ 這項工作的狀態會寫入的目錄

    > [AZURE.NOTE] 請注意豬拉丁陳述式中的空格會被取代，`+`字元搭配捲曲時。

    這個命令應傳回可以用來檢查狀態的工作，例如工作識別碼︰

        {"id":"job_1415651640909_0026"}

3. 若要檢查的工作狀態，請使用下列命令。 上一個步驟中傳回的值取代**JOBID** 。 例如，如果傳回的值是`{"id":"job_1415651640909_0026"}`，然後就會**JOBID** `job_1415651640909_0026`。

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    完成工作後，如果狀態會**成功**。

    > [AZURE.NOTE] 此捲曲要求傳回 JavaScript 物件標記法 (JSON) 文件的工作的相關資訊，然後 jq 用來擷取狀態值。

##<a id="results"></a>檢視結果

當工作狀態已變更為**成功**時，您可以從 Azure Blob 儲存體來擷取工作的結果。 `statusdir`傳遞查詢參數包含輸出檔案; 的位置在此情況下， **wasbs: / 範例/pigcurl**。 此位址 HDInsight 叢集所使用的預設儲存容器中的**範例/pigcurl**目錄中儲存工作的成果。

您可以清單，然後使用[Azure CLI](../xplat-cli-install.md)下載這些檔案。 例如，將清單檔案**範例/pigcurl**，請使用下列命令︰

    azure storage blob list <container-name> example/pigcurl

若要下載的檔案，請使用下列步驟︰

    azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] 您必須指定使用包含 blob 儲存體帳戶名稱`-a`和`-k`參數或設定**AZURE\_儲存\_帳戶**和**AZURE\_儲存\_存取\_鍵**環境變數。

##<a id="summary"></a>摘要

在這份文件中所示，您可以使用原始的 HTTP 要求執行、 監控及 HDInsight 叢集上檢視的豬作業的結果。

如需有關使用本文中的其餘部分介面的詳細資訊，請參閱[WebHCat 參照](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)。

##<a id="nextsteps"></a>後續步驟

如需一般有關 HDInsight 豬的詳細資訊︰

* [Hadoop HDInsight 上搭配使用的豬](hdinsight-use-pig.md)

有關的其他方法您可以使用 Hadoop HDInsight 上︰

* [使用上 HDInsight Hadoop 登錄區](hdinsight-use-hive.md)

* [使用上 HDInsight Hadoop MapReduce](hdinsight-use-mapreduce.md)
