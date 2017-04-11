<properties
pageTitle="Azure 搜尋與編製索引的 Azure Blob 儲存體"
description="瞭解如何 Azure Blob 儲存體編製索引，並從 Azure 搜尋的文件中擷取文字"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="10/16/2016"
ms.author="eugenesh" />

# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>編製索引的文件中使用 Azure 搜尋 Azure Blob 儲存體

本文將示範如何使用 Azure 搜尋索引的文件 (例如 Pdf，Microsoft Office 文件和數種其他常見的格式) 儲存在 Azure Blob 儲存體。 新的搜尋 Azure blob 索引器可讓此程序，快速且順暢。 

> [AZURE.IMPORTANT] 目前這項功能在預覽中。 僅在使用版本**2015年 02-28 預覽**REST API 中使用。 請記住，[預覽 Api 供測試與評估，和不應該用於生產環境中。

## <a name="supported-document-formats"></a>支援的文件格式

Blob 索引器可以從下列文件格式中擷取文字︰

- PDF
- Microsoft Office 格式︰ DOCX/文件 XLSX/PPTX/PPT，XLS 訊息 （Outlook 電子郵件）  
- HTML
- XML
- 郵遞區號
- 和
- 純文字檔案  
- JSON （如需詳細資訊，請參閱[編製索引作業 JSON 二進位大型物件](search-howto-index-json-blobs.md)）
- CSV （如需詳細資訊，請參閱[編製索引作業 CSV 二進位大型物件](search-howto-index-csv-blobs.md)）

## <a name="setting-up-blob-indexing"></a>設定 blob 編製索引

您可以設定 Azure Blob 儲存體重新使用︰

- [Azure 入口網站](https://ms.portal.azure.com)
- Azure 搜尋[REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx)
- Azure 搜尋.NET SDK[版本 2.0 預覽](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) 

> [AZURE.NOTE] 某些功能 （例如欄位對應） 目前尚不提供在入口網站中，必須使用以程式設計方式。 

本文中，我們將會設定索引子使用 REST API 的三個步驟︰ 建立資料來源、 建立索引設定索引器。

### <a name="step-1-create-a-data-source"></a>步驟 1︰ 建立資料來源

資料來源指定要編製索引，認證存取資料及原則以有效率地識別資料 （新增、 修改或刪除資料列） 中的變更所需的資料。 資料來源可以使用相同的搜尋服務中的多個索引子。

對 blob 編製索引，資料來源必須具備下列必要的屬性︰ 

- **名稱**是您的搜尋服務中的資料來源的唯一名稱。 

- **型別**必須`azureblob`。 

- **認證**提供儲存帳戶連線字串做為`credentials.connectionString`參數。 您可以從 Azure 入口網站取得連線字串，瀏覽至您要的儲存帳戶刀 >**設定** > **索引鍵**和使用 「 主要的連接字串 」 或 「 第二個連接字串 」 的值。 

- **容器**會指定容器，在您儲存的帳戶。 根據預設，擷取容器內的所有 blob 都的。 如果您只想要特定的虛擬目錄中的索引 blob，您可以指定使用選擇性的**查詢**參數的目錄。 

下列範例所示的資料來源定義︰

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

如需建立資料來源 API 的詳細資訊，請參閱[建立資料來源](search-api-indexers-2015-02-28-preview.md#create-data-source)。

### <a name="step-2-create-an-index"></a>步驟 2︰ 建立索引 

索引文件，屬性中指定的欄位和圖案的搜尋其他建構體驗。  

Blob 編製索引作業，請確定您的索引具有可`content`儲存 blob 的欄位。

    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-target-index",
        "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
        ]
    }

如需建立索引 API 的詳細資訊，請參閱[建立索引](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="step-3-create-an-indexer"></a>步驟 3︰ 建立索引 

重新連接資料來源與目標搜尋索引，並提供排程資訊，好讓您可以自動重新整理資料。 一旦索引與資料來源建立之後，就建立參照的資料來源及目標索引的索引子相當簡單。 例如︰

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

這個索引子會執行 （排程間隔設定為 「 PT2H 」） 每兩個小時。 若要執行索引子每 30 分鐘，設定 「 PT30M 」 的間隔。 最短的支援的間隔為 5 分鐘。 排程為選用-如果省略，索引子一次執行時建立。 不過，您也可以隨時執行重新指定。   

如需建立索引子 API 的詳細資訊，請參閱[建立索引](search-api-indexers-2015-02-28-preview.md#create-indexer)。


## <a name="document-extraction-process"></a>文件擷取程序

Azure 搜尋索引每份文件 (blob) 的如下所示︰

- 文件的整個文字內容擷取至名為字串欄位`content`。 我們目前不提供支援從單一 blob 擷取多份文件︰
    - 例如，以 CSV 檔案是編製索引，一份文件。 如果您需要將不同的文件為 csv 檔案中的每一行，投票[此 UserVoice 建議](https://feedback.azure.com/forums/263029-azure-search/suggestions/13865325-please-treat-each-line-in-a-csv-file-as-a-separate)。
    - 複合或內嵌文件 （例如 ZIP 封存或 Word 文件的內嵌包含附件的 Outlook 電子郵件） 也會以單一文件索引。

- 使用者指定的中繼資料屬性存在於上 blob，如果有的話，擷取逐字。 中繼資料屬性也可用來控制文件擷取程序的某部分︰ 如需詳細資訊，請[使用自訂中繼資料，以控制文件擷取](#CustomMetadataControl)。

- 下列欄位到展開的標準 blob 的中繼資料屬性︰

    - **中繼資料\_儲存\_名稱**(Edm.String)-blob 的檔案名稱。 例如，如果您有 blob /my-container/my-folder/subfolder/resume.pdf，這個欄位的值是`resume.pdf`。

    - **中繼資料\_儲存\_路徑**(Edm.String)-blob，包括儲存帳戶的完整 URI。 例如，`https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

    - **中繼資料\_儲存\_內容\_類型**(Edm.String) 的內容類型所指定的您用來上傳 blob 的程式碼。 例如， `application/octet-stream`。

    - **中繼資料\_儲存\_最後一個\_修改**(Edm.DateTimeOffset)-上次修改 blob 的時間戳記。 Azure 搜尋會用來識別變更的 blob，若要避免重新索引所有項目之後初始編製索引的這個時間戳記。

    - **中繼資料\_儲存\_大小**(Edm.Int64)-blob 位元組大小。

    - **中繼資料\_儲存\_內容\_md5**(Edm.String)-MD5 雜湊 blob 內容，如果有的話。

- 每個文件格式的特定的中繼資料屬性會將欄位列出[以下](#ContentSpecificMetadata)解壓縮。

您不需要在您的搜尋索引中定義的所有上述屬性的欄位-只要擷取您的應用程式，您需要的內容。 

> [AZURE.NOTE] 通常，您現有的索引中的欄位名稱是不同的文件擷取期間所產生的欄位名稱。 您可以使用**功能變數對應**到對應至您的搜尋索引中的欄位名稱 Azure 搜尋所提供的屬性名稱。 您會看到的欄位對應使用下方的範例。 

## <a name="picking-the-document-key-field-and-dealing-with-different-field-names"></a>挑選的文件索引鍵欄位，以及處理不同的欄位名稱

Azure 搜尋中的文件索引鍵可唯一識別文件。 每個搜尋索引必須類型 Edm.String 的一個索引鍵欄位。 需要每份文件新增至 （是實際唯一的必要的欄位） 索引鍵欄位。  
   
您應該仔細考量解壓縮的欄位應該對應到您的索引鍵欄位。 候選項目是︰

- **中繼資料\_儲存\_名稱**-，這可能是方便求職者，但請注意，1) 的名稱可能不是唯一的因為您可能會有不同的資料夾，相同的名稱與 2） 名稱 blob 可能包含在文件按鍵，例如虛線無效的字元。 您可以使用處理無效的字元`base64Encode`[欄位對應函數](search-indexer-field-mappings.md#base64EncodeFunction)-如果這麼做，請記住，當傳遞這些 API 中例如查閱呼叫編碼文件索引鍵。 （例如，.net 您可以使用[UrlTokenEncode 方法](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx)來建立群組）。

- **中繼資料\_儲存\_路徑**-使用完整路徑可確保唯一性，但成長包含路徑`/`中的文件索引鍵是[無效](https://msdn.microsoft.com/library/azure/dn857353.aspx)的字元。  上方，您可以選擇編碼使用按鍵`base64Encode`[函數](search-indexer-field-mappings.md#base64EncodeFunction)。

- 如果沒有上述選項適用於您時，您可以新增自訂的中繼資料屬性 blob。 這個選項，不過，需要 blob 上傳程序將中繼資料屬性新增到所有的二進位大型物件。 索引鍵是必要的屬性，因為沒有該摘要的所有 blob 將都無法能編製索引。

> [AZURE.IMPORTANT] Azure 搜尋索引中的索引鍵欄位沒有明確對應時，會自動使用`metadata_storage_path`鍵和 64 基本編碼關鍵值 （第二個選項上方）。

此範例中，讓我們來挑選`metadata_storage_name`文件索引鍵欄位。 現在就讓我們也假設您索引已命名的索引鍵欄位`key`及`fileSize`來儲存文件的大小。 若要電線設定為所要的項目，指定下列欄位對應時建立或更新您的索引︰

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

若要將此一起，以下說明如何新增欄位對應，並啟用 64 基本編碼的現有索引子機碼︰

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }

> [AZURE.NOTE] 若要深入瞭解功能變數對應，請參閱[本文](search-indexer-field-mappings.md)。

## <a name="incremental-indexing-and-deletion-detection"></a>累加編製索引及刪除偵測

當您設定 blob 重新排程執行時，它會重新索引只變更的 blob，是由 blob 的`LastModified`時間戳記。

> [AZURE.NOTE] 您沒有指定變更偵測原則 – 累加編製索引作業已啟用您自動。

若要支援刪除文件，使用 「 柔刪除 」 的方法。 如果您刪除徹底 blob，對應的文件不會從搜尋索引。 請改用下列步驟︰  

1. 指出 Azure 搜尋，以邏輯方式刪除 blob 中新增自訂的中繼資料屬性
2. 在 [資料來源上設定柔刪除偵測原則
3. 一旦索引器處理 blob （如下所示的索引狀態 API），您可以實際刪除 blob

例如，下列原則會考慮如果有中繼資料屬性中刪除 blob`IsDeleted`值`true`:

    PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",   
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }   

<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>內容的特定類型的中繼資料屬性

下表摘要說明程序，針對每個文件格式]，並說明 Azure 搜尋所擷取的中繼資料屬性。

文件格式 / 內容類型 | 特定內容類型的中繼資料屬性 | 處理詳細資料
-------------------------------|-------------------------------------------|-------------------
HTML (`text/html`) | `metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` | 刪除的 HTML 標記，並擷取文字
PDF (`application/pdf`) | `metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title`| 擷取文字，包括內嵌文件 （不含圖像）
DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` | 擷取文字，包括內嵌的文件
文件 (應用程式/msword) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` | 擷取文字，包括內嵌的文件
XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` | 擷取文字，包括內嵌的文件
XLS (應用程式/vnd.ms-excel) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` | 擷取文字，包括內嵌的文件
PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` | 擷取文字，包括內嵌的文件
PPT (應用程式/vnd.ms-powerpoint) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` | 擷取文字，包括內嵌的文件
訊息 (應用程式/vnd.ms-outlook) | `metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` | 擷取文字，包括附件
ZIP (應用程式/zip) | `metadata_content_type` | 從封存中的所有文件中擷取文字
XML (應用程式/xml) | `metadata_content_type`</br>`metadata_content_encoding`</br> | 移除 XML 標記，並擷取文字
JSON (應用程式/json) | `metadata_content_type`</br>`metadata_content_encoding` | 擷取文字<br/>注意︰ 如果您需要從 JSON blob 擷取多個文件欄位，請參閱[編製索引作業 JSON blob](search-howto-index-json-blobs.md)如需詳細資訊
EML (郵件/rfc822) | `metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` | 擷取文字，包括附件
純文字 （純文字/） | `metadata_content_type`</br>`metadata_content_encoding`</br> | 

<a name="CustomMetadataControl"></a>
## <a name="using-custom-metadata-to-control-document-extraction"></a>使用自訂的中繼資料，以控制文件擷取

您可以加入來控制 blob 編製索引及文件擷取程序的某部分的 blob 的中繼資料屬性。 目前支援下列屬性︰

屬性名稱 | 屬性值 | 說明
--------------|----------------|------------
AzureSearch_Skip | "true" | 若要完全略過 blob; blob 索引的指示嘗試執行的中繼資料和內容都不擷取。 當您想要略過特定內容類型，或特定 blob 失敗，請重複，中斷編製索引的程序，這是很有用。
AzureSearch_SkipContent | "true" | 指示 blob 索引子索引中繼資料並略過 blob 的解壓縮的內容。 如果 blob 內容不是最感興趣，但您仍要編製索引附加至 blob 的中繼資料，這是很有用。

<a name="IndexerParametersConfigurationControl"></a>
## <a name="using-indexer-parameters-to-control-document-extraction"></a>若要控制文件擷取使用索引子參數

多個參數可用於控制其 blob，及 blob 的內容和中繼資料中的哪些部分的索引設定已確實建立索引。 

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>與特定的副檔名 blob 編製索引

您可以使用指定的檔案副檔名與 blob 編製索引`indexedFileNameExtensions`重新設定參數。 值為字串，包含逗點分隔 （含前置的點） 的檔案副檔名清單。 例如，以僅限索引。PDF 和。DOCX 二進位大型物件中，執行下列動作︰ 

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions-from-indexing"></a>排除特定檔案 blob 編製索引

您可以從編製索引作業使用排除二進位大型物件的特定檔案副檔名`excludedFileNameExtensions`設定參數。 值為字串，包含逗點分隔 （含前置的點） 的檔案副檔名清單。 例如，要所有 blob 都除外的索引。PNG 及。JPEG 副檔名，執行下列動作︰ 

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

如果有兩個`indexedFileNameExtensions`和`excludedFileNameExtensions`參數有、 Azure 搜尋第一次查看`indexedFileNameExtensions`，然後在`excludedFileNameExtensions`。 這表示的相同的副檔名是否有兩個清單中，將不會包含從編製索引作業。 

### <a name="index-storage-metadata-only"></a>索引儲存中繼資料

您可以儲存中繼資料編製索引及完全略過的文件擷取程序使用`indexStorageMetadataOnly`設定屬性。 當您不需要的文件內容，也不需要的任何內容的特定類型的中繼資料屬性時，這是很有用。 若要這麼做，請將`indexStorageMetadataOnly`屬性設定為`true`: 

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexStorageMetadataOnly" : true } }
    }

### <a name="index-both-storage-and-content-type-metadata-but-skip-content-extraction"></a>編製索引儲存和內容類型的中繼資料，但略過內容擷取

如果您要擷取所有的中繼資料，但略過所有 blob 擷取內容時，您可以要求而不需要新增使用索引的設定，此行為`AzureSearch_SkipContent`中繼資料，每個個別 blob。 若要這麼做，請將`skipContent`要重新設定屬性`true`: 

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "skipContent" : true } }
    }

## <a name="help-us-make-azure-search-better"></a>協助我們更有效地進行 Azure 搜尋

如果您有功能要求或想法的改良功能，請連絡我們我們[UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search/)上。
