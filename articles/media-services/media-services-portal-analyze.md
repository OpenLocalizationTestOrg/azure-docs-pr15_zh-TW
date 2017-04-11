<properties
    pageTitle="分析您使用 [Azure 入口網站的媒體 |Microsoft Azure"
    description="本主題探討如何處理您的媒體分析媒體處理器 (MPs) 使用 Azure 入口網站的媒體。"
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="juliako"/>


# <a name="analyze-your-media-using-the-azure-portal"></a>分析您的媒體，使用 [Azure 入口網站

> [AZURE.NOTE] 若要完成此教學課程中，您需要 Azure 帳戶。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。 

## <a name="overview"></a>概觀

Azure 媒體服務分析是，可讓您更輕鬆地組織及衍生當中的深入見解從其視訊檔案的企業語音和願景元件 （在企業、 法規遵循、 安全性和到） 的集合。 Azure 媒體服務分析更詳細的概觀，請參閱[本](media-services-analytics-overview.md)主題。 

本主題探討如何處理您的媒體分析媒體處理器 (MPs) 使用 Azure 入口網站的媒體。 媒體分析 MPs 產生 MP4 檔案或 JSON 檔案。 如果媒體處理器所產生的 MP4 檔案，您可以逐步下載檔案。 如果媒體處理器產生 JSON 檔案，您可以下載檔案從 Azure blob 儲存體。 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>選擇您要分析的資產 
 
1. 在[Azure 入口網站](https://portal.azure.com/)中，選取您的 Azure 媒體服務帳戶。
2. 在 [**設定**] 視窗中，選取 [**資產**]。  
.
    ![分析影片](./media/media-services-portal-analyze/media-services-portal-analyze001.png)

2. 選取您想要分析，然後按 [**分析**] 按鈕的資產。
        
    ![分析影片](./media/media-services-portal-analyze/media-services-portal-analyze002.png)

3. 在 [**程序與媒體分析的媒體資產**] 視窗中，選取 [處理器]。 

    本文其餘部分說明原因，以及如何使用每個處理器。 
   
4. 按**建立**以開始工作。

## <a name="azure-media-indexer"></a>Azure 媒體索引

**Azure 媒體重新**媒體處理器可讓您進行搜尋，媒體檔案和內容，以及產生關閉隱藏式輔助字幕追蹤。 此節會提供選項，您可以指定此 MP 的相關詳細資料。

![分析影片](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>語言

若要將多媒體檔案中自然語言。 例如，英文或西班牙文。 

### <a name="captions"></a>標題

您可以選擇將您的內容從產生的標題格式。 編製索引作業可以產生字幕檔案，下列格式︰  

- **沙米文**
- **TTML**
- **WebVTT**

關閉標題 （副本） 以下列格式的檔案可以用來障礙人士聽力殘障人士音訊和視訊檔案。

### <a name="aib-file"></a>AIB 檔案

如果您想要產生自訂的 SQL Server i 篩選搭配使用的音訊索引 Blob 檔案，請選取這個選項。 如需詳細資訊，請參閱[此](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/)部落格。

### <a name="keywords"></a>關鍵字

如果您想要產生關鍵字 XML 檔案，請選取這個選項。 這個檔案包含關鍵字從語音內容中，展開頻率與位移的資訊。

### <a name="job-name"></a>工作名稱

好記的名稱，可讓您找出工作。 [本文](media-services-portal-check-job-progress.md)將說明如何監視的工作進度。 

### <a name="output-file"></a>輸出檔案

可讓您識別輸出內容的好記的名稱。 

## <a name="azure-media-hyperlapse"></a>Azure 媒體 Hyperlapse

Azure 媒體 Hyperlapse 是從第一個人員或動作相機內容建立平滑的時間屆影片 MP。  如需詳細資訊，請參閱[本](media-services-hyperlapse-content.md)主題。 此節會提供選項，您可以指定此 MP 的相關詳細資料。

![分析影片](./media/media-services-portal-analyze/media-services-portal-analyze004.png)

### <a name="speed"></a>速度 

指定要用來加速輸入視訊的速度。 輸出是輸入視訊的 stabilized 和時間屆轉譯。

### <a name="job-name"></a>工作名稱

好記的名稱，可讓您找出工作。 [本文](media-services-portal-check-job-progress.md)將說明如何監視的工作進度。 

### <a name="output-file"></a>輸出檔案

可讓您識別輸出內容的好記的名稱。 

## <a name="azure-media-face-detector"></a>Azure 媒體美元偵測器

**Azure 媒體美元偵測**媒體處理器 (MP) 可讓您計算與追蹤計時，甚至評量的對象參與及反應透過臉部的運算式。 這項服務包含兩個功能︰ 

- **美元偵測**

    美元偵測尋找，並追蹤內視訊的人力面。 多個面可以偵測到及後續追蹤為它們移動，以傳回 JSON 檔案中的時間和位置的中繼資料。 在 [追蹤] 會嘗試該人員移動在畫面上，即使對方會受到阻擋或簡要離開圖文框時，讓一致的識別碼相同的圖示。

    >[AZURE.NOTE]此服務並不會執行臉部辨識。 個別使用者離開框架或變成受到阻擋的時間太長時，會指定新的識別碼，就會傳回。

- **情緒偵測**
    
    情緒偵測是美元偵測媒體處理器，傳回上多個情感屬性的分析，從面偵測到，包括快樂、 sadness、 擔心、 anger，及其他功能的選用元件。 

![分析影片](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>偵測模式

下列模式的其中一個可用的處理器來︰

- 美元偵測
- 每個美元情緒偵測
- 彙總情緒偵測

### <a name="job-name"></a>工作名稱

好記的名稱，可讓您找出工作。 [本文](media-services-portal-check-job-progress.md)將說明如何監視的工作進度。 

### <a name="output-file"></a>輸出檔案

可讓您識別輸出內容的好記的名稱。 

## <a name="azure-media-motion-detector"></a>Azure 媒體動作偵測器

**Azure 媒體動作偵測器**媒體處理器 (MP) 可讓您以有效率地識別有興趣，否則長和較為簡單的影片中的章節。 移動偵測可以用於靜態相機資料出移動發生的視訊部分。 它會產生 JSON 檔案包含時間戳記與發生事件的週地區中繼資料。

衝安全性視訊的摘要，這項技術就能將移動分類成相關事件和誤判光源變更陰影等。 這可讓您從相機摘要產生的安全性警告，而不垃圾無限不相關的事件，信件時無法擷取感興趣的時間太長監視視訊。

![分析影片](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure 媒體視訊的縮圖

這個處理器可協助您建立的較長的視訊摘要自動選取最感興趣的程式碼片段，從來源視訊。 當您想要提供的發生的事長視訊中的快速概觀，這是很有用。 如需詳細的資訊與範例，請參閱[使用 Azure 媒體視訊縮圖，即可建立視訊摘要](media-services-video-summarization.md)

![分析影片](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>工作名稱

好記的名稱，可讓您找出工作。 [本文](media-services-portal-check-job-progress.md)將說明如何監視的工作進度。 

### <a name="output-file"></a>輸出檔案

可讓您識別輸出內容的好記的名稱。 


##<a name="next-steps"></a>後續步驟

檢視媒體服務學習路徑。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


