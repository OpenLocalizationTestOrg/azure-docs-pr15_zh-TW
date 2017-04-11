
<properties
    pageTitle="從 DataMarket 建議 API 移轉 Azure 認知服務建議 API |Microsoft Azure"
    description="Azure 電腦學習建議-建議認知服務移轉"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="luisca"/>


# <a name="migrate-to-azure-cognitive-services-recommendations-api-from-the-datamarket-recommendations-api"></a>從 DataMarket 建議 API 移轉 Azure 認知服務建議 API
本文將說明如何從[Microsoft DataMarket 建議 API](https://datamarket.azure.com/dataset/amla/recommendations)移轉到[Microsoft Azure 認知服務建議 API](https://www.microsoft.com/cognitive-services/en-us/recommendations-api)。

DataMarket 建議 API 會停止接受新客戶 2016 年 12 月 31 日，且會遭取代 2017 年 2 月 28。

## <a name="how-do-i-start-using-the-azure-cognitive-services-recommendations-api"></a>如何開始使用 Azure 認知服務建議 API？

若要移轉到認知服務建議 API，執行下列動作︰

1.  如果您還沒有 Azure 的訂閱，其中一個 [[登入](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1)。 

1.  從 [[快速入門指南](cognitive-services-recommendations-quick-start.md)註冊認知服務建議 API，並以程式設計方式使用取得逐步指示。 

1.  移至[認知服務建議 API 登陸頁面](https://www.microsoft.com/cognitive-services/en-us/recommendations-api)若要深入瞭解服務及尋找文件。

## <a name="i-used-the-recommendations-ui-to-build-my-models-is-there-a-similar-tool-for-the-cognitive-services-recommendations-api"></a>我可以使用建議 UI 來建立 [我的模型。 認知服務建議 api 上有類似的工具嗎？

絕對 ！ 新的[建議使用者介面](http://recommendations-portal.azurewebsites.net/)的 URL 是 http://recommendations-portal.azurewebsites.net。 

>[AZURE.NOTE] DataMarket 認證運作以下所示。 註冊 Azure 入口網站中的訂閱，並取得所需的使用新的[建議使用者介面](http://recommendations-portal.azurewebsites.net/)的 [帳戶金鑰。
如果您沒有帳戶金鑰]，請參閱[快速入門指南](cognitive-services-recommendations-quick-start.md)的任務 1。

##<a name="is-the-new-api-format-the-same-as-the-datamarket-recommendations-api"></a>DataMarket 建議 API 相同是新的 API 格式？

API 支援為 DataMarket 版本，支援這些案例的相同的案例及程序的流程，但實際的 API 介面已經更新，以符合[Microsoft REST API 準則](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md)。 Api 更加一致，而且工作更有效地使用工具支援 Swagger。

若要瞭解每個 Api，查看[API 檔案總管]](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3db)。
使用*嘗試*測試 API 通話] 按鈕。 未變更的建議 API （類別目錄及使用方式的檔案） 所使用的檔案格式，讓您可以繼續使用相同的檔案及/或您建置產生這些檔案的基礎結構。

##<a name="what-are-some-new-features-in-the-cognitive-services-recommendations-api"></a>什麼是認知服務建議 API 的一些新功能？

在最後一個的兩個月，我們有認知服務建議 api 發行新功能︰
-   建議訓練與測試模型，而不需撰寫程式碼 UI
-   可讓您數以千計的建議，一次計分批次
-   建立查詢建議模型品質指標支援
-   商務規則的支援
-   列舉及下載檔案使用情況和目錄的功能
-   若要查詢的建議模型中的項目功能品質排名建置支援
-   搜尋產品類別目錄中的新增的功能

## <a name="when-does-microsoft-stop-supporting-the-datamarket-recommendations-api"></a>Microsoft 時停止支援 DataMarket 建議 API？

[在 DataMarket 的建議 API](https://datamarket.azure.com/dataset/amla/recommendations)停止接受 2016 年 12 月 31 日之後的新客戶，並會完全 2017 年 2 月 28，來取代。 

## <a name="what-if-i-dont-have-the-data-that-i-need-to-recreate-my-models-in-the-cognitive-services-recommendations-api"></a>如果沒有我需要重新建立我模型認知服務建議 API 中的資料？

我們想要讓此轉場效果盡可能的簡單。 我們可以協助您將舊模型 DataMarket 帳戶移至您的新 Azure 認知服務建議 API 訂閱。 與我們連絡[mlapi@microsoft.com](mailto://mlapi@microsoft.com)。 我們會向您提供您 DataMarket 訂閱 ID 和認知服務訂閱識別碼，才能我們與您的新帳戶建立關聯的模型。
