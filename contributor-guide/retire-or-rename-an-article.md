# <a name="steps-to-follow-when-you-retire-or-change-the-name-of-an-acom-technical-article"></a>當您停用或變更 ACOM 技術文件的名稱，請依照下列步驟

本指南為 Sme 列為必須從技術文件的區段 azure.microsoft.com 已停用的文件的作者。 步驟也適用於重新命名檔案。

如果您是我們 Azure 社群的成員，而且您認為文章應該已停用基於任何原因，請將註解 Disqus 註解資料流，讓知道有問題，請參閱撰寫文件中。

SME 作者需要遵循幾個步驟，讓網站使用者的無需不正確的體驗，我們淘汰網站的內容時，正常淘汰內容。 刪除文件，或變更其名稱應會發生的最後一個項目 ！

## <a name="step-1-set-the-article-to-no-indexno-follow-and-republish-it-recommended"></a>步驟 1︰ 設定為 [無索引/無追蹤的文件並重新發佈 （建議使用）

首先，您應該執行的重新，請參閱發佈為無索引/無追蹤您在刪除之前的幾週。 這會被視為的最佳作法 」 測試工作 」 的淘汰內容。 執行此動作會移除，請參閱搜尋引擎索引，讓使用者不會找到，請參閱在搜尋中。 [請參閱內部 wiki 的詳細資料。](https://microsoft.sharepoint.com/teams/azurecontentguidance/wiki/Pages/Remove%20published%20pages%20and%20request%20redirects.aspx)

## <a name="step-2-manage-inbound-links-required"></a>步驟 2︰ 管理輸入的連結 （必要）

判斷是否有任何非 Microsoft 的連入的連結至您的內容。 通常，部落格、 論壇和其他網路上的內容會指向文章。 通常，您可以使用部落格擁有者，若要變更這些連結，，您可以移除或更新來自論壇文章的連結。 Web 分析工具會告訴您是否有任何您可能需要這種方式管理高流量輸入的連結。

## <a name="step-3-remove-all-crosslinks-to-the-article-from-the-technical-content-repository-required"></a>步驟 3︰ 移除所有交互連結文章技術內容存放庫 （必要）

不要依賴重新導向至負責交互從其他文件的連結。 更新或移除交互參照至您要刪除或重新命名的文件，包括文件中所擁有其他人。

1. 請確定您使用最新的本機分支 – 執行`git pull upstream master`（或在此命令適當的變化。

2.  掃描 azure 內容 pr/文章資料夾和 azure-內容-pr/包括資料夾的文件和包含該連結至您想要停用的文件，選擇移除交互連結或以適當的新交互連結取代它們。 您可以使用搜尋及取代公用程式，來尋找交互連結，如果您有安裝。 如果沒有的話，可以免費使用 Windows PowerShell ！ 以下說明如何使用 PowerShell 來尋找交互連結︰

 。 啟動 Windows PowerShell。

 b。 在 PowerShell 提示中變更到 azure-內容-pr\articles 資料夾︰

 `cd azure-content-pr\articles`

 c。 輸入下列命令，就會列出檔案中包含您要刪除的文件的參照︰

 `Get-ChildItem -Recurse -Include *.md* | Select-String "<the name of the topic you are deleting>" | group path | select name`

  如果您想要傳送的檔案名稱清單至文字檔案 （在此案例、 命名的 psoutput.txt)，您可以︰

  `Get-ChildItem -Recurse -Include *.md* | Select-String "<the name of the topic you are deleting>" | group path | select name | Out-File C:\Users\<your account>\psoutput.txt`

3. 新增接受所有變更，傳送至您的分叉，並建立您的變更將您分叉移到主存放庫的主版分支的提取要求。

## <a name="step-4-update-the-fwlink-tool-required"></a>步驟 4︰ 更新 Fw 工具 （必要）

檢查文件可能會指向任何 FWLinks Fw 工具。 任何 FWLinks 指向取代項目的內容。如果您不是擁有連結的別名，請將它加入。 如果擁有者不會更新連結，檔案以變更連結的 MSCOM 票證。 其他資訊-[內部 wiki](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Manage%20inbound%20links%20to%20retired%20topics.aspx)。

## <a name="step-5-remove-all-crosslinks-to-the-article-from-other-pages-on-azuremicrosoftcom-and-create-a-redirect-for-the-retired-page-if-appropriate-required"></a>步驟 5︰ 文件中移除 azure.microsoft.com 上的其他頁面的所有交互連結，並在建立重新導向] 已停用的頁面上，如果適當 （必要）

您必須使用會維護及更新您的服務，這組件的文件登陸頁面的人員。 如果您不知道該人員是誰，請連絡您內容的小組的合作夥伴。 維護並更新文件登陸頁面的人員將需要執行兩個動作︰

1. 在 Visual Studio 中，瀏覽**整個**ACOM 網頁解決方案的交互參照到要淘汰檔案。 移除交互參照，或以更新的交互參照取代它們。 您需要移除 HTML 連結，以及 HTML 連結的相關的資源字串。 -的詳細資訊請參閱[內部 wiki](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Create%20or%20edit%20a%20service%20landing%20page%20or%20left%20nav.aspx)

2. 如果取代文章，建立重新導向。 -的詳細資訊請參閱[內部 wiki](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Remove%20published%20pages%20and%20request%20redirects.aspx)。

3. 核取到儲存機制所做的變更。

## <a name="step-6-retire-the-article"></a>步驟 6︰ 淘汰，請參閱

您已完成的先前的步驟，並會即時這些變更之後，您可以刪除，請參閱從存放庫。 

**重要︰**當您刪除檔案時，您必須使用`git add --all`] 命令。

## <a name="step-7-remove-links-from-msdn-required"></a>步驟 7︰ 從 MSDN （必填） 移除連結

檢閱內容問與答工具的 [中斷連結的已停用或重新命名的主題，並移除/修正連結中所有受影響的 MSDN 主題。

## <a name="step-8-remove-cached-pages-from-search-engines-only-if-absolutely-necessary"></a>步驟 8︰ 將快取的頁面移除搜尋引擎 （僅有必要）

如果要快速移除由於法律或嚴重客戶問題，因此需要的內容，請執行此僅。 從 Google 的最佳作法，每一般優先順序頁面刪除應只是由處理自然搜尋引擎處理程序。 移至這些網頁，若要移除搜尋引擎中的快取的網頁︰

[Bing](https://www.bing.com/webmaster/tools/content-removal?rflid=1)
[Google](https://www.google.com/webmasters/tools/removals?pli=1)


### <a name="contributors-guide-links"></a>參與者的指南的連結

- [概觀文章](./../README.md)
- [指導文件的索引](./contributor-guide-index.md)
