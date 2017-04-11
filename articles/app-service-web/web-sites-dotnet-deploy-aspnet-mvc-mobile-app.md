<properties 
    pageTitle="部署 ASP.NET MVC 5 行動 web 應用程式中 Azure 應用程式服務" 
    description="此教學課程會指導您如何將 web 應用程式部署到 Azure 應用程式服務 ASP.NET MVC 5 web 應用程式中使用行動裝置的功能。" 
    services="app-service" 
    documentationCenter=".net" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="01/12/2016" 
    ms.author="cephalin;riande"/>


# <a name="deploy-an-aspnet-mvc-5-mobile-web-app-in-azure-app-service"></a>部署 ASP.NET MVC 5 行動 web 應用程式中 Azure 應用程式服務

本教學課程中將會教您如何建立的行動易於 ASP.NET MVC 5 web 應用程式，並將其部署至 Azure 應用程式服務的基本概念。 在此教學課程中，您需要[Visual Studio Express 版 2013 Web] [Visual Studio Express 2013]或 Visual studio，如果您已經有的專業版。 您可以使用[Visual Studio 2015]但螢幕擷取畫面會有不同，您必須使用 ASP.NET 4.x 範本。

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-youll-build"></a>您會建立

在此教學課程中，您要新增行動裝置的功能所提供的[起始專案]的簡單會議清單應用程式[StarterProject]。 下列的螢幕擷取畫面會顯示 ASP.NET 工作階段中完成的應用程式，在 Internet Explorer 11 F12 開發人員工具] 中的瀏覽器模擬器中所見。

![][FixedSessionsByTag]

您可以使用 Internet Explorer 11 F12 開發工具和[Fiddler 工具][Fiddler]協助偵錯應用程式。 

## <a name="skills-youll-learn"></a>您將學習技巧

以下是您將學習︰

-   如何使用 Visual Studio 2013 Azure 應用程式服務中的 [發佈 web 應用程式直接在 web 應用程式。
-   ASP.NET MVC 5 範本來改善顯示在行動裝置上的所使用的 CSS 啟動安裝程式架構
-   如何建立目標特定的行動裝置瀏覽器，例如 iPhone 和 Android 的行動電話的特定檢視
-   如何建立回應檢視 （在裝置上不同的瀏覽器回應的檢視）

## <a name="set-up-the-development-environment"></a>設定的開發環境

安裝 Azure SDK.net 2.5.1 來設定您的開發環境或更新版本。 

1. 若要安裝 Azure SDK.net 時，按一下下方的連結。 如果您沒有安裝的 Visual Studio 2013，它將會安裝的連結。 本教學課程需要 Visual Studio 2013。 [Azure SDK Visual Studio 2013][AzureSDKVs2013]
1. 在 [Web 平台安裝程式] 視窗中，按一下 [**安裝**]，然後繼續進行安裝。

您也必須行動裝置瀏覽器模擬器。 下列任何一項都將能夠運作︰

-   在[Internet Explorer 11 F12 開發人員工具] 中]的瀏覽器模擬器[EmulatorIE11]（用於所有行動裝置瀏覽器的螢幕擷取畫面）。 Windows Phone 8、 Windows Phone 7，和 Apple iPad 有使用者代理程式字串預設格式。
-   在瀏覽器模擬器中[Google Chrome DevTools][EmulatorChrome]。 包含許多 Android 的裝置，以及 Apple iPhone、 Apple iPad 及 Amazon Kindle Fire 的預設格式。 也會模擬觸控事件。
-   [Opera 行動模擬器][EmulatorOpera]

Visual Studio 專案 c\#原始程式碼可隨附本主題︰

-   [入門專案下載][StarterProject]
-   [完成專案下載][CompletedProject]

##<a name="bkmk_DeployStarterProject"></a>部署入門專案至 Azure web 應用程式

1.  下載會議清單應用程式[入門專案][StarterProject]。

