<properties pageTitle="建立新的文章，或更新現有的文件給命令" description="使用技術 Azure 步驟內容 GitHub 存放庫]。" metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="carolz" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="01/16/2015" ms.author="tysonn" />

# <a name="git-commands-for-creating-a-new-article-or-updating-an-existing-article"></a>建立新的文章，或更新現有的文件給命令


## <a name="standard-process-working-from-master"></a>標準程序 （從母片）
請遵循本文，在您的電腦上建立本機工作分支，讓您可以建立的 azure.microsoft.com 技術文件] 區段的新的文章，或更新現有的文件中的步驟進行。

1. 啟動給艦隊 （或給您使用的命令列工具）。

 **附註︰**如果您使用公用存放庫，請變更 azure-內容-pr azure-內容的所有命令。

2. 變更 pr-azure-內容︰

        cd azure-content-pr
3. 請查看主版分支︰

        git checkout master

4. 建立新本機工作分支衍生自主分支︰

        git pull upstream master:<working branch>


5. 移動到新的工作分支︰

        git checkout <working branch>

6. 讓您知道您建立本機工作分支的分叉︰

        git push origin <working branch>

7. 建立您的新文章，或變更現有的文件。 使用 Windows 檔案總管開啟並建立新的 markdown 檔案，並使用 Atom (http://atom.io) 作為 markdown 編輯器。 您建立或修改您的文章和圖像之後，請移至下一個步驟。

8. 新增並確認您所做的變更︰

        git add .
        git commit –m "<comment>"
        
   或者，您也可以新增只特定檔案修改您︰

        git add <file path>
        git commit –m "<comment>"

   如果您刪除檔案時，您必須使用這個︰
   
        git add --all
        git commit -m "<comment>"

9. 更新您的本機工作分支來自上游的變更︰

        git pull upstream master

10. 變更推入您分叉，GitHub 上︰

        git push origin <working branch>

12. 當您準備好提交您的內容，臨時，驗證]，及/或發佈，上游主分支 GitHub ui 上建立提取要求您分叉從主分支。

13. 如果您是員工，將私人存放庫內使用分段自動安裝您提交的變更，並提取要求寫入暫存的連結。 請檢閱分段的內容，並登入提取要求註解加**#sign 關閉**註解。  這表示必須推入即時準備所做的變更。  如果您不想要接受-提取要求，如果您只臨時變更-加入提取要求**#hold 關閉**記事。

14. 擷取邀請接受檢閱您提取要求、 提供意見反應，及/或接受您擷取的要求。 

15. 您也可以驗證您的已發佈的文章或變更

 http://azure.microsoft.com/documentation/articles/*name-of-your-article-without-the-MD-extension*

## <a name="publishing"></a>發佈

- 文件發佈大約 10:00 AM，3:00 PM 太平洋時間星期一至星期五。 它可能需要 30 分鐘，以便在發行後顯示為線上的文件。 請記住，您提取要求之前所做的變更可以包含在執行下一個排程發佈提取要求檢閱者合併。 您需要使用提取要求檢閱者提前以確保提取要求特定的發佈執行的合併。 否則，請檢閱 」 PRs 他們已提交的順序。

- 如果您是在私人存放庫中工作的員工，所有提取要求都是需要解決可以合併提取要求之前的驗證規則。 

## <a name="working-with-release-branches"></a>使用版本分支

當您正在使用發行分支時，從發行分支建立本機工作分支的最佳方式就是使用此命令語法︰

    git checkout upstream/<upstream branch name> -b <local working branch name>

這會建立本機分支直接從上游分支，避免任何本機合併。

