<properties
    pageTitle="快速入門中 NodeJS Azure 搜尋 |Microsoft Azure |裝載的雲端搜尋服務"
    description="逐步執行上使用您的程式設計語言 NodeJS Azure 建立搜尋裝載的雲端搜尋服務應用程式。"
    services="search"
    documentationCenter=""
    authors="EvanBoyle"
    manager="pablocas"
    editor="v-lincan"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.date="07/14/2016"
    ms.author="evboyle"/>

# <a name="get-started-with-azure-search-in-nodejs"></a>快速入門中 NodeJS Azure 搜尋
> [AZURE.SELECTOR]
- [入口網站](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

瞭解如何建立自訂的 NodeJS 搜尋應用程式使用其搜尋體驗 Azure 搜尋。 本教學課程中會使用[Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/dn798935.aspx)來建構物件和在練習中使用的作業。

我們使用[NodeJS](https://nodejs.org)與 NPM、[適文字 3](http://www.sublimetext.com/3)和 Windows PowerShell Windows 8.1 上開發並測試此程式碼。

若要執行這個範例中，您必須 Azure 搜尋服務，您可以註冊[Azure 入口網站](https://portal.azure.com)中。 如需逐步指示，請參閱[建立 Azure 搜尋服務在入口網站](search-create-service-portal.md)。

## <a name="about-the-data"></a>資料的相關資訊

此範例應用程式使用[美國地理服務 (USG)](http://geonames.usgs.gov/domestic/download_data.htm)，篩選上狀態的羅德島來縮小資料集的資料。 我們將使用這項資料來建立傳回地標建築物醫院和學校，以及地理資料流、 湖和 summits 等功能，例如搜尋應用程式。

在此應用程式， **DataIndexer**程式建置並載入使用[重新](https://msdn.microsoft.com/library/azure/dn798918.aspx)架構，從公用 Azure SQL 資料庫擷取篩選的 USG 資料集的索引。 認證與連線至線上的資料來源資訊所提供的程式碼。 沒有進一步的設定，則需要。

> [AZURE.NOTE] 我們在此保持在免費價格層的 10000 的文件限制] 下的資料集套用篩選。 如果您使用的標準層時，不適用此限制。 如需每個價格層的容量的詳細資訊，請參閱[搜尋服務限制](search-limits-quotas-capacity.md)。


<a id="sub-2"></a>
## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>尋找您的 Azure 搜尋服務的 api 金鑰與服務名稱

建立服務後，返回入口網站以取得 URL 或`api-key`。 連線到您的搜尋服務要求您的 URL，然後`api-key`驗證通話。

1. [Azure 入口網站](https://portal.azure.com)登入。
2. 跳轉列中，按一下 [**搜尋服務**] 清單中的所有 Azure 搜尋服務佈建後，您的訂閱。
3. 選取您想要使用的服務。
4. 在服務儀表板，您會看到磚的基本資訊，以及存取系統按鍵的按鍵圖示。

    ![][3]

5. 複製服務的 URL、 管理員索引鍵和查詢鍵。 更新版本中，當您將其新增至 config.js 檔案時，您將需要三個。

## <a name="download-the-sample-files"></a>下載範例檔案

您可以使用 [與下列方法之一來下載的範例。

1. 移至[AzureSearchNodeJSIndexerDemo](https://github.com/AzureSearch/AzureSearchNodeJSIndexerDemo)。
2. 按一下 [**下載 ZIP**、 儲存.zip 檔案，並解壓縮其包含的所有檔案。

所有後續檔案修改和執行陳述式會對此資料夾中的檔案。


## <a name="update-the-configjs-with-your-search-service-url-and-api-key"></a>更新 config.js。 搜尋服務 URL] 與 [api 金鑰

使用 URL 和 api 金鑰您先前複製的設定檔中指定 URL，管理員鍵，然後查詢機碼。

管理員鍵授與服務的作業，包括建立或刪除索引並載入文件的完全控制權。 相反地，查詢索引鍵是唯讀的作業，通常是用來連線到 Azure 搜尋的用戶端應用程式。

在這個範例中，我們可以包含查詢索引鍵，以協助強化用戶端應用程式中使用查詢索引鍵的最佳作法。

以下螢幕擷取畫面會顯示**config.js**開啟文字編輯器] 中，demarcated，使您可以看到要更新的檔案與值的有效搜尋服務的相關項目。

![][5]


## <a name="host-a-runtime-environment-for-the-sample"></a>Host （主機) 範例在執行階段環境

樣本需要 HTTP 伺服器，您可以全面使用 npm 對其進行安裝。

使用下列命令 PowerShell 視窗。

1. 瀏覽至含有**package.json**檔案的資料夾。
2. 輸入`npm install`。
2. 輸入`npm install -g http-server`。

## <a name="build-the-index-and-run-the-application"></a>建立索引，並執行應用程式

1. 輸入`npm run indexDocuments`。
2. 輸入`npm run build`。
3. 輸入`npm run start_server`。
4. 直接在瀏覽器`http://localhost:8080/index.html`

## <a name="search-on-usgs-data"></a>搜尋 USG 資料

USG 資料集包含所要狀態的羅德島相關記錄。 如果您按一下 [**搜尋**] 空的搜尋方塊，您會收到前 50 個項目，這是預設值。

輸入搜尋字詞產生搜尋引擎內容移。 請輸入地區的名稱。 「 Roger Williams 」 是羅德島的第一個的管理員。 許多公園、 建築物及學校被命名他。

![][9]

您也可以嘗試的任何這些條件︰

- Pawtucket
- Pembroke
- goose + 海景


## <a name="next-steps"></a>後續步驟

這是根據 NodeJS 與 USG 資料集的第一個 Azure 搜尋教學課程。 一段時間，我們會延伸此教學課程以示範額外的搜尋功能可能會想要使用的您自訂的解決方案。

如果您已經有一些背景 Azure 搜尋] 中，您可以使用 [以創造這個範例的嘗試 suggesters （預先輸入 」 或 「 自動完成查詢）、 篩選及多面向導覽。 您也可以藉由新增計數批次處理文件，好讓使用者可以瀏覽頁面結果改善在搜尋結果頁面。

Azure 搜尋的新手嗎？ 我們建議您嘗試其他教學課程，瞭解您可以建立。 請造訪我們[的說明文件頁面](https://azure.microsoft.com/documentation/services/search/)，尋找更多資源。 您也可以檢視連結，在我們的[影片和教學課程清單](search-video-demo-tutorial-list.md)存取更多的資訊。

<!--Image references-->
[1]: ./media/search-get-started-nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-nodejs/AzSearch-NodeJS-configjs.png
[9]: ./media/search-get-started-nodejs/rogerwilliamsschool.png