2.  然後在 [Windows 檔案總管] 中以滑鼠右鍵按一下下載的 ZIP 檔案，然後選擇 [*內容*。

3.  在 [**屬性**] 對話方塊中，選擇 [**解除封鎖**] 按鈕。 （解除封鎖防止安全性警告，當您嘗試使用您從網站下載*.zip*檔案）。

4.  以滑鼠右鍵按一下 ZIP 檔案，然後選取 [**所有擷取**解壓縮檔案。 

5.  在 Visual Studio 中，開啟*C#\Mvc5Mobile.sln*檔案。

6.  在方案總管中，以滑鼠右鍵按一下專案，然後按一下 [**發佈**]。

    ![][DeployClickPublish]

7.  在發佈網站，按一下 [ **Microsoft Azure 應用程式服務**。

    ![][DeployClickWebSites]

8.  如果您還沒有登入 Azure，請按一下 [**新增帳戶**]。

    ![][DeploySignIn]

9.  遵循提示，請登入您的 Azure 帳戶。

11. [應用程式服務] 對話方塊應該現在會顯示您為登入。 按一下 [**新增**]。

    ![][DeployNewWebsite]  

12. 在 [ **Web 應用程式名稱**] 欄位中，指定唯一的應用程式名稱前置字元。 完整的 web 應用程式名稱會*&lt;前置詞 >*。 azurewebsites.net。 此外，選取或指定**資源] 群組**中的新的資源群組名稱。 然後按一下 [**新增**]，建立新的應用程式服務方案。

    ![][DeploySiteSettings]

13. 設定新的應用程式服務方案，然後按一下**[確定**]。 

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7a.png)

13. 回 [建立應用程式服務] 對話方塊中，按一下 [**建立**。

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7b.png) 

13. Azure 後會建立資源，發佈網站] 對話方塊將會填入新的應用程式的設定。 按一下 [**發佈**]。

    ![][DeployPublishSite]

    Visual Studio 完成起始專案發佈至 Azure web 應用程式，一旦桌面瀏覽器會開啟並顯示即時 web 應用程式。

14. 啟動行動裝置瀏覽器模擬器、 複製會議應用程式的 URL (*<prefix>*。 azurewebsites.net) 到模擬器中，然後按一下右上角] 按鈕，然後選取 [**標籤]，瀏覽**。 如果您使用 Internet Explorer 11 預設瀏覽器，您只需要輸入`F12`，然後`Ctrl+8`，然後將瀏覽器設定檔變更為**Windows Phone**。 下圖顯示 [ *AllTags* ] 檢視中直向模式 （選擇 [**標記]，瀏覽**]）。

    ![][AllTags]

>[AZURE.TIP] 雖然您可以偵錯 MVC 5 應用程式從 Visual Studio 中，您可以一次以驗證即時 web 應用程式直接從您的行動裝置瀏覽器或瀏覽器模擬器 Azure 發佈 web 應用程式。

顯示為行動裝置上得很容易閱讀。 您已經看到部分啟動安裝程式 CSS 架構所套用的視覺效果。
按一下 [ **ASP.NET** ] 連結。

![][SessionsByTagASP.NET]

[ASP.NET 標記是畫面，啟動安裝程式會為您自動縮放填寫。 不過，您可以改善此檢視，根據行動裝置瀏覽器。 例如，[**日期**] 欄很難閱讀。 稍後在教學課程中，您會變更，使其易於行動*AllTags*檢視。

##<a name="bkmk_bootstrap"></a>啟動安裝 CSS 架構

新增在 MVC 5 範本是內建的啟動安裝支援。 您已經看到如何它立即可以改善您的應用程式中的不同檢視。 例如，在頂端導覽列，就自動可摺疊瀏覽器寬度會比較小。 在桌面瀏覽器中，請嘗試調整大小的瀏覽器視窗，請參閱導覽列如何變更網站外觀與風格。 這是回應網頁設計的內建啟動安裝程式。

若要查看的 Web 應用程式的外觀不啟動安裝程式，請開啟*應用程式\_開始\\BundleConfig.cs*和註解的線條，包含*bootstrap.js*和*bootstrap.css*。 下列程式碼會顯示的最後兩個陳述式`RegisterBundles`變更後的方法︰

     bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
              //"~/Scripts/bootstrap.js",
              "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css").Include(
              //"~/Content/bootstrap.css",
              "~/Content/site.css"));

按下`Ctrl+F5`執行應用程式。

