# <a name="azure-technical-documentation-contributor-guide"></a>Azure 技術文件參與者指南

您已經找到 GitHub 存放庫來存放您已發佈至 Azure 文件中心在[http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation)技術文件的來源。

此存放庫也包含指導方針可協助您參與我們技術文件。  參與者的指南中的文件的清單，請參閱[索引](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md)。

## <a name="contribute-to-azure-documentation"></a>參與 Azure 文件

Azure 文件中您感興趣的感謝您 ！

* [若要提供的方式](#ways-to-contribute)
* [進行的程式碼](#code-of-conduct)
* [關於您捐贈 Azure 內容](#about-your-contributions-to-azure-content)
* [存放庫組織](#repository-organization)
* [使用 GitHub、 給，與此存放庫](#use-github-git-and-this-repository)
* [如何使用 markdown 格式化您的主題](#how-to-use-markdown-to-format-your-topic)
* [意見反應、 註解，以及支援](./contributor-guide/feedback-and-comments.md)
* [更多資源](#more-resources)
* [所有參與者指南文章的索引](./contributor-guide/contributor-guide-index.md)（隨即會開啟新的頁面）

## <a name="ways-to-contribute"></a>若要提供的方式 

您可以參與[Azure](http://azure.microsoft.com/documentation/)文件，以數種不同的方法︰

* 參與[討論論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home)。
* 送出 Disqus 底部的 [文件的註解。
* 您可以輕鬆地參與技術 GitHub 使用者介面中的文件。 尋找，請參閱此存放庫內，或造訪[http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) ，請參閱，並按一下 [移至本文的 GitHub 來源文件中的連結。
* 如果您要製作大幅變更現有的文章，新增或變更圖像或參與的新的文章，您需要分叉此存放庫、 安裝給艦隊，Markdown 鍵台，並瞭解一些給命令。

##<a name="code-of-conduct"></a>進行的程式碼

此專案已採用[Microsoft 開啟來源辦法](https://opensource.microsoft.com/codeofconduct/)。 如需詳細資訊，請參閱的[程式碼的進行常見問題集](https://opensource.microsoft.com/codeofconduct/faq/)或連絡人[opencode@microsoft.com](mailto:opencode@microsoft.com)以任何其他問題或註解。

##<a name="about-your-contributions-to-azure-content"></a>關於您捐贈 Azure 內容

###<a name="minor-corrections"></a>次要校正

[Azure 網站的 [使用規定 (ToU)](http://azure.microsoft.com/support/legal/website-terms-of-use/)所涵蓋次要校正或您的文件和程式碼的範例，此 repo 中送出的說明。


###<a name="larger-submissions"></a>較大的送出 」

如果您提交提取要求與新的或重大變更文件和程式碼範例，我們會要求您提交線上比重授權合約 (CLA)，如果您在其中一個群組 GitHub 傳送註解︰

* Microsoft 開啟技術群組的成員。
* Microsoft 無法運作的參與者。

我們需要您完成線上表單，我們可以接受您提取要求之前。

可在[http://azure.github.io/guidelines/#cla](http://azure.github.io/guidelines/#cla)完整的詳細資料。

## <a name="repository-organization"></a>存放庫組織

Azure 內容存放庫中的內容上[Azure.Microsoft.com](http://azure.microsoft.com)追蹤的文件的組織。 此存放庫包含兩個根資料夾︰

### <a name="articles"></a>\articles

*\Articles*資料夾包含格式化為*.md*副檔名為 markdown 檔案的文件文章。

文章的根目錄的路徑中發佈至 Azure.Microsoft.com *http://azure.microsoft.com/documentation/articles/ {文章-名稱-不-會員} /*。

* **文章的檔名︰**請參閱[我們的檔案命名指引](./contributor-guide/file-names-and-locations.md)。

其服務的資料夾中的文件的路徑中發佈至 Azure.Microsoft.com *http://azure.microsoft.com/documentation/articles/service-folder/ {文章-名稱-不-會員} /*

* **媒體子資料夾︰***\Articles*資料夾包含根目錄文章媒體檔案的*\media*資料夾內會與每篇文章的圖像的子資料夾。  [服務] 資料夾包含每個服務的資料夾中的文件的另一個媒體資料夾。 文件檔案，不包括*.md*檔案副檔名相同名稱文章圖像資料夾。

### <a name="includes"></a>\includes

您可以建立可重複使用內容的章節，以包含一或多個文件中。 請參閱[我們技術的內容中使用自訂副檔名](./contributor-guide/custom-markdown-extensions.md)。

### <a name="markdown-templates"></a>\markdown 範本

這個資料夾包含基本 markdown 格式設定您需要的文章我們標準 markdown 範本。

### <a name="contributor-guide"></a>\contributor-guide

這個資料夾包含屬於我們參與者的指南的文章。  

## <a name="use-github-git-and-this-repository"></a>使用 GitHub、 給，與此存放庫

瞭解如何參與、 如何使用 GitHub UI 參與變更，以及如何分叉和複製更投入的存放庫的資訊，請參閱[安裝和設定中 GitHub 的撰寫工具](./contributor-guide/tools-and-setup.md)。

如果您安裝 GitBash，並選擇本機工作，建立新的本機工作分支、 進行變更，及送出變更回主分支的步驟會列在[給命令來建立新的文章或更新現有的文件](./contributor-guide/git-commands-for-master.md)

### <a name="branches"></a>分支

我們建議您建立本機工作分支的目標特定範圍的變更。 每個分支應該限於單一概念/文章，同時以簡化工作流程，並減少合併衝突。  新的分支的適當範圍有下列工作︰

* 新的文章 （以及相關聯的圖像）
* 在文件的拼字及文法檢查編輯。
* 將單一的格式設定變更套用一組大型文件 （例如新著作權頁尾）。

## <a name="how-to-use-markdown-to-format-your-topic"></a>如何使用 markdown 格式化您的主題

在此存放庫中的所有文件使用 GitHub flavored 的 markdown。  以下是資源的清單。

- [Markdown 基本概念](https://help.github.com/articles/markdown-basics/)

- [可列印的 markdown cheatsheet](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

- Markdown 編輯器我們清單，請參閱的[工具和設定主題](./contributor-guide/tools-and-setup.md#install-a-markdown-editor)。

## <a name="article-metadata"></a>文件中繼資料

文件中繼資料可讓 azure.microsoft.com 網站上的特定功能，例如作者屬性、 參與者屬性、 階層連結、 文章描述和 SEO 最佳化，以及報告 Microsoft 會使用評估之內容的效能。 因此，中繼資料是重要 ！ [以下右完成的指導方針，確保您的中繼資料](./contributor-guide/article-metadata.md)。

### <a name="labels"></a>標籤

自動化的標籤指派給提取要求來協助我們管理提取要求工作流程，並說明可讓您知道原因與提取要求︰

* 相關的比重授權合約
    * 不需要 cla︰ 變更幅度不大，而不需要登入 CLA。
    * cla 必要︰ 變更的範圍很大，以及需要登入 CLA。
    * cla 簽章︰ 參與者登入 CLA，因此提取要求可以立即以供檢閱向前移動。
* 圓柱標籤︰ 例如 PnP 標籤、 現代化的應用程式，與 TDC 協助內部貴組織所要檢閱提取要求分類提取要求。
* 傳送給作者的變更︰ 作者已擱置提取要求的通知。

## <a name="more-resources"></a>更多資源

請參閱[我們的參與者指南 》 的索引](./contributor-guide/contributor-guide-index.md)的所有我們指引主題。
