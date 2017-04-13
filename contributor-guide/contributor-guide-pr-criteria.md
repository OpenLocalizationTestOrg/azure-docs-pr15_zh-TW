# <a name="quality-criteria-for-pull-request-review"></a>品質準則提取要求檢閱

這些準則是建立與維護技術文件的作者，以及擷取要求檢閱者檢閱內容提取要求。 如果您提取要求不符合[自動合併](contributor-guide-pull-request-etiquette.md#in-a-hurry-submit-prs-that-can-be-accepted-automatically)，它將人力提取要求檢閱者檢閱。 擷取要求檢閱者通常會檢閱只會新增或變更。 擷取要求檢閱者評估根據封鎖和非封鎖品質檢閱項目本文中所列的提取要求的變更。

## <a name="blocking-content-quality-items"></a>封鎖內容的品質項目

在邀請中擷取更新必須符合下列準則，以合併。 擷取要求檢閱者中提供意見反應提取要求註解的項目及類型`#hold-off`中擷取傳回的要求，您 （作者） 的意見反應。

| 類別 | 品質檢閱項目 |
|----------|---------------------|
|必要條件| 「 準備要-合併 」 和 「 驗證成功 」 標籤指派給 PR.|
|必要條件| 合併衝突的使用者無法會封鎖提取要求。|
|Repo 完整性|    擷取要求包含無明顯的內容回復。|
|Repo 完整性|    內嵌的 repo 或任何異常、 沒有直接關聯的檔案不包含提取要求。|
|Repo 完整性 |提取要求包含少於 100 位已變更的檔案，除非 PR 刻意更新發行分支從母片。 （真的 PR 應該包含最少，但之後已變更的 100 個檔案，GitHub 不會顯示差異）。|
|命名 |新檔案的檔案名稱中，請依照下列[檔案命名方針](file-names-and-locations.md)。|
|命名 |新的資料夾會導入 repo 追蹤[資料夾的命名指引](file-names-and-locations.md#folder-names-in-the-repo)。|
|內容    |文件是技術文件，因此中正確的內容頻道。 請參閱[在哪個位置指引](content-channel-guidance.md)。|
|內容    |技術文件中的主旨適合技術文件。 請參閱[在哪個位置指引](content-channel-guidance.md)。|
|內容    |本文包含一段介紹和程序或概念性本文的內容。 文件必須包含足夠，完成的內容，以強調本身做文章。 不應小型片段的資訊。 (例外狀況︰ 如果列出所有的服務限制的大型文件的內容中的 「 限制 」 主題。)|
|內容| 頁碼的編號應該編號的清單的項目，應該未排序清單的項目項目符號。 這是基本的可用性。|
|內容| 異常或嶄新的圖形、 資訊架構或結構或明顯非標準設計需要與潛在客戶 PR 檢閱者會驗證。 小組會嘗試使用新的項目所需要的問題/方案畫布 」 或 「 計劃在評估實驗的位置。|
|網站設計的功能| Switchers 僅適用於使用相同的文件的多個版本之間切換。|
|網站設計的功能| Switchered 文章標題包含與不同之處每篇文章 switchered 設定的其他文章的資訊。|
|網站設計的功能| 不允許手動撰寫目錄。 文件必須依賴 H2s 其頁面上的目錄。|
|網站設計的功能| 如果 H2 標題，本文會包含至少兩個 H2 標題。 使用一個 H2 標題所建立的單一項目文章目錄。 H2 標題必須使用前附近的 H3 標題，以確保建立目錄。|
|Markdown| HTML︰ 來源的內容並不會包含在區塊層級 – 次要允許 HTML – 例如上標、 下標、 特殊字元和您無法使用 markdown 做其他次要項目的內嵌 HTML。 只有當資料表包含項目符號或編號清單中，但這通常是內容需要可在 markdown 撰寫來源簡化的指示允許 HTML 表格。|
|Markdown   |在適當處，則會使用自訂 markdown 項目。 例如︰ 備忘稿使用 AZURE 編碼。請注意分機]，不會以純文字。|
|SEO    |「 & #124;Microsoft Azure 」 網站識別碼資格|
|SEO    |H1 標題包含足夠的資訊來說明文章:，區別其他 Azure 的文件，與對應到可能客戶關鍵字的內容。 例如 H1 標題為 「 概觀 」 會失敗。|
|詞彙| 使用 ARM 縮寫、 V1、 或 V2 為傳統和資源管理員部署模型的參照是封鎖的術語項目。|
|圖像 |動畫的 Gif 不接受到 repo。|
|圖像 | 圖像清除解析度、 免費的拼錯的單字，並不包含私人資訊 | 
|執行|文件預覽必須 clean 上執行。 它不能包含任何明顯的格式設定問題︰ <br><br>的顯示為段落編號或項目符號清單 <br> 程式碼會出現部分中的程式碼區塊只能以外的程式碼區塊中 <br>-編號不正確，因為有錯誤的縮排編號的步驟|

## <a name="non-blocking-content-quality-items"></a>非封鎖內容品質項目

這些項目，擷取要求檢閱者會提供意見反應及作者進行修正更新提取邀請中的指示進行。 不過，此意見反應並不會封鎖決定来合併。 作者應該追蹤與修正的 3 個工作天內。

| 類別 | 品質檢閱項目 |
|----------|---------------------|
|內容|文件結尾處 1-3 相關且令人讚嘆下一個步驟應有 」 下一步]。 簡短文字應該包括在內，有助於了解為何相關的下一個步驟的客戶。 （新的文件僅限）。 範例︰ <https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-install/><br>![](media/contributor-guide-pr-criteria/nextstepsexample.PNG)|
|內容|拼字檢查、 文法檢查]，以及其他寫作問題-提取要求檢閱者可能會提供意見反應一些次要問題為非封鎖的意見反應。 如果有多個編輯問題，請檢閱者登入後出版物編輯本文的編輯要求。|
|圖像|圖像使用正確的圖說文字樣式和色彩]，並螢幕擷取畫面使用正確的框線和版面配置區樣式。 [請參閱圖像指南](https://github.com/Azure/azure-content/blob/master/contributor-guide/create-images-markdown.md)。|
|圖像|圖像中加入替代文字。 [請參閱圖像指南](https://github.com/Azure/azure-content/blob/master/contributor-guide/create-images-markdown.md)。|
|網站設計的功能|H2 標題，在頁面上的目錄中，呈現時應該理想換列不超過 2 的線條。 長標題進行難掃描目錄的文件。|
|樣式慣例|所有標題和標題都是句首大寫、 每 Azure 樣式。|
|程序|如果提取要求可能有輕鬆地重新設定可受益 PRmerger 自動化，擷取要求檢閱者會提供意見反應，瞭解如何使用分支，因此無法自動合併變更撰寫。 請參閱[PR 禮儀文章](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-pull-request-etiquette.md#in-a-hurry-submit-prs-that-can-be-accepted-automatically)。|
|程序|當您刪除或重新命名文件時，請確定您依照此程序。 提取要求檢閱者應註解中加入下列註解和連結︰<br><br>*請確認 [刪除文件的照參與者的指南中的程序︰ <https://github.com/Azure/azure-content/blob/master/contributor-guide/retire-or-rename-an-article.md> 。*|

## <a name="related"></a>相關

- [擷取要求禮儀及 Microsoft 參與者的最佳作法](contributor-guide-pull-request-etiquette.md)

- [擷取要求註解自動化](contributor-guide-pull-request-comments.md)