您會看到可摺疊的導覽列現在只一般未排序的清單。 再按一下 [**瀏覽標籤**，然後按一下 [ **ASP.NET**。
在行動裝置模擬器] 檢視中，您可以查看已不再縮放填寫] 畫面，並必須才能查看 [右側表格的兩側捲動。

![][SessionsByTagASP.NETNoBootstrap]

復原變更並重新整理行動瀏覽器] 以確認已還原行動易於顯示。

啟動安裝程式不是特定 ASP.NET MVC 5、，您可以利用這些功能在任何 web 應用程式。 但是，現在內建 ASP.NET MVC 5 專案範本，以便您 MVC 5 Web 應用程式可以利用啟動安裝程式的預設。

如需啟動安裝程式的詳細資訊，請移至 [[啟動安裝程式][BootstrapSite]網站。

下一節中，您會看到如何提供行動瀏覽器的特定檢視。

##<a name="bkmk_overrideviews"></a>覆寫檢視、 版面配置及部分的檢視

您可以針對行動裝置瀏覽器一般而言，個別的行動裝置瀏覽器，或任何特定的瀏覽器會覆寫任何檢視中 （包括版面配置及部分檢視）。 若要提供行動特定檢視，您可以檢視將檔案複製到，並新增*。行動*的檔案名稱。 例如，若要建立*索引*行動裝置檢視，您可以複製*檢視\\常用\\Index.cshtml*至*檢視\\常用\\Index.Mobile.cshtml*。

在此區段中，您會建立 mobile 特定版面配置檔案。

若要開始，請複製*檢視\\共用\\\_Layout.cshtml*至*檢視\\共用\\\_Layout.Mobile.cshtml*。 開啟*\_Layout.Mobile.cshtml*和變更標題**MVC5 應用程式**從**MVC5 應用程式 （行動電話）**。

在每個`Html.ActionLink`導覽列的通話，「 瀏覽] 在每個連結*ActionLink*中移除。 下列程式碼會顯示已完成`<ul class="nav navbar-nav">`行動版面配置檔案的標籤。

    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Home")</li>
        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
    </ul>

複製*檢視\\常用\\AllTags.cshtml*檔案*檢視\\常用\\AllTags.Mobile.cshtml*。 開啟新的檔案，並變更`<h2>`從 「 標籤 」 的元素以 「 標籤 (M) 」:

    <h2>Tags (M)</h2>

瀏覽至使用桌面瀏覽器，使用行動裝置瀏覽器模擬器中的 [標籤] 頁面。 在行動裝置瀏覽器模擬器顯示兩個您所做的變更 (從標題*\_Layout.Mobile.cshtml*和*AllTags.Mobile.cshtml*標題)。

![][AllTagsMobile_LayoutMobile]

相反地，沒有變更桌面顯示 (具有標題從*\_Layout.cshtml*和*AllTags.cshtml*)。

![][AllTagsMobile_LayoutMobileDesktop]

##<a name="bkmk_browserviews"></a>建立瀏覽器的特定檢視

除了 mobile 與桌面特定的檢視，您可以建立個別的瀏覽器的檢視。 例如，您可以建立專門用來在 iPhone 或 Android 瀏覽器的檢視。 在此區段中，您將建立 iPhone 瀏覽器和 iPhone 版*AllTags*檢視的版面的配置。

開啟*Global.asax*檔案，然後將下列程式碼新增至底部`Application_Start`方法。

    DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone")
    {
        ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf
            ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0)
    });

將此程式碼定義新的顯示模式，名為 「 iPhone 」 會比對每個傳入要求。 要求符合您定義 （亦即，如果使用者代理程式包含字串 「 iPhone 」） 的條件，ASP.NET MVC 會尋找其名稱中包含 「 iPhone 」 後稱謂的檢視。

>[AZURE.NOTE] 行動瀏覽器特定顯示模式時，例如 iPhone 和 Android 中，請務必將第一個引數設定為`0`（插入清單的頂端），請確定您的瀏覽器特定模式優先於行動範本 (*。Mobile.cshtml)。 如果行動範本改為位於頂端的清單]，將在預定的顯示模式 （第一個相符項目獲勝及行動範本符合所有的行動裝置瀏覽器） 選取它。 

