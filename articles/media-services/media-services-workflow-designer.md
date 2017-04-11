<properties 
    pageTitle="使用工作流程設計工具建立進階編碼的工作流程 |Microsoft Azure" 
    description="進一步瞭解如何使用工作流程設計工具建立進階編碼的工作流程。" 
    services="media-services" 
    documentationCenter="" 
    authors="anilmur" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016"
    ms.author="juliako;johndeu;anilmur"/>


#<a name="create-advanced-encoding-workflows-with-workflow-designer"></a>使用工作流程設計工具建立進階編碼的工作流程

##<a name="overview"></a>概觀

**工作流程設計工具**是用來設計和建立編碼與**媒體 Encoder 進階版的工作流程**的自訂工作流程的 Windows 桌面工具。
藉由使用的工作流程設計工具，您可以設計並建立複雜的工作流程在**Media Encoder 進階版**中執行。  

工作流程可以包含客戶決策邏輯而分支輸入的來源檔案的屬性。 您可以建立工作流程可覆寫屬性和動態可輕鬆更複雜的編碼工作重複及自訂雲端中的值。

範例工作流程，您可以建立包括︰

- 決定以檢查來源的內容解析度和編碼只想要的輸出追蹤的工作流程。  這是 helfpul 消除會產生 upscaling 來源內容 inadvertantly 浪費的追蹤。
- 多個輸入的檔案可支援標題、 覆疊和訂分成一組的內容。 

此工具可以用於修改任何我們[發佈工作流程](media-services-workflow-designer.md#existing_workflows)。 

>[AZURE.NOTE]若要取得您的工作流程設計工具的複本，請連絡mepd@microsoft.com。


工作流程檔案建立後，它可以上傳為資產，並可用於編碼媒體檔案。 如何與**媒體 Encoder 進階版工作流程**使用**.NET**編碼的詳細資訊，請參閱[進階編碼與媒體 Encoder 進階版的工作流程](media-services-encode-with-premium-workflow.md)。

##<a id="existing_workflows"></a>修改現有的工作流程

[發佈工作流程](media-services-workflow-designer.md#existing_workflows)的預設可以修改使用設計工具。 您可以取得預設工作流程檔案[以下](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)。 資料夾也會包含這些檔案的描述。

下列影片示範如何使用設計工具。

###<a name="day-1--getting-started"></a>日期 1-快速入門

第 1 天影片涵蓋︰

- 設計工具的概觀
- 基本的工作流程 – 「 好 」
- 建立倍數輸出用於 Azure 媒體服務串流 MP4 檔案

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-1]

###<a name="day-2"></a>第 2 天

第 2 天影片涵蓋︰

- 不同來源檔案案例︰ 處理音訊
- 使用進階邏輯的工作流程
- 圖形階段

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-2]

###<a name="day-3"></a>3 天

3 天影片涵蓋︰

- 在 [工作流程/藍圖指令碼
- 使用目前編碼器的限制
- 問與答
 
> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-3]


## <a name="next-step"></a>下一步

檢閱媒體服務學習路徑。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


如果您需要支援，或是已在 [工作流程設計工具中建立自訂工作流程相關的問題，請傳送電子郵件至mepd@microsoft.com。

##<a name="see-also"></a>另請參閱

[Azure 進階版 Encoder 工作流程設計工具的訓練課程](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)
