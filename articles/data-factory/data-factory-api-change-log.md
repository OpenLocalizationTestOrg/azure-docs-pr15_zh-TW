<properties 
    pageTitle="資料工廠-.NET API 變更記錄 |Microsoft Azure" 
    description="說明重大變更的新增功能、 修正錯誤等...Azure 資料工廠.NET API 特定版本。" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="spelluru"/>

# <a name="azure-data-factory---net-api-change-log"></a>Azure 資料工廠.NET API 變更記錄 
本文會提供在特定的版本中 Azure 資料工廠 sdk 的變更的相關資訊。 您可以找到最新的 NuGet 套件的 Azure 資料工廠[以下](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) 

## <a name="version-4110"></a>版本 4.11.0
新增功能︰

- 已新增下列連結的服務類型︰
    - [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
    - [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
    - [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
- 已新增下列資料集類型︰ 
    - [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
    - [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
- 已新增下列複製來源類型︰
    - [MongoDbSource](https://msdn.microsoft.com/en-US/library/mt765123.aspx)

## <a name="version-4100"></a>版本 4.10.0
- TextFormat 已經新增下列選用的屬性︰
    - [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
    - [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
    - [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
- 已新增下列連結的服務類型︰
    - [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
    - [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
- 已新增下列資料集類型︰
    - [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
- 已新增下列複製來源類型︰
    - [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
- 新增 AzureMLBatchExecutionActivity [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx)屬性 
    - 啟用傳遞多個 web 服務輸入 Azure 電腦學習實驗


## <a name="version-491"></a>版本 4.9.1

### <a name="bug-fix"></a>錯誤修正

- 取代[WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx)WebApi 式驗證。

## <a name="version-490"></a>版本 4.9.0

### <a name="feature-additions"></a>新增功能

- 將 CopyActivity [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx)和[StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx)屬性。 如需詳細資料] 功能，請參閱[分段複製](data-factory-copy-activity-performance.md#staged-copy)。


### <a name="bug-fix"></a>錯誤修正

- 介紹[ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx)方法，可接受[ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx)執行個體的超量的狀況。
- [標示為選用中 CopySink [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx)和[WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) 。

## <a name="version-480"></a>版本 4.8.0

### <a name="feature-additions"></a>新增功能
- 若要啟用的複製效能調整複製活動類型已新增下列選用的屬性︰
    - [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
    - [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>版本 4.7.0

### <a name="feature-additions"></a>新增功能
* 新增新 StorageFormat 類型[OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx)複製最佳化的資料列的欄 (ORC) 格式的檔案。
* 將 SqlDWSink [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx)和 PolyBaseSettings 屬性。
    * 啟用的將資料複製到 SQL Data Warehouse PolyBase。

## <a name="version-461"></a>版本 4.6.1

### <a name="bug-fixes"></a>錯誤修正
* 修正列出活動 windows HTTP 要求。
    * 資源群組名稱] 和 [資料工廠名稱移除邀請內容。

## <a name="version-460"></a>版本 4.6.0

### <a name="feature-additions"></a>新增功能

- [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx)已經新增下列屬性︰
    - [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
    - [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
    - [資料集](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
- [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx)已經新增下列屬性︰
    - [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
- 新增新[StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx)輸入[JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx)類型定義其資料是採用 JSON 格式的資料集。 

## <a name="version-450"></a>版本 4.5.0

### <a name="feature-additions"></a>新增功能
* 新增的[清單活動視窗作業](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx)。
    * 新增擷取活動 windows 篩選依據的實體類型 （也就是資料工廠、 資料集、 管線與活動） 的方法。
* 已新增下列連結的服務類型︰ 
    * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx) [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* 已新增下列資料集類型︰ 
    * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx) [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* 已新增下列複製來源類型︰  
    * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>版本 4.4.0

### <a name="feature-additions"></a>新增功能

- 下列連結的服務類型] 已新增為資料來源，且接收複製活動︰
    - [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx)。 請參閱[Azure 儲存 SA 連結服務](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service)概念性資訊與範例。 

## <a name="version-430"></a>版本 4.3.0

### <a name="feature-additions"></a>新增功能

- 下列連結的服務類型樂園已新增為資料來源的複製活動︰
    - [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx)。 請參閱[將資料從 HDFS 使用資料工廠移](data-factory-hdfs-connector.md)概念性資訊與範例。 
    - [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx)。 請參閱[將資料從 ODBC 資料會儲存使用 Azure 資料工廠](data-factory-odbc-connector.md)概念性資訊與範例。 

## <a name="version-420"></a>版本 4.2.0

### <a name="feature-additions"></a>新增功能

- 已新增下列新的活動類型︰ [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx)。 如需關於活動的詳細資訊，請參閱[使用更新資源活動的更新 Azure 毫升模型](data-factory-azure-ml-batch-execution-activity.md#updating-azure-ml-models-using-the-update-resource-activity)。
- 新的選用屬性[updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx)已新增至[AzureMLLinkedService 類別](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx)]。 
- [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx)和[LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx)屬性已新增至[DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx)課程。 
- 允許用戶端的來電至] 資料工廠服務逾時的設定。 


## <a name="version-410"></a>版本 4.1.0

### <a name="feature-additions"></a>新增功能
* 已新增下列連結的服務類型︰ 
    * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
    * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* 已新增下列活動類型︰ 
    * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* 已新增下列資料集類型︰ 
    * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* 已新增下列來源與接收類型複製活動︰
    * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
    * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)


## <a name="version-401"></a>版本 4.0.1

### <a name="breaking-changes"></a>重大變更
下列類別已經重新命名。 新名稱的原始的類別名稱，再放開 4.0.0。 
 
4.0.0 中的名稱 | 4.0.1 中的名稱
:------------ | :------------ 
AzureSqlDataWarehouseDataset | [AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx)
AzureSqlDataset | [AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx)
AzureDataset | [AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx)
OracleDataset | [OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx)
RelationalDataset | [RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx)
SqlServerDataset | [SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx)


## <a name="version-400"></a>版本 4.0.0

### <a name="breaking-changes"></a>重大變更



- 下列類別/介面已經重新命名。

| 舊的名稱 | 新的名稱 |
| :-------- | :-------- |
| ITableOperations | [IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |  
| 表格 | [資料集](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) | 
| TableProperties | [DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) | 
| TableTypeProprerties | [DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters | [DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) | 
| TableCreateOrUpdateResponse | [DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) | 
| TableGetResponse | [DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) | 
| TableListResponse | [DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters | [DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) | 
    

- **清單**方法現在傳回分頁的結果。 如果回應包含的非空白**NextLink**屬性，用戶端應用程式必須繼續擷取的下一個頁面，直到會傳回所有頁面。  以下是範例︰ 

        PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
        var pipelines = new List<Pipeline>(response.Pipelines);
    
        string nextLink = response.NextLink;
        while (string.IsNullOrEmpty(response.NextLink))
        {
            PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
            pipelines.AddRange(nextResponse.Pipelines);
    
            nextLink = nextResponse.NextLink;
        }
    
- **清單**管線 API 傳回管線，而不是完整詳細資料的摘要。 例如，管線摘要中的活動只能包含名稱與類型。

### <a name="feature-additions"></a>新增功能
- [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx)類別支援兩個新屬性**SliceIdentifierColumnName** ， **SqlWriterCleanupScript**，支援冪複製至 Azure SQL Data Warehouse。 請參閱[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) ，[特別是機制 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1)和[機制 2](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2)節，如需詳細資訊，這些屬性。

- 我們現在支援預存程序正在 Azure SQL 資料庫與 Azure SQL Data Warehouse 來源，做為複製活動的一部分。 [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx)和[SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx)類別有下列屬性︰ **SqlReaderStoredProcedureName**和**StoredProcedureParameters**。 請參閱[Azure SQL 資料庫](data-factory-azure-sql-connector.md#sqlsource)及[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) Azure.com 上的下列屬性的詳細資料。  