<properties
   pageTitle="測試您的服務商場的 VM 提議 |Microsoft Azure"
   description="瞭解如何測試 Azure Marketplace VM 圖像。"
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
   ms.date="08/01/2016"
   ms.author="hascipio" />

# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>測試您的 VM 提議 Azure Marketplace 的臨時中

臨時表示部署您 SKU 中包含私人的 「 沙箱 「 您可以在此測試並將其部署到服務商場前驗證其功能。 SKU 會出現在臨時已部署客戶一樣。 必須推入臨時，必須經過認證 VM 圖像。

## <a name="step-1-push-your-offer-to-staging"></a>步驟 1︰ 推播臨時您提供的優惠方案

1. 在 [**發佈**] 索引標籤中，按一下 [**推入臨時**。

    ![繪圖](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)

2. 如果發佈入口網站會通知您的任何錯誤，修正。
3.  在 [**誰能夠存取您的分段的提議？**對話方塊方塊中，輸入您要用來預覽您的提議[Azure 預覽入口網站](https://portal.azure.com)中的 Azure 訂閱清單。

    >[AZURE.NOTE] 若虛擬機器和解決方案範本，請**不要**whitelist 訂閱類型 CSP，DreamSpark 或 Azure 中開啟。


    > 若虛擬機器，當您按一下按鈕**以臨時推入**，請執行下列步驟場景後面。 您可以檢視每個步驟，在 [發佈] 索引標籤底下的進度，在 [發佈入口網站。 您必須檢查此頁面定期間隔 （直到狀態] 顯示分段） 需要從您的結束修正任何失敗資訊。

    > - 第一次您暫存要求進入驗證 vhd 憑證小組。 不過，如果您的要求具有只有行銷變更，那麼就憑證步驟會略過。
    > - 完成的憑證之後，優惠的複寫開始過所有 Azure 資料中心。 通常採用 24-48hours 複製完成，但最多可能需要一週根據 vhd 的大小。 不過，如果您的要求具有只有行銷變更，那麼複寫是更快。
    > - 複寫完成後，優惠會有[Azure 入口網站](http:/portal.azure.com)。 時間狀態的 at 成為進入 [發佈入口網站。 分段的優惠看得見僅使用與優惠分段安裝與訂閱相關聯的電子郵件識別碼[Azure 入口網站](http:/portal.azure.com)中。

4. 使用其中一個 Azure 上一個步驟中所列的訂閱登入[Azure 預覽入口網站](https://portal.azure.com)。
5. 尋找您的提議，和驗證您的 VM 圖像點︰
  - 請確定的行銷內容正確顯示市場中。
  - VM 圖像的端對端部署。

      ![影像地圖-入口網站](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [AZURE.IMPORTANT] 您的提議仍會保留在臨時通知 Microsoft 透過發佈入口網站，直到 [**發佈**] 索引標籤 > 按一下按鈕**「 要求核准至推入至生產 」**] 您已準備好推入生產。 這是沒有所有項目會列出您的提議準備您的小組核取的所有成員的理想時間。

> 在預覽模式中測試優惠的設計暫存的平台發行者。 我們強烈建議使用此 platofrm commerical 用途。

## <a name="next-steps"></a>後續步驟
現在，您的提議的動作 「 分段 」，您已測試其功能與行銷內容，您可以繼續到最後一個發佈階段**步驟 4**︰[部署服務商場您提供的優惠方案](marketplace-publishing-push-to-production.md)。

## <a name="see-also"></a>另請參閱
- [快速入門︰ 如何將提供發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)
