<properties
    pageTitle="執行 Hadoop 工作使用 DocumentDB 和 HDInsight |Microsoft Azure"
    description="瞭解如何使用 DocumentDB 和 Azure HDInsight 執行簡單的登錄區豬，與 MapReduce 工作。"
    services="documentdb"
    authors="dennyglee"
    manager="jhubbard"
    editor="mimig"
    documentationCenter=""/>


<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="denlee"/>

#<a name="DocumentDB-HDInsight"></a>執行使用 DocumentDB 和 HDInsight Hadoop 工作

本教學課程教您如何執行[Apache 登錄區][apache-hive]， [Apache 豬][apache-pig]，及[Apache Hadoop] [ apache-hadoop] Azure HDInsight 與 DocumentDB 的 Hadoop 連接器的 MapReduce 工作。 DocumentDB 的 Hadoop 連接器可讓 DocumentDB 做為來源和接收登錄區豬，與 MapReduce 工作。 本教學課程中會使用作為資料來源與目的地 DocumentDB，Hadoop 工作。

完成本教學課程之後，您便可以回答下列問題︰

- 如何使用登錄區、 豬或 MapReduce 工作 DocumentDB 從載入資料？
- 如何將資料儲存在 DocumentDB 使用登錄區、 豬或 MapReduce 工作？

我們建議您快速入門觀看以下影片，我們執行透過使用 DocumentDB 和 HDInsight 登錄區工作的位置。

> [AZURE.VIDEO use-azure-documentdb-hadoop-connector-with-azure-hdinsight]

然後，返回本文中，您會收到上您可以在如何 DocumentDB 資料執行分析工作的完整詳細資料的位置。

> [AZURE.TIP] 本教學課程假設您已使用 Apache Hadoop、 登錄區，及/或豬先前體驗。 如果您是新 Apache Hadoop、 登錄區，並豬，我們建議您造訪[Apache Hadoop 文件][apache-hadoop-doc]。 本教學課程中也會假設您有使用 DocumentDB，而且有 DocumentDB 帳戶。 如果您不熟悉 DocumentDB 或您沒有 DocumentDB 帳戶，請查看我們的[快速入門][getting-started]頁面。

不會有的時間才能完成教學課程，而且只想要用於登錄區與 MapReduce 豬，取得 PowerShell 指令碼完整的範例？ 不是問題，取得這些[以下][documentdb-hdinsight-samples]。 下載也包含這些範例 hql 豬，與 java 檔案。

## <a name="NewestVersion"></a>最新版本

<table border='1'>
    <tr><th>Hadoop 連接器版本</th>
        <td>1.2.0</td></tr>
    <tr><th>指令碼 Uri</th>
        <td>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v04.ps1</td></tr>
    <tr><th>修改日期</th>
        <td>04/26 2016</td></tr>
    <tr><th>支援的 HDInsight 版本</th>
        <td>3.1、 3.2 捨位</td></tr>
    <tr><th>變更記錄</th>
        <td>更新的 DocumentDB Java SDK 1.6.0</br>
            新增的支援分割為來源和接收的集合</br>
        </td></tr>
</table>

## <a name="Prerequisites"></a>必要條件
之前的指示執行本教學課程中，請確定您有下列動作︰

- DocumentDB 帳戶、 資料庫及文件內的集合。 如需詳細資訊，請參閱[快速入門 DocumentDB][getting-started]。 範例資料匯入您的 DocumentDB 帳戶與[DocumentDB 匯入工具][documentdb-import-data]。
- 處理量。 讀取和寫入從 HDInsight 會計算向您集合您分配的要求單位。 如需詳細資訊，請參閱[Provisioned 處理量、 要求單位和資料庫作業][documentdb-manage-throughput]。
- 在每個額外的預存程序的容量輸出集合。 預存程序適用於轉接產生的文件。 如需詳細資訊，請參閱[集合和能夠處理量][documentdb-manage-document-storage]。
- 從群組 」、 「 豬或 「 MapReduce 工作結果的文件的容量。 如需詳細資訊，請參閱[管理 DocumentDB 容量和效能][documentdb-manage-collections]。
- [*選擇性*]其他集合的容量。 如需詳細資訊，請參閱[Provisioned 文件儲存和索引負荷][documentdb-manage-document-storage]。

