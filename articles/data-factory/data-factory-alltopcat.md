<properties
    pageTitle="資料工廠服務的所有主題 |Microsoft Azure"
    description="Azure 服務的所有主題的資料表命名為資料工廠存在於 http://azure.microsoft.com/documentation/articles/、 標題和描述。"
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="data-factory"
    ms.workload="data-factory"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="spelluru"/>


# <a name="all-topics-for-azure-data-factory-service"></a>Azure 資料工廠服務的所有主題

本主題列出會直接套用至**資料工廠**服務 Azure 的每個主題。 您可以使用**Ctrl + F**，尋找目前感興趣的主題中搜尋關鍵字此網頁。




## <a name="new"></a>新

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 1 | [移動資料 Amazon Redshift 」 從使用 Azure 資料工廠](data-factory-amazon-redshift-connector.md) | 進一步瞭解如何將資料從 Amazon Redshift 使用 Azure 資料工廠移。 |
| 2 | [將資料從 Amazon 簡單儲存服務使用 Azure 資料工廠移動](data-factory-amazon-simple-storage-service-connector.md) | 瞭解如何將資料從 Amazon 簡單儲存服務 (S3) 使用 Azure 資料工廠。 |
| 3 | [Azure 資料工廠-複製精靈](data-factory-azure-copy-wizard.md) | 瞭解如何使用資料工廠 Azure 複製精靈，將資料複製到接收支援的資料來源。 |
| 4 | [教學課程︰ 建立您使用的資料工廠 REST API 的第一個 Azure 資料工廠](data-factory-build-your-first-pipeline-using-rest-api.md) | 在本教學課程中，您可以建立使用資料工廠 REST API 範例 Azure 資料工廠管線。 |
| 5 | [教學課程︰ 建立含使用.NET API 複製活動的管線](data-factory-copy-activity-tutorial-using-dotnet-api.md) | 在本教學課程中，您建立 Azure 資料工廠管線複製活動與使用.NET API。 |
| 6 | [教學課程︰ 建立含使用 REST API 複製活動的管線](data-factory-copy-activity-tutorial-using-rest-api.md) | 在本教學課程中，您建立 Azure 資料工廠管線複製活動與使用 REST API。 |
| 7 | [資料工廠複製精靈](data-factory-copy-wizard.md) | 瞭解如何使用資料工廠複製精靈，將資料複製到接收支援的資料來源。 |
| 8 | [資料管理閘道器](data-factory-data-management-gateway.md) | 將資料閘道器設定為內部部署與雲端之間移動資料。 Azure 資料工廠中使用資料管理閘道器移動資料。 |
| 9 | [從內部部署 Cassandra 資料庫使用 Azure 資料工廠移動資料](data-factory-onprem-cassandra-connector.md) | 進一步瞭解如何從內部部署 Cassandra 資料庫使用 Azure 資料工廠移動資料。 |
| 10 | [將資料從 MongoDB 使用 Azure 資料工廠移動](data-factory-on-premises-mongodb-connector.md) | 瞭解如何將資料從 Azure 資料工廠 MongoDB 資料庫。 |
| 11 | [將資料從 Salesforce 移使用 Azure 資料工廠](data-factory-salesforce-connector.md) | 瞭解如何將資料從 Salesforce，使用 Azure 資料工廠。 |


## <a name="updated-articles-data-factory"></a>更新文件，資料工廠

此區段列出文件已更新，更新已大或嚴重的位置。 每個更新的文件，會顯示概略新增的 markdown 文字的程式碼片段。 文件已更新至**2016年-10-05** **2016年-08 22**的日期範圍內。