在 [程式碼，請以滑鼠右鍵按一下`DefaultDisplayMode`，選擇 [**解決**，接著再選擇`using System.Web.WebPages;`。 新增的參考`System.Web.WebPages`命名空間，也就是位置`DisplayModeProvider`和`DefaultDisplayMode`類型定義。

![][ResolveDefaultDisplayMode]

或者，您可以僅手動新增至的下列行`using`檔案的區段。

    using System.Web.WebPages;

儲存變更。 複製*檢視\\共用\\\_Layout.Mobile.cshtml*檔案*檢視\\共用\\\_Layout.iPhone.cshtml*。 開啟新的檔案，然後變更 [標題`MVC5 Application (Mobile)`至`MVC5 Application (iPhone)`。

複製*檢視\\常用\\AllTags.Mobile.cshtml*檔案*檢視\\常用\\AllTags.iPhone.cshtml*。 在新的檔案，變更`<h2>`」 標記 (iPhone) 」 的項目從 「 標籤 (M) 」。

執行應用程式。 執行行動裝置瀏覽器模擬器、 請確定其使用者代理程式設為 「 iPhone 」，並瀏覽至*AllTags*檢視。 如果您使用模擬器在 Internet Explorer 11 F12 開發人員工具] 中，設定下列模擬︰

-   在瀏覽器設定檔 =**在 Windows Phone**
-   使用者代理程式字串 =**自訂**
-   自訂字串 = **Apple-iPhone5C1/1001.525**

下圖顯示 [ *AllTags*檢視在 Internet Explorer 11 F12 開發人員工具，以自訂使用者代理程式字串中的模擬器中呈現 （這是 iPhone 5 C 使用者代理程式字串）。

![][AllTagsIPhone_LayoutIPhone]

在行動裝置瀏覽器中，選取 [**喇叭**] 連結。 預設喇叭檢視 (*AllSpeakers.cshtml*) 使用 [行動版面配置] 檢視呈現不是行動裝置檢視 (*AllSpeakers.Mobile.cshtml*)，因為 (*\_Layout.Mobile.cshtml*)。 標題**MVC5 應用程式 （行動電話）** ，如下所示，定義在*\_Layout.Mobile.cshtml*。

![][AllSpeakers_LayoutMobile]

您可以藉由設定全域停用預設 （非行動電話） 檢視內行動版面配置呈現`RequireConsistentDisplayMode`至`true`中*檢視\\\_ViewStart.cshtml*檔案，就像這樣︰

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = true;
    }

當`RequireConsistentDisplayMode`設定為 [ `true`，行動版面配置 (*\_Layout.Mobile.cshtml*) 只適用於行動裝置檢視 （亦即時檢視檔案的表單***ViewName**。Mobile.cshtml*)。 您可能會想要設定`RequireConsistentDisplayMode`至`true`如果您的行動版面配置也無法使用您的非行動檢視。 以下螢幕擷取畫面顯示 [*喇叭*] 頁面的呈現方式何時`RequireConsistentDisplayMode`設定為 [ `true` （不含頂端的導覽列的字串 」 （行動電話） 」）。

![][AllSpeakers_LayoutMobileOverridden]

您可以藉由設定停用一致的顯示模式中的特定檢視`RequireConsistentDisplayMode`至`false`view 檔案中。 在下列標記*檢視\\常用\\AllSpeakers.cshtml*檔案集`RequireConsistentDisplayMode`至`false`:

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All speakers";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = false;
    }

此區段中，我們已過如何建立行動版面配置及檢視，以及如何建立版面配置和 iPhone 等特定裝置的檢視。
不過，啟動安裝程式 CSS 架構的主要優點是回應的版面配置，這表示單一樣式表，可以套用整個桌面、 手機與平板電腦的瀏覽器建立一致的外觀與風格。 下一節中，您會看到如何運用啟動安裝程式來建立 mobile 易於檢視。

##<a name="bkmk_Improvespeakerslist"></a>改善 [喇叭] 清單

當您只看到 [*喇叭*] 檢視可讀取，但連結較小，會在行動裝置上點選。 在此區段中，就能讓*AllSpeakers*檢視行動易於，其中顯示大型的簡單點選連結，並包含搜尋方塊中，若要快速找到喇叭。

您可以使用啟動安裝[連結的清單] 群組中][]的樣式設定，以改善 [*喇叭*] 檢視。 在 [*檢視\\常用\\AllSpeakers.cshtml*，Razor 檔案的內容取代下列程式碼。

     @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker }, new { @class = "list-group-item" })
        }
    </div>