> [AZURE.WARNING] 為了避免出現建立新的集合，其中一項工作期間，您可以列印到 stdout 結果、 將輸出儲存到您的 WASB 容器，或指定現有的集合。 若是指定現有的集合，會在集合內建立新文件，然後如果*識別碼*中有衝突的使用者，只會影響現有的文件。 [**連接器自動將會覆寫現有的文件識別碼發生衝突**。 您可以關閉此功能了] 選項設定為 false。 如果了為 false，而且會發生衝突，就無法 Hadoop 工作。報告識別碼衝突錯誤。


## <a name="ProvisionHDInsight"></a>步驟 1︰ 建立新的 HDInsight 叢集
本教學課程中使用自訂 HDInsight 叢集指令碼動作，從 Azure 入口網站。 在本教學課程中，我們會使用 Azure 入口網站，來建立 HDInsight 叢集。 如何使用 PowerShell cmdlet 或 HDInsight.NET SDK 的指示，請參閱[使用指令碼的巨集指令的自訂 HDInsight 叢集][hdinsight-custom-provision]文章。

1. [Azure 入口網站]登入[azure-portal]。

2. 按一下 [ **+ 新增**上方的左側導覽畫面中，搜尋**HDInsight**的新刀上方的搜尋列]。

3. **HDInsight**發佈**microsoft**會顯示在頂端的結果。 按一下它，然後按一下 [**建立**]。

4. 新的 HDInsight 叢集上建立刀、 輸入您的**叢集名稱**並選取您想要佈建在此資源的**訂閱**。

    <table border='1'>
        <tr><td>叢集名稱</td><td>名稱叢集。<br/>
   DNS 名稱必須開始和結束以數字的 alpha 字元，並可能包含連字號。<br/>
   欄位必須是介於 3 至 63 個字元的字串。</td></tr>
        <tr><td>訂閱名稱</td>
            <td>如果您有一個以上的 Azure 訂閱，請選取 [將裝載 HDInsight 叢集訂閱]。 </td></tr>
    </table>

