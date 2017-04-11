<properties
   pageTitle="測試您的資料服務提議 Marketplace 的 |Microsoft Azure"
   description="瞭解如何測試版 Azure Marketplace 資料服務提供。"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2016"
   ms.author="hascipio; avikova" />

# <a name="testing-your-data-service-offer-in-staging"></a>測試您的資料服務提議中執行

>[AZURE.IMPORTANT] **現在我們已不再登入任何新的資料服務的發行者。新的 dataservices 非取得已核准的清單。** 如果您有您想要發佈上 AppSource SaaS 商務應用程式，您就可以找到更多資訊[以下](https://appsource.microsoft.com/partners)。 如果您有 IaaS 應用程式，或您想要發佈 Azure 服務商場的開發人員服務可以找到更多資訊[以下](https://azure.microsoft.com/marketplace/programs/certified/)。

完成[建立您的 Microsoft 開發人員帳戶](marketplace-publishing-accounts-creation-registration.md)，並[建立您的資料發佈入口網站中的服務提供](marketplace-publishing-data-service-creation.md)的前兩個步驟之後您準備好在 Azure Marketplace 提供您提供的服務。 本主題會引導您執行的第一個，中繼步驟稱為 「 臨時 」

臨時表示部署私人 「 沙箱 」 便可在此測試並確認其功能之前先將它推生產中的提供。 優惠會出現在臨時已部署客戶一樣。

## <a name="step-1-pushing-your-offer-to-staging"></a>步驟 1。 推入臨時您提供的優惠方案
推入臨時您提供的優惠方案，可讓您測試優惠之前可供未來的訂閱者使用。  您可以看到您的提議會如何顯示，並函數的訂閱您的資料。  

  ![繪圖](media/marketplace-publishing-data-service-test-in-staging/step-1.1.png)

1.  將[發佈入口網站](https://publish.windowsazure.com)登入
2.  選取 [左側的導覽視窗中的 [**資料服務**
3.  選取您想要推入臨時您提供的服務。 您會看到 [上方] 畫面。
4.  按一下**以臨時推入**] 按鈕。  
5.  如果有需要完成之前發送至臨時優惠的問題，您會看到顯示的清單。  在清單中的每個項目上的 [修正這些項目。 進行時，所有校正，再按一下 [**推入臨時**] 按鈕。

如果不有任何問題，與您的提議，您會看到以下的快顯視窗。  

如果您不規劃/不核准呈現您的提議 Azure 入口網站 （目前有限容量），然後關閉快顯視窗。

若要測試 Azure 入口網站中的資料服務 （除了 DataMarket 入口網站中），您必須使用測試 Azure 訂閱識別碼。  此訂閱 ID 會找出可以測試您的提議的帳戶。  

剪下並貼上您的訂閱識別碼，按一下 [核取記號，以繼續進行。

  ![繪圖](media/marketplace-publishing-data-service-test-in-staging/step-1.2.png)

> [AZURE.NOTE] Azure 訂閱識別碼只在所需的測試和臨時[Azure 管理入口網站](https://manage.windowsazure.com)中。 不需要在 Azure 服務商場測試。

下一個畫面中出現的發佈正在進行中會顯示 「 正在進行 」 圖示會顯示的反白顯示黃色下方。 發送至臨時會介於 10 到 15 分鐘。  如果花更長的時間，第一次重新整理瀏覽器 （ie 按 F5）。  在您的提議仍發送小時後接移至到少見的情況下，按一下 [的連絡人我們連結讓我們知道有問題。

  ![繪圖](media/marketplace-publishing-data-service-test-in-staging/step-1.3.png)

推入至臨時完成 」 的進度] 圖示會停止移動及狀態會更新為 「 分段 」。  您已準備好測試您的提議。  

## <a name="step-2-test-your-staged-offer-in-datamarket"></a>步驟 2。 DataMarket 中測試您分段的優惠

按一下 [追蹤**「 看到您的服務提供 at....」**的文字] 連結 若要顯示 [訂閱者將會看到您的提議前往生產，並會出現在 [DataMarket] 畫面。

  ![繪圖](media/marketplace-publishing-data-service-test-in-staging/step-2.2.png)

確定每個 12 的項目標示上方，以確保所有的標誌、 價格/交易、 文字、 圖像、 文件，以及連結正確，以及工作正確或測試。  這是良好的時間，以確保您輸入時建立您的提議的任何測試值被取代實際的值。

1. 提供標誌
2. 提供名稱
3. Publisher 名稱/貴公司的網站連結
4. 為您提供的搜尋類別
5. 若要協助訂閱者提供的支援連結
6. 您的提議的關聯式說明
7. 提供描述帳單詳細資料的計劃
8. 連結至實作程式碼
9. 範例圖像，示範使用優惠資料
10. 每個服務優惠內輸入輸出中繼資料
11. 優惠的使用規定
12. 提供資料的預覽


最後，請檢查服務的運作 Datamarket 透過按一下 [瀏覽此資料集] 的連結。  隨即會開啟新視窗工具中我們稱為 「 服務檔案總管]，您可以針對您的服務預覽查詢的結果。  在此視窗中，您可以輸入所需的參數，並查看您的服務查詢從顯示的結果。   此外，是顯示 URL 查詢。  

> [AZURE.NOTE] 請務必檢閱服務頂端顯示的文字描述。  如果您的提議組成一個以上的服務來電，請按一下 [切換到下一步服務，來檢視並測試底部的索引標籤。



## <a name="next-step"></a>下一步
如果您有問題，需要協助解決這些請連絡[Azure Publisher 支援]( http://go.microsoft.com/fwlink/?LinkId=272975)。

如果您是沒有問題並準備好要發佈您的提議請閱讀的[推播實際執行要求核准](marketplace-publishing-push-to-production.md)文件。

## <a name="see-also"></a>另請參閱
- [快速入門︰ 如何將提供發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)
