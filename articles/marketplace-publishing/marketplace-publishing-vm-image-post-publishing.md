<properties
   pageTitle="管理您的虛擬機器上圖像 Azure Marketplace |Microsoft Azure"
   description="如何管理您的虛擬機器上圖像 Azure Marketplace 初始出版物之後的詳細說明。"
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="08/03/2016"
   ms.author="hascipio;"/>

# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>在 Azure Marketplace 的虛擬機器優惠後製指南

本文說明如何更新中 Azure Marketplace 的即時虛擬機器優惠。 同時也會引導您新增至現有的優惠的一或多個新 Sku 的程序，並移除 [從 Azure Marketplace 的 [即時虛擬機器優惠或 SKU。

一旦優惠/SKU 分段[Azure 入口網站](http://portal.azure.com)中，您無法變更欄位如下所示︰

- **提供識別碼︰**[發佈入口網站]-> [虛擬機器]-> [的選取您的提議]-> [VM 圖像] 索引標籤提供的識別碼]->]
- **SKU 識別碼︰**[發佈入口網站]-> [虛擬機器]-> [的選取您的提議]-> [Sku] 索引標籤]-> [新增 SKU]
- **Publisher 命名空間︰**[發佈入口網站]-> [虛擬機器]-> [逐步解說] 索引標籤]-> [操作說明我們瞭解您的公司 （發現在 [步驟 2 註冊您的公司 」）]-> Publisher 命名空間]-> [命名空間]

一旦優惠/SKU 會列於[Azure Marketplace](http://azure.microsoft.com/marketplace)，您無法變更的欄位，如下所示︰

- **提供識別碼︰**[發佈入口網站]-> [虛擬機器]-> [的選取您的提議]-> [VM 圖像] 索引標籤提供的識別碼]->]
- **SKU 識別碼︰**[發佈入口網站]-> [虛擬機器]-> [的選取您的提議]-> [Sku] 索引標籤]-> [新增 SKU]
- **Publisher 命名空間︰**[發佈入口網站]-> [虛擬機器] 索引標籤]-> [逐步解說]-> [告訴我們瞭解您的公司 （找到在步驟 2 註冊） Publisher 命名空間]-> [命名空間]
- **連接埠**[發佈入口網站]-> [虛擬機器]-> [的選取您的提議]-> [VM 圖像] 索引標籤]-> [開啟連接埠]
- **價格列出 SKU(s) 變更**
- **付款列出 SKU(s) 模型變更**
- **移除的付款列出 SKU(s) 區域**
- **變更資料磁碟的計數列出 SKU(s)**



## <a name="1-how-to-update-the-technical-details-of-a-sku"></a>1.如何更新的 SKU 技術詳細資料

您可以新增新的版本所列的 SKU，並重新發佈您的提議，按照下面所列的步驟進行︰

