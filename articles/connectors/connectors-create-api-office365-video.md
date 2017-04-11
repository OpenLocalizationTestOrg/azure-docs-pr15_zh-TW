<properties
pageTitle="邏輯應用程式中使用 Office 365 影片連接器 |Microsoft Azure"
description="開始使用您的 Microsoft Azure 應用程式服務邏輯應用程式中的 Office 365 影片連接器"
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
ms.date="05/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-office365-video-connector"></a>開始使用 office 365 影片連接器
連線至 Office 365 影片若要取得 Office 365 的相關資訊的視訊，請取得視訊及其他的清單。 透過使用 Office 365 影片連接器︰

- 邏輯應用程式 

>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2015年-08-01-預覽結構描述版本。 任何先前的結構描述版本不支援此連接器。

使用 Office 365 影片中，您可以︰

- 建立您的商務流程根據您收到來自 Office 365 影片的資料。 
- 使用動作的核取視訊入口網站的狀態，取得所有視訊頻道，及其他內容的清單。 這些動作獲得回應，然後再輸出可用的其他動作。 例如，您可以使用 Bing 搜尋連接器如果是 Office 365 影片中，搜尋，然後使用 Office 365 視訊連接器，以取得該視訊的相關資訊。 如果視訊符合您的需求，您就可以在 Facebook 上張貼這段影片。 

若要新增作業邏輯應用程式中，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作

Office 365 影片連接器有下列可用的動作。 有任何引動程序。

| 觸發程序 | 動作|
| --- | --- |
| 無 | <ul><li>檢查視訊入口網站的狀態</li><li>取得所有可檢視的頻道</li><li>取得視訊的播放 url Azure 媒體服務資訊清單</li><li>取得承載者權杖，來存取解密視訊</li><li>取得特定 office 365 的相關資訊的視訊</li><li>列出所有的 office 365 視訊頻道中的簡報</li></ul>

所有的連接器支援 JSON 和 XML 格式的資料。 

## <a name="create-a-connection-to-office365-video-connector"></a>建立 office 365 影片連接器的連線
當您新增此連接器至您的邏輯應用程式時，您必須登入您的 Office 365 影片帳戶並允許邏輯相關應用程式連線至您的帳戶。

>[AZURE.INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

您建立的連線之後，輸入 Office 365 的視訊內容，如租用戶名稱或通道識別碼。 **REST API 參考**本主題中說明這些屬性。

>[AZURE.TIP] 您可以在其他邏輯應用程式中使用此相同的 Office 365 影片連線。

## <a name="swagger-rest-api-reference"></a>Swagger REST API 參照
適用於版本︰ 1.0。

### <a name="checks-video-portal-status"></a>檢查視訊入口網站的狀態 
檢查 [視訊入口網站狀態，請參閱是否已啟用視訊服務]。  
```GET: /{tenant}/IsEnabled``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|租用戶|字串|[是]|路徑|無|目錄使用者的租用戶名稱是部分|


#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|順利完成作業|
|400|BadRequest|
|401|未獲授權|
|404|找不到|
|500|內部伺服器錯誤|
|預設值|作業失敗。|



### <a name="get-all-viewable-channels"></a>取得所有可檢視的頻道 
取得所有使用者擁有檢視的存取權的頻道。  
```GET: /{tenant}/Channels``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|租用戶|字串|[是]|路徑|無|目錄使用者的租用戶名稱是部分|


#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|順利完成作業|
|400|BadRequest|
|401|未獲授權|
|404|找不到|
|500|內部伺服器錯誤|
|預設值|作業失敗。|




### <a name="lists-all-the-office365-videos-present-in-a-channel"></a>列出所有的 office 365 視訊頻道中的簡報 
列出所有的 office 365 視訊頻道中的簡報。  
```GET: /{tenant}/Channels/{channelId}/Videos``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|租用戶|字串|[是]|路徑|無|目錄使用者的租用戶名稱是部分|
|channelId|字串|[是]|路徑|無|從視訊擷取的需要頻道識別碼|


#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|順利完成作業|
|400|BadRequest|
|401|未獲授權|
|404|找不到|
|500|內部伺服器錯誤|
|預設值|作業失敗。|




### <a name="gets-information-about-a-particular-office365-video"></a>取得特定 office 365 的相關資訊的視訊 
取得特定 office 365 的相關資訊的視訊。  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|租用戶|字串|[是]|路徑|無|目錄使用者的租用戶名稱是部分|
|channelId|字串|[是]|路徑|無|頻道識別碼|
|videoId|字串|[是]|路徑|無|視訊的識別碼|


#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|順利完成作業|
|400|BadRequest|
|401|未獲授權|
|404|找不到|
|500|內部伺服器錯誤|
|預設值|作業失敗。|




### <a name="get-playback-url-of-the-azure-media-services-manifest-for-a-video"></a>取得視訊的播放 url Azure 媒體服務資訊清單 
取得視訊播放 url Azure 媒體服務資訊清單。  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|租用戶|字串|[是]|路徑|無|目錄使用者的租用戶名稱是部分|
|channelId|字串|[是]|路徑|無|頻道識別碼|
|videoId|字串|[是]|路徑|無|視訊的識別碼|
|streamingFormatType|字串|[是]|查詢|無|串流格式類型。 1-平滑串流或 MPEG 虛線。 0-HLS 串流|


#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|順利完成作業|
|400|BadRequest|
|401|未獲授權|
|404|找不到|
|500|內部伺服器錯誤|
|預設值|作業失敗。|




### <a name="get-the-bearer-token-to-get-access-to-decrypt-the-video"></a>取得承載者權杖，來存取解密視訊 
取得承載者權杖，來存取解密視訊。  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|租用戶|字串|[是]|路徑|無|目錄使用者的租用戶名稱是部分|
|channelId|字串|[是]|路徑|無|頻道識別碼|
|videoId|字串|[是]|路徑|無|視訊的識別碼|


#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|順利完成作業|
|400|BadRequest|
|401|未獲授權|
|404|找不到|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## <a name="object-definitions"></a>物件定義

#### <a name="channel-channel-class"></a>頻道︰ 頻道類別

| 名稱 | 資料類型 | 所需|
|---|---|---|
|識別碼|字串|沒有|
|標題|字串|沒有|
|描述|字串|沒有|


#### <a name="video"></a>視訊 

| 名稱 | 資料類型 |所需|
|---|---|---|
|識別碼|字串|沒有|
|標題|字串|沒有|
|描述|字串|沒有|
|CreationDate|字串|沒有|
|擁有者|字串|沒有|
|ThumbnailUrl|字串|沒有|
|VideoUrl|字串|沒有|
|VideoDuration|整數|沒有|
|VideoProcessingStatus|整數|沒有|
|ViewCount|整數|沒有|


## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。
