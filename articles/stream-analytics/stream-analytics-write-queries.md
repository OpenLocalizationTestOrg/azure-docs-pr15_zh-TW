<properties 
    pageTitle="如何撰寫串流分析查詢 |Microsoft Azure" 
    description="撰寫查詢資料流分析及查詢資料 |學習路徑區段。"
    keywords="如何撰寫查詢資料的查詢，撰寫查詢，撰寫查詢"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

# <a name="how-to-write-queries-in-stream-analytics"></a>在 [串流分析撰寫查詢的方式

撰寫查詢資料流處理邏輯中 Azure 資料流分析實作為 「 站查詢 「 開始工作，並執行資料，因為達到工作之前所定義。 資料轉換表示類似 SQL 查詢的語言，這是主要 T SQL 加上等[視窗化](https://msdn.microsoft.com/library/azure/dn835019.aspx)用來表示暫時語意一些加入的語言延伸子集。

## <a name="writing-queries"></a>撰寫查詢︰ ##

1. 在您分析工作資料流在 Azure 管理入口網站中，按一下 [**查詢**]。

    ![選取查詢](./media/stream-analytics-write-queries/1-stream-analytics-write-queries.png)  

    在 Azure 入口網站中，按一下 [**查詢**]。

    ![選取 [查詢預覽]](./media/stream-analytics-write-queries/query-preview-portal.png)  

2.  新的工作有查詢範本，以協助您開始。 查詢範本會從輸入事件專案的所有欄位的 「 傳送 「 查詢執行將輸出。  

    - 如果您已經定義至少有一個輸入與輸出工作，您可以取代預留位置 」 [YourOutputAlias] 」 和 「 [YourInputAlias] 」 欄位的輸入與您想要的輸出別名使用第一次。 此外，您仍撰寫及測試 Azure 傳統入口網站中的查詢，而不在工作上定義的輸入與輸出。
    - 如果您想要執行簡單的通過比處理更多，您可以編輯查詢定義。 若要開始與查詢撰寫，查看擷取的圖樣一些常見查詢[以下](stream-analytics-stream-analytics-query-patterns.md)。  
  
    ![查詢資料] 視窗](./media/stream-analytics-write-queries/2-stream-analytics-write-queries.png)  

## <a name="to-validate-query-data-is-working"></a>驗證資料查詢運作︰ ##

您可以測試您的查詢運作正常執行，在瀏覽器中移至一或多個本機 JSON 包含測試資料檔案。 這不會啟動工作，或有任何帳單的影響。

> [AZURE.NOTE] 目前在瀏覽器查詢測試不支援在 Azure 入口網站。  

1.  請確定 （否則測試按鈕將會停用） 查詢中沒有任何錯誤，然後按一下 [測試] 按鈕。  

    ![查詢資料測試](./media/stream-analytics-write-queries/3-stream-analytics-write-queries.png)  

2.  會提示您輸入查詢中參考的每個指定的檔案。 在此範例中，範本查詢保留為-是，名為 「 yourinputalias 」 輸入提示] 對話方塊。  

    ![測試資料查詢](./media/stream-analytics-write-queries/4-stream-analytics-write-queries.png)  

3.  瀏覽至測試檔案。 多個範例檔案上提供[github](https://github.com/Azure/azure-stream-analytics/tree/master/Sample Data)而您也可以從您自己的資料串流輸入透過 [輸入] 索引標籤上的範例資料函數來擷取範例資料。  

    ![輸入查詢](./media/stream-analytics-write-queries/5-stream-analytics-write-queries.png)  

4.  先關閉對話方塊，查詢會執行測試資料，您會看到結果底部的 [查詢] 頁面。  

    ![查詢摘要](./media/stream-analytics-write-queries/6-stream-analytics-write-queries.png)  

## <a name="get-help"></a>取得說明
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)
