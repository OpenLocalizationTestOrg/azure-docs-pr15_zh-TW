<properties
pageTitle="Azure 搜尋索引子中的欄位對應"
description="設定 Azure 搜尋索引欄位對應的欄位名稱和資料格式的差異"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" 
ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="10/17/2016"
ms.author="eugenesh" />

# <a name="field-mappings-in-azure-search-indexers"></a>Azure 搜尋索引子中的欄位對應

使用時 Azure 搜尋索引子，您可以在您輸入的資料不完全相符的目標索引的結構描述的情況下有時候發現自己。 在這些情況下，您可以使用**功能變數對應**，將您的資料轉換成您要的圖案。 

某些情況下，其中的欄位對應很有用︰
 
- 您的資料來源具有欄位`_id`，但 Azure 搜尋不允許以底線開頭的欄位名稱。 欄位對應可讓您 「 變更 」 的欄位。 
- 您想要填入使用相同的資料來源資料，例如索引中的多個欄位，因為您想要套用不同的分析器這些欄位。 欄位對應可讓您 「 分叉 」 的資料來源的欄位。
- 您需要到 Base64 編碼或解碼您的資料。 欄位對應支援許多**對應函數**，包含函數的 Base64 編碼和解碼。   


> [AZURE.IMPORTANT] 目前，欄位對應的功能是在預覽中。 僅在使用版本**2015年 02-28 預覽**REST API 中使用。 請記住，[預覽 Api 供測試與評估，和不應該用於生產環境中。

## <a name="setting-up-field-mappings"></a>設定欄位對應

建立新的索引使用[建立索引子](search-api-indexers-2015-02-28-preview.md#create-indexer)API 時，您可以新增欄位對應。 您可以管理上使用[更新重新](search-api-indexers-2015-02-28-preview.md#update-indexer)API 編製索引索引的欄位對應。 

欄位對應包含 3 部分︰ 

1. A `sourceFieldName`，表示您的資料來源中的欄位。 需要此屬性。 

2. 選擇性`targetFieldName`，表示您的搜尋索引中的欄位。 如果省略，則會使用相同的名稱與資料來源。 

3. 選擇性`mappingFunction`，其中可以轉換資料使用的數個預先定義的函數。 [下面](#mappingFunctions)，是函數的完整清單。

若要新增的欄位對應`fieldMappings`上重新定義陣列。 

例如，以下是可以容納差異欄位名稱的方式︰ 

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 
} 
```

索引子可以有多個欄位對應。 例如，以下方式您可以 「 分叉 」 欄位︰

```JSON

"fieldMappings" : [ 
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }, 
] 
```

> [AZURE.NOTE] Azure 搜尋會使用區分大小寫，以解決欄位對應欄位和函數名稱。 這是方便 （您沒有安裝正確的大小寫，），但這表示您的資料來源或索引不能有大小寫只不同的欄位。  

<a name="mappingFunctions"></a>
## <a name="field-mapping-functions"></a>欄位對應函數

目前支援下列功能︰ 

- [base64Encode](#base64EncodeFunction)
- [base64Decode](#base64DecodeFunction)
- [extractTokenAtPosition](#extractTokenAtPositionFunction)
- [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>
### <a name="base64encode"></a>base64Encode 

執行*URL 安全*Base64 編碼方式輸入字串。 假設輸入 utf-8 編碼。 

#### <a name="sample-use-case"></a>範例使用案例 

僅限 URL 安全字元可以出現在 Azure 搜尋文件索引鍵 （因為客戶必須為能地址，例如使用查閱 API，在文件）。 如果您的資料包含 URL 不安全的字元，且您想要用來填入您的搜尋索引中的索引鍵欄位，請使用此函數。   

#### <a name="example"></a>範例 

```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Path", 
    "targetFieldName" : "UrlSafePath",
    "mappingFunction" : { "name" : "base64Encode" } 
  }] 
```

<a name="base64DecodeFunction"></a>
### <a name="base64decode"></a>base64Decode

執行 Base64 解碼輸入字串。 輸入會引用本身*的安全 URL* Base64 編碼字串。 

#### <a name="sample-use-case"></a>範例使用案例 

Blob 自訂的中繼資料值必須是 ASCII 編碼。 您可以使用 Base64 編碼代表 blob 自訂中繼資料中的任意 Unicode 字串。 不過，若要讓搜尋有意義，您可以使用此函數轉換編碼的資料回 「 一般 」 字串時填入您的搜尋索引。  

#### <a name="example"></a>範例 

```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Base64EncodedMetadata", 
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode" } 
  }] 
```

<a name="extractTokenAtPositionFunction"></a>
### <a name="extracttokenatposition"></a>extractTokenAtPosition

分割字串欄位使用指定的分隔符號，並選取權杖產生分割中指定的位置。

例如，如果是`Jane Doe`、`delimiter`是`" "`（空格） 和`position`為 0，則結果為`Jane`;如果`position`為 1，結果是`Doe`。 如果參照不存在的權杖位置，則會傳回錯誤。

#### <a name="sample-use-case"></a>範例使用案例 

您的資料來源包含`PersonName`] 欄位中，而且您想要為兩個不同的索引`FirstName`和`LastName`欄位。 若要分割的分隔符號使用空格字元的輸入，您可以使用此函數。

#### <a name="parameters"></a>參數

- `delimiter`︰ 時要使用的分隔符號分割輸入的字串的字串。
- `position`: 整數起始位置，以挑選輸入的字串分割後的 token。    

#### <a name="example"></a>範例

```JSON 

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } } 
  }, 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } } 
  }] 
```

<a name="jsonArrayToStringCollectionFunction"></a>
### <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection

轉換成字串陣列可以用來填入格式化為 JSON 陣列的字串的字串`Collection(Edm.String)`索引中] 欄位。 

例如，如果輸入的字串是`["red", "white", "blue"]`，然後輸入目標欄位`Collection(Edm.String)`填入三個值`red`，`white`和`blue`。 無法剖析為 JSON 字串陣列的輸入值，會傳回錯誤。 

#### <a name="sample-use-case"></a>範例使用案例

Azure SQL 資料庫沒有符合對應至內建的資料類型`Collection(Edm.String)`Azure 搜尋中的欄位。 若要填入字串集合欄位，設定您的來源資料，以 JSON 字串陣列的格式，使用此函數。 

#### <a name="example"></a>範例 

```JSON

"fieldMappings" : [ 
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
] 
```

## <a name="help-us-make-azure-search-better"></a>協助我們更有效地進行 Azure 搜尋

如果您有功能要求或想法的改良功能，請連絡我們我們[UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search/)上。