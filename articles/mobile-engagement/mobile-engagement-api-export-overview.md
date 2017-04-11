<properties
    pageTitle="行動裝置的互動匯出 API 概觀"
    description="瞭解匯出您的使用者裝置運用它自己的工具中所產生的原始資料的基本概念"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="kpiteira"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile"
    ms.date="04/26/2016"
    ms.author="kpiteira"/>

# <a name="mobile-engagement-export-api-overview"></a>行動裝置的互動匯出 API 概觀

## <a name="introduction"></a>簡介

在此文件中，您將學習匯出您的使用者裝置運用它自己的工具中所產生的原始資料的基本概念。

## <a name="pre-requisites"></a>必要條件

從行動互動匯出的原始資料需要︰

- 若要能夠使用 （請參閱[驗證手動設定](mobile-engagement-api-authentication-manual.md)） 的 Api，API 驗證設定
- 使用 REST Api 或[.net SDK](mobile-engagement-dotnet-sdk-service-api.md)
- Azure 儲存體帳戶。

>[AZURE.NOTE] 我們也通知絕佳的[Microsoft Azure 儲存檔案總管](http://storageexplorer.com/)] 中，至少開發階段為它提供容易使用的使用者介面的互動 Azure 儲存體。

## <a name="what-can-be-exported"></a>可匯出什麼？

行動互動 」 可讓其使用者收集許多類型的資料，因此有許多種類型的匯出適合這些不同的資料類型。
有 2 種基本匯出︰

- 使用快照集︰ 通常是匯出資料的代表狀態和行動互動沒有歷程記錄。 例如，這包含標籤 （應用程式的資訊），權杖或推入行銷活動的意見反應。 因此這些類型的匯出與不相關的日期。
- 歷史︰ 匯出用的資料，例如累積一段時間，例如活動。

下表說明徹底所有可能的匯出︰

| 匯出類型 | 資料類型 | 描述                                                                                                                                 |
|-------------|-----------|---------------------------------------------------------------------------------------------------------------------------------------------|
| 快照    | 推入      | 產生的每個 deviceid/使用者識別碼為基礎的推播行銷活動意見反應的匯出                                                              |
| 快照    | 標記       | 產生匯出至每個裝置相關聯的標籤 （應用程式資訊）                                                                       |
| 快照    | 裝置    | 產生大部分的 technicals （模型、 地區設定時區，...）、 標籤、 指南的第一次，例如和裝置有關的資料匯出... |
| 快照    | 權杖     | 產生所有有效的權杖匯出                                                                                                 |
| 歷程記錄  | 活動  | 產生之所有活動的每個裝置上指定的時間內的匯出                                                           |
| 歷程記錄  | 事件     | 產生之所有活動的每個裝置上指定的時間內的匯出                                                           |
| 歷程記錄  | 工作       | 產生在指定的期間的每個裝置的所有工作的匯出                                                                 |
| 歷程記錄  | 錯誤     | 產生在指定的期間的每個裝置的所有錯誤的匯出                                                               |

## <a name="how-does-it-work"></a>如何運作？

匯出很長的執行的工作可能會產生大量的資料檔案。 因此，也不能叫用立即傳回下載檔案。
若要從行動互動匯出資料，您就必須建立透過 API**匯出工作**，您指定通常︰

- 匯出 （快照集或歷史） 的類型
- 資料類型，
- **Azure 存放容器**（包括與寫入存取權的有效 SA） 的匯出結果寫入的位置。

請注意，可能需要幾分鐘讓您的工作，開始使用時，它可能會從執行幾秒鐘的小的應用程式有很多使用者或活動的應用程式的幾個小時。

一旦建立工作，可能是檢查其狀態設成是否仍在執行或完成。

一旦作業成功，產生的資料檔位於提供的存放容器上。