`class="list-group"`屬性`<div>`標籤適用於啟動安裝清單樣式，以及`class="input-group-item"`屬性會啟動安裝清單項目樣式套用至每個連結。

重新整理行動裝置瀏覽器。 更新的檢視看起來像這樣︰

![][AllSpeakersFixed]

啟動安裝[連結的清單] 群組][]樣式可讓您整個] 方塊中的每個連結為可點選，這是更好的使用者經驗。 切換至 [桌面] 檢視，可以一同觀看一致的外觀與風格。

![][AllSpeakersFixedDesktop]

雖然行動裝置瀏覽器檢視已經過改良，很難瀏覽喇叭的完整清單。 啟動安裝程式不提供搜尋篩選功能--現成的但您可以將其新增幾行程式碼。 您第一次會新增至檢視中，搜尋方塊，然後使用 JavaScript 程式碼篩選函數的連線。 在 [*檢視\\常用\\AllSpeakers.cshtml*，新增\<表單\>只之後標記\<h2\>標記，如下所示︰

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <form class="input-group">
        <span class="input-group-addon"><span class="glyphicon glyphicon-search"></span></span>
        <input type="text" class="form-control" placeholder="Search speaker">
    </form>
    <br />
    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class = "list-group-item" })
        }
    </div>

請注意，`<form>`和`<input>`兩個標籤都有啟動安裝套用的樣式。 `<span>`項目新增至 [搜尋] 方塊的[glyphicon][]啟動安裝。

在 [*指令碼*] 資料夾中，新增一個稱為*filter.js*的 JavaScript 檔案。 開啟檔案，並貼上下列的程式碼︰

    $(function () {

        // reset the search form when the page loads
        $("form").each(function () {
            this.reset();
        });

        // wire up the events to the <input> element for search/filter
        $("input").bind("keyup change", function () {
            var searchtxt = this.value.toLowerCase();
            var items = $(".list-group-item");

            // show all speakers that begin with the typed text and hide others
            for (var i = 0; i < items.length; i++) {
                var val = items[i].text.toLowerCase();
                val = val.substring(0, searchtxt.length);
                if (val == searchtxt) {
                    $(items[i]).show();
                }
                else {
                    $(items[i]).hide();
                }
            }
        });
    });

您也需要您註冊配搭中包含 filter.js。 開啟*應用程式\_開始\\BundleConfig.cs*變更第一個配搭。 變更第一個`bundles.Add`（適用於**jquery**套件組合） 的陳述式包含*指令碼\\filter.js*、，如下所示︰

     bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                "~/Scripts/jquery-{version}.js",
                "~/Scripts/filter.js"));

依預設已呈現**jquery**套件*\_版面配置*檢視。 之後，您可以使用相同的 JavaScript 程式碼，若要套用其他清單檢視的篩選功能。

