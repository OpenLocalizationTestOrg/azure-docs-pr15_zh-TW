

#<a name="metadata-for-azure-technical-articles"></a>Azure 技術文件的中繼資料

所有 Azure 技術文件包含兩個的中繼資料區段的 [屬性] 區段和標籤區段。 [屬性] 區段可讓某些網站自動化和 SEO 內容時，[標籤] 區段中，可讓許多內部內容的報告。 這兩個區段所需。

- [語法]
- [使用方式]
- [屬性和 [屬性] 區段的值]
- [屬性和 [標籤] 區段中的值]

##<a name="syntax"></a>語法

[屬性] 區段會使用下列語法︰

    <properties
       pageTitle="Page title that displays in search results and the browser tab | Microsoft Azure"
       description="Article description that will be displayed on landing pages and in most search results"
       services="service-name"
       documentationCenter="dev-center-name"
       authors="GitHub-alias-of-only-one-author"
       manager="manager-alias"
       editor=""
       tags="optional"
       keywords="For use by SEO champs only. Separate terms with commas. Check with your SEO champ before you change content in this article containing these terms."/>

[標籤] 區段會使用下列語法︰

    <tags
       ms.service="required"
       ms.devlang="may be required"
       ms.topic="article"
       ms.tgt_pltfrm="may be required"
       ms.workload="na"
       ms.date="mm/dd/yyyy"
       ms.author="Your MSFT alias or your full email address;semicolon separates two or more"/>

##<a name="usage"></a>使用方式

