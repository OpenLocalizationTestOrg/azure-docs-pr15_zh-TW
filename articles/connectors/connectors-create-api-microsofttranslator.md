<properties
    pageTitle="邏輯應用程式中新增 Microsoft Translator |Microsoft Azure"
    description="Microsoft Translator 連接器與 REST API 參數的概觀"
    services=""
    suite=""
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

# <a name="get-started-with-the-microsoft-translator-connector"></a>開始使用 Microsoft Translator 連接器
連線至 Microsoft Translator 翻譯的文字、 偵測使用語言]，及其他內容。 使用 Microsoft Translator，您可以︰ 

- 建立您的商務流程根據您收到來自 Microsoft Translator 的資料。 
- 若要翻譯的文字，請偵測使用語言]，以及其他使用動作。 這些動作獲得回應，然後再輸出可用的其他動作。 例如，Dropbox 中建立新的檔案時，您可以翻譯另一種語言使用 Microsoft Translator 檔案中的文字。

若要新增作業邏輯應用程式中，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作
Microsoft Translator 包含下列的動作。 有任何引動程序。

觸發程序 | 動作
--- | ---
無 | <ul><li>偵測使用語言</li><li>文字轉換語音</li><li>翻譯文字</li><li>取得語言</li><li>取得語音語言</li></ul>

所有的連接器支援 JSON 和 XML 格式的資料。


## <a name="create-a-connection-to-microsoft-translator"></a>建立 Microsoft Translator 的連線

>[AZURE.INCLUDE [Steps to create a connection to Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]


## <a name="swagger-rest-api-reference"></a>Swagger REST API 參照
適用於版本︰ 1.0。

### <a name="detect-language"></a>偵測使用語言    
偵測到的來源語言特定的文字。  
```GET: /Detect```

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|查詢|字串|[是]|查詢|無 |文字會識別的語言|

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


### <a name="text-to-speech"></a>文字轉換語音    
將指定的文字轉換語音音訊串流音訊格式為。  
```GET: /Speak```

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|查詢|字串|[是]|查詢|無 |若要轉換的文字|
|語言|字串|[是]|查詢|無 |語言程式碼，以產生語音 (範例: 「 短-我們的)|

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


### <a name="translate-text"></a>翻譯文字    
翻譯文字，以指定使用 Microsoft Translator 語言。  
```GET: /Translate```

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|查詢|字串|[是]|查詢|無 |若要翻譯的文字|
|languageTo|字串|[是]|查詢| 無|目標語言程式碼 (範例: 「 這個方法 」)|
|languageFrom|字串|沒有|查詢|無 |來源語言;如果未提供，Microsoft Translator 會嘗試自動偵測。 (範例︰ 短)|
|類別|字串|沒有|查詢|一般 |翻譯類別 (預設: 「 一般 」)|

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


### <a name="get-languages"></a>取得語言    
擷取所有 Microsoft Translator 支援的語言。  
```GET: /TranslatableLanguages```

沒有這個呼叫參數。 

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


### <a name="get-speech-languages"></a>取得語音語言    
擷取可用的語音合成的語言。  
```GET: /SpeakLanguages``` 

沒有這個呼叫參數。

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|

## <a name="object-definitions"></a>物件定義

#### <a name="language-language-model-for-microsoft-translator-translatable-languages"></a>Microsoft Translator 翻譯語言的語言︰ 語言模型

|屬性名稱 | 資料類型 | 所需|
|---|---|---|
|程式碼|字串|沒有|
|名稱|字串|沒有|


## <a name="next-steps"></a>後續步驟

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

回到[Api 清單](apis-list.md)。


<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png
