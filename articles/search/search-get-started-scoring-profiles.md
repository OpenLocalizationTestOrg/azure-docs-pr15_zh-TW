<properties 
    pageTitle="如何使用計分設定檔中 Azure 搜尋 |Microsoft Azure |裝載的雲端搜尋服務" 
    description="調整搜尋排名透過計分 Azure 搜尋，在 Microsoft Azure 裝載的雲端搜尋服務中的設定檔。" 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="mblythe" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

# <a name="how-to-use-scoring-profiles-in-azure-search"></a>Azure 在搜尋中使用計分的設定檔的方式

計分的設定檔是 Microsoft Azure 搜尋的自訂搜尋成績的計算影響項目如何排名搜尋結果清單中的功能。 您可以將計分模型關聯性，以設定檔，以促進符合預先定義的準則的項目。 例如，假設您的應用程式是線上旅館保留網站。 以促進`location`欄位中，搜尋包含字詞等西雅圖會產生更高分數的項目中的西雅圖`location`欄位。 請注意，您可以有多個計分的設定檔] 或 [無，如果預設計分不足，您的應用程式。

若要協助您嘗試使用計分的設定檔，您可以下載範例使用的應用程式評分的設定檔來變更排名搜尋結果的順序。 樣本依然是學習工具是主控台應用程式 – 或許不非常檢驗實際的應用程式開發 – 但有幫助。 

範例應用程式示範使用虛構的資料，稱為計分行為`musicstoreindex`。 簡單範例應用程式可讓您更輕鬆地修改計分的設定檔和查詢，然後在執行程式時，請參閱等級順序排列的即時效果。

<a id="sub-1"></a>
## <a name="prerequisites"></a>必要條件

範例應用程式中會寫入 C# 使用 Visual Studio 2013。 如果您還沒有 Visual Studio 複本，請嘗試免費的[Visual Studio 2013 Express 版](http://www.visualstudio.com/products/visual-studio-express-vs.aspx)。

您必須 Azure 的訂閱和 Azure 搜尋服務完成教學課程。 設定服務的相關說明，請參閱[建立搜尋服務在入口網站](search-create-service-portal.md)。

[AZURE。包含[必須完成本教學課程 Azure 帳戶︰](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## <a name="download-the-sample-application"></a>下載的範例應用程式

若要下載本教學課程中所述的範例應用程式的 codeplex 上，請移至[Azure 搜尋計分設定檔示範](https://azuresearchscoringprofiles.codeplex.com/)。

在 [程式碼] 索引標籤上按一下 [**下載**]，以取得 zip 檔案的解決方案。 

 ![][12]

<a id="sub-3"></a>
## <a name="edit-appconfig"></a>編輯 app.config

1. 擷取檔案之後，請編輯設定檔的 Visual Studio 中開啟的方案。
1. 在方案總管中，按兩下 [ **app.config**]。 此檔案指定服務端點和`api-key`用來驗證要求。 您可以從 [傳統] 入口網站，以取得這些值。
1. [Azure 入口網站](https://portal.azure.com)登入。
1. Azure 搜尋，請移至服務儀表板。
1. 按一下 [**屬性**] 方塊，以複製服務的 URL
1. 按一下 [**索引鍵**] 方塊，以複製`api-key`。

當您完成新增 URL 時和`api-key`app.config，應用程式設定看起來應該像這樣︰

   ![][11]


<a id="sub-4"></a>
## <a name="explore-the-application"></a>瀏覽應用程式

您幾乎準備好建立並執行應用程式，但您執行的動作前，看看用來建立並填入索引的 JSON 檔案。

**Schema.json**定義索引，包括在此示範的強調計分設定檔。 結構描述定義的所有欄位索引，例如包含非可搜尋欄位中使用之通知`margin`，您可以使用計分的設定檔中。 計分的設定檔的語法說明[新增至 Azure 搜尋索引計分設定檔](http://msdn.microsoft.com/library/azure/dn798928.aspx)。

**Data1 3.json**提供的資料，跨少數幾個內容類型的 246 相簿。 資料是實際相簿和藝人資訊，例如虛構欄位的組合`price`和`margin`用來說明搜尋作業。 資料檔符合索引，而且上傳至您的搜尋 Azure 服務。 上傳和索引資料之後，您可以發行查詢。

**Program.cs**會執行下列作業︰

- 開啟主控台視窗。

- 連線到 Azure 搜尋使用服務的 URL 及`api-key`。

- 刪除`musicstoreindex`已經存在。

- 建立新`musicstoreindex`使用 schema.json 檔案。

- 填入使用資料檔案的索引。

- 查詢的使用四個查詢的索引。 請注意計分的設定檔已指定為查詢參數。 所有查詢搜尋相同的字詞，「 最適合 」。 第一個查詢示範預設分數。 剩餘的三個查詢使用計分的設定檔。

<a id="sub-5"></a>
## <a name="build-and-run-the-application"></a>建立並執行應用程式

若要排除連線或組件參考問題，建立並執行應用程式，以確保沒有任何問題，找出第一個。 您應該會看到主控台應用程式在背景中開啟。 所有的四個查詢執行順序，而不暫停。 許多系統在 15 秒內會執行整個程式。 如果主控台應用程式中包含一個訊息，指出 「 完成。 按下 enter 以繼續進行 「、 已順利完成的程式。 

若要比較查詢便會執行，您可以標記複製貼上從主控台查詢結果，並貼到 Excel 檔案。 

下圖顯示第三個查詢-並存結果。 查詢的所有使用相同的搜尋字詞，」 最適合 」，其會出現在許多相簿標題。

   ![][10]

第一個查詢會使用預設分數。 搜尋字詞只會出現在相簿標題和其他的準則，因為有 」 最適合 」 相簿標題中的項目會傳回在其中搜尋服務會找出它們的順序。 

第二個查詢使用計分的設定檔，但請注意，設定檔有任何效果。 結果為完全相同的第一個查詢。 這是因為計分的設定檔提升不是密切關係查詢的欄位 （「 類型 」）。 搜尋字詞 「 最佳 」 中沒有任何文件的任何 「 內容類型] 欄位。 當計分的設定檔不有任何效果時，結果為預設計分相同。  

第三個查詢是第一份證據的計分的設定檔的影響。 搜尋字詞，最好仍 ''，我們會使用一組相同的相簿，但因為計分的設定檔提供其他的準則，提升 '評等 」 和 「 上次更新 」，某些項目會推動更高的清單中。

下圖顯示第四個和最終查詢，提高的 [邊界]。 [邊界] 欄位不可搜尋，而無法傳回搜尋結果中。 [邊界] 值被新增至試算表，以說明較高的邊界的項目顯示搜尋結果清單中較高的點。 

   ![][9]

現在，您必須嘗試與計分的設定檔，請嘗試變更要使用不同的查詢語法程式計分設定檔或更豐富的資料。 下一節中的連結提供的更多資訊。

<a id="next-steps"></a>
## <a name="next-steps"></a>後續步驟

進一步瞭解計分設定檔。 如需詳細資訊，請參閱[新增 Azure 搜尋索引的計分設定檔](http://msdn.microsoft.com/library/azure/dn798928.aspx)。

進一步瞭解搜尋語法] 與 [查詢參數。 如需詳細資訊，請參閱[搜尋文件 (Azure 搜尋 REST API)](http://msdn.microsoft.com/library/azure/dn798927.aspx) 。

需要步驟後，並深入瞭解建立索引嗎？ 若要瞭解基本概念，[觀看這段影片](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh)。

<!--Anchors-->
[Prerequisites]: #sub-1
[Download the sample application]: #sub-2
[Edit app.config]: #sub-3
[Explore the application]: #sub-4
[Build and run the application]: #sub-5
[Next steps]: #next-steps

<!--Image references-->
[12]: ./media/search-get-started-scoring-profiles/AzureSearch_CodeplexDownload.PNG
[11]: ./media/search-get-started-scoring-profiles/AzureSearch_Scoring_AppConfig.PNG
[10]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX1.PNG
[9]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX2.PNG 