- 項目名稱] 和 [屬性名稱會區分大小寫。
- [ <properties> 區段必須是檔案的您的第一行。
- 每一個中繼資料區段之後，以及您的頁面標題，以確保的頁面標題會正確轉換為 HTML 期間發佈程序之前，請保留空白列。

## <a name="attributes-and-values-for-the-properties-section"></a>屬性和 [屬性] 區段的值

![](./media/article-metadata/checkmark-small.png)**pageTitle**︰ 必要;重要 SEO。 此屬性的文字會出現在瀏覽器索引標籤，然後為搜尋結果中的標題。 使用 55 60 個字元，包括空格和包括網站識別碼*|Microsoft Azure* (輸入為︰ 空間管道空間 Microsoft Azure)。  PageTitle 應該 H1 不同。

![](./media/article-metadata/checkmark-small.png)**描述**︰ 必要;重要事項 SEO （相關性） 和網站的功能。 描述應該是至少 125 字元久，以最大值，包括空格 155 字元。 說明內容的目的，讓客戶就會知道是否要從搜尋結果清單中選取它。 值為︰

- 此文字可能會顯示為描述或抽象 Google 上的搜尋結果中的段落。
- 此文字會顯示在[文件索引結果](https://azure.microsoft.com/documentation/articles/)。

![](./media/article-metadata/checkmark-small.png)**服務**︰ 需處理服務的文件。 此值 ofter 稱為 「 服務原理和 」。 列出所有適用的服務，並以逗號分隔。 所列出的第一個服務會驅動網頁和左側導覽畫面中的頁面 diplayed 導覽的階層連結。

指定服務值，並 documentationCenter 值的文章，在 [服務] 的值會磁碟機階層連結。 您的清單會顯示為已發佈的文件中的標記的其他值。 值︰

- active directory
- 作用中的目錄-b2c
- 作用中目錄 ds
- 應用程式 service\api
- api 管理
- 應用程式服務
- 應用程式 servic\mobile
- 應用程式 service\web
- 應用程式 service\logic
- 應用程式閘道器
- 應用程式深入資訊
- 自動化
- azure 入口網站
- azure 資源管理員
- azure 堆疊
- 備份
- 批次
- 最佳作法
- biztalk 服務
- 快取
- cdn
- 雲端服務
- 資料目錄
- 資料工廠
- 狀況 lake 資料分析
- 資料 lake 存放區
- devtest 實驗室
- dns
- documentdb
- expressroute
- 事件集線器
- hdinsight
- iot 中心
- 索引鍵保存庫
- 負載平衡器
- 電腦學習
- 服務商場
- 媒體服務
- 行動互動
- 行動電話服務
- 多重因素驗證
- 通知集線器
- 操作獲得深入見解
- 組合管理作業軟體
- powerapps
- 復原管理員
- 意指快取
- remoteapp
- 權限管理
- 排程器
- 搜尋
- 資訊安全中心
- 服務匯流排
- 服務布料的轉印圖樣
- 網站復原
- sql 資料庫
- sql-資料-倉庫
- sql 報告
- 儲存空間
- 存放區
- storsimple
- 資料流狀況分析
- 流量管理員
- 虛擬機器
- 虛擬網路
- visual studio-連線
- vpn 閘道器
- 網站

![](./media/article-metadata/checkmark-small.png)**documentationCenter**︰ 所需的開發人員中心文章最佳主要透過開發人員中心。 指定單一開發人員中心或適用於文件的語言。 您所列的值會驅動導覽的階層連結的頁面。 指定服務值，並 documentationCenter 值的文章，在 [服務] 的值會磁碟機階層連結。 值︰

- **.net**
- **nodejs**
- **java**
- **php**
- **python**
- **[注音標示**
- **行動**︰ 已遭取代。 以特定的行動裝置平台取代。
- **ios**︰ 區此新的值
- **android**︰ 驗證此新的值
- **windows**︰ 驗證此新的值
- **xamarin**︰ 驗證此新的值

![](./media/article-metadata/checkmark-small.png)**作者**︰ 要求，只有一個值。 列出的主要作者或文章 SME GitHub 帳戶。 此屬性的磁碟機署名已發佈的文章。 列出只有一個，無論屬性的複數的名稱。

![](./media/article-metadata/checkmark-small.png)**管理員**︰ 如果您 Microsoft 參與者所需。 清單中的 [技術] 區域的內容發佈管理員電子郵件別名。 如果您是社群參與者，包含屬性，但保留空白，我們可以填寫。

![](./media/article-metadata/checkmark-small.png)**編輯器**︰ 無法使用。 請勿將其用於其他用途。

![](./media/article-metadata/checkmark-small.png)**標籤**︰ 選用。 您想要啟用文件階層連結至文件索引頁面 (http://azure.microsoft.com/documentation/articles/)] 下的連結時，才包括 prefiltered 清單的其中一個已核准的值相符的文件。 這些值都是用來提供內容的分組不是服務特定一起組成群組內容。 這些標記也可以提供標記的指示，請參閱適用於技術堆疊。 此值，**並不會**支援任意標籤或 hashtags;您必須在網站上啟用標籤。 您可以提供一份文件，並以逗號分隔的多個標籤值。 已核准的值如下︰

  - 架構
  - azure 資源管理員
  - azure 服務管理
  - 帳單
  - mysql

![](./media/article-metadata/checkmark-small.png)**關鍵字**︰ 選用。 SEO champs 只使用。 以逗號分開的字詞。 **變更或刪除本文包含這些條款的內容之前，請洽詢您 SEO champ。** 這個屬性會記錄 SEO champ 目標，並以改善搜尋排名正在追蹤的關鍵字。 關鍵字不會呈現在發佈 HTML。 驗證時，不需要這個屬性。

## <a name="attributes-and-values-for-the-tags-section"></a>屬性和 [標籤] 區段中的值

![](./media/article-metadata/checkmark-small.png)**ms.service**︰ 必要。 指定 Azure 服務、 工具或功能，請參閱適用於。 每頁的一個值。

 如果頁面套用到多個服務，請選擇 [最直接套用的; 的服務例如，示範服務匯流排功能的網站使用裝載於應用程式的文件應有**服務匯流排**值，而不是**網站**。 如果頁面套用至多個服務的平均，選擇**多個**項目。 如果頁面不適用於任何服務 （這是很少） 中，選擇 [ **NA**]。

 - **active directory**
 - **作用中的目錄-b2c**
 - **作用中目錄 ds**
 - **api 管理**
 - **應用程式服務**︰ 僅適用於一般概念應用程式服務的資料
 - **應用程式服務 api**
 - **應用程式服務邏輯**
 - **應用程式服務行動**
 - **服務 web 應用程式**
 - **應用程式深入資訊**
 - **應用程式閘道器**
 - **自動化**
 - **azure 資源管理員**
 - **azure 安全性**
 - **azure 堆疊**
 - **備份**
 - **批次**
 - **最佳作法**
 - **biztalk 服務**
 - **帳單**
 - **快取**
 - **cdn**
 - **雲端服務**
 - **資料目錄**
 - **資料 lake 存放區**
 - **狀況 lake 資料分析**
 - **devtest 實驗室**
 - **expressroute**
 - **hdinsight**
 - **網際網路項目**
 - **iot 中心**
 - **索引鍵保存庫**
 - **電腦學習**
 - **服務商場**︰ Azure marketplace 相關的文件
 - **媒體服務**
 - **行動互動**
 - **行動電話服務**
 - **多重因素驗證**
 - **多個**︰ 頁面平均套用至多個服務
 - **na**︰ 頁面不會套用任何服務 （極少用）
 - **通知集線器**
 - **操作獲得深入見解**
 - **powerapps**
 - **復原管理員**
 - **意指快取**
 - **remoteapp**
 - **權限管理**
 - **排程器**
 - **資訊安全中心**
 - **服務匯流排**
 - **服務布料的轉印圖樣**
 - **網站復原**︰ 前身為修復服務
 - **sql 資料庫**
 - **sql-資料-倉庫**
 - **sql 報告**
 - **儲存空間**
 - **儲存**︰ 透過 Azure 市集提供的服務相關的文件
 - **storsimple**
 - **流量管理員**
 - **虛擬機器**
 - **虛擬網路**
 - **visual studio-連線**
 - **vpn 閘道器**
 - **網站**

![](./media/article-metadata/checkmark-small.png)**ms.devlang**︰ 必要。 指定，請參閱適用於的程式設計語言。 每頁的單一值。

 如果頁面套用到兩個程式設計語言的平均，選擇**多個**項目。 如果頁面是主要概念，且其內容通常用於多個程式的語言，選擇**多個**項目。 如果頁面不針對開發人員和程式設計語言套用不相關，選擇 [ **NA**]。 您可以使用**rest api**來識別 REST API 參考主題。

 - **cpp**
 - **dotnet**
 - **java**
 - **javascript**
 - **多個**︰ 頁面套用至多個程式語言平均。
 - **na**︰ 頁面不針對選取目標開發人員，，而非特定任何程式設計語言。
 - **nodejs**
 - **目標 c**
 - **php**
 - **python**
 - **rest api**
 - **[注音標示**


![](./media/article-metadata/checkmark-small.png)**ms.topic**︰ 必要。 輸入特定主題。 大部分參與者所建立的新頁面會文章或參照。

 - **文章**︰ 概念性主題、 教學課程，功能指南或其他非參考文章

 - **行銷活動頁面**︰ 僅 Azure.com。  專為外部行銷活動，登陸頁面，也不包含主站 IA.頁面  不應使用的文件的文件或登陸頁面的一般文件。  範例︰ azure.microsoft.com/develop/net/aspnet/;azure.microsoft.com/develop/mobile/ios/

 - **開發人員中心的首頁上**︰ 僅 Azure.com。  開發置首頁，例如/開發/網路 /

 - **取得使用文章**︰ 指派給文章在左側導覽畫面中的服務的開始或概觀一節。

 - **示範用文章**︰ 設計用來提供服務的簡介或功能的 「 示範用 「 教學課程取得訪客使用服務的快速入門及磁碟機] 免費試用版註冊及 MSDN 啟動。 此值指定僅功能在您的服務的文件登陸頁面頂端的文件。

 - **首頁上**︰ 上方層級的文件的首頁。 我們只需要兩個︰ azure.microsoft.com/documentation/ 和 msdn.microsoft.com/library/azure/

 - **索引頁**︰ 第二層登陸頁面程式設計語言、 服務或功能。 這些分散 Azure.com 和文件庫，並作為其他特定的範圍資訊的進入點。 範例︰ http://azure.microsoft.com/develop/mobile/resources-wp8/ http://msdn.microsoft.com/library/azure/jj673460.aspx、 http://msdn.microsoft.com/library/azure/hh689864.aspx

 - **infographic 頁面**︰ 僅 Azure.com。 功能可瀏覽 infographic 或海報，例如 http://azure.microsoft.com/documentation/infographics/windows-azure/ 文件的頁面

 - **參照**: （包括 REST API） 的 API 參考頁面或 PowerShell cmdlet 參考頁面

 - **服務首頁**︰ 僅 Azure.com。  文件服務首頁上，例如 /documentation/services/virtual-machines /

 - **網站] 區段的首頁上**︰ 僅 Azure.com。 「 首頁 」 特定類型的內容 azure.com 範例︰ http://azure.microsoft.com/documentation/infographics/、 http://azure.microsoft.com/documentation/scripts/、 http://azure.microsoft.com/documentation/videos/home/ http://azure.microsoft.com/downloads/

 - **影片頁面**︰ 僅 Azure.com。  功能視訊，例如 http://azure.microsoft.com/documentation/videos/azure-webjobs-hosting-testing-net/ 頁面

![](./media/article-metadata/checkmark-small.png)**ms.tgt_pltfrm**︰ 必要。 指定目標平台，例如 Windows、 Linux、 Windows Phone、 iOS、 Android 或特殊的快取平台。 每頁的一個值。 此值會**NA**大部分行動以外的主題和虛擬機器。

 - **快取中角色**
 - **快取倍數**
 - **快取意指**
 - **快取服務**
 - **快取共用**
 - **命令列介面**
 - **ibiza**︰ 使用 [Ibiza 入口網站的內容。 使用此選項只在案例中所討論的功能使用跨 Ibiza 入口網站與目前的入口網站。
 - **行動 android**︰ 僅限目前 Azure.com
 - **行動 html**︰ 僅限目前 Azure.com
 - **行動 ios**︰ 僅限目前 Azure.com
 - **行動 kindle**︰ 僅限目前 Azure.com
 - **行動倍數**
 - **行動 nokia x**︰ 僅限目前 Azure.com
 - **行動 phonegap**︰ 僅限目前 Azure.com
 - **行動 sencha**︰ 僅限目前 Azure.com
 - **mobile windows**︰ 僅限目前; Azure.comWindows 標準
 - **行動電話**
 - **mobile windows 市集**
 - **行動 xamarin**︰ 僅限目前; Azure.comXamarin 所有的平台
 - **行動 xamarin android**︰ 僅限目前 Azure.com
 - **mobile-xamarin-ios**︰ 僅限目前 Azure.com
 - **多個**︰ 頁面平均套用至多個平台
 - **na**︰ 平台規範不適用此頁面
 - **powershell**
 - **vm linux**
 - **vm 倍數**
 - **vm windows**
 - **vm windows sharepoint**
 - **vm windows-sql server**
 - **與--快速入門**︰ 識別與快速入門] 頁面群組。 新增 12/1/14 這個標記。
 - **與-處理-發生**︰ 識別與快速入門有何改變的頁面。 新增 12/1/14 這個標記。

![](./media/article-metadata/checkmark-small.png)**ms.workload**︰ 必要。 指定網頁適用於 Azure 工作量。 僅在每個文件的一個值。

**更新 8/6/15**Ms.workload 值會被對應的 xls，而不是.md 檔案中的值。 Ms.workload 值，仍需要驗證，直到可以更新的功能。 現在所排定的工時。  請現在，為值使用**「 na 」** 。

![](./media/article-metadata/checkmark-small.png)**ms.date**︰ 必要。 指定的日期，最後一個已檢閱，請參閱相關性、 正確性、 正確的螢幕擷取畫面，以及工作連結。 Mm/dd/yyyy 格式輸入的日期。 在此日期也會顯示已發佈的文章為上次更新日期。

![](./media/article-metadata/checkmark-small.png)**ms.author**︰ 必要。 指定 author(s) 主題相關聯。 內部報表 （例如新鮮度） 右 author(s) 關聯，請參閱使用這個值。 若要指定多個值您應該將並以分號分隔。 Microsoft 別名或完整的電子郵件地址可接受。 長度不能超過 200 個字元。


###<a name="contributors-guide-links"></a>參與者的指南的連結

- [概觀文章](./../README.md)
- [指導文件的索引](./contributor-guide-index.md)


<!--Anchors-->
[語法]: #syntax
[使用方式]: #usage
[屬性和 [屬性] 區段的值]: #attributes-and-values-for-the-properties-section
[屬性和 [標籤] 區段中的值]: #attributes-and-values-for-the-tags-section
