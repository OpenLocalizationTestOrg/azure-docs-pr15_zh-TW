<properties
    pageTitle="以視覺化方式呈現和疑難排解串流分析工作 |Microsoft Azure"
    description="瞭解如何以視覺化方式呈現資料流分析工作管線自助疑難排解使用診斷圖表功能。"
    keywords=""
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


# <a name="visualize-and-troubleshoot-stream-analytics-jobs"></a>以視覺化方式呈現和疑難排解串流分析工作

資料流分析，就跟其他雲端的技術，疑難排解有時需要查詢為什麼工作不會產生預期的輸出 （或任何輸出該項目的）。 使用此記住，串流分析會提供視覺化串流工作的功能。 這也十分實用作為模型工具，而且這些需要的文件，其工作的好處。

在 [視覺效果] 面板中輸入值則會顯示所執行的查詢，然後所有輸出設定以及項目。 連線或組態問題可能會變得更清楚看到，也可以查看您的設定的視覺呈現，還是有幫助。

## <a name="using-the-diagnosis-diagram-tool"></a>使用診斷圖表工具

若要存取這個視覺化檢視，只要按一下 「 診斷圖表 」 中] 按鈕上的 [設定] 刀的資料流分析作業。

![stream-analytics-troubleshoot-visualization-diagnosis-diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

每個輸入與輸出是該元件、 色彩標示，以表示目前狀態，如下所示。

![stream-analytics-troubleshoot-visualization-input-map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

當使用者想要查看中繼查詢步驟，以了解資料流程模式中工作時，視覺效果工具提供查詢的分析的檢視其元件步驟和流程順序。 按一下每個查詢步驟，會顯示查詢編輯所示的窗格中的相對應的一節。 

![stream-analytics-troubleshoot-visualization-intermediate-steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)




## <a name="next-steps"></a>後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)
