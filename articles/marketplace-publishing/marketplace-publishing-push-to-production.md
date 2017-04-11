<properties
   pageTitle="將您的提議部署至 Azure Marketplace |Microsoft Azure"
   description="深入了解與逐步指示部署您的提議，虛擬機器圖像、 開發人員服務、 資料服務等，以 Azure Marketplace。"
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
   ms.date="08/02/2016"
   ms.author="hascipio" />

# <a name="deploy-your-offer-to-the-azure-marketplace"></a>將您的提議部署至 Azure Marketplace
當您滿意您提供的服務 （也就是您已測試客戶案例、 行銷內容等），並準備好要啟動，請在 [**發佈**] 索引標籤上要求**推入生產**。  

1. 在 [發佈頁面的逐步解說的四個步驟入口網站應該已完成和綠色。 虛擬機器優惠] 之下，確定會遵循下列方針。

    ![繪圖][img-pubportal-walkthru-checked]

2. 從左側清單中選取 [**發佈**] 索引標籤。

    ![繪圖][img-pubportal-menu-publish]

3. 按一下 [**要求核准的推入生產**] 按鈕。 一旦提出要求時，「 核准 」 小組執行最終檢閱，，然後您的提議便會出現在 Azure Marketplace。

    ![繪圖][img-pubportal-publish-pushproduction]

>[AZURE.IMPORTANT] 若虛擬機器，當您按一下按鈕要求核准的推入生產，請執行下列步驟場景後面。 您可以檢視每個步驟，在 [發佈] 索引標籤底下的進度，在 [發佈入口網站。 您必須檢查此頁面定期間隔 （直到狀態] 顯示 「 列出 「） 需要從您的結束修正任何失敗資訊。

> - 第一次生產要求進入驗證 vhd 憑證小組。 不過，如果您要更新您已列出提供的服務要求有只有行銷變更，然後憑證步驟會略過。
> - 在下一個步驟中，要求了內容驗證小組人員驗證優惠行銷內容。
> - 如果上述步驟成功，請在生產環境中已核准優惠。 目前狀態會變成 」 」 中列出發佈入口網站。 不過，此 」 列出 「 狀態不表示有完成程序。 需要先完成優惠 Azure Marketplace 是使用下列步驟。
> - 優惠經過核准實際執行上述步驟後的優惠複寫開始過所有 Azure 資料中心。 通常採用 24-48hours 複製完成，但最多可能需要一週根據 vhd 的大小。 不過，如果您要更新您已列出的提議，其中有只有行銷變更複寫是更快。
> - 複寫完成後，然後優惠便會出現在 Azure Marketplace。

> 在**「 草稿 」**狀態 （亦即，永遠不會 [**推入臨時**或**正式的推入**） 時，您隨時都可以刪除優惠。 按一下 [**歷程記錄**] 索引標籤中，若要刪除草稿頁面底部的 [**放棄草稿**] 按鈕。


## <a name="production-checklist-for-all-virtual-machine-offers"></a>所有的虛擬機器優惠生產檢查清單

- 確保您的 Microsoft Azure 認證的合作夥伴
- Sku] 索引標籤下選項 」 隱藏市集此 SKU 因為它應該一律購買方案範本透過 「 標示為 [是] 只有 SKU 是方案範本的一部分。 在所有其他的情況，此選項一律標示為 [否]。
- 請記住︰ 您應該不變更 [SKU 可見度設定後會列出 SKU。 我們不支援這項功能。
- 請確定標誌依循 Azure Marketplace 標誌指導方針，如下所示。
- 優惠和 SKU 描述應該不相同。
- SKU 的標題和提供長摘要應該不相同。
- SKU 標題與提供摘要應該不相同。
- 不應提供的多個 Sku 相同 SKU 標題。

**Azure 服務商場標誌指導方針**

- Azure 設計有簡單的調色盤。 保留的數字的主要和次要色彩在您的標誌低。
- Azure 入口網站的佈景主題色彩是白色和黑色。 因此請避免使用這些色彩為您的標誌的背景色彩。 使用某些會讓您的標誌顯眼 Azure 入口網站中的色彩。 我們建議您簡單的主要色彩。 如果您使用透明背景，請確認標誌/文字不是白色或黑色。
- 不要使用標誌漸層背景。
- 避免在標誌放置文字，即使您的公司或品牌名稱。
- 您的標誌的外觀與風格應為 「 一般 」，並應避免漸層。
- 不可延伸標誌。

**示範用圖像標誌的其他的指導方針︰**

- 示範用圖像標誌為選用步驟。 選擇 publisher 不可以上傳的示範用圖像標誌。 **但是一次上傳的示範用圖像圖示無法刪除的 [發佈入口網站。此時，合作夥伴必須遵循生產示範用圖像圖示還會核准優惠的 Azure Marketplace 指導方針。**
- Publisher 顯示名稱、 SKU 標題和長摘要優惠會顯示在白色的字型色彩]。 因此您應該避免保留示範用圖像圖示的背景中的任何精簡的色彩。 黑色，白色和透明背景不允許的示範用圖像圖示。
- Publisher 顯示名稱、 SKU 標題、 優惠 long 摘要和 [建立] 按鈕會內嵌以程式設計方式內的示範用圖像標誌後優惠進入列出。 因此您不應該輸入的任何文字在設計示範用圖像標誌。 只要離開右側空白區域，因為文字 （亦即 publisher 顯示名稱]、 [SKU 標題 long 摘要優惠） 將會包含以程式設計方式，我們有。 文字的空白區域應該 415 x 100 右側 （而從左至右 370px 位移）。


## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>提供其他生產已列出的虛擬機器檢查清單

- 檢查是否已提供您公司內的相同的方案名稱。 如果是的您應該在現有優惠，而不是建立新的重複優惠新增 SKU 的新版本。
- 資料磁碟不應該變更之間的相同的 SKU 的兩個版本。
- Azure Marketplace 不支援價格變更列出的 SKU，因為它會影響到現有的客戶的帳單。 請確定您無法變更 SKU 有的何處區域中列出的 Sku 的價格。 不過，您可以新增新的 Sku，或將新的區域新增至現有的 SKU。


## <a name="next-steps"></a>後續步驟
一旦優惠上市後，測試驗證所有合約與功能正常運作為生產環境中測試並確認執行環境中的客戶案例。

## <a name="see-also"></a>另請參閱
- [快速入門︰ 如何將提供發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png