重新整理行動裝置瀏覽器並移至 [ *AllSpeakers*檢視。 在 [搜尋] 方塊中，輸入 「 sc 」。 [喇叭] 清單現在應該篩選依據您搜尋的字串。

![][AllSpeakersFixedSearchBySC]

##<a name="bkmk_improvetags"></a>改善 [標籤] 清單

[*喇叭*] 檢視中，例如 [*標記*] 檢視可讀取，但連結很小，且容易在行動裝置上點選。 您可以修正*標籤*檢視您修正 [*喇叭*] 檢視中，如果您使用的程式碼變更所述，但以下列方式相同`Html.ActionLink`方法語法中的*檢視\\常用\\AllTags.cshtml*:

    @Html.ActionLink(tag, 
                     "SessionsByTag", 
                     new { tag }, 
                     new { @class = "list-group-item" })

重新整理的桌面瀏覽器如下所示︰

![][AllTagsFixedDesktop]

然後，重新整理的行動裝置瀏覽器看起來，如下所示︰ 

![][AllTagsFixed]

>[AZURE.NOTE] 如果您發現的原始的清單格式設定還在行動裝置瀏覽器中，而不知道您部份的啟動安裝樣式有何改變，這是所造成的舊版動作建立行動裝置的特定檢視。 不過，現在您使用啟動安裝程式 CSS 架構建立回應網頁設計，移標頭並移除下列行動的特定檢視和行動特定版面配置檢視。 一旦您這麼做，重新整理的行動裝置瀏覽器會顯示啟動安裝的樣式。

##<a name="bkmk_improvedates"></a>改善 [日期] 清單

如果您使用的程式碼變更之前，所述，改良 [*喇叭*] 與 [*標記*檢視等，但下列，您可以改善*日期*檢視`Html.ActionLink`方法語法中的*檢視\\常用\\AllDates.cshtml*:

    @Html.ActionLink(date.ToString("ddd, MMM dd, h:mm tt"), 
                     "SessionsByDate", 
                     new { date }, 
                     new { @class = "list-group-item" })

您會收到重新整理的行動裝置瀏覽器檢視像這樣︰

![][AllDatesFixed]

您可以進一步就能改善*日期*檢視組織中的日期時間值的日期。 這可以使用啟動安裝[面板][]樣式。 取代的內容*檢視\\常用\\AllDates.cshtml*下列程式碼的檔案︰

    @model IEnumerable<DateTime>

    @{
        ViewBag.Title = "All Dates";
    }

    <h2>Dates</h2>

    @foreach (var dategroup in Model.GroupBy(x=>x.Date))
    {
        <div class="panel panel-primary">
            <div class="panel-heading">
                @dategroup.Key.ToString("ddd, MMM dd")
            </div>
            <div class="panel-body list-group">
                @foreach (var date in dategroup)
                {
                    @Html.ActionLink(date.ToString("h:mm tt"), 
                                     "SessionsByDate", 
                                     new { date }, 
                                     new { @class = "list-group-item" })
                }
            </div>
        </div>
    }

將此程式碼會建立不同的`<div class="panel panel-primary">`在清單中，每個不同日期的標籤，並為之前使用[連結的清單] 群組][]的個別的連結。 以下是什麼行動裝置瀏覽器看起來像此程式碼時執行︰

![][AllDatesFixed2]

切換至桌面瀏覽器。 同樣地，請注意一致的外觀。

![][AllDatesFixed2Desktop]

##<a name="bkmk_improvesessionstable"></a>改善 SessionsTable 檢視

在 [此節]，就能讓更多的行動易於*SessionsTable*檢視。 這項變更為更廣泛的上一個變更。

在行動裝置瀏覽器中，請點選 [**標籤**] 按鈕，然後輸入`asp`在搜尋方塊中。

![][AllTagsFixedSearchByASP]

點選 [ **ASP.NET** ] 連結。

![][SessionsTableTagASP.NET]

如您所見，目前設計用來在桌面瀏覽器中檢視表格的格式顯示。 不過，則稍微難以閱讀在行動裝置瀏覽器上。 若要修正此問題，請開啟*檢視\\常用\\SessionsTable.cshtml*再將該檔案的內容取代下列程式碼︰

    @model IEnumerable<Mvc5Mobile.Models.Session>

    <h2>@ViewBag.Title</h2>

    <div class="container">
        <div class="row">
            @foreach (var session in Model)
            {
                <div class="col-md-4">
                    <div class="list-group">
                        @Html.ActionLink(session.Title, 
                                         "SessionByCode", 
                                         new { session.Code }, 
                                         new { @class="list-group-item active" })
                        <div class="list-group-item">
                            <div class="list-group-item-text">
                                @Html.Partial("_SpeakersLinks", session)
                            </div>
                            <div class="list-group-item-info">
                                @session.DateText
                            </div>
                            <div class="list-group-item-info small hidden-xs">
                                @Html.Partial("_TagsLinks", session)
                            </div>
                        </div>
                    </div>
                </div>
            }
        </div>
    </div>

程式碼會 3 的項目︰

-   使用[連結的自訂清單] 群組中][]啟動安裝設定格式的工作階段資訊，讓所有資訊都均可讀取 （使用類別，例如 [清單] 群組中的項目文字） 的行動裝置瀏覽器上
-   讓桌面瀏覽器和垂直 （使用欄會員 4 課程） 的行動裝置瀏覽器中的工作階段項目會水平版面配置]，套用[格線系統][]
-   若要隱藏的工作階段標籤] （使用隱藏 x 課程） 的行動裝置瀏覽器中檢視時使用[回應公用程式][]

