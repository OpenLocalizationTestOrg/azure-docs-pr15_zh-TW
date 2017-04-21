<properties
pageTitle="安裝和設定中 GitHub 的撰寫工具"
description="工具和打造撰寫 GitHub Azure 內容設定的步驟。"
services="contributor-guide"
documentationCenter=""
authors="tysonn"  
manager="carolz" />

<tags
ms.service="contributor-guide"
 ms.devlang=""
 ms.topic="article"
  ms.tgt_pltfrm=""
  ms.workload=""
  ms.date="01/19/2015"
  ms.author="tysonn" />

#<a name="install-and-set-up-tools-for-authoring-in-github"></a>安裝和設定中 GitHub 的撰寫工具

請遵循本文設定造成 Azure 技術文件工具中的步驟。 普通和偶爾發生的參與者可能可以使用 GitHub 步驟 2 中所述的使用者介面。

如果您不熟悉給，您可能會想要檢閱的一些給詞彙︰ [https://help.github.com/articles/github-glossary](https://help.github.com/articles/github-glossary)。 此外，此 StackOverflow 執行緒包含詞彙表給會發生這組中的步驟執行︰ [http://stackoverflow.com/questions/7076164/terminology-used-by-git](http://stackoverflow.com/questions/7076164/terminology-used-by-git)

## <a name="contents"></a>內容

- [建立 GitHub 帳戶並設定您的設定檔](#create-a-github-account-and-set-up-your-profile)
- [註冊 Disqus](#sign-up-for-disqus)
- [決定您是否真的需要執行其餘的步驟](#determine-whether-you-really-need-to-follow-the-rest-of-these-steps)
- [GitHub 中的權限](#permissions-in-github)
- [安裝 Windows 版的給](#install-git-for-windows)
- [啟用雙因素驗證](#enable-two-factor-authentication)
- [安裝 markdown 編輯器](#install-a-markdown-editor)
- [設定 Atom](#configure-atom)
- [分叉存放庫，並將它複製到您的電腦](#fork-the-repository-and-copy-it-to-your-computer)
- [設定您的使用者名稱和電子郵件至本機](#configure-your-user-name-and-email-locally)
- [後續步驟](#next-steps)

## <a name="create-a-github-account-and-set-up-your-profile"></a>建立 GitHub 帳戶並設定您的設定檔

若要參與 Azure 技術的內容，您必須[GitHub](http://www.github.com)帳戶。

如果您是 Microsoft 參與者，必須先設定 GitHub 帳戶，讓您清楚識別為 Microsoft 員工。 設定您的設定檔，如下所示︰

- **設定檔圖片**︰ 您 （必填） 的圖片
- **名稱**︰ 您的第一個和最後一個名稱 （必要）
- **電子郵件**: （選擇性） 您 Microsoft 電子郵件地址
- **公司**︰ Microsoft Corporation （必要）
- **位置**︰ 清單 （選用） 您所在位置

您的設定檔應該類似這個設定檔︰

<p align="center">
 ![GitHub 設定檔範例](./media/tools-and-setup/githubprofile.png)

## <a name="sign-up-for-disqus"></a>註冊 Disqus

每個已發佈的 Azure 技術文件有 Disqus 服務所提供的註解資料流。

 ![繼續討論標誌](./media/tools-and-setup/discus.png)

如果您是 Microsoft 員工，而且您是作者或參與者的文章，您需要註冊 Disqus，讓您可以參與文件的註解資料流。

1. 註冊[http://www.disqus.com/](http://www.disqus.com/)的帳戶
2. 填滿] 查看您的設定檔，如下所示︰

 - **完整名稱**︰ 您的完整名稱與顯示在您 Microsoft 地址通訊錄清單中，加上括號的資訊，也就是您的別名中加上@MSFT。 格式︰*第一次最後[alias@MSFT]*
 - **位置**︰ 您的位置
 - **簡短的自我介紹**︰ 您的標題

## <a name="determine-whether-you-really-need-to-follow-the-rest-of-these-steps"></a>決定您是否真的需要執行其餘的步驟

您可能不需要遵循本文中的所有步驟。 這取決於您想要或需要進行的內容比重的排序。

###<a name="submit-a-text-only-change-to-an-existing-article"></a>送出純文字變更現有的文件

如果您只需要或想要對現有的文件中的文字更新，您可能不需要遵循其餘的步驟。 您可以使用 GitHub 的 web 式 markdown 編輯器提交您的變更。 只要按一下您想要修改的文件中的 [GitHub] 連結︰

 ![GitHub 設定檔範例](./media/tools-and-setup/contributetogit.png)

 然後按一下 [在 GitHub 版本的文件中的 [編輯] 圖示

 ![GitHub 設定檔範例](./media/tools-and-setup/editicon.PNG)

 如此會開啟輕鬆地使用 web 編輯器，可讓您更容易送出的變更。 您不需要遵循本文中的其他步驟。

###<a name="all-other-changes"></a>所有其他修訂
GitHub UI 支援建立新的檔案並將拖放圖像。 不過，當您使用的使用者介面，管理分支是令人混淆，我們通常建議您安裝工具，並瞭解建立和管理文章的命令。 如果您想要使用的使用者介面，請參閱︰

- [建立 Github 上的檔案](https://github.com/blog/1327-creating-files-on-github)
- [上傳檔案到您存放庫](https://github.com/blog/2105-upload-files-to-your-repositories)

為工作下列排序，我們強烈建議您安裝，並瞭解如何使用的工具︰

 - 對一篇文章的主要變更
 - 建立及發佈新的文章
 - 新增新的圖像或更新的圖像
 - 更新文章內的天數，而不進行發佈變更每個的天數
 - 建立具有若要移在一段時間的特定日子版本的內容

##<a name="permissions-in-github"></a>GitHub 中的權限

任何人 GitHub 帳戶即可參與在[https://github.com/Azure/azure-content](https://github.com/Azure/azure-content)我們公用存放庫透過 Azure 技術的內容。 沒有特殊的權限所需。

如果您是 Microsoft PM 或文件撰寫人員正在處理 Azure 內容，您必須在我們的私人工作內容存放庫，azure-內容-pr。 請造訪[https://repos.opensource.microsoft.com](https://repos.opensource.microsoft.com )要求讀取權限可讓您進行到私人的 repo-捐贈登入 GitHub 使用] 按鈕 > 按一下 Azure > 按一下 [**加入小組**] 或 [**加入另一個小組**，然後在搜尋和加入**azure 內容閱讀**群組。

## <a name="install-git-for-windows"></a>安裝 Windows 版的給

安裝 Windows 版的給從[http://git-scm.com/download/win](http://git-scm.com/download/win)。 下載安裝給版本控制系統，並將其安裝給艦隊，您會使用您的本機給存放庫使用互動的命令列應用程式。

您可以接受預設的設定。如果您想要使用 Windows 命令列中的命令，請選取 [啟用時的選項。

<p align="center">
 ![GitHub 設定檔範例](./media/tools-and-setup/gitbashinstall.png)

(請注意︰ 這不是 「 Windows Github 」 相同。 「 在 Windows 版的 Github 」 是不同的 gui 工具，如果您想要閱讀您自己，也就能使用。 [https://windows.github.com/](https://windows.github.com/)中)

## <a name="enable-two-factor-authentication"></a>啟用雙因素驗證

您必須啟用兩個因素驗證 (2FA) GitHub 帳戶如果您使用的私人內容存放庫。 需要將私人存放庫內。

若要啟用此問題，請依照下列兩個下列 GitHub 說明主題中的指示︰

- [關於雙因素驗證](https://help.github.com/articles/about-two-factor-authentication/)
- [建立命令列使用的存取權杖](https://help.github.com/articles/creating-an-access-token-for-command-line-use/)

當您建立的權杖時，選取所有可用的權杖建立使用者介面 （[每個範圍的詳細資料](https://developer.github.com/v3/oauth/#scopes)） 的範圍

啟用 2FA 之後，您必須輸入，而不是在命令提示字元 GitHub 密碼的存取權杖，當您嘗試從命令列存取 GitHub 存放庫。 存取權杖無法設定 2FA 時的簡訊收到的驗證碼。 很長字串看起來像這樣︰ fdd3b7d3d4f0d2bb2cd3d58dba54bd6bafcd8dee。 瞭解這幾個附註︰

- 當您建立您存取權杖時，請將其儲存，使其可無障礙在需要時文字檔案。

- 更新版本中，當您要貼上權杖，知道有兩種方式命令列中貼上︰

 - 按一下 [命令] 視窗的左上角的圖示 > 編輯 > 貼。
 - 以滑鼠右鍵按一下中視窗的左上角的圖示，然後按一下 [內容 > 選項] > [快速編輯模式。 讓您可以貼上以滑鼠右鍵按一下 [命令] 視窗中，這就會設定命令列。

## <a name="install-a-markdown-editor"></a>安裝 markdown 編輯器

我們作者在檔案中，使用簡單的 「 markdown 」 標記法，而內容比複數 「 加價率 」 （HTML、 XML）。 因此，您需要安裝 markdown 編輯器。

- **Atom**︰ 大多數的人使用 GitHub 的 Atom markdown 編輯器︰ [http://atom.io](http://atom.io)。 不需要授權的商務使用。 有拼字檢查。

- **記事本**︰ 您可以使用記事本非常輕量型的選項。

- **Prose**︰ 這是提供預覽與輕量型簡潔、 線上，然後開啟來源 markdown 編輯器。 請造訪[http://prose.io](http://prose.io) ，並授權 Prose 在您儲存機制。

- **[Visual Studio 程式碼](https://www.visualstudio.com/products/code-vs.aspx)**-此空間中的 Microsoft 的項目。

## <a name="configure-atom"></a>設定 Atom

如果您使用 Atom 時，您會需要設定幾個項目。

- Atom 預設的索引標籤上，使用 2 的空格，但 Markdown 預期 4 個空格。 如果您保留它的兩個預設值，您的文章會呈現精美外觀在本機預覽中，但不是時將其匯入至 Azure。 因此，設定使用 4 個空格 Atom-您可以找到這項設定檔案在 > 設定 > 編輯器設定 >] 索引標籤的長度。
- 您也想開啟柔此區段中自動換行，其會 」 文字換行]，在 「 記事本 」 相同。
- 若要開啟 markdown 預覽，請按一下 [封包 > Markdown 預覽 > 切換預覽。 您可以使用 Ctrl Shift M 切換預覽 HTML 檢視。

## <a name="fork-the-repository-and-copy-it-to-your-computer"></a>分叉存放庫，並將它複製到您的電腦

1. 建立 GitHub 存放庫分叉-移至右上方的頁面並按一下 [分叉] 按鈕。 如果出現提示，請選取您的帳戶為應該建立分叉的位置。 這樣會建立存放庫，您就可以給中樞帳戶內的複本。 一般而言，技術的作者和程式管理員必須分叉 azure-內容-pr，私人 repo。 分叉 azure 內容，公用 repo 需要社群參與者。 您只需要分叉一次。第一份安裝之後, 如果您想要將您分叉複製到另一部電腦，您只需要執行的命令，請依本節 repo 複製到您的電腦。  如果您選擇建立分支的兩個存放庫，您必須建立的每個存放庫分叉。

2. 複製個人存取權杖，您可以向[https://github.com/settings/tokens](https://github.com/settings/tokens)。 您可以接受預設的權限的 token。  將儲存個人的存取權杖，供日後重複使用文字檔案。

3. 接下來，複製到您的電腦，使用您的認證存放庫內嵌命令字串中。  若要這麼做，請開啟給艦隊並以系統管理員身分執行。 在命令提示字元中，輸入下列命令。  這個命令會在您的電腦上建立 azure-content(-pr) 目錄。  如果您使用的預設位置，就會在 c:\users<your Windows user name>\azure-content(-pr)。

公用 repo:

        git clone https://[your GitHub user name]:[token]@github.com/<your GitHub user name>/azure-content.git

私人 repo:

        git clone https://[your GitHub user name]:[token]@github.com/<your GitHub user name>/azure-content-pr.git

例如，此複製命令，可能看起來像這樣︰

        git clone https://smithj:b428654321d613773d423ef2f173ddf4a312345@github.com/smithj/azure-content-pr.git  

## <a name="set-remote-repository-connection-and-configure-credentials"></a>設定遠端存放庫連線並設定認證

輸入下列命令以建立根存放庫的參照。 這會設定連線到儲存機制 GitHub 中，讓您可以取得最新變更至您的本機電腦，並變更推 GitHub。 這個命令也設定您的權杖本機讓您不必在每次嘗試存取上游 repo 和您分叉 GitHub 上的輸入您的名稱和密碼。

公用 repo:

        cd azure-content
        git remote add upstream https://[your GitHub user name]:[token]@github.com/Azure/azure-content.git
        git fetch upstream

私人 repo:

        cd azure-content-pr
        git remote add upstream https://[your GitHub user name]:[token]@github.com/Azure/azure-content-pr.git
        git fetch upstream

這通常需要一段時間。 完成之後，您不需要再次分叉或再次輸入您的認證。 您只需要分支本機電腦上重新複製如果您在另一部電腦上設定的工具。


## <a name="configure-your-user-name-and-email-locally"></a>設定您的使用者名稱和電子郵件至本機

若要確保您能以參與者列出，必須先設定給本機您的使用者名稱和電子郵件。

1. 開始給艦隊並切換到 azure 內容或 pr-azure-內容︰

   ````
   cd azure-content
   ````

 或

   ````
   cd azure-content-pr
   ````

2. 設定您的使用者名稱，使其符合您的名稱，當您將其設定 GitHub 設定檔中︰

    ````
    git config --global user.name "John Doe"
    ````
3. 設定您的電子郵件，使其符合 GitHub 設定檔; 中指定的主要電子郵件如果您是 MSFT 員工，它應該 MSFT 的電子郵件地址︰

    ````
    git config --global user.email "alias@example.com"
    ````
4. 輸入`git config -l`檢閱您本機的設定，以確保使用者名稱和電子郵件中設定正確無誤。

##<a name="next-steps"></a>後續步驟

- 瞭解技術內容 repo，在所屬的內容類型，並知道什麼不屬於。 [內容的頻道指引](./content-channel-guidance.md)，請參閱 ！
- 請遵循[下列步驟，建立或修改文件並提交發佈](./git-commands-for-master.md)。
- 複製[markdown 範本](../markdown templates/markdown-template-for-new-articles.md)為基礎的新的文章。
- 使用[這份檢查清單，以驗證您的提取要求將符合品質準則](./contributor-guide-pr-criteria.md)合併。


###<a name="contributors-guide-navigation"></a>參與者的指南導覽

- [概觀文章](./../README.md)
- [指導文件的索引](./contributor-guide-index.md)



<!--Anchors-->
[Use a customer-friendly voice]: #use-a-customer-friendly-voice
[Consider localization and machine translation]: #consider-localization-and-machine-translation
[other style and voice issues to watch for]: #other-style-and-voice-issues-to-watch-for


[Create a GitHub account and set up your profile]: #create-a-github-account-and-set-up-your-profile
[Determine whether you really need to follow the rest of these steps]: #determine-whether-you-really-need-to-follow-the-rest-of-these-steps
[Permissions in GitHub]: #permissions-in-github
[Install Git for Windows]: #install-git-for-windows
[Enable two-factor authentication]: #enable-two-factor-authentication
[Install a markdown editor]: #install-a-markdown-editor
[Fork the repository and copy it to your computer]: #fork-the-repository-and-copy-it-to-your-computer
[Install git-credential-winstore]: #install-git-credential-winstore
[Sign up for Disqus]: #sign-up-for-disqus
[Configure your user name and email locally]: #configure-your-user-name-and-email-locally
[Next steps]: #next-steps
