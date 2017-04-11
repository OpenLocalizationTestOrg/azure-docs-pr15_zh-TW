<properties
    pageTitle="將方塊連接器新增至您的邏輯應用程式 |Microsoft Azure"
    description="REST API 參數的方塊連接器的概觀"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-box-connector"></a>快速入門] 方塊中連接器
連線至] 方塊中，並建立檔案、 刪除檔案等。 

>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2015年-08-01-預覽結構描述版本。

使用] 方塊中，您可以︰

- 建立您的商務流程根據您收到來自] 方塊中的資料。 
- 建立或更新檔案時，請使用引動程序。
- 使用動作，將檔案複製到、 刪除檔案]，及其他內容。 這些動作獲得回應，然後再輸出可用的其他動作。 例如，當檔案會變更] 方塊中，就可以該檔案，並使用 Office 365 電子郵件。

若要新增作業邏輯應用程式中，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作
] 方塊中會包含下列的觸發程序和動作。

| 觸發程序 | 動作|
| --- | --- |
|<ul><li>當建立的檔案</li><li>修改檔案</li></ul> | <ul><li>建立檔案</li><li>當建立的檔案</li><li>複製檔案</li><li>刪除檔案</li><li>擷取封存資料夾</li><li>取得使用識別碼檔案內容</li><li>取得使用路徑的檔案內容</li><li>取得檔案的中繼資料使用識別碼</li><li>取得檔案的中繼資料使用的路徑</li><li>更新檔案</li><li>修改檔案</li></ul>

所有的連接器支援 JSON 和 XML 格式的資料。

## <a name="create-a-connection-to-box"></a>建立的連線] 方塊
當您新增此連接器至您的邏輯應用程式時，您必須授權邏輯應用程式連線到您的方塊。

>[AZURE.INCLUDE [Steps to create a connection to box](../../includes/connectors-create-api-box.md)]

您建立的連線之後，您可以輸入內容。 **REST API 參考**本主題中說明這些屬性。

>[AZURE.TIP] 在其他邏輯應用程式中，您可以使用此相同的方塊連線。

## <a name="swagger-rest-api-reference"></a>Swagger REST API 參照
適用於版本︰ 1.0。

### <a name="create-file"></a>建立檔案
上傳檔案的方塊。  
```POST: /datasets/default/files```

| 名稱|資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|folderPath|字串|[是]|查詢|無 |若要上傳檔案至] 方塊中的資料夾路徑|
|名稱|字串|[是]|查詢|無 |若要在] 方塊中所建立的檔案名稱|
|本文|string(binary) |[是]|本文|無 |檔案上傳至] 方塊中的內容|

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


### <a name="when-a-file-is-created"></a>當建立的檔案
方塊資料夾中建立新的檔案時，觸發程序的流程。  
```GET: /datasets/default/triggers/onnewfile```

| 名稱|資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|folderId|字串|[是]|查詢|無 |資料夾] 方塊中的唯一識別碼|

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


### <a name="copy-file"></a>複製檔案
將檔案複製到] 方塊。  
```POST: /datasets/default/copyFile```

| 名稱|資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|來源|字串|[是]|查詢|無 |Url 來源檔案|
|目的地|字串|[是]|查詢| 無|在方塊中，包括目標檔名的目的檔案路徑|
|覆寫|布林值|無|查詢| 無|如果將會覆寫目的檔案設為 「 true 」|

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


### <a name="delete-file"></a>刪除檔案
刪除的檔案] 方塊。  
```DELETE: /datasets/default/files/{id}```


| 名稱|資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|字串|[是]|路徑|無 |若要刪除方塊的檔案的唯一識別碼|

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


### <a name="extract-archive-to-folder"></a>擷取封存資料夾
到] 方塊中的資料夾中抽選封存檔 (範例︰.zip)。  
```POST: /datasets/default/extractFolderV2```

| 名稱|資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|來源|字串|[是]|查詢| |封存檔案路徑|
|目的地|字串|[是]|查詢| |若要擷取的封存內容] 方塊中路徑|
|覆寫|布林值|無|查詢| |如果將會覆寫的目標檔案設為 「 true 」|

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


### <a name="get-file-content-using-id"></a>取得使用識別碼檔案內容
從 [識別碼] 方塊中擷取檔案內容。  
```GET: /datasets/default/files/{id}/content```

| 名稱|資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|字串|[是]|路徑|無 |唯一識別碼] 方塊中的檔案|

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


### <a name="get-file-content-using-path"></a>取得使用路徑的檔案內容
從使用路徑] 方塊中擷取檔案內容。  
```GET: /datasets/default/GetFileContentByPath```

| 名稱|資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|路徑|字串|[是]|查詢|無 |唯一在] 方塊中的檔案路徑|

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


### <a name="get-file-metadata-using-id"></a>取得檔案的中繼資料使用識別碼
從使用檔案識別碼] 方塊中擷取檔案的中繼資料。  
```GET: /datasets/default/files/{id}```

| 名稱|資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|字串|[是]|路徑| 無|唯一識別碼] 方塊中的檔案|

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


### <a name="get-file-metadata-using-path"></a>取得檔案的中繼資料使用的路徑
從使用路徑] 方塊中擷取檔案的中繼資料。  
```GET: /datasets/default/GetFileByPath```

| 名稱|資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|路徑|字串|[是]|查詢|無 |唯一在] 方塊中的檔案路徑|

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


### <a name="update-file"></a>更新檔案
更新] 方塊中的檔案。  
```PUT: /datasets/default/files/{id}```

| 名稱|資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|字串|[是]|路徑| 無|若要在] 方塊中更新檔案的唯一識別碼|
|本文|string(binary) |[是]|本文|無 |若要在] 方塊中更新檔案內容|

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


### <a name="when-a-file-is-modified"></a>修改檔案
在方塊中的資料夾中修改檔案時，會觸發流程。  
```GET: /datasets/default/triggers/onupdatedfile```

| 名稱|資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|folderId|字串|[是]|查詢|無 |資料夾] 方塊中的唯一識別碼|

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="object-definitions"></a>物件定義

#### <a name="datasetsmetadata"></a>DataSetsMetadata

|屬性名稱 | 資料類型 | 所需|
|---|---|---|
|表格式|未定義|沒有|
|blob|未定義|沒有|

#### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|來源|字串|沒有|
|顯示名稱|字串|沒有|
|urlEncoding|字串|沒有|
|tableDisplayName|字串|沒有|
|tablePluralName|字串|沒有|

#### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|來源|字串|沒有|
|顯示名稱|字串|沒有|
|urlEncoding|字串|沒有|

#### <a name="blobmetadata"></a>BlobMetadata

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|識別碼|字串|沒有|
|名稱|字串|沒有|
|顯示名稱|字串|沒有|
|路徑|字串|沒有|
|上次修改|字串|沒有|
|大小|整數|沒有|
|媒體類型|字串|沒有|
|IsFolder|布林值|沒有|
|ETag|字串|沒有|
|FileLocator|字串|沒有|

## <a name="next-steps"></a>後續步驟

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。
