<properties
pageTitle="新增 Excel 連接器 |Microsoft Azure"
description="Excel 有連接器 REST API 參數的概觀"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="08/23/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-excel-connector"></a>開始使用 Excel 連接器

目前，則表示沒有 Excel 連接器邏輯應用程式。 

## <a name="to-use-excel-data"></a>若要使用 Excel 資料
您可以將 Excel 資料儲存為逗點分隔值 (CSV) 檔案中儲存的資料夾，例如[OneDrive](connectors-create-api-onedrive.md)。 您也可以使用此 CSV 檔案與[一般檔案連接器](../app-service-logic/app-service-logic-enterprise-integration-flatfile.md)。

<!---

There is no Excel connector in Logic Apps. Originally, this topic only referenced PowerApps. Removed all PowerApps references. 



Connect to Excel to insert a row, delete a row, and more. 

## Triggers and actions
Excel includes the following action. There are no triggers. 

|Trigger|Actions|
|--- | ---|
|None | <ul><li>Get rows</li><li>Insert row</li><li>Delete row</li><li>Get row</li><li>Get tables</li><li>Update row</li></ul>

All connectors support data in JSON and XML formats. 

## Swagger REST API reference
Applies to version: 1.0.

### Inserts a new row into an Excel table
```POST: /datasets/{dataset}/tables/{table}/items``` 



| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|none|Excel file name|
|table|string|yes|path|none|Excel table name|
|item| |yes|body|none|Row to insert into the specified Excel table|


### Response

|Name|Description|
|---|---|
|200|OK|
|default|Operation Failed.|




### Retrieves a single row from an Excel table
```GET: /datasets/{dataset}/tables/{table}/items/{id}``` 



| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|none|Excel file name|
|table|string|yes|path|none|Excel table name|
|id|string|yes|path|none|Unique identifier of row to retrieve|


### Response

|Name|Description|
|---|---|
|200|OK|
|default|Operation Failed.|




### Deletes a row from an Excel table
```DELETE: /datasets/{dataset}/tables/{table}/items/{id}``` 



| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|none|Excel file name|
|table|string|yes|path|none|Excel table name|
|id|string|yes|path|none|Unique identifier of the row to delete|


### Response

|Name|Description|
|---|---|
|200|OK|
|default|Operation Failed.|




### Updates an existing row in an Excel table
```PATCH: /datasets/{dataset}/tables/{table}/items/{id}``` 



| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|none|Excel file name|
|table|string|yes|path|none|Excel table name|
|id|string|yes|path|none|Unique identifier of the row to update|
|item| |yes|body|none|Row with updated values|


### Response

|Name|Description|
|---|---|
|200|OK|
|default|Operation Failed.|




## Object definitions

#### DataSetsMetadata

| Name | Data Type | Required|
|---|---|---|
|tabular|not defined|no|
|blob|not defined|no|

#### TabularDataSetsMetadata

| Name | Data Type |Required|
|---|---|---|
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

| Name | Data Type |Required|
|---|---|---|
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|

#### TableMetadata

| Name | Data Type |Required|
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|not defined|no|

#### DataSetsList

| Name | Data Type |Required|
|---|---|---|
|value|array|no|

#### DataSet

| Name | Data Type |Required|
|---|---|---|
|Name|string|no|
|DisplayName|string|no|

#### Table

| Name | Data Type |Required|
|---|---|---|
|Name|string|no|
|DisplayName|string|no|

#### Item

| Name | Data Type |Required|
|---|---|---|
|ItemInternalId|string|no|

#### TablesList

| Name | Data Type |Required|
|---|---|---|
|value|array|no|

#### ItemsList

| Name | Data Type |Required|
|---|---|---|
|value|array|no|


## Next Steps
[Create a logic app](../app-service-logic/app-service-logic-create-a-logic-app.md)  


-->