5. 按一下 [**選取叢集類型**，下列屬性設定為指定的值。

    <table border='1'>
        <tr><td>叢集類型</td><td><strong>Hadoop</strong></td></tr>
        <tr><td>叢集層</td><td><strong>標準</strong></td></tr>
        <tr><td>作業系統</td><td><strong>Windows</strong></td></tr>
        <tr><td>版本</td><td>最新版本</td></tr>
    </table>

    現在，請按一下 [**選取**]。

    ![提供 Hadoop HDInsight 初始叢集詳細資料][image-customprovision-page1]

6. 按一下 [**認證**來設定您的登入] 和 [遠端存取的認證。 選擇您**叢集登入的使用者名稱**] 和 [**叢集登入密碼**。

    如果您想要遠端到叢集，**刀底部選取，然後提供使用者名稱和密碼。

7. 按一下要設定您的主要位置存取資料的**資料來源**。 選擇**選取方法**和指定現有的儲存空間帳戶或建立新的範本。

8. 在相同的刀，指定**預設容器**與**位置**。 然後按一下 [**選取**]。

    > [AZURE.NOTE] 選取關閉您的最佳效能的 DocumentDB 帳戶地區的位置

8. 按一下以選取的數字與類型節點的**價格**。 您可以保留預設設定，稍後不按比例縮放的工作者節點數目。

9. 按一下 [**選擇性的設定**，然後選擇性組態刀中的**指令碼動作**]。

    在 [指令碼動作輸入下列資訊，以自訂 HDInsight 叢集。

    <table border='1'>
        <tr><th>屬性</th><th>值</th></tr>
        <tr><td>名稱</td>
            <td>指定指令碼動作的名稱。</td></tr>
        <tr><td>指令碼 URI</td>
            <td>指定自訂叢集叫用的指令碼 URI。</br></br>
   請輸入︰ </br> <strong>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v04.ps1</strong>。</td></tr>
        <tr><td>標頭</td>
            <td>按一下 [執行到不對節點的 PowerShell 指令碼的核取方塊。</br></br>
            <strong>選取此核取方塊</strong>。</td></tr>
        <tr><td>背景工作</td>
            <td>按一下 [執行到工作者節點的 PowerShell 指令碼的核取方塊。</br></br>
            <strong>選取此核取方塊</strong>。</td></tr>
        <tr><td>動物園管理員</td>
            <td>按一下 [執行到動物園管理員的 PowerShell 指令碼的核取方塊。</br></br>
            <strong>不需要</strong>。
            </td></tr>
        <tr><td>參數</td>
            <td>如果所需的指令碼，請指定參數。</br></br>
            <strong>無參數需要</strong>。</td></tr>
    </table>

10. 建立新**資源群組**或使用現有的資源群組] 底下您 Azure 的訂閱。

11. 現在，核取 [**固定至儀表板**追蹤其部署，並按一下 [**建立**！

## <a name="InstallCmdlets"></a>步驟 2︰ 安裝和設定 PowerShell 的 Azure

1. 安裝 Azure PowerShell。 您可以找到相關指示[以下][powershell-install-configure]。

    > [AZURE.NOTE] 或者，只要的登錄區查詢，您可以使用 HDInsight 的線上登錄區編輯器。 若要這麼做，請登入[Azure 入口網站][azure-portal]，按一下 [若要檢視您的 HDInsight 叢集清單左邊的窗格上的 [ **HDInsight** 。 按一下您想要執行登錄區查詢，的叢集，然後按一下 [**查詢主控台**。

2. 開啟 PowerShell 的 Azure 整合指令碼環境︰
    - 在電腦上執行 Windows 8 或 Windows Server 2012 或更新版本，您可以使用內建的搜尋。 從 [開始] 畫面中，輸入**powershell ise [以系統**，然後按一下 [**輸入**]。
    - 在電腦上執行的版本早於 Windows 8 或 Windows Server 2012，使用 [開始] 功能表。 從 [開始] 功能表中，輸入**命令提示字元中**，在 [搜尋] 方塊，然後在結果清單中按一下 [**命令提示字元**。 在命令提示字元中，輸入**powershell_ise** ，然後按**enter 鍵**。

3. 新增 Azure 帳戶。
    1. 在 [主控台] 窗格中，輸入**新增 AzureAccount**然後按一下**Enter**。
    2. 輸入與您 Azure 訂閱相關聯的電子郵件地址，然後按一下 [**繼續**]。
    3. 輸入您的 Azure 訂閱的密碼。
    4. 按一下 [**登入**]。

4. 下圖會識別您 Azure PowerShell 指令碼環境重要的部分。

    ![Azure PowerShell 的圖表][azure-powershell-diagram]

## <a name="RunHive"></a>步驟 3︰ 執行使用 DocumentDB 和 HDInsight 登錄區工作

> [AZURE.IMPORTANT] 使用您的組態設定都必須填寫所有 < > 所指出的變數。

1. 在 PowerShell 指令碼] 窗格中，設定下列變數。

        # Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide the HDInsight cluster name where you want to run the Hive job.
        $clusterName = "<HDInsightClusterName>"

2. <p>現在就讓我們開始建構查詢字串。 我們要撰寫登錄區查詢的所有文件系統產生的時間戳記 (_ts) 和唯一識別碼 (_rid) 從 DocumentDB 集合，以分，計算所有文件後再儲存結果轉成新的 DocumentDB 集合。</p>

    <p>首先，我們 DocumentDB 集合中建立的登錄區資料表。 將下列程式碼片段 PowerShell 指令碼窗格<strong>後</strong>的程式碼片段 # 1。 請確定包含 trim 選擇性 DocumentDB.query 參數 t 我們只 _ts 的文件和 _rid。</p>

    > [AZURE.NOTE]**命名 DocumentDB.inputCollections 並未發生錯誤。** 是的我們讓做為輸入新增多個集合︰ </br>

        '*DocumentDB.inputCollections*' = '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*' A1A</br> The collection names are separated without spaces, using only a single comma.


        # Create a Hive table using data from DocumentDB. Pass DocumentDB the query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "drop table DocumentDB_timestamps; "  +
                            "create external table DocumentDB_timestamps(id string, ts BIGINT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " +
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  接下來，讓我們來建立輸出集合的登錄區資料表。 輸出文件摘要資訊會月、 日、 小時、 分鐘和總次數。

    > [AZURE.NOTE]**再次，命名 DocumentDB.outputCollections 並未發生錯誤。** 是的我們讓作為輸出新增多個集合︰ </br>
「*DocumentDB.outputCollections*'='*\<DocumentDB 輸出集合名稱 1\>*，*\<DocumentDB 輸出集合名稱 2\>*」 </br> 不含空格，使用單一逗點分隔集合名稱。 </br></br>
文件會在多個集合分散式的循環。 以批次的文件會儲存在一個集合，然後第二個批次的文件會儲存在下一個集合等等。

        # Create a Hive table for the output data to DocumentDB.
        $queryStringPart2 = "drop table DocumentDB_analytics; " +
                              "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                              "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " +
                              "tblproperties ( " +
                                  "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                  "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                  "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                  "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "

4. 最後，現在就讓我們來月、 日、 小時和分鐘清點文件並插入結果回輸出的登錄區資料表。

        # GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "

5. 新增下列指令碼片段建立登錄區工作定義從先前的查詢。

        # Create a Hive job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

    您也可以使用的檔案選項，可以指定 HDFS HiveQL 指令碼檔案。

6. 新增下列程式碼片段儲存的開始時間並提交登錄區工作。

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7. 新增下列登錄區工作完成時，請等候。

        # Wait for the Hive job to complete.
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8. 新增下列列印標準輸出及開始和結束時間。

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. **執行**您新增的指令碼 ！ **按一下**[綠色執行] 按鈕。

10. 檢查結果。 登入[Azure 入口網站][azure-portal]。
    1. 在左側面板上，按一下 [<strong>瀏覽</strong>]。 </br>
    2. 按一下 [<strong>所有項目</strong>在右上方的 [瀏覽] 面板。 </br>
    3. 尋找並按一下<strong>DocumentDB 帳戶</strong>。 </br>
    4. 接下來，找到您的<strong>DocumentDB 帳戶</strong>，，然後<strong>DocumentDB 資料庫</strong>及您<strong>DocumentDB 集合</strong>相關聯的登錄區查詢中指定的輸出集合。</br>
    5. 最後，按一下 [<strong>開發人員工具</strong>下方<strong>文件檔案總管</strong>]。</br></p>

    您會看到您的登錄區查詢的結果。

    ![登錄區查詢結果][image-hive-query-results]

## <a name="RunPig"></a>步驟 4︰ 執行使用 DocumentDB 和 HDInsight 豬工作

> [AZURE.IMPORTANT] 使用您的組態設定都必須填寫所有 < > 所指出的變數。

1. 在 PowerShell 指令碼] 窗格中，設定下列的變數。

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"

2. <p>現在就讓我們開始建構查詢字串。 我們要撰寫豬查詢的所有文件系統產生的時間戳記 (_ts) 和唯一識別碼 (_rid) 從 DocumentDB 集合，以分，計算所有文件後再儲存結果轉成新的 DocumentDB 集合。</p>
    <p>首先，請載入 DocumentDB HDInsight 到文件。 將下列程式碼片段 PowerShell 指令碼窗格<strong>後</strong>的程式碼片段 # 1。 請確定您要修剪我們只 _ts 的文件的選擇性 DocumentDB 查詢參數新增 DocumentDB 查詢和 _rid。</p>

    > [AZURE.NOTE]是的我們讓做為輸入新增多個集合︰ </br>
「*\<DocumentDB 輸入集合名稱為 1\>*，*\<DocumentDB 輸入集合名稱 2\>*」</br> 不含空格，使用單一逗點分隔集合名稱。 </b>

    文件會在多個集合分散式的循環。 以批次的文件會儲存在一個集合，然後第二個批次的文件會儲存在下一個集合等等。

        # Load data from DocumentDB. Pass DocumentDB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  接下來，現在就讓我們來月、 日、 小時、 分鐘和總次數清點文件。

        # GROUP BY minute and COUNT entries for each.
        $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                            "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                            "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "

4. 最後，我們將結果儲存到新輸出集合。

    > [AZURE.NOTE]是的我們讓作為輸出新增多個集合︰ </br>
「\<DocumentDB 輸出集合名稱 1\>，\<DocumentDB 輸出集合名稱 2\>」</br> 不含空格，使用單一逗點分隔集合名稱。</br>
文件會在多個集合分散式的循環。 以批次的文件會儲存在一個集合，然後第二個批次的文件會儲存在下一個集合等等。

        # Store output data to DocumentDB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "

5. 新增下列指令碼程式碼片段，若要從前述查詢中建立的豬工作定義。

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

    您也可以使用的檔案選項，可以指定 HDFS 豬指令碼檔案。

6. 新增下列程式碼片段儲存的開始時間並提交的豬工作。

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition

7. 新增下列豬工作完成時，請等候。

        # Wait for the Pig job to complete.
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8. 新增下列列印標準輸出及開始和結束時間。

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. **執行**您新增的指令碼 ！ **按一下**[綠色執行] 按鈕。

10. 檢查結果。 登入[Azure 入口網站][azure-portal]。
    1. 在左側面板上，按一下 [<strong>瀏覽</strong>]。 </br>
    2. 按一下 [<strong>所有項目</strong>在右上方的 [瀏覽] 面板。 </br>
    3. 尋找並按一下<strong>DocumentDB 帳戶</strong>。 </br>
    4. 接下來，找到您的<strong>DocumentDB 帳戶</strong>，，然後<strong>DocumentDB 資料庫</strong>及您<strong>DocumentDB 集合</strong>相關聯的豬查詢中指定的輸出集合。</br>
    5. 最後，按一下 [<strong>開發人員工具</strong>下方<strong>文件檔案總管</strong>]。</br></p>

    您會看到您的豬查詢的結果。

    ![豬查詢結果][image-pig-query-results]

## <a name="RunMapReduce"></a>步驟 5︰ 執行使用 DocumentDB 和 HDInsight MapReduce 工作

1. 在 PowerShell 指令碼] 窗格中，設定下列變數。

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name

2. 我們會執行計算的每個您 DocumentDB 集合中的文件摘要資訊的次數 MapReduce 工作。 新增此指令碼的程式碼片段**之後**上述的程式碼片段。

        # Define the MapReduce job.
        $TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

    > [AZURE.NOTE] TallyProperties v01.jar 隨附 DocumentDB Hadoop 連接器的自訂安裝。

3. 新增下列命令以提交 MapReduce 工作。

        # Save the start time and submit the job.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    除了 MapReduce 工作定義，您也會提供您要執行 MapReduce 工作，以及憑證 HDInsight 叢集名稱。 開始 AzureHDInsightJob 是非同步的通話。 若要檢查作業完成時，使用 [*等待 AzureHDInsightJob*指令程式。

4. 新增下列命令以檢查執行 MapReduce 工作的任何錯誤。

        # Get the job output and print the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

5. **執行**您新增的指令碼 ！ **按一下**[綠色執行] 按鈕。

6. 檢查結果。 登入[Azure 入口網站][azure-portal]。
    1. 在左側面板上，按一下 [<strong>瀏覽</strong>]。
    2. 按一下 [<strong>所有項目</strong>在右上方的 [瀏覽] 面板。
    3. 尋找並按一下<strong>DocumentDB 帳戶</strong>。
    4. 接下來，找到您的<strong>DocumentDB 帳戶</strong>，，然後<strong>DocumentDB 資料庫</strong>及您<strong>DocumentDB 集合</strong>相關聯 MapReduce 工作中指定的輸出集合。
    5. 最後，按一下 [<strong>開發人員工具</strong>下方<strong>文件檔案總管</strong>]。

    您會看到 MapReduce 工作的結果。

    ![MapReduce 查詢結果][image-mapreduce-query-results]

## <a name="NextSteps"></a>後續步驟

恭喜您 ！ 您只是要執行使用 Azure DocumentDB 和 HDInsight 您第一份登錄區豬，與 MapReduce 工作。

我們已開啟為來源我們 Hadoop 連接器。 如果您有興趣，您可以參與上[GitHub][documentdb-github]。

若要深入瞭解，請參閱下列文章︰

- [開發與 Documentdb Java 應用程式][documentdb-java-application]
- [Hadoop HDInsight 中為開發 Java MapReduce 程式][hdinsight-develop-deploy-java-mapreduce]
- [開始使用 Hadoop 與 HDInsight 中的群組，以分析行動話筒使用][hdinsight-get-started]
- [使用 HDInsight MapReduce][hdinsight-use-mapreduce]
- [使用 HDInsight 的登錄區][hdinsight-use-hive]
- [使用 HDInsight 的豬][hdinsight-use-pig]
- [自訂 HDInsight 叢集使用指令碼的巨集指令][hdinsight-hadoop-customize-cluster]

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: documentdb-get-started.md

[azure-portal]: https://portal.azure.com/
[azure-powershell-diagram]: ./media/documentdb-run-hadoop-with-hdinsight/azurepowershell-diagram-med.png

[documentdb-hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[documentdb-github]: https://github.com/Azure/azure-documentdb-hadoop
[documentdb-java-application]: documentdb-java-application.md
[documentdb-manage-collections]: documentdb-manage.md#Collections
[documentdb-manage-document-storage]: documentdb-manage.md#IndexOverhead
[documentdb-manage-throughput]: documentdb-manage.md#ProvThroughput
[documentdb-import-data]: documentdb-import-data.md

[hdinsight-custom-provision]: ../hdinsight/hdinsight-provision-clusters.md#powershell
[hdinsight-develop-deploy-java-mapreduce]: ../hdinsight/hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-hadoop-customize-cluster]: ../hdinsight/hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight/hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-storage]: ../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight-use-mapreduce]: ../hdinsight/hdinsight-use-mapreduce.md
[hdinsight-use-pig]: ../hdinsight/hdinsight-use-pig.md

[image-customprovision-page1]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page1.png
[image-hive-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: ../powershell-install-configure.md
