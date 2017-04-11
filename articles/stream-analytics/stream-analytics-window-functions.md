<properties
    pageTitle="資料流分析視窗函數簡介 |Microsoft Azure"
    description="深入了解資料流分析 （解決跳、 滑動） 的三個視窗函數。"
    keywords="解決視窗中，將 [視窗] 跳視窗"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>


# <a name="introduction-to-stream-analytics-window-functions"></a>資料流分析視窗函數簡介

在許多即時資料流案例，就必須只在暫時 windows 中所包含的資料上執行作業。 原生支援視窗化函數會在開發人員生產力撰寫複雜的資料流處理工作之間移動針狀的 Azure 資料流分析的主要功能。 資料流分析可讓開發人員使用[**解決**](https://msdn.microsoft.com/library/dn835055.aspx) [**Hopping**](https://msdn.microsoft.com/library/dn835041.aspx) ，然後[**滑動**](https://msdn.microsoft.com/library/dn835051.aspx)windows 執行暫時串流資料作業。 值得所有[視窗](https://msdn.microsoft.com/library/dn835019.aspx)作業都輸出視窗的**結尾**處的結果。 在視窗的輸出會根據使用的彙總函式的單一事件。 事件會有時間戳記的視窗的結尾，所有視窗函數所都定義的固定的長度。 最後，它是注意所有視窗函數應該都用於[**GROUP BY**](https://msdn.microsoft.com/library/dn835023.aspx)子句。

![資料流分析視窗函數的概念](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>解決視窗

解決函數用來將不同的時間區段區段資料流，並執行例如下面的範例中，針對函數的視窗。 解決視窗的重要的區別因素是，他們重複，不會重疊和事件不屬於多個解決視窗。

![解決簡介串流分析視窗函數](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>跳頻視窗

跳時間轉接視窗函數躍點，以指定期間內。 可能會將它們可以重疊，請解決 windows，事件可以屬於多個 Hopping 視窗結果集，以便更容易。 若要讓 Hopping 視窗相同解決一個視窗只要指定要視窗大小相同的躍點大小。 

![資料流分析視窗函數跳簡介](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>將視窗

滑動視窗函數，有別於解決或 Hopping 視窗，輸出**只**產生事件發生時。 每個視窗必須至少有一個事件和視窗持續前移 € (epsilon) 來。 跳 Windows 中，例如事件可以屬於多個滑動視窗。

![將附註的資料流分析視窗函數](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>取得視窗函數的說明

進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)
