<properties title="" pageTitle="Azure 技術文件的檔案名稱與位置" description="說明文章和命名慣例，當您建立新的文章時，應該先按照檔案結構。" metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="required" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="03/14/2016" ms.author="tysonn" />

#<a name="file-names-and-locations-for-azure-technical-articles"></a>Azure 技術文件的檔案名稱與位置

在我們技術內容存放庫中，我們會使用單一資料夾 （**文件**資料夾） 的所有文件。 沒有資料夾階層-live 一般檔案結構中的所有文件。 如果您建立資料夾中的文章時，無法發佈您的文章。

而不是使用的檔案結構做為組織的原則，我們要使用的清楚識別主題，並根據網頁上的可探索能力的佔嚴格檔案命名慣例。

以下是您需要知道什麼︰

+ [規則]
+ [圖樣]
+ [標準的範例]
+ [服務商場內容]
+ [檔案名稱核准]

##<a name="rules"></a>規則

- 檔案名稱可以包含僅小寫字母、 數字和連字號。 
- 沒有空格或標點符號字元。 使用連字號分隔文字和檔案名稱中的數字。
- 不超過 80 字元-這是發佈的系統限制
- 使用巨集指令動詞特定，例如開發、 購買、 建立、 進行疑難排解。 無-ing 文字。
- 不含任何小型字-，，、 in、 或，等。
- 所有檔案必須是 markdown，並使用.md 的副檔名。
- 拼出; 文字避免在檔案名稱中的未經或不必要的縮寫

縮寫與 initialisms 在檔案名稱-特定的指導方針︰

- 請勿縮寫 Azure 服務名稱-第一個字的檔案名稱應為標準，拼出 Azure 服務或技術的名稱。 
-   不允許 rm 或手臂作為檔案名稱中的任何位置的縮寫
- 其他業界標準縮寫是可接受視檔案名稱。 

##<a name="pattern"></a>圖樣

以下是一般模式︰

 **service-platform-language-content-product-version.md**

使用模式的組件的套用，並檢閱，了解現有名稱的存放庫中的文件的清單。 不開始開發平台的名稱或服務名稱為可能可疑和 [透過進度落後。

##<a name="standard-examples"></a>標準的範例

以下是一些有效遵循模式的名稱的範例。 :

- 雲端式服務-dotnet-連續-delivery.md
- mobile-服務-ios-取得-started.md
- documentdb 管理 account.md
- mobile-services-dotnet-backend-get-started-settings-sync.md
- active-directory-java-authenticate-users-access-control-eclipse.md
- virtual-machines-install-windows-server-2008r2.md
- 快取-aspnet-工作階段-狀態-提供者
- azure-sdk-dotnet-release-notes-2-8
- storsimple-disaster-recovery-using-azure-site-recovery

##<a name="marketplace-content"></a>服務商場內容

若要區別解釋合作夥伴捐贈至 Azure marketplace 的內容，開始使用 「 服務商場 」 的檔案名稱。 此內容不應該是最常見的大部分的合作夥伴內容應該建立夥伴自己的網站上。

- marketplace-mongodb-virtual-machines-install-windows-server-2008r2.md

##<a name="file-name-approval"></a>檔案名稱核准

它是我們的提取要求檢閱者當新檔案的第一次提交到存放庫檔案名稱] 群組的工作。 擷取要求檢閱者應檢閱檔案名稱，並提供意見反應可透過提取要求的註解資料流，如果需要的變更。 檔案名稱必須先接受提取要求修正。 參與者可以輕鬆地將更新推播擱置提取要求。

##<a name="folder-names-in-the-repo"></a>資料夾名稱中 repo

資料夾應該建立僅適用於服務及檔案名稱應符合服務原理和。 只使用字母和連字號，並使用所有的大小寫字母。 建立新的資料夾並非發行服務之前，請從存放庫管理員取得核准。

##<a name="changing-case-in-file-names"></a>變更檔案名稱的大小寫

Windows 作業系統不區分大小寫，因此如果您要變更檔案名稱，若要修正問題的大小寫，請以進行一些變更，除非您能夠進行 Linux 或 mac 上的變更 例如︰

  biztalk-administration-and-Development-Task-List-in-BizTalk-Services--> biztalk-services-administration-and-development-task-list

若要重新命名檔案中使用下列命令︰
```
  git mv <articles/service-folder/current-file-name.md> <articles/service-folder/new-file-name>
```

###<a name="contributors-guide-links"></a>參與者的指南的連結

- [概觀文章](./../README.md)
- [指導文件的索引](./contributor-guide-index.md)


<!--Anchors-->
[規則]: #rules
[圖樣]: #pattern
[標準的範例]: #standard-examples
[服務商場內容]: #marketplace-content
[檔案名稱核准]: #file-name-approval