您也可以點選標題連結以移至個別的工作階段。 下圖會反映出的程式碼變更。

![][FixedSessionsByTag]

您會自動套用啟動安裝格線系統排列垂直在行動裝置瀏覽器中的工作階段。 此外，請注意，不會顯示在標籤。 切換至桌面瀏覽器。

![][SessionsTableFixedTagASP.NETDesktop]

在桌面瀏覽器中，請注意，現在會顯示標籤。 此外，您可以看到您所套用的啟動安裝格線系統來排列工作階段中的項目兩個資料行。 如果您放大瀏覽器，您會看到三個資料行，變更排列方式。

##<a name="bkmk_improvesessionbycode"></a>改善 SessionByCode 檢視

最後，您會修正*SessionByCode*檢視，使其易於行動。

在行動裝置瀏覽器中，請點選 [**標籤**] 按鈕，然後輸入`asp`在搜尋方塊中。

![][AllTagsFixedSearchByASP]

點選 [ **ASP.NET** ] 連結。 顯示 ASP.NET 標記的工作階段。

![][FixedSessionsByTag]

選擇 [**建置 ASP.NET 與 AngularJS 單一頁面應用程式**] 連結。

![][SessionByCode3-644]

預設桌面檢視很好的但您可以輕鬆地改善外觀，使用部分啟動安裝程式 GUI 元件。

開啟*檢視\\常用\\SessionByCode.cshtml* ，並以下列標記取代內容︰

    @model Mvc5Mobile.Models.Session

    @{
        ViewBag.Title = "Session details";
    }
    <h3>@Model.Title (@Model.Code)</h3>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Speakers
        </div>
        @foreach (var speaker in Model.Speakers)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class="panel-body" })
        }
    </div>

    <p>@Model.Abstract</p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Tags
        </div>
        @foreach (var tag in Model.Tags)
        {
            @Html.ActionLink(tag, 
                             "SessionsByTag", 
                             new { tag }, 
                             new { @class = "panel-body" })
        }
    </div>

新的標記會使用啟動安裝面板設定樣式來改善行動裝置檢視。 

重新整理行動裝置瀏覽器。 下圖會反映您剛才所做的程式碼變更︰

![][SessionByCodeFixed3-644]

## <a name="wrap-up-and-review"></a>自動換行，並檢閱

本教學課程中顯示如何使用 ASP.NET MVC 5 開發行動易於 Web 應用程式。 這些功能包括︰