| &nbsp; | 文件 | 更新的文字、 程式碼片段 | 更新的時機 |
| --: | :-- | :-- | :-- |
| 12 | [Azure 資料工廠.NET API 變更記錄](data-factory-api-change-log.md) | 本文會提供在特定的版本中 Azure 資料工廠 sdk 的變更的相關資訊。 您可以找到最新的 NuGet 套件的 Azure 資料工廠以下 (https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)**版本 4.11.0**功能新增: / 已新增下列連結的服務類型:-OnPremisesMongoDbLinkedService (https://msdn.microsoft.com/library/mt765129.aspx)-AmazonRedshiftLinkedService (https://msdn.microsoft.com/library/mt765121.aspx)-AwsAccessKeyLinkedService (https://msdn.microsoft.com/library/mt765144.aspx) / 已新增下列資料集類型:-MongoDbCollectionDataset (https://msdn.microsoft.com/library/mt765145.aspx)-AmazonS3Dataset (https://msdn.microsoft.com/library/mt765112.aspx) / 已新增下列複製來源類型:-MongoDbSource (https://msdn.microsoft.com/en-US/library/mt765123.aspx)**版本 4.10.0** / TextFormat 已經新增下列選用的屬性:-滑雪促銷 | 2016-09-22 |
| 13 | [從 Azure Blob 使用 Azure 資料工廠移動資料](data-factory-azure-blob-connector.md) |  / copyBehavior / 當來源 BlobSource 或檔案系統定義複製行為。  / **PreserveHierarchy:**保留檔案中的階層的目標資料夾。 相對路徑的來源資料夾的來源檔案是相同的目標檔案到目標資料夾的相對路徑.巴西 /。巴西 /。**FlattenHierarchy:**的來源資料夾的所有檔案都是第一層級的目標資料夾。 目標檔案有自動產生的名稱。 .br /。巴西 /。**MergeFiles: （預設）**合併至一個檔案的來源資料夾的所有檔案。 如果指定檔案/Blob 名稱，合併的檔案名稱會指定的名稱。否則，就會自動產生的檔案名稱。  / 不 / **BlobSource**也支援這兩個屬性的回溯相容性。 / **treatEmptyAsNull**︰ 指定是否要 null 或空字串視為 null 值。 / **skipHeaderLineCount** -指定需要略過行數。 僅當輸入資料集使用 TextFormat 是適用。 同樣地， **BlobSink**支援第 | 2016-09 28 |
| 14 | [建立預測使用 Azure 電腦學習活動的管線](data-factory-azure-ml-batch-execution-activity.md) | **Web 服務需要多個輸入**如果 web 服務需要多個輸入，請使用**webServiceInputs**屬性，而非**webServiceInput**。 **WebServiceInputs**所參照的資料集也必須包含活動**輸入**中。 Azure 毫升實驗中, web 服務輸入與輸出連接埠和全域參數有預設的名稱 （「 input1 」、 「 input2 」），您可以自訂。 您使用 webServiceInputs、 webServiceOutputs，及 globalParameters 設定名稱必須確實符合實驗中的名稱。 您可以檢視範例要求裝載批次執行協助您 Azure 毫升結束點，以驗證預期的對應頁面上。    {"名稱": 「 PredictivePipeline 」，[內容]: {「 描述 」: [使用 AzureML 模型]，「 活動 」: {"名稱": 「 MLActivity 」，「 類型 」: 「 AzureMLBatchExecution 」，「 描述 」: 「 預測分析批次輸入 「，」 輸入 「: {"名稱": 「 inputDataset1 」}，{"名稱": 「 inputDatase | 2016-09-13 |
| 15 | [複製活動效能與調整輔助線](data-factory-copy-activity-performance.md) | 1。**建立比較基準**。 在開發階段，請針對代表資料範例使用複製活動測試您的管線。 您可以使用資料 Factory 切割模型 (資料-工廠-排程-和-execution.md time-series-datasets-and-data-slices) 來限制您使用的資料量。  收集使用的**監控和管理應用程式**的執行時間和效能特性。 在您的資料工廠首頁上，選擇 [**監控和管理**]。 在樹狀檢視中，選擇 [**輸出資料集**]。 在 [**活動視窗**] 清單中，選擇 [複製活動執行]。 **活動 Windows**列出複製活動持續時間] 及 [複製資料的大小。 **活動視窗檔案總管**中，會列出處理量。 若要深入瞭解應用程式，請參閱監視器使用及管理 Azure 資料工廠管線監控和管理應用程式 (資料-工廠-監視器-管理-app.md)。  ! 執行 [詳細資料 (./media/data-factory-copy-activity-performance/mmapp-activity-run-details.pn 的活動 | 2016 年 09 月 27 日 |
| 16 | [教學課程︰ 使用 [複製活動使用 Visual Studio 建立管線](data-factory-copy-activity-tutorial-using-visual-studio.md) |   請注意下列幾點:-資料集**類型**] 設定為**AzureBlob**。     - **linkedServiceName**設定為 [ **AzureStorageLinkedService**。 您在步驟 2 中建立此連結的服務。     - **folderPath**設定為 [ **adftutorial**容器。 您也可以指定 blob 中使用**檔案名稱**] 屬性的資料夾的名稱。 您不指定 blob 的名稱，因為所有 blob 容器中的資料會被視為輸入的資料。    -格式**類型**設定為 [ **TextFormat** -文字檔案 ΓÇô**名字**和**姓氏**ΓÇô 並以逗號字元 (**columnDelimiter**) 中有兩個欄位-**顯示狀態**設定為 [**每小時**（**頻率**設為**小時**和**間隔**設為**1**）。 因此，資料工廠尋找輸入資料的每個小時 blob 容器 (**adftutorial**) 您所指定的根資料夾中。  如果您不指定的**輸入**資料集的**檔案名稱**，所有檔案/二進位大型物件從 [輸入] 資料夾 (**folderPath**) 都是 consid | 2016-09-29 |
| 17 | [建立、 監控及管理使用資料工廠.NET SDK Azure 資料工廠](data-factory-create-data-factories-programmatically.md) | 記下應用程式識別碼] 和 [密碼 （用戶端密碼），然後將其用於逐步解說。 **取得 Azure 訂閱和租用戶識別碼**如果您沒有安裝在您的電腦上的 PowerShell 的最新版本，請依照下列指示如何安裝和設定 PowerShell 的 Azure (.../ powershell-安裝-configure.md） 來進行安裝。 1。 啟動 Azure PowerShell，並執行下列命令 2。 執行下列命令，然後輸入使用者名稱和密碼，您用於登入 Azure 入口網站。         登入 AzureRmAccount，如果您有此帳戶相關聯的只有一個 Azure 訂閱，您不需要執行兩個步驟。 3。 執行下列命令以檢視此帳戶的所有訂閱。       取得 AzureRmSubscription 4。 執行下列命令以選取您想要使用的訂閱。 取代**NameOfAzureSubscription** Azure 訂閱的名稱。       取得 AzureRmSubscription-SubscriptionName NameOfAzureSubscription / 設定 AzureRmCo | 2016-09-14 |
| 18 | [管線和 Azure 資料工廠的活動](data-factory-create-pipelines.md) |       [開始]: 「 2016年-07-12T00:00:00Z 」，「 結束 」: 「 2016年-07-13T00:00:00Z 」}} 請注意下列點︰ 在 [活動] 區段中，有只有一個活動**類型**其設定為 [**複製**/。 / 活動輸入設定為 [ **InputDataset** ，並**OutputDataset**調活動的輸出效果。 / 在**typeProperties** ] 區段中，指定**BlobSource**來源類型，然後**SqlSink**指定為接收類型]。 建立這個管線的完整的逐步解說，請參閱教學課程︰ 資料複製到 SQL 資料庫 (data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) Blob 儲存體。 **範例轉換管線**在下列範例管線中，有一個活動的類型**HDInsightHive** [**活動**] 區段中。 在此範例中，HDInsight 登錄區活動 (資料-工廠-登錄區-activity.md) 從轉換資料 Azure Blob 儲存體執行 Azure HDInsight Hadoop 叢集登錄區指令碼檔案。  {"名稱": 「 TransformPipeline"，"p | 2016 年 09 月 27 日 |
| 19 | [轉換 Azure 資料工廠中的資料](data-factory-data-transformation-activities.md) | 資料工廠支援下列資料轉換活動可以新增至的管線 (資料-工廠-建立-pipelines.md) 是個別] 或 [鏈結與另一個活動。 .  AZURE。附註的逐步解說逐步指示，請參閱建立管線與登錄區轉換 (data-factory-build-your-first-pipeline.md) 文件。 **HDInsight 登錄區活動**HDInsight 登錄區活動資料工廠管道跟您自己的登錄區查詢或視/Linux windows HDInsight 叢集。 請參閱登錄區活動 (資料-工廠-登錄區-activity.md) 的文件的詳細資料此活動。 **HDInsight 豬活動**HDInsight 豬活動資料工廠管道跟您自己的豬查詢或視/Linux windows HDInsight 叢集。 請參閱此活動的詳細資料的豬活動 (資料-工廠-豬-activity.md) 文章。 **HDInsight MapReduce 活動**在資料工廠管線 HDInsight MapReduce 活動 y 上執行 MapReduce 程式 | 2016-09 26 |
| 20 | [資料工廠排程和執行](data-factory-scheduling-and-execution.md) | 只有當 CopyActivity1 已經順利執行並 Dataset2 有，就會執行 CopyActivity2。 以下是範例管線 JSON: {"名稱": 「 ChainActivities 」，[內容]: {「 描述 」: 」 執行的活動順序 」，「 活動 」: {「 類型 」: 「 複製 」、 「 typeProperties 」: {「 來源 」: {「 類型 」: 「 BlobSource 」}，「 接收 」: {「 類型 」: 「 BlobSink 「，」 copyBehavior 」: 」 PreserveHierarchy 「，」 writeBatchSize 」: 0，「 writeBatchTimeout 」: 」 00: 00:00 」}}，」 輸入 「: {"名稱": 」 Dataset1 」}，「 輸出 」: {"名稱": 「 Dataset2 」}，」 原則 」: {」 會出現逾時 」: 」 01: 00:00 」}，「 排程器 」: {」 頻率 」: 「 小時 」、 「 間隔 」: 1} 「 名稱 」、: 「 CopyFromBlob1ToBlob2 」，「 描述 」: 」 將資料從 blob 複製到另一個 」}，{「 類型 」: 「 複製 」、 「 typeProperties 」: {「 來源 」: {「 類型 」: 「 BlobSource 」}，「 接收 」: {「 type 」: 「 BlobSink 「，」 writeBatchSize 」: 0，「 writeBatchTimeout 」: 「 00: 00:00 」}}，」 中 | 2016-09 28 |





## <a name="tutorials"></a>教學課程

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 21 | [教學課程︰ 建立您的第一個管線使用 Hadoop 叢集的程序資料](data-factory-build-your-first-pipeline.md) | 本 Azure 資料工廠教學課程中會顯示如何建立及排程資料工廠處理 Hadoop 叢集上使用登錄區指令碼的資料。 |
| 22 | [教學課程︰ 建立您使用 Azure 資源管理員範本的第一個 Azure 資料工廠](data-factory-build-your-first-pipeline-using-arm.md) | 在此教學課程中，您可以建立使用 Azure 資源管理員範本的範例 Azure 資料工廠管線。 |
| 23 | [教學課程︰ 建立您使用 Azure 入口網站的第一個 Azure 資料工廠](data-factory-build-your-first-pipeline-using-editor.md) | 在本教學課程中，您可以建立範例 Azure 資料工廠管線 Azure 入口網站中使用資料工廠編輯器。 |
| 24 | [教學課程︰ 建立您使用 PowerShell 的 Azure 的第一個 Azure 資料工廠](data-factory-build-your-first-pipeline-using-powershell.md) | 在本教學課程中，您可以建立使用 PowerShell 的 Azure 範例 Azure 資料工廠管線。 |
| 25 | [使用 Microsoft Visual Studio 教學課程︰ 建立您 Azure 的第一個資料工廠](data-factory-build-your-first-pipeline-using-vs.md) | 在本教學課程中，您可以建立使用 Visual Studio 範例 Azure 資料工廠管線。 |
| 26 | [教學課程︰ 使用 Azure 入口網站複製活動建立管線](data-factory-copy-activity-tutorial-using-azure-portal.md) | 在本教學課程中，您建立 Azure 資料工廠管線與複製活動 Azure 入口網站中使用資料工廠編輯器。 |
| 27 | [教學課程︰ 建立含使用 PowerShell 的 Azure 複製活動的管線](data-factory-copy-activity-tutorial-using-powershell.md) | 在此教學課程中，您建立 Azure 資料工廠管線複製活動與使用 PowerShell 的 Azure。 |
| 28 | [教學課程︰ 使用 [複製活動使用 Visual Studio 建立管線](data-factory-copy-activity-tutorial-using-visual-studio.md) | 在此教學課程中，您建立 Azure 資料工廠管線複製活動與使用 Visual Studio。 |
| 29 | [資料複製到 SQL 資料庫使用資料工廠 Blob 儲存體](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | 本教學課程教您如何使用 Azure 資料工廠管道的複製活動，將資料複製到 SQL 資料庫 Blob 儲存體。 |
| 30 | [教學課程︰ 建立與使用資料工廠複製精靈複製活動的管線](data-factory-copy-data-wizard-tutorial.md) | 在本教學課程中，您建立 Azure 資料工廠管線複製活動與使用支援的資料工廠複製精靈 |



## <a name="data-movement"></a>移動資料

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 31 | [從 Azure Blob 使用 Azure 資料工廠移動資料](data-factory-azure-blob-connector.md) | 瞭解如何複製中 Azure 資料工廠 blob 資料。 使用我們的範例︰ 如何將資料複製 Azure Blob 儲存體和 Azure SQL 資料庫。 |
| 32 | [從 Azure 資料湖存放區使用 Azure 資料工廠移動資料](data-factory-azure-datalake-connector.md) | 瞭解如何將資料從 Azure 資料湖存放區使用 Azure 資料工廠 |
| 33 | [移動資料 DocumentDB 使用 Azure 資料工廠](data-factory-azure-documentdb-connector.md) | 瞭解如何將資料移到或從使用 Azure 資料工廠 Azure DocumentDB 集合 |
| 34 | [移動資料使用 Azure 資料工廠 Azure SQL 資料庫](data-factory-azure-sql-connector.md) | 瞭解如何將資料從使用 Azure 資料工廠 Azure SQL 資料庫。 |
| 35 | [從 Azure SQL Data Warehouse 使用 Azure 資料工廠移動資料](data-factory-azure-sql-data-warehouse-connector.md) | 瞭解如何將資料從 Azure SQL Data Warehouse 使用 Azure 資料工廠 |
| 36 | [從 Azure 資料表使用 Azure 資料工廠移動資料](data-factory-azure-table-connector.md) | 瞭解如何將資料從使用 Azure 資料工廠 Azure 資料表儲存體。 |
| 37 | [複製活動效能與調整輔助線](data-factory-copy-activity-performance.md) | 瞭解當您使用複製活動時，會影響 Azure 資料工廠資料移動的效能的主要因素。 |
| 38 | [使用複製活動移動資料](data-factory-data-movement-activities.md) | 深入了解資料移動資料工廠管線︰ 資料移轉之間雲端儲存區，以及在內部部署市集和雲端儲存區。 使用複製活動。 |
| 39 | [資料管理閘道器的版本資訊](data-factory-gateway-release-notes.md) | 資料管理閘道器 tory 版本資訊 |
| 40 | [將資料從內部部署 HDFS 使用 Azure 資料工廠移動](data-factory-hdfs-connector.md) | 進一步瞭解如何將內部部署 HDFS 使用 Azure 資料工廠的資料。 |
| 41 | [監控和管理 Azure 資料工廠管線使用新的監控和管理應用程式](data-factory-monitor-manage-app.md) | 瞭解如何使用監控和管理 Azure 資料工廠和管線的監控和管理應用程式。 |
| 42 | [資料管理閘道器內部部署與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) | 將資料閘道器設定為內部部署與雲端之間移動資料。 Azure 資料工廠中使用資料管理閘道器移動資料。 |
| 43 | [移動資料使用 Azure 資料工廠 OData 從來源](data-factory-odata-connector.md) | 瞭解如何將資料來源的 OData 使用 Azure 資料工廠。 |
| 44 | [將資料從 ODBC 資料存放區使用 Azure 資料工廠](data-factory-odbc-connector.md) | 進一步瞭解如何將使用 Azure 資料工廠 ODBC 資料儲存區中的資料。 |
| 45 | [使用 Azure 資料工廠 DB2 移動資料](data-factory-onprem-db2-connector.md) | 進一步瞭解如何將使用 Azure 資料工廠 DB2 資料庫中的資料 |
| 46 | [使用將資料移至並從內部部署檔案系統 Azure 資料工廠](data-factory-onprem-file-system-connector.md) | 瞭解如何使用 Azure 資料工廠內部部署檔案系統中往返移動資料。 |
| 47 | [將資料從 MySQL 使用 Azure 資料工廠移動](data-factory-onprem-mysql-connector.md) | 進一步瞭解如何將使用 Azure 資料工廠 MySQL 資料庫中的資料。 |
| 48 | [將資料從內部部署 Oracle 使用 Azure 資料工廠移動](data-factory-onprem-oracle-connector.md) | 瞭解如何將資料從 Oracle 資料庫的部署使用 Azure 資料工廠。 |
| 49 | [將資料從 PostgreSQL 使用 Azure 資料工廠移動](data-factory-onprem-postgresql-connector.md) | 進一步瞭解如何將使用 Azure 資料工廠 PostgreSQL 資料庫中的資料。 |
| 50 | [將資料從 Sybase 使用 Azure 資料工廠移動](data-factory-onprem-sybase-connector.md) | 進一步瞭解如何將資料從 Sybase 資料庫使用 Azure 資料工廠移。 |
| 51 | [將資料從 Teradata 使用 Azure 資料工廠移動](data-factory-onprem-teradata-connector.md) | 瞭解資料工廠服務可讓您將資料從 Teradata 資料庫移 Teradata 連接器 |
| 52 | [移動資料 SQL Server 內部部署或 IaaS (Azure VM) 上使用 Azure 資料工廠](data-factory-sqlserver-connector.md) | 瞭解如何將資料從內部部署的 SQL Server 資料庫或使用 Azure 資料工廠 Azure VM。 |
| 53 | [移動資料來源的 Web 資料表使用 Azure 資料工廠](data-factory-web-table-connector.md) | 進一步瞭解如何將內部部署中的資料使用 Azure 資料工廠網頁中的表格。 |



## <a name="data-transformation"></a>資料轉換

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 54 | [建立預測使用 Azure 電腦學習活動的管線](data-factory-azure-ml-batch-execution-activity.md) | 說明如何建立建立預測使用 Azure 資料工廠和 Azure 電腦學習的管線 |
| 55 | [計算連結的服務](data-factory-compute-linked-services.md) | 瞭解您可以使用 Azure 資料工廠管線轉換處理資料計算 enviornments。 |
| 56 | [使用資料工廠和批次的程序大型資料集](data-factory-data-processing-using-batch.md) | 說明如何使用 Azure 批次的平行處理功能處理大量 Azure 資料工廠管線中的資料。 |
| 57 | [轉換 Azure 資料工廠中的資料](data-factory-data-transformation-activities.md) | 瞭解如何轉換資料或程序中的資料使用 Hadoop、 電腦學習或 Azure 資料湖分析 Azure 資料工廠。 |
| 58 | [Hadoop 串流活動](data-factory-hadoop-streaming-activity.md) | 瞭解如何使用 Hadoop 串流活動 Azure 資料工廠上視需要/您自己的 HDInsight 叢集上執行 Hadoop 串流的程式。 |
| 59 | [登錄區活動](data-factory-hive-activity.md) | 瞭解如何使用登錄區活動 Azure 資料工廠上視需要/您自己的 HDInsight 叢集上執行登錄區查詢。 |
| 60 | [呼叫 [從資料工廠 MapReduce 程式](data-factory-map-reduce.md) | 瞭解如何從 Azure 資料工廠，Azure HDInsight 叢集上執行 MapReduce 程式來處理資料。 |
| 61 | [豬活動](data-factory-pig-activity.md) | 瞭解如何使用豬活動 Azure 資料工廠上視需要/您自己的 HDInsight 叢集上執行的豬指令碼。 |
| 62 | [呼叫 [從資料工廠火花程式](data-factory-spark.md) | 瞭解如何叫用從使用 MapReduce 活動 Azure 資料工廠火花程式。 |
| 多為 63 | [SQL Server 預存程序活動](data-factory-stored-proc-activity.md) | 瞭解如何使用 SQL Server 預存程序活動叫用 Azure SQL 資料庫或 Azure SQL Data Warehouse 中的從資料工廠管線預存程序。 |
| 64 | [Azure 資料工廠管線中使用自訂活動](data-factory-use-custom-activities.md) | 瞭解如何建立自訂活動和 Azure 資料工廠管線中使用它們。 |
| 65 | [從 Azure 資料工廠 Azure 資料湖分析執行 U SQL 指令碼](data-factory-usql-activity.md) | 瞭解如何透過 Azure 資料湖分析計算服務執行 U SQL 指令碼處理資料。 |



## <a name="samples"></a>範例

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 66 | [Azure 資料工廠-範例](data-factory-samples.md) | Azure 資料工廠服務提供詳細範例。 |



## <a name="use-cases"></a>使用案例

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 67 | [客戶案例研究](data-factory-customer-case-studies.md) | 深入了解如何我們的客戶部分使用 Azure 資料工廠。 |
| 68 | [使用大小寫的客戶剖析](data-factory-customer-profiling-usecase.md) | 瞭解如何 Azure 資料工廠用來建立的資料導向工作流程 （管線） 若要設定檔遊戲客戶。 |
| 69 | [使用案例-產品建議](data-factory-product-reco-usecase.md) | 深入了解以及其他服務中使用 Azure 資料工廠實作使用大小寫。 |



## <a name="monitor-and-manage"></a>監控和管理

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 70 | [監控和管理 Azure 資料工廠管線](data-factory-monitor-manage-pipelines.md) | 瞭解如何使用 Azure 入口網站與 PowerShell 的 Azure 監控和管理 Azure 資料工廠和管線您所建立。 |



## <a name="sdk"></a>SDK

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 71 | [Azure 資料工廠.NET API 變更記錄](data-factory-api-change-log.md) | 說明重大變更的新增功能、 修正錯誤等...Azure 資料工廠.NET API 特定版本。 |
| 72 | [建立、 監控及管理使用資料工廠.NET SDK Azure 資料工廠](data-factory-create-data-factories-programmatically.md) | 瞭解如何以程式設計方式建立、 監控及使用資料工廠 SDK 管理 Azure 資料工廠。 |
| 73 | [Azure 資料工廠開發人員參考](data-factory-sdks.md) | 瞭解不同的方式來建立、 監控及管理 Azure 資料工廠 |



## <a name="miscellaneous"></a>其他

| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 74 | [Azure 資料工廠-常見問題集](data-factory-faq.md) | 常見問題集 Azure 資料工廠。 |
| 75 | [Azure 資料工廠-函式和系統變數](data-factory-functions-variables.md) | 提供 Azure 資料工廠函數和系統變數的清單 |
| 76 | [Azure 資料工廠-命名規則](data-factory-naming-rules.md) | 說明資料工廠實體命名規則。 |
| 77 | [資料工廠問題進行疑難排解](data-factory-troubleshoot.md) | 瞭解如何使用 Azure 資料工廠問題進行疑難排解。 |

