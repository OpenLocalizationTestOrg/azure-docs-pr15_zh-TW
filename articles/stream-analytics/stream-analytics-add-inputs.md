<properties
    pageTitle="新增您的資料流分析工作資料輸入 |Microsoft Azure"
    description="瞭解如何將連結的資料來源，以資料流分析工作為串流資料輸入從事件集線器或參考資料從部落格的儲存空間。"
    keywords="資料輸入，串流資料"
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


# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>將資料流資料輸入或參考資料新增至資料流分析工作

瞭解如何將連結的資料來源，以資料流分析工作為串流資料輸入從事件集線器或參考資料從 Blob 儲存體。

Azure 資料流分析作業可連線至一個資料輸入或更多]，每個現有的資料來源中定義的連線。 傳送資料到該資料來源，它是由資料流分析作業，處理為串流資料的即時。 資料流分析有第一本課程整合[Azure 事件集線器](https://azure.microsoft.com/services/event-hubs/)和[Azure Blob 儲存體](../storage/storage-dotnet-how-to-use-blobs.md)內部和外部工作的訂閱。

本文是[串流分析學習路徑](/documentation/learning-paths/stream-analytics/)的步驟。

## <a name="data-input-streaming-data-and-reference-data"></a>資料輸入︰ 串流資料和參考資料

有兩種不同的資料流分析中輸入︰ 資料流和參考資料。

- **資料流**︰ 串流分析工作必須包含至少一個資料串流輸入 consumed 和轉換的工作。 為資料串流輸入來源支援 azure Blob 儲存體和 Azure 事件集線器。 Azure 事件集線器用來收集事件資料流時，連接的裝置、 服務或應用程式。 Azure Blob 儲存體可以用於為輸入來源 ingesting 大量資料，以資料流。  
- **參考資料**︰ 串流分析支援第二個輔助輸入稱為的參考資料類型。  而非在移動中的資料，這項資料是靜態或減慢變更。  通常是執行查詢並使用資料串流的關聯性的建立更豐富的資料集。  Azure Blob 儲存體目前是參考資料僅支援輸入的來源。  

若要新增的輸入您的資料流分析工作︰

1. Azure 入口網站中按一下**輸入**，然後按一下 [資料流分析工作中的 [**新增輸入**。

    ![Azure 傳統入口網站-新增輸入。](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  

    Azure 入口網站中按一下 [資料流分析工作中的 [**輸入**] 磚。  

    ![Azure 入口網站-新增資料輸入。](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  

2. 指定輸入類型: [**資料流**] 或 [**參考資料**。

    ![新增正確的資料輸入、 串流或參照](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  

    ![新增正確的資料輸入、 串流或參照](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  

3. 如果建立資料流輸入，指定輸入的來源類型。  可以略過此步驟，為此次支援儲存空間的 Blob 的參考資料建立期間。

    ![新增資料串流資料輸入](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  

    ![新增資料串流資料輸入](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  

4. 提供輸入別名] 方塊中輸入易記的名稱。  此名稱將用於您的工作查詢稍後輸入參照。

    填寫其餘的連線到您的資料來源的所需的連線屬性。 這些欄位依類型的輸入與來源類型而定，而定義的詳細資料[以下](stream-analytics-create-a-job.md)。  

    ![新增事件] 中心內的資料輸入](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  

5. 指定輸入資料的序列化設定︰
    - 若要確定您的查詢的運作的方式您預期，指定的**事件序列化格式**的內送的資料。  支援的序列化格式是 JSON、 CSV 及 Avro。
    - 確認**編碼**的資料。  Utf-8 這次是只支援編碼格式。

    ![輸入資料的資料序列化設定](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  

    ![輸入資料的資料序列化設定](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  

6. 完成後輸入的建立，串流分析將確認它可以連線到輸入的來源。  您可以在 [通知] 中心檢視的 [測試連接] 作業的狀態。

    ![[測試連線的資料流資料輸入](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  

    ![[測試連線的資料流資料輸入](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>取得說明串流資料輸入
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)
