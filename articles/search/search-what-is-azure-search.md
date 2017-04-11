<properties
    pageTitle="什麼是 Azure 搜尋 |Microsoft Azure |裝載的雲端搜尋服務"
    description="Azure 搜尋是完全管理裝載的雲端搜尋服務。 瞭解更多本功能概觀。"
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="what-is-azure-search"></a>什麼是 Azure 搜尋？

Azure 搜尋是委派至 Microsoft，讓您準備要使用服務，您可以填入您的資料，然後使用 [新增至您的網頁或行動應用程式的搜尋伺服器和基礎結構的管理雲端搜尋-為-的-服務方案。 Azure 搜尋可讓您輕鬆地使用簡單的[REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)或[.NET SDK](search-howto-dotnet-sdk.md)沒有管理搜尋基礎結構，或成為搜尋中的專家應用程式中新增強大的搜尋體驗。

## <a name="give-your-users-a-powerful-search-experience"></a>授與您強大的搜尋體驗︰

使用[簡單的查詢語法](https://msdn.microsoft.com/library/azure/dn798920.aspx)，它提供了邏輯運算子、 片語搜尋運算子後, 稱謂運算子優先順序運算子可撰寫**功能強大的查詢**。 此外， [Lucene 查詢語法](https://msdn.microsoft.com/library/azure/mt589323.aspx)可以啟用模糊搜尋、 鄰近範圍搜尋、 字詞促進，以及規則運算式。 Azure 搜尋也支援自訂語彙分析器，好讓您的應用程式處理使用注音標示相符的複雜的搜尋查詢和規則運算式。

**語言支援**是[包含 56 不同的語言](https://msdn.microsoft.com/library/azure/dn879793.aspx)。 Azure 搜尋可以同時使用 Lucene 分析器和 Microsoft 分析器 （精簡方式處理 Office 和 Bing 的自然語言的年），來分析聰明地處理語言特定語意包括動詞時態、 性別、 不規則複數名詞 （例如 「 滑鼠 」 與 「 滑鼠 」），word 就能取消複合、 斷字 （適用於語言不加空格） 及其他應用程式的 [搜尋] 方塊中的文字。

您可以啟用**搜尋建議**autocompleted 搜尋列和預先輸入的查詢。 為使用者的 [[實際索引中的文件的建議](https://msdn.microsoft.com/library/azure/dn798936.aspx)，請輸入輸入的部分搜尋。

**按下醒目提示**若要查看每個包含相符項目其查詢的結果中的文字片段[可讓](https://msdn.microsoft.com/library/azure/dn798927.aspx)使用者。 您可以挑選哪一個欄位傳回反白顯示的程式碼片段。

**多面向導覽**輕鬆新增至您的搜尋結果頁面，使用 Azure 搜尋中。 使用[只為單一查詢參數](https://msdn.microsoft.com/library/azure/dn798927.aspx)，Azure 搜尋會傳回所有必要的資訊，以建構多面向搜尋體驗︰ 您的應用程式使用者介面，可讓使用者向下切入並篩選搜尋結果 （例如篩選目錄項目價格範圍或品牌）。

**地理空間**支援可讓您聰明地處理程序、 篩選和顯示不同地理位置。 Azure 搜尋可讓您瀏覽資料根據附近的搜尋結果到指定的位置，或根據特定地理區域的使用者。 這段影片說明如何運作︰[頻道 9: Azure 搜尋及休士頓資料](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)。

**篩選**可輕鬆地將多面向導覽合併到您的應用程式使用者介面，增強查詢編寫和篩選為基礎的使用者或開發人員-指定準則。 建立功能強大的篩選，使用[OData 語法](https://msdn.microsoft.com/library/azure/dn798921.aspx)。

## <a name="empower-your-developers-with-an-easy-to-use-service"></a>讓您輕鬆地使用服務的開發人員

**可用性**可確保太可靠的搜尋服務體驗。 當縮放正常運作， [Azure 搜尋提供 99.9 %sla](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

**完全管理**做為端對端解決方案，Azure 搜尋需要絕對不基礎結構的管理。 您的服務可以輕鬆地配合您的需求來中處理更多文件儲存空間、 更高查詢載入或兩者的兩個尺寸的縮放比例。

使用[索引子](https://msdn.microsoft.com/library/azure/dn946891.aspx)**資料整合**可讓 Azure 搜尋自動編目 Azure SQL 資料庫、 Azure DocumentDB 或[Azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md)同步處理您的搜尋索引的內容，您主要的資料存放區。

**文件破解**是用於 （目前預覽版本）[讀取和編製索引的主要的檔案格式](search-howto-indexing-azure-blob-storage.md)，包括 Microsoft Office，以及 PDF 和 HTML 文件。

**搜尋流量分析**是[輕鬆地收集並分析](search-traffic-analytics.md)，若要解除鎖定的使用者在搜尋方塊中的輸入深入資訊。

**簡單計分**是 Azure 搜尋的主要優點。 [計分設定檔](https://msdn.microsoft.com/library/azure/dn798928.aspx)用來在自己的文件中允許組織模型相關性函數的值。 例如，您可能會想較新的產品或折扣顯示搜尋結果中較高的產品。 您也可以建置計分使用標籤個人化計分根據您追蹤及個別儲存的客戶搜尋喜好設定的設定檔。

**排序**是提供多個欄位，透過索引結構描述，然後切換在查詢階段與單一搜尋參數。

**分頁**和節流設定您的搜尋結果相當[簡單微調控制項](search-pagination-page-layout.md)Azure 搜尋提供在搜尋結果。  

**搜尋檔案總管]**可讓您針對所有索引的問題查詢右從您的帳戶 Azure 入口網站，讓您可以輕鬆地測試查詢並調整計分的設定檔。

## <a name="how-it-works"></a>運作方式

### <a name="1-provision-service"></a>1.佈建服務
您可以使用[Azure 入口網站](https://portal.azure.com/)或[Azure 資源管理 API](https://msdn.microsoft.com/library/azure/dn832684.aspx)Azure 搜尋服務向上微調。

根據您如何設定搜尋服務，您將使用的免費的層的其他 Azure 搜尋訂閱，與共用服務，或是[支付層](https://azure.microsoft.com/pricing/details/search/)的 dedicates 資源，只使用您的服務。 佈建時您的服務，您也可以選擇主控您的服務資料中心的區域。

根據您所選擇的服務的哪些層，您可以調整您的服務中的兩個維度︰ 1） 新增複本提升您處理經常查詢載入並 2） 新增若要新增更多文件的儲存空間的磁碟分割區的容量。 藉由獨立處理文件儲存和查詢處理量，您可以自訂您的搜尋服務，針對您特定需求。

### <a name="2-create-index"></a>2.建立索引
您可以 Azure 搜尋服務上傳您的內容之前，您必須先定義 Azure 搜尋索引。 索引就像是保留您的資料，並可以接受搜尋查詢的資料庫資料表。 您定義索引結構描述對應到您想要搜尋，類似於欄位資料庫中的文件的結構。

這些索引的結構描述可以是在 Azure 入口網站，或以程式設計方式[使用.NET SDK](search-howto-dotnet-sdk.md)或建立[REST API](https://msdn.microsoft.com/library/azure/dn798941.aspx)。 一旦定義索引後，您可以後續索引 Azure 搜尋服務的方式來然後上傳您的資料。

### <a name="3-index-data"></a>3.索引資料
一旦您已經定義的欄位和索引屬性，您準備好要上傳至索引的內容。 您可以使用發送或提取模型上傳至索引的資料。

透過可設定為在指定或已排程的更新 （請參閱[索引作業 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn946891.aspx)），讓您輕鬆地內嵌資料和 Azure DocumentDB、 Azure SQL 資料庫、 Azure Blob 儲存體，或裝載於 Azure VM 的 SQL Server 資料變更的索引子提供提取模式。

推入模式是透過 SDK 或用於更新文件傳送給索引的 REST Api 提供。 您可以從幾乎任何使用 JSON 格式的資料集發送資料。 請參閱[新增、 更新或刪除文件](https://msdn.microsoft.com/library/azure/dn798930.aspx)或[如何使用.NET SDK)](search-howto-dotnet-sdk.md)的指引載入資料。

### <a name="4-search"></a>4.搜尋
一旦您已填入您的 Azure 搜尋索引，您可以立即[問題搜尋查詢](https://msdn.microsoft.com/library/azure/dn798927.aspx)REST API 的.NET SDK 使用簡單的 HTTP 要求您服務端點。

## <a name="try-it-now-for-free"></a>立即試用 （免費 ！）
您可以立即試用 Azure 搜尋 ！ 如果您已經有 Azure 帳戶，您可以[佈建免費層中的服務](search-create-service-portal.md)。

如果您沒有您可以嘗試免費、 60 分鐘的工作階段，無 Azure 帳戶登入所需。 移至[嘗試 Azure 應用程式服務](http://go.microsoft.com/fwlink/p/?LinkId=618214)，然後選取 「 Web 應用程式]。 然後選取的 「 ASP.NET + Azure Search 」 範本，即可開始使用。
