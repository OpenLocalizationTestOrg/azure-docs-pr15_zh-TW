<properties
pageTitle="商務用 OneDrive |Microsoft Azure"
description="Azure 應用程式服務建立邏輯應用程式。 連線至 OneDrive for Business 來管理您的檔案。 您可以執行各種動作，例如 [上傳]，更新，請取得，，刪除的檔案。"
services="logic-apps"   
documentationCenter=".net,nodejs,java"  
authors="msftman"   
manager="erikre"    
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-onedrive-for-business-connector"></a>快速入門連接器商務用 OneDrive

連線至 OneDrive for Business 來管理您的檔案。 您可以執行各種動作，例如 [上傳]，更新，請取得，，刪除的檔案。

>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2015年-08-01-預覽結構描述版本。 

您可以開始時，現在建立邏輯應用程式，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作

為的動作; 可連接器商務用 OneDrive有觸發程序。 所有的連接器支援 JSON 和 XML 格式的資料。 

 連接器商務用 OneDrive 有以下的動作及/或可用的觸發程序︰

### <a name="onedrive-for-business-actions"></a>動作商務用 OneDrive
您可以採取下列動作︰

|巨集指令|描述|
|--- | ---|
|[GetFileMetadata](connectors-create-api-onedriveforbusiness.md#getfilemetadata)|擷取使用識別碼的商務用 OneDrive 中檔案的中繼資料|
|[UpdateFile](connectors-create-api-onedriveforbusiness.md#updatefile)|更新商務用 onedrive 檔案|
|[DeleteFile](connectors-create-api-onedriveforbusiness.md#deletefile)|從商務用 OneDrive 中刪除的檔案|
|[GetFileMetadataByPath](connectors-create-api-onedriveforbusiness.md#getfilemetadatabypath)|擷取使用路徑的商務用 OneDrive 中檔案的中繼資料|
|[GetFileContentByPath](connectors-create-api-onedriveforbusiness.md#getfilecontentbypath)|擷取使用路徑的商務用 OneDrive 中檔案的內容|
|[GetFileContent](connectors-create-api-onedriveforbusiness.md#getfilecontent)|擷取使用識別碼的商務用 OneDrive 中檔案的內容|
|[Createfile 而](connectors-create-api-onedriveforbusiness.md#createfile)|上傳到商務用 OneDrive 的檔案|
|[CopyFile](connectors-create-api-onedriveforbusiness.md#copyfile)|將檔案複製到商務用 OneDrive|
|[ListFolder](connectors-create-api-onedriveforbusiness.md#listfolder)|OneDrive for Business 資料夾清單檔案|
|[ListRootFolder](connectors-create-api-onedriveforbusiness.md#listrootfolder)|列出 OneDrive 中的 [商務根資料夾中的檔案|
|[ExtractFolderV2](connectors-create-api-onedriveforbusiness.md#extractfolderv2)|擷取至商務用 OneDrive 資料夾|
### <a name="onedrive-for-business-triggers"></a>商務用 OneDrive 觸發程序
您可以接聽這些事件︰

|觸發程序 | 描述|
|--- | ---|
|當建立的檔案|在商務用 OneDrive 資料夾中建立新的檔案時，將流量的觸發程序|
|修改檔案|在商務用 OneDrive 資料夾中修改檔案時，會觸發流程|


## <a name="create-a-connection-to-onedrive-for-business"></a>建立連線到商務用 OneDrive
若要建立邏輯應用程式與商務用 OneDrive，您必須先建立的**連線**，然後提供下列屬性的詳細資料︰ 

|屬性| 所需|描述|
| ---|---|---|
|權杖|[是]|提供商務認證的 OneDrive|
您建立的連線之後，您可以執行的動作並接聽本文所述的觸發程序使用它。 

>[AZURE.INCLUDE [Steps to create a connection to OneDrive for Business](../../includes/connectors-create-api-onedriveforbusiness.md)]

>[AZURE.TIP] 您可以在其他邏輯應用程式中使用此連線。

## <a name="reference-for-onedrive-for-business"></a>針對商務用 onedrive 的參照
適用於版本︰ 1.0

## <a name="getfilemetadata"></a>GetFileMetadata
取得檔案的中繼資料使用識別碼︰ 擷取使用識別碼的商務用 OneDrive 中檔案的中繼資料 

```GET: /datasets/default/files/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|字串|[是]|路徑|無|指定的檔案|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="updatefile"></a>UpdateFile
更新檔案︰ 更新商務用 onedrive 檔案 

```PUT: /datasets/default/files/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|字串|[是]|路徑|無|指定要更新的檔案|
|本文| |[是]|本文|無|在商務用 OneDrive 中更新檔案內容|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="deletefile"></a>DeleteFile
刪除檔案︰ 從商務用 OneDrive 中刪除的檔案 

```DELETE: /datasets/default/files/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|字串|[是]|路徑|無|指定要刪除的檔案|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="getfilemetadatabypath"></a>GetFileMetadataByPath
取得檔案的中繼資料使用路徑︰ 擷取使用路徑的商務用 OneDrive 中檔案的中繼資料 

```GET: /datasets/default/GetFileByPath``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|路徑|字串|[是]|查詢|無|商務用 OneDrive 中檔案的唯一路徑|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="getfilecontentbypath"></a>GetFileContentByPath
取得使用路徑的檔案內容︰ 擷取使用路徑的商務用 OneDrive 中檔案的內容 

```GET: /datasets/default/GetFileContentByPath``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|路徑|字串|[是]|查詢|無|商務用 OneDrive 中檔案的唯一路徑|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="getfilecontent"></a>GetFileContent
取得檔案內容使用識別碼︰ 擷取使用識別碼的商務用 OneDrive 中檔案的內容 

```GET: /datasets/default/files/{id}/content``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|字串|[是]|路徑|無|指定的檔案|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="createfile"></a>Createfile 而
建立檔案︰ 上傳到商務用 OneDrive 的檔案 

```POST: /datasets/default/files``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|folderPath|字串|[是]|查詢|無|若要將檔案上傳到商務用 OneDrive 資料夾路徑|
|名稱|字串|[是]|查詢|無|在商務用 OneDrive 中建立的檔案名稱|
|本文| |[是]|本文|無|若要上傳到商務用 OneDrive 檔案的內容|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="copyfile"></a>CopyFile
複製檔案︰ 將檔案複製到商務用 OneDrive 

```POST: /datasets/default/copyFile``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|來源|字串|[是]|查詢|無|Url 來源檔案|
|目的地|字串|[是]|查詢|無|在商務用 OneDrive，包括目標檔案名稱中的目的檔案路徑|
|覆寫|布林值|沒有|查詢|false|如果將會覆寫目的檔案設為 「 true 」|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="onnewfile"></a>OnNewFile
當建立的檔案︰ 在商務用 OneDrive 資料夾中建立新的檔案時，將流量的觸發程序 

```GET: /datasets/default/triggers/onnewfile``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|folderId|字串|[是]|查詢|無|指定的資料夾|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="onupdatedfile"></a>OnUpdatedFile
修改檔案︰ 在商務用 OneDrive 資料夾中修改檔案時，會觸發流程 

```GET: /datasets/default/triggers/onupdatedfile``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|folderId|字串|[是]|查詢|無|指定的資料夾|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="listfolder"></a>ListFolder
清單資料夾中的檔案︰ 列出中 OneDrive for Business 資料夾中的檔案 

```GET: /datasets/default/folders/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|字串|[是]|路徑|無|指定的資料夾|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="listrootfolder"></a>ListRootFolder
清單根資料夾︰ 列出 OneDrive 中的 [商務根資料夾中的檔案 

```GET: /datasets/default/folders``` 

沒有這個呼叫參數
#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="extractfolderv2"></a>ExtractFolderV2
擷取資料夾︰ 擷取到商務用 OneDrive 資料夾 

```POST: /datasets/default/extractFolderV2``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|來源|字串|[是]|查詢|無|封存檔案路徑|
|目的地|字串|[是]|查詢|無|若要擷取的封存內容的商務用 OneDrive 中的路徑|
|覆寫|布林值|沒有|查詢|false|如果將會覆寫的目標檔案設為 「 true 」|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="object-definitions"></a>物件定義 

### <a name="datasetsmetadata"></a>DataSetsMetadata


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|表格式|未定義|無 |
|blob|未定義|無 |



### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|來源|字串|無 |
|顯示名稱|字串|無 |
|urlEncoding|字串|無 |
|tableDisplayName|字串|無 |
|tablePluralName|字串|無 |



### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|來源|字串|無 |
|顯示名稱|字串|無 |
|urlEncoding|字串|無 |



### <a name="blobmetadata"></a>BlobMetadata


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|識別碼|字串|無 |
|名稱|字串|無 |
|顯示名稱|字串|無 |
|路徑|字串|無 |
|上次修改|字串|無 |
|大小|整數|無 |
|媒體類型|字串|無 |
|IsFolder|布林值|無 |
|ETag|字串|無 |
|FileLocator|字串|無 |



### <a name="object"></a>物件


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|


## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)