-   部署 ASP.NET MVC 5 應用程式至應用程式服務 web 應用程式
-   使用啟動安裝程式 MVC 5 應用程式中建立回應 web 版面配置
-   覆寫版面配置、 檢視和部分的檢視，全域與個別的檢視
-   控制項版面配置] 和 [部份覆寫強制使用`RequireConsistentDisplayMode`屬性
-   建立目標特定的瀏覽器，例如 iPhone 瀏覽器的檢視
-   套用 Razor 程式碼中的啟動安裝樣式

## <a name="see-also"></a>另請參閱

-   [9 的回應網站設計的基本原則](http://blog.froont.com/9-basic-principles-of-responsive-web-design/)
-   [啟動安裝程式][BootstrapSite]
-   [正式啟動安裝部落格][]
-   [從教學課程共和國 twitter 啟動安裝教學課程][]
-   [啟動安裝遊樂場][]
-   [W3C 建議行動 Web 應用程式的最佳作法][]
-   [W3C 候選建議媒體查詢][]

## <a name="whats-changed"></a>變更的項目
* 若要變更的指南，從網站應用程式服務請參閱︰ [Azure 應用程式服務與程式影響現有 Azure 服務](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- Internal Links -->
[Deploy the starter project to an Azure web app]: #bkmk_DeployStarterProject
[Bootstrap CSS Framework]: #bkmk_bootstrap
[Override the Views, Layouts, and Partial Views]: #bkmk_overrideviews
[Create Browser-Specific Views]:#bkmk_browserviews
[Improve the Speakers List]: #bkmk_Improvespeakerslist
[Improve the Tags List]: #bkmk_improvetags
[Improve the Dates List]: #bkmk_improvedates
[Improve the SessionsTable View]: #bkmk_improvesessionstable
[Improve the SessionByCode View]: #bkmk_improvesessionbycode

<!-- External Links -->
[Visual Studio Express 2013]: http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-web
[Visual Studio 2015]: https://www.visualstudio.com/downloads/download-visual-studio-vs
[AzureSDKVs2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
[Fiddler]: http://www.fiddler2.com/fiddler2/
[EmulatorIE11]: http://msdn.microsoft.com/library/ie/dn255001.aspx
[EmulatorChrome]: https://developers.google.com/chrome-developer-tools/docs/mobile-emulation
[EmulatorOpera]: http://www.opera.com/developer/tools/mobile/
[StarterProject]: http://go.microsoft.com/fwlink/?LinkID=398780&clcid=0x409
[CompletedProject]: http://go.microsoft.com/fwlink/?LinkID=398781&clcid=0x409
[BootstrapSite]: http://getbootstrap.com/
[WebPIAzureSdk23NetVS13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk23NetVS13.png
[連結的清單] 群組]: http://getbootstrap.com/components/#list-group-linked
[glyphicon]: http://getbootstrap.com/components/#glyphicons
[面板]: http://getbootstrap.com/components/#panels
[連結的自訂清單] 群組]: http://getbootstrap.com/components/#list-group-custom-content
[格線系統]: http://getbootstrap.com/css/#grid
[回應公用程式]: http://getbootstrap.com/css/#responsive-utilities
[正式啟動安裝部落格]: http://blog.getbootstrap.com/
[從教學課程共和國 twitter 啟動安裝教學課程]: http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/
[啟動安裝遊樂場]: http://www.bootply.com/
[W3C 建議行動 Web 應用程式的最佳作法]: http://www.w3.org/TR/mwabp/
[W3C 候選建議媒體查詢]: http://www.w3.org/TR/css3-mediaqueries/

<!-- Images -->
[DeployClickPublish]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-1.png
[DeployClickWebSites]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-2.png
[DeploySignIn]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-3.png
[DeployUsername]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-4.png
[DeployPassword]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-5.png
[DeployNewWebsite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-6.png
[DeploySiteSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7.png
[DeployPublishSite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-8.png
[MobileHomePage]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/mobile-home-page.png
[FixedSessionsByTag]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-Fixed.png
[AllTags]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags.png
[SessionsByTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET.png
[SessionsByTagASP.NETNoBootstrap]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-NoBootstrap.png
[AllTagsMobile_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile.png
[AllTagsMobile_LayoutMobileDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile-Desktop.png
[ResolveDefaultDisplayMode]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Resolve-DefaultDisplayMode.png
[AllTagsIPhone_LayoutIPhone]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsIPhone-_LayoutIPhone.png
[AllSpeakers_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile.png
[AllSpeakers_LayoutMobileOverridden]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile-Overridden.png
[AllSpeakersFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed.png
[AllSpeakersFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-Desktop.png
[AllSpeakersFixedSearchBySC]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-SearchBySC.png
[AllTagsFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-Desktop.png 
[AllTagsFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed.png
[AllDatesFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed.png
[AllDatesFixed2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2.png
[AllDatesFixed2Desktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2-Desktop.png
[AllTagsFixedSearchByASP]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-SearchByASP.png
[SessionsTableTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Tag-ASP.NET.png
[SessionsTableFixedTagASP.NETDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Fixed-Tag-ASP.NET-Desktop.png
[SessionByCode3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-3-644.png
[SessionByCodeFixed3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-Fixed-3-644.png
 