1. 登入的[發佈入口網站](https://publish.windowsazure.com)。
2. 瀏覽至 [**虛擬機器**] 索引標籤，然後選取您的提議。
3. 左側側邊] 功能表上按一下 [ **VM 圖像**] 索引標籤。
4. 從 [ **VM 圖像**] 索引標籤的 [ **Sku** ] 區段中，找出您想要更新的 SKU。
5. 之後，新增新的版本號碼的 SKU，並按一下**[+]**按鈕。 新的版本應該 X.Y.Z 格式的 X Y Z 為整數的位置。 版本變更只會累加。
6. 在**OS VHD URL** ] 方塊中，新增 URI 建立作業系統 VHD 共用的 access 簽名，並儲存變更。

    >[AZURE.IMPORTANT] 您無法增加/減少資料磁碟的計數列出的 SKU。 您需要在此情況下建立新的 SKU。 請參閱[3 一節。如何新增底下列出的優惠的新 SKU](#3-how-to-add-a-new-sku-under-a-live-offer)的詳細指示。

7. 進行變更之後，瀏覽至 [**發佈**] 索引標籤，然後按一下 [] 按鈕**來執行推播**。 取得執行環境中測試您的提議的詳細指示，請參閱此[連結](marketplace-publishing-vm-image-test-in-staging.md)
8. 一旦您已在臨時測試您的提議，瀏覽至 [**發佈**] 索引標籤的 [發佈入口網站和中，按一下 [**要求核准至推入至生產**重新發佈您的提議，在 Azure Marketplace] 按鈕。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku"></a>2.如何更新的服務提供或 SKU 技術詳細資料

您可以更新技術 （行銷法律，支援，類別） 即時優惠或在 Azure Marketplace 的 SKU 的詳細資料。

### <a name="21-update-the-offer-description-and-logos"></a>2.1 更新的優惠描述及標誌

您可以更新提供詳細資料，並重新發佈您的提議，依照下列步驟︰

1. 登入的[發佈入口網站](https://publish.windowsazure.com)。
2. 瀏覽至 [**虛擬機器**] 索引標籤，然後選取您的提議。
3. [左側側邊] 功能表上按一下 [**行銷**] 索引標籤。
4. 按一下 [**英文 （美國）** ] 按鈕。
5. [左側側邊] 功能表上按一下 [**詳細資料**] 索引標籤。 在 [**詳細資料**] 索引標籤的 [*描述*] 區段底下，可以更新優惠標題、 提供摘要、 提供完整的摘要並儲存變更。

    >[AZURE.NOTE] 請注意下列任一時您要更新的 SKU 詳細資料。
    **不要輸入 [重複優惠描述與 SKU 描述] 下的文字。不要輸入 [重複的文字，在 [SKU 標題] 和 [長摘要優惠] 之下。不要輸入 [重複的 SKU 標題與優惠摘要] 下的文字。**

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)

6. 在 [**詳細資料**] 索引標籤的 [*標誌*] 區段中，您可以更新標誌。 不過，確定 [標誌遵循[Azure Marketplace 準則](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)(參閱一節步驟 1︰ 提供服務商場行銷內容]-> [詳細資料]-> [Azure Marketplace 標誌指導方針)。

    >[AZURE.NOTE] 示範用圖像圖示是選擇性的。 您可以選擇無法上傳的示範用圖像圖示。 不過之後上傳示範用圖像圖示，, 則若要刪除 [發佈沒有佈建入口網站。 在此情況下，您必須遵循[示範用圖像圖示準則](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)(參閱一節步驟 1︰ 提供服務商場行銷內容]-> [詳細資料]-> [其他方針主題標誌橫幅)。

7. 進行變更之後，瀏覽至 [**發佈**] 索引標籤，然後按一下 [] 按鈕**來執行推播**。 取得執行環境中測試您的提議的詳細指示，請參閱此[連結](marketplace-publishing-vm-image-test-in-staging.md)。
8. 一旦您已在臨時測試您的提議，瀏覽至 [**發佈**] 索引標籤的 [發佈入口網站和中，按一下 [**要求核准至推入至生產**重新發佈您的提議，在 Azure Marketplace] 按鈕。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="22-update-the-sku-description"></a>2.2。 [更新 SKU 的描述

您可以更新 SKU 詳細資料，並重新發佈您的提議，依照下列步驟︰

1. 登入的[發佈入口網站](https://publish.windowsazure.com)
2. 瀏覽至 [**虛擬機器**] 索引標籤，然後選取您的提議。
3. [左側側邊] 功能表上按一下 [**行銷**] 索引標籤。
4. 按一下 [**英文 （美國）** ] 按鈕。
5. [左側側邊] 功能表上按一下 [**方案**] 索引標籤。 在 [**方案**] 索引標籤的*Sku*區段中，您可以更新 SKU 標題、 SKU 摘要和 SKU 描述詳細資料和儲存所做的變更。

    >[AZURE.NOTE] 請注意下列任一時您要更新的 SKU 詳細資料。 **不要輸入 [重複優惠描述與 SKU 描述] 下的文字。不要輸入 [重複的文字，在 [SKU 的標題和長摘要優惠] 之下。不要輸入 [重複 SKU 標題與優惠摘要] 下的文字。**

6. 進行變更之後，瀏覽至 [**發佈**] 索引標籤，然後按一下 [] 按鈕**來執行推播**。 取得執行環境中測試您的提議的詳細指示，請參閱此連結
7. 一旦您已在臨時測試您的提議，瀏覽至 [**發佈**] 索引標籤的 [發佈入口網站和中，按一下 [**要求核准至推入至生產**重新發佈您的提議，在 Azure Marketplace] 按鈕。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="23-change-the-existing-links-or-add-new-links"></a>2.3 變更現有的連結，或新增連結

您可以變更現有的連結或新增連結，然後依照下列步驟重新發佈您的提議︰

1. 登入的[發佈入口網站](https://publish.windowsazure.com)
2. 瀏覽至 [**虛擬機器**] 索引標籤，然後選取您的提議。
3. [左側側邊] 功能表上按一下 [**行銷**] 索引標籤。
4. 按一下 [**英文 （美國）** ] 按鈕。
5. [左側側邊] 功能表上按一下 [**連結**] 索引標籤。
6. 如果您想要新增連結，然後底下的 [*連結*] 區段按一下 [**新增連結**] 按鈕。 *[新增連結]*對話方塊會開啟。 在此對話方塊，您可以新增連結的標題和 URL 的欄位，並儲存變更。 您可以輸入任何連結，其中包含可協助客戶的資訊。
7. 如果您想要更新或刪除現有的連結，然後選取適當的連結，再按一下 [編輯] 按鈕或 [刪除] 按鈕上的 [依此。

    >[AZURE.NOTE] 請務必，您必須輸入此區段中的連結正常運作，為這些連結取得已驗證生產要求過程。

8. 進行變更之後，瀏覽至 [**發佈**] 索引標籤，然後按一下 [] 按鈕**來執行推播**。 取得執行環境中測試您的提議的詳細指示，請參閱此[連結](marketplace-publishing-vm-image-test-in-staging.md)。
9. 一旦您已在臨時測試您的提議，瀏覽至 [**發佈**] 索引標籤的 [發佈入口網站和中，按一下 [**要求核准至推入至生產**重新發佈您的提議，在 Azure Marketplace] 按鈕。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="24-change-an-existing-sample-image-or-add-a-new-sample-image"></a>2.4 變更現有的範例圖像或新增新的範例圖像

您可以變更現有的範例圖像或新增新的範例圖像，然後依照下列步驟重新發佈您的提議︰

>[AZURE.NOTE] 只有一個範例圖像會顯示在[https://portal.azure.com](https://portal.azure.com)。

1. 登入的[發佈入口網站](https://publish.windowsazure.com)
2. 瀏覽至 [**虛擬機器**] 索引標籤，然後選取您的提議。
3. [左側側邊] 功能表上按一下 [**行銷**] 索引標籤。
4. 按一下 [**英文 （美國）** ] 按鈕。
5. 左側側邊] 功能表上按一下 [**範例圖像**] 索引標籤。
6. 如果您想要新增新的範例圖像，然後在 [*範例圖像*] 區段底下按一下 [**上傳新圖像**] 按鈕，然後儲存變更。

    >[AZURE.NOTE] 包括範例圖像是選擇性的步驟。

7. 如果您想要更新或刪除現有的範例圖像，然後尋找適當的範例圖像，然後按一下 [**取代影像**] 按鈕或 [刪除] 按鈕會相應地。

8. 進行變更之後，瀏覽至 [**發佈**] 索引標籤，然後按一下 [] 按鈕**來執行推播**。 取得執行環境中測試您的提議的詳細指示，請參閱此[連結](marketplace-publishing-vm-image-test-in-staging.md)。
9. 一旦您已在臨時測試您的提議，瀏覽至 [**發佈**] 索引標籤的 [發佈入口網站和中，按一下 [**要求核准至推入至生產**重新發佈您的提議，在 Azure Marketplace] 按鈕。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="25-update-the-legal-content"></a>2.5 更新法律內容

您可以更新的法律內容，並重新發佈您的提議，依照下列步驟︰

1. 登入的[發佈入口網站](https://publish.windowsazure.com)
2. 瀏覽至 [**虛擬機器**] 索引標籤，然後選取您的提議。
3. [左側側邊] 功能表上按一下 [**行銷**] 索引標籤。
4. 按一下 [**英文 （美國）** ] 按鈕。
5. 從 [左側側邊] 功能表中，按一下 [**法律**] 索引標籤上。 在*法律*] 區段底下，您可以更新您原則/使用條款。 輸入或貼上*的 [使用規定*] 文字方塊中的原則/字詞，然後儲存變更。
6. 使用法律規定的字元限制為 1000000 的字元。
7. 進行變更之後，瀏覽至 [**發佈**] 索引標籤，然後按一下 [] 按鈕**來執行推播**。 取得執行環境中測試您的提議的詳細指示，請參閱此[連結](marketplace-publishing-vm-image-test-in-staging.md)
8. 一旦您已在臨時測試您的提議，瀏覽至 [**發佈**] 索引標籤的 [發佈入口網站和中，按一下 [**要求核准至推入至生產**重新發佈您的提議，在 Azure Marketplace] 按鈕。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="26-update-the-support-information"></a>2.6 更新的支援資訊

您可以更新的支援資訊，並重新發佈您的提議，依照下列步驟︰

1. 登入的[發佈入口網站](https://publish.windowsazure.com)
2. 瀏覽至 [**虛擬機器**] 索引標籤，然後選取您的提議。
3. [左側側邊] 功能表上按一下 [**支援**] 索引標籤。
4. 在 [**支援**] 索引標籤的 [*工程連絡人*] 區段底下，您可以更新連絡人的詳細資料。 以下詳細適用於內部和之間的通訊合作夥伴 Microsoft 只。
5. 在 [**支援**] 索引標籤的*客戶支援*區段中，您可以更新**名稱、 電子郵件、 手機**與**支援 URL**等支援連絡人詳細資料。 以下詳細適用於內部和之間的通訊合作夥伴 Microsoft 只。

    >[AZURE.NOTE] 如果您想要只提供電子郵件的支援，提供的**客戶支援部門**] 區段下一個空的電話號碼。 在此情況下，就會改用您提供的電子郵件。

6. 進行變更之後，瀏覽至 [**發佈**] 索引標籤，然後按一下 [] 按鈕**來執行推播**。 取得執行環境中測試您的提議的詳細指示，請參閱此[連結](marketplace-publishing-vm-image-test-in-staging.md)
7. 一旦您已在臨時測試您的提議，瀏覽至 [**發佈**] 索引標籤的 [發佈入口網站和中，按一下 [**要求核准至推入至生產**重新發佈您的提議，在 Azure Marketplace] 按鈕。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="27-update-the-categories"></a>2.7 更新類別

您可以更新您的提議 [類別] 區段，並重新發佈您的提議，依照下列步驟︰

1. 登入的[發佈入口網站](https://publish.windowsazure.com)
2. 瀏覽至 [**虛擬機器**] 索引標籤，然後選取您的提議。
3. 從 [左側側邊] 功能表中，按一下 [**類別**] 索引標籤。
4. 在 [*類別*] 區段底下，您可以更新您的提議的類別，並儲存變更。 您可以選取 Azure Marketplace 庫最多五個類別。
5. 進行變更之後，瀏覽至 [**發佈**] 索引標籤，然後按一下 [] 按鈕**來執行推播**。 取得執行環境中測試您的提議的詳細指示，請參閱此[連結](marketplace-publishing-vm-image-test-in-staging.md)
6. 一旦您已在臨時測試您的提議，瀏覽至 [**發佈**] 索引標籤的 [發佈入口網站和中，按一下 [**要求核准至推入至生產**重新發佈您的提議，在 Azure Marketplace] 按鈕。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="3-how-to-add-a-new-sku-under-a-listed-offer"></a>3.如何新增底下列出的優惠的新 SKU

您可以新增您即時提供的服務底下的新 SKU 下面所列的步驟進行︰

1. 登入的[發佈入口網站](https://publish.windowsazure.com)。
2. 瀏覽至 [**虛擬機器**] 索引標籤，然後選取您的提議。
3. 從 [左側側邊] 功能表中，按一下 [ **Sku** ] 索引標籤上。 之後按一下的 [**新增 A SKU**] 按鈕。  隨即會開啟新的對話方塊。 輸入 SKU 識別碼小寫。 如果您想要發佈的新 SKU BYOL 付款的模型，請核取 [顯示-您-擁有您的帳單 model(BYOL) 的核取方塊。 否則，請取消核取核取方塊 BYOL。 之後按一下的 [建立新的 SKU] 對話方塊中的刻度標記上。 如果您不是新 SKU 的選擇 BYOL 帳單模型，然後付款的模型就會自動設定為每小時新 SKU。 如果您想要啟用下限付款的模型 30days 免費試用版，然後按一下 「 1 個月 」 選項 」 有免費試用版？ 」。 否則，請選取 「 無試用版]。 [附註: 選項] 是提供免費試用版？ 」 僅會顯示是否您尚未選取 BYOL] 對話方塊中時建立新的 SKU。]

    >[AZURE.IMPORTANT] 應該 」 隱藏市集此 SKU 因為它應該一律購買透過方案範本 」 的選項會標示為 [是] 是否您要發佈的解決方案範本優惠在 Azure Marketplace 核准。 否則，這個選項，應該一律會標示為 [否]。

4. 現在從左側側邊] 功能表中，按一下**VM 圖像**] 索引標籤，然後找出您建立的新 SKU。
5. 若要設定新的 SKU，請參閱此[連結](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image)取得指示的步驟 5。
6. 若要新增的新 SKU 的行銷內容，請參閱一節步驟 1︰ 提供服務商場行銷內容]-> [詳細資料]-> [此[連結](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)的點數字 2 到 5。
7. 若要新增的新 SKU 的價格資訊，請參閱 2.1] 區段。 設定此[連結](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)的 VM 價格
8. 進行變更之後，瀏覽至 [**發佈**] 索引標籤，然後按一下 [] 按鈕**來執行推播**。 取得執行環境中測試您的提議的詳細指示，請參閱此[連結](marketplace-publishing-vm-image-test-in-staging.md)
9. 一旦您已在臨時測試您的提議，瀏覽至 [**發佈**] 索引標籤的 [發佈入口網站和中，按一下 [**要求核准至推入至生產**重新發佈您的提議，在 Azure Marketplace] 按鈕。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="4-how-to-change-the-data-disk-count-for-a-listed-sku"></a>4.變更的方式列出的 SKU 的資料磁碟計數

您無法增加/減少資料磁碟的計數列出的 SKU。 在此情況下，您必須建立新的 SKU。 請參閱[3 一節。如何新增新 SKU 的即時優惠下](#3-how-to-add-a-new-sku-under-a-live-offer)的詳細指示。

## <a name="5---how-to-delete-a-listed-offer-from-the-azure-marketplace"></a>5。 如何刪除的列的優惠從 Azure Marketplace

有各式各樣需要注意若要移除的即時優惠的要求。 請遵循下列步驟來從支援小組，若要移除列出的優惠從 Azure Marketplace 取得指導方針︰

1.  提高使用此[連結](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681)的支援票證
2.  選取**「 管理優惠 」**問題類型，並選取類別為**「 修改優惠及/或 SKU 已在實際執行中 」**
3.  提交要求

支援小組會引導您完成優惠/SKU 刪除程序。

>[AZURE.NOTE] 在 「 草稿 」 狀態時，您可以隨時刪除優惠 （亦即不臨時或生產） 按一下 [**歷程記錄**] 索引標籤下的 [**放棄草稿**] 按鈕上。

## <a name="6-how-to-delete-a-listed-sku-from-the-azure-marketplace"></a>6。 如何刪除列出的 SKU 從 Azure Marketplace

您可以從 Azure Marketplace 刪除列出的 SKU，按照下面所列的步驟進行︰

1. 登入的[發佈入口網站](https://publish.windowsazure.com)。
2. 瀏覽至 [**虛擬機器**] 索引標籤，然後選取您的提議。
3. 從左側側邊窗格中，按一下 [ **SKU** ] 索引標籤。
4. 選取您要刪除，再按一下 [刪除] 按鈕，對該 SKU 的 SKU。
5. 一旦完成，瀏覽至 [發佈] 索引標籤的 [發佈入口網站和中，按一下 [重新發佈在 Azure Marketplace 提供的**要求核准至推入至生產**] 按鈕。
6. 一旦優惠取得重新發佈在 Azure Marketplace，會刪除 SKU 從 Azure Marketplace 和 Azure 入口網站。

## <a name="7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace"></a>7。 如何刪除目前版本的列出的 SKU 從 Azure Marketplace

您可以從 Azure Marketplace 刪除目前版本的列出的 SKU，如下所示的步驟，即可。 一旦完成程序，SKU 會復原先前的版本。

1. 登入的[發佈入口網站](https://publish.windowsazure.com)。
2.  瀏覽至 [**虛擬機器**] 索引標籤，然後選取您的提議。
3.  從左側側邊窗格中，按一下 [ **VM 圖像**] 索引標籤。
4.  選取您想要刪除，再按一下 [刪除] 按鈕，對該版本的目前版本的 SKU。
5.  一旦完成，瀏覽至 [**發佈**] 索引標籤的 [發佈入口網站和中，按一下 [重新發佈在 Azure Marketplace 提供的**要求核准至推入至生產**] 按鈕。
6.  一旦優惠取得重新發佈在 Azure Marketplace，列出 SKU 的目前版本會刪除從 Azure Marketplace 和 Azure 入口網站。 SKU 會復原先前的版本。

## <a name="8-how-to-revert-listing-price-to-production-values"></a>8。 如何還原為生產值的清單價格
我已變更的列的 SKU 價格 （或移除了付款列出的 SKU 區域）。 不支援在 Azure Marketplace，因為我想要還原為我的變更為生產值。 如何達到的？

請遵循下列步驟︰

1. 登入的[發佈入口網站](https://publish.windowsazure.com)。
2. 瀏覽至 [**虛擬機器**] 索引標籤，然後選取您的提議。
3. 從 [左側側邊] 功能表中，按一下 [**價格**] 索引標籤。
4. 在 [價格] 索引標籤中，選取您想要重設價格的區域。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)

5. 若每小時付款的模型的 Sku，重設所有核心的價格，只要在選取區域生產中。 BYOL 帳單模型的 sku，提供 SKU 區域中，請核取針對 EXTERNALLY-LICENSED (BYOL) SKU 可用性] 區段下的 SKU 的核取方塊 （請參閱下面的螢幕擷取畫面）。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)

6. 現在按一下**AUTOPRICE 其他市場以開啟價格在 UNITED 狀態**] 按鈕。

    >[AZURE.NOTE] 做為不同區域，而您所選取按鈕的標籤。 因為我們所選的美國建立這份文件時，因此按鈕標記為 「 自動價格根據價格，在 [美國其他市場 」 在下面的螢幕擷取畫面。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)

7. 隨即會開啟自動價格精靈。 第一頁會顯示為基底市場選取範圍。 讓您] 區段中，移至下一步] 頁面 [ **]-> [」** ] 按鈕上的。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)

8. 選取 [核心] 和 [方案選項會顯示在第 2 頁。 選取您要的計劃和核心，然後按一下 []->] 按鈕。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)

9. 第 3 頁會顯示市場/地區。 按一下以選取所有的區域，或手動核取方塊區域全部切換按鈕。 按一下]->] 按鈕，移至下一個頁面。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)

10. 第 4 頁會顯示匯率。 按一下 [完成] 按鈕，以完成的步驟。 精靈會根據您的選取範圍價格重設。

11. 現在瀏覽至 [價格] 索引標籤，然後按一下 [檢視和變更摘要 」] 按鈕。
「 草稿 」 中的 [檢視版本] 區段，「 產品 」 區段中選取 「 比較與 」 （請參閱下面的螢幕擷取畫面）。 如果您看到沒有價格的差異，就表示價格已還原到生產值成功。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)

12. 進行變更之後，瀏覽至 [發佈] 索引標籤，然後按一下 [] 按鈕**來執行推播**。 取得執行環境中測試您的提議的詳細指示，請參閱此[連結](marketplace-publishing-vm-image-test-in-staging.md)
13. 一旦您已在臨時測試您的提議，瀏覽至 [發佈] 索引標籤的 [發佈入口網站和中，按一下 [**要求核准至推入至生產**重新發佈您的提議，在 Azure Marketplace] 按鈕。

## <a name="9-how-to-revert-billing-model-to-production-values"></a>9。 還原付款的模型，以生產值的方式
我已變更付款列出的 SKU 的模型。 不支援在 Azure Marketplace，因為我想要還原為我的變更為生產值。 如何達到的？

請遵循下列步驟︰

1. 登入的[發佈入口網站](https://publish.windowsazure.com)。
2. 瀏覽至 [**虛擬機器**] 索引標籤，然後選取您的提議。
3. 從 [左側側邊] 功能表中，按一下**SKU** ] 索引標籤。
4. 按一下 [編輯] 按鈕，以還原付款的模型]。 在視窗隨即會開啟。 核取或取消核取**[帳單與授權完成外部從 Azure （又稱將您自己授權）]**核取方塊會相應地。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)

5. 一次完成，請參閱這份文件，以還原價格的 8 問題的答案。
6. 瀏覽至 [**發佈**] 索引標籤的 [發佈之後入口網站及推入提供的優惠方案臨時加以測試。 一次您完成測試優惠，，然後按一下 [] 按鈕重新發佈您在 Azure Marketplace 提供的服務**要求核准至推入至生產**。

## <a name="10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value"></a>10。 若要還原的生產值列出 SKU 的可見度設定的方式

請遵循下列步驟︰

1. 登入的[發佈入口網站](https://publish.windowsazure.com)。
2. 瀏覽至 [**虛擬機器**] 索引標籤，然後選取您的提議。
3. 從 [左側側邊] 功能表中，按一下**SKU** ] 索引標籤。
4. 選取您的 SKU，並還原為生產值的 SKU 的可見度設定。

    ![繪圖](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)

5. 一次您完成變更，，然後按一下 [] 按鈕重新發佈您在 Azure Marketplace 提供的服務**要求核准至推入至生產**。

## <a name="see-also"></a>另請參閱
- [快速入門︰ 如何將提供發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)
- [了解銷售者獲得深入見解報告](marketplace-publishing-report-seller-insights.md)
- [了解支付額報告](marketplace-publishing-report-payout.md)
- [如何變更您的雲端解決方案提供者的轉銷商動機](marketplace-publishing-csp-incentive.md)
- [服務商場的發佈常見問題的疑難排解](marketplace-publishing-support-common-issues.md)
- [為發行者取得支援](marketplace-publishing-get-publisher-support.md)
- [建立 VM 圖像內部部署](marketplace-publishing-vm-image-creation-on-premise.md)
- [建立虛擬機器執行 Windows Azure 預覽入口網站中](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
