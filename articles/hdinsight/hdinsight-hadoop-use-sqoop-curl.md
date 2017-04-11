<properties
   pageTitle="使用中 HDInsight 捲曲 Hadoop Sqoop |Microsoft Azure"
   description="瞭解如何從遠端送出 HDInsight 使用捲曲 Sqoop 工作。"
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="jgao"/>

#<a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>在使用捲曲 HDInsight Hadoop 執行的 Sqoop 工作

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

瞭解如何使用捲曲 HDInsight Hadoop 叢集上執行 Sqoop 工作。

捲曲用來說明您可以與互動的方式 HDInsight，請使用原始的 HTTP 要求執行、 監控及擷取 Sqoop 作業的結果。 此作業使用 WebHCat REST API （先前稱為 Templeton） HDInsight 叢集所提供的運作。

> [AZURE.NOTE] 如果您已熟悉使用 Linux 型 Hadoop 伺服器，但不熟悉 HDInsight，請參閱[使用 linux HDInsight 的相關資訊](hdinsight-hadoop-linux-information.md)。

##<a name="prerequisites"></a>必要條件

若要完成此文件中的步驟進行，您將需要下列項目︰

* Hadoop HDInsight 叢集上的 (Linux 或 windows)

* [捲曲](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a name="submit-sqoop-jobs-by-using-curl"></a>使用捲曲提交 Sqoop 工作

> [AZURE.NOTE] 當使用 WebHCat 捲曲或任何其他的其餘部分通訊，請您必須驗證要求提供 HDInsight 叢集系統管理員的使用者名稱和密碼。 您也必須使用叢集名稱一部分的統一資源識別項 (URI) 用來將邀請傳送至伺服器。
>
> 本節中的命令，為**使用者名稱**與叢集，驗證使用者並**密碼**取代使用者帳戶的密碼。 **CLUSTERNAME**換成您叢集的名稱。
>
> REST API 受到保護透過[基本驗證](http://en.wikipedia.org/wiki/Basic_access_authentication)。 您隨時都應該要要求使用安全 HTTP (HTTPS)，以確保您的認證安全地傳送至伺服器。

1. 從命令列中，確認您可以連線到您的 HDInsight 叢集使用下列命令︰

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    您應該會收到下列類似的回應︰

        {"status":"ok","version":"v1"}

    使用這個命令中的參數如下所示︰

    * **-u** -的使用者名稱與密碼驗證要求。
    * **-G** -指出這是 GET 要求。

    開頭的 URL， **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**，將會針對所有要求相同。 路徑， **/status**，表示要求傳回狀態為 [WebHCat (也稱為 Templeton) 伺服器。 

2. 您可以使用下列提交 sqoop 工作︰


        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /tutorials/usesqoop/data --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop

    使用這個命令中的參數如下所示︰

    * **-d** -由於`-G`不是，要求預設值為 [文章] 方法。 `-d`指定資料值，會傳送要求。

        * **user.name** -使用者執行命令。

        * **命令**-Sqoop 執行命令。

        * **statusdir** -這項工作的狀態會寫入的目錄。

    這個命令應傳回可以用來查看工作狀態的作業識別碼。

        {"id":"job_1415651640909_0026"}

3. 若要檢查的工作狀態，請使用下列命令。 上一個步驟中傳回的值取代**JOBID** 。 例如，如果傳回的值是`{"id":"job_1415651640909_0026"}`，然後就會**JOBID** `job_1415651640909_0026`。

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    完成工作後，如果狀態會**成功**。

    > [AZURE.NOTE] 此捲曲要求傳回 JavaScript 物件標記法 (JSON) 文件的工作; 的相關資訊jq 用來擷取狀態值。

4. 一旦工作狀態已變更為**成功**，您可以從 Azure Blob 儲存體來擷取工作的結果。 `statusdir`傳遞查詢參數包含輸出檔案; 的位置在此情況下， **wasbs: / 範例/捲曲**。 此位址 HDInsight 叢集所使用的預設儲存容器的**範例/捲曲**目錄中儲存工作的成果。

    您可以清單，然後使用[Azure CLI](../xplat-cli-install.md)下載這些檔案。 例如，將清單檔案**範例/捲曲**，請使用下列命令︰

        azure storage blob list <container-name> example/curl

    若要下載的檔案，請使用下列步驟︰

        azure storage blob download <container-name> <blob-name> <destination-file>

    > [AZURE.NOTE] 您必須指定使用包含 blob 儲存體帳戶名稱`-a`和`-k`參數或設定**AZURE\_儲存\_帳戶**和**AZURE\_儲存\_存取\_鍵**環境變數。 請參閱 < href = 」 hdinsight-上傳-data.md 「 目標 = 「 _blank 」，如需詳細資訊。

##<a name="limitations"></a>限制

* 大量匯出-使用 Linux 型 HDInsight、 用來將資料匯出至 Microsoft SQL Server Azure SQL 資料庫或 Sqoop 連接器目前不支援大量插入。

* 批次處理-Linux 型 HDInsight，與使用時`-batch`切換執行插入時，Sqoop 會執行，而不是批次處理插入作業的多個插入。

##<a name="summary"></a>摘要

在這份文件中所示，您可以使用原始的 HTTP 要求執行、 監控及檢視 HDInsight 叢集 Sqoop 作業的結果。

如本文所用的其餘部分介面的詳細資訊，請參閱<a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Sqoop REST API 指南</a>。

##<a name="next-steps"></a>後續步驟

如需使用 HDInsight 的登錄區的一般資訊︰

* [使用上 HDInsight Hadoop Sqoop](hdinsight-use-sqoop.md)

如需的資訊的其他方法您可以使用 Hadoop HDInsight 上︰

* [使用上 HDInsight Hadoop 登錄區](hdinsight-use-hive.md)

* [Hadoop HDInsight 上搭配使用的豬](hdinsight-use-pig.md)

* [使用上 HDInsight Hadoop MapReduce](hdinsight-use-mapreduce.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


