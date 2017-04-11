<properties 
    pageTitle="如何設定資料輸出資料流分析工作 |Microsoft Azure" 
    description="設定輸出資料流分析工作 |學習路徑區段。"
    keywords="資料輸出，移動資料"
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

# <a name="how-to-configure-data-outputs-for-stream-analytics-jobs"></a>如何設定資料輸出資料流分析工作

Azure 資料流分析作業可連線到一或多個資料輸出到現有的資料接收定義連線。 您的資料流分析工作程序，並轉換內送的資料，您的工作成果寫入資料輸出事件的資料流。

資料流分析資料來源即時的儀表板或通知，可輸出觸發資料移動工作流程，，或只是封存資料以批次稍後處理。 資料流分析已有數種 Azure 服務，會記錄在以下詳細資料的第一本課程整合。

新增您的資料流分析工作成果︰

1. 在 Azure 傳統入口網站中，按一下 [**輸出**，然後按一下**新增輸出**資料流分析工作中。

    ![新增輸出](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)  

    Azure 入口網站中按一下 [**輸出**磚串流分析工作中。

    ![Azure Porta 新增輸出](./media/stream-analytics-add-outputs/5-stream-analytics-add-outputs.png)

2. 指定輸出的類型︰

    ![選擇移動的資料類型](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)  

    ![Azure 入口網站選擇移動的資料類型](./media/stream-analytics-add-outputs/6-stream-analytics-add-outputs.png)

3. 提供此**輸出別名**] 方塊中的輸出好記的名稱。 此名稱可在您的工作查詢稍後輸出參照。  
    
    填寫其餘的連線到您的輸出的所需的連線屬性。  這些欄位依輸出類型而定，以下詳細資料中所定義。  

    ![新增資料輸出屬性](./media/stream-analytics-add-outputs/3-stream-analytics-add-outputs.png)  

4. 根據輸出類型，您可能需要指定資料如何序列化或格式設定。 此處所列的每一種輸出類型的特定序列化設定。

    填寫其餘的連線到您的資料來源的所需的連線屬性。 這些欄位依類型的輸入與來源類型而定，而定義的詳細資料[以下](stream-analytics-create-a-job.md)。  

    ![新增資料輸出到事件集線器](./media/stream-analytics-add-outputs/4-stream-analytics-add-outputs.png)  

    ![Azure 入口網站的資料輸出到事件集線器](./media/stream-analytics-add-outputs/7-stream-analytics-add-outputs.png)  

> [Azure.Note] 新增工作至任何輸出項目，必須先啟動工作和事件開始的書寫方向存在的。 例如，如果您使用 Blob 儲存體作為輸出時，工作不會建立儲存帳戶自動。 需要先建立使用者 ASA 工作開始。

## <a name="get-help"></a>取得說明
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)
