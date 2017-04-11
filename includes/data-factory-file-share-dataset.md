## <a name="fileshare-dataset-type-properties"></a>檔案共用資料集類型屬性

節與適用於定義資料集的內容的完整清單，請參閱[建立資料集](../articles/data-factory/data-factory-create-datasets.md)。 例如結構、 可用性和資料集 JSON 原則節類似的所有資料集類型。 

**TypeProperties**區段是不同的資料集的每個類型的。 會提供特定資料集類型的資訊。 類型**檔案共用**資料集的資料集 typeProperties 區段具有下列屬性︰

屬性 | 描述 | 所需
-------- | ----------- | --------
folderPath | 子資料夾的路徑。 使用逸出字元 ' \ 」 字串中的特殊字元。 如需範例，請參閱[範例連結的服務與資料集的定義](#sample-linked-service-and-dataset-definitions)。<br/><br/>您可以結合此屬性與**partitionBy**有根據扇形區資料夾路徑開始/結束日期時間。 | [是]
檔案名稱 | 如果您想要參照的資料夾中的特定檔案，請在 [ **folderPath**中指定檔案的名稱。 如果您沒有指定此屬性的任何值，表格會指向資料夾中的所有檔案。<br/><br/>輸出資料集未指定的檔案名稱時所產生的檔案名稱就會在下列格式︰ <br/><br/>資料。<Guid>.txt (範例︰ Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | 否
partitionedBy | partitionedBy 可用來指定動態 folderPath，時間數列資料檔名。 例如，folderPath 參數化資料的每個小時。 | 否
格式 | 支援下列格式類型︰ **TextFormat**與**AvroFormat**， **JsonFormat**， **OrcFormat**。 [格式] 下的 [**類型**] 屬性設其中一個值。 請參閱[指定 TextFormat](#specifying-textformat)、[指定 AvroFormat](#specifying-avroformat)、[指定 JsonFormat](#specifying-jsonformat)，以及[指定 OrcFormat](#specifying-orcformat)區段，如需詳細資訊。 如果您想要複製檔案另存為-是檔案式存放區之間 （二進位複本），您可以略過兩邊輸入與輸出資料集的定義中的 [格式] 區段。 | 否
取值 | 指定篩選，以用來選取子集 folderPath 中的檔案，而不是所有檔案。<br/><br/>允許的值︰ `*` （多個字元） 和`?`（單一字元）。<br/><br/>範例 1:`"fileFilter": "*.log"`<br/>範例 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> 取值是適用於輸入檔案共用資料集。 HDFS 不支援此屬性。  | 否
| 壓縮 | 指定類型和層級壓縮的資料。 支援的類型︰ **GZip**與**結實**，及**BZip2**支援層級是︰ **Optimal**和**最快**。 目前，壓縮設定不支援**AvroFormat**或**OrcFormat**中的資料。 如需詳細資訊，請參閱[壓縮支援](#compression-support)一節。  | 否 |
| useBinaryTransfer | 指定是否使用二進位傳輸模式。 二進位模式與 false 的 ASCII，則為 true。 預設值︰ True。 類型的相關聯的連結的服務類型時，可以只使用此屬性︰ FtpServer。 | 否 | 
 

> [AZURE.NOTE] 無法同時使用檔案名稱和取值。

### <a name="using-partionedby-property"></a>使用 partionedBy 屬性

如前一節所述，您可以指定動態 folderPath，與 partitionedBy 時間數列資料檔名。 您可以執行資料 Factory 巨集與系統變數 SliceStart，SliceEnd 表示指定的資料扇形區邏輯的時間範圍。 

若要瞭解時間數列資料集、 排程和扇形區，請參閱[建立資料集](../articles/data-factory/data-factory-create-datasets.md)、[排程及執行](../articles/data-factory/data-factory-scheduling-and-execution.md)，並[建立管線](../articles/data-factory/data-factory-create-pipelines.md)文章。 

#### <a name="sample-1"></a>範例 1:

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy": 
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

在此範例中 {扇形區} 會取代格式 (YYYYMMDDHH) 中的資料工廠系統變數 SliceStart 指定的值。 SliceStart 指的是扇形區的開始時間。 FolderPath 是不同的每個扇形區項目。 範例︰ wikidatagateway/wikisampledataout/2014100103 或 wikidatagateway/wikisampledataout/2014100104。

#### <a name="sample-2"></a>範例 2:

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

在此範例中，年、 月、 日和 SliceStart 時間擷取到另一個變數所使用的 folderPath 和檔名] 屬性。
