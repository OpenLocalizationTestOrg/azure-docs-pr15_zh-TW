<properties
   pageTitle="Azure 應用程式服務本機快取概觀 |Microsoft Azure"
   description="本文將說明如何啟用、 調整大小，以及查詢 Azure 應用程式服務本機快取功能的狀態"
   services="app-service"
   documentationCenter="app-service"
   authors="SyntaxC4"
   manager="yochayk"
   editor=""
   tags="optional"
   keywords=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/04/2016"
   ms.author="cfowler"/>

# <a name="azure-app-service-local-cache-overview"></a>Azure 應用程式服務本機快取概觀

Azure web app 內容儲存於 Azure 儲存體，並以長期的方式，為內容共用資料夾中提出。 此設計要使用各種不同的應用程式，並具有下列屬性︰  

* 跨多個虛擬機器 (VM) 執行個體的 web 應用程式共用的內容。
* 內容永久性且可以修改執行 web 應用程式。
* 記錄檔和診斷資料可供使用資料夾底下的相同共用內容。
* 直接發佈新內容更新內容的資料夾。 您可以立即檢視相同的內容，透過 SCM 網站和執行 web 應用程式 （通常是一些技術，例如 ASP.NET 執行啟動 web 應用程式重新啟動電腦上取得最新內容的一些檔案變更）。

雖然許多 web 應用程式使用其中一個或所有這些功能，但有些 web 應用程式只需要高效能、 唯讀的內容儲存，他們可以執行使用高可用性。 這些應用程式從 VM 的執行個體特定的本機快取的效益。

Azure 應用程式服務本機快取功能提供 web 角色檢視的內容。 此內容是非同步啟動網站上建立儲存內容的寫入-但-捨棄快取。 當快取時時，請網站會切換至帶快取的內容。 在本機快取執行的 web 應用程式有下列優點︰

* 這些不受存取 Azure 儲存體內容時，會發生的延遲。
* 這些不受計劃的升級或非預期的使用量和發生內容共用的伺服器的任何其他 Azure 儲存體中斷。
* 他們擁有較少的應用程式重新啟動，因為儲存共用變更。

## <a name="how-local-cache-changes-the-behavior-of-app-service"></a>本機快取如何變更行為的應用程式服務

* 本機快取是 /site 和 /siteextensions 資料夾的 web 應用程式的複本。 建立 web 應用程式啟動時的本機 VM 執行個體。 每個 web 應用程式的本機快取大小僅限於 300 MB 根據預設，但您可以增加 1 GB。
* 本機快取是讀寫。 不過，當移動虛擬機器 web 應用程式，或取得重新啟動時，將捨棄任何修改。 您不應使用本機快取的應用程式儲存內容的儲存區中的重要資料。
* Web 應用程式，可以繼續寫入記錄檔和診斷資料和其目前一樣。 記錄檔和資料，但會儲存在本機 VM。 然後他們會複製到定期共用的內容儲存。 複製到共用的內容儲存為出欄投入-寫入備份可能會遺失到期 VM 執行個體的突發當機。
* 有使用本機快取的 web 應用程式的記錄檔和資料資料夾的資料夾結構中的變更。 遵循命名模式 」 唯一識別碼 」 + 時間戳記的儲存記錄檔] 和 [資料資料夾中現在有子資料夾。 每一個子資料夾會對應到 web 應用程式正在執行的位置，或執行 VM 執行個體。  
* 發佈變更透過任何發佈機制 web 應用程式會將共用的內容儲存發佈。 這是根據設計，因為我們想要長期已發佈的內容。 若要重新整理本機快取的 web 應用程式，它必須重新啟動。 這會似乎是多餘的步驟嗎？ 若要讓生命週期順暢，請參閱本文稍後的資訊。
* D:\Home 會指向本機快取。 D:\local 會繼續指向暫時 VM 特定儲存空間。
* SCM 網站的預設內容檢視會繼續是共用的內容儲存。

## <a name="enable-local-cache-in-app-service"></a>啟用應用程式服務中的本機快取

您可以使用的保留應用程式設定組合，以設定本機快取。 您可以設定這些應用程式使用以下兩種方法︰

* [Azure 入口網站](#Configure-Local-Cache-Portal)
* [Azure 資源管理員](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>使用 [Azure 入口網站設定本機快取
<a name="Configure-Local-Cache-Portal"></a>

您啟用本機快取，您可以在每個 web app 使用此應用程式設定︰`WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Azure 入口網站應用程式設定︰ 本機快取](media/app-service-local-cache/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>使用 Azure 資源管理員設定本機快取
<a name="Configure-Local-Cache-ARM"></a>

```
...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],
    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>變更本機快取中的 [大小] 設定

根據預設，本機快取大小是**300 MB**。 這包含 /site 和複製內容商店 /siteextensions 資料夾，以及任何本機建立的記錄檔和資料資料夾。 若要增加此限制，請使用 [應用程式設定`WEBSITE_LOCAL_CACHE_SIZEINMB`。 您可以增加每個 web 應用程式的大小上限為**1 GB** (1000 MB)。

## <a name="best-practices-for-using-app-service-local-cache"></a>使用應用程式服務本機快取的最佳做法

我們建議您[臨時環境](../app-service-web/web-sites-staged-publishing.md)功能搭配使用本機快取。

* 新增_連_應用程式設定`WEBSITE_LOCAL_CACHE_OPTION`值`Always`到您**生產**位置。 如果您使用的`WEBSITE_LOCAL_CACHE_SIZEINMB`，也將其新增為一自黏的設定，以便您生產位置。
* 建立**臨時**位置並發佈您的臨時位置。 您通常不設定使用本機快取啟用臨時如果您的本機快取優點取得生產位置順暢建立部署測試週期暫存的位置。
*   測試您的網站，針對您臨時位置。  
*   當您準備好時，會發出您執行與生產位置之間[交換作業](../app-service-web/web-sites-staged-publishing.md#to-swap-deployment-slots)。  
*   自黏設定包括名稱和連到的位置。 因此臨時位置取得交換生產，它會繼承的本機快取應用程式設定。 新交換的生產位置會執行對本機快取，請稍候幾分鐘，然後將可接手屬於位置準備交換之後。 因此位置交換完成後，您生產位置會正在執行本機快取。

## <a name="frequently-asked-questions-faq"></a>常見問題 (集 FAQ)

### <a name="how-can-i-tell-if-local-cache-applies-to-my-web-app"></a>如何判斷是否本機快取會套用至我的 web 應用程式？

如果您的 web 應用程式需要高效能、 可靠的內容儲存、 撰寫重要資料在執行階段，不使用內容儲存且小於 1 GB 總大小，然後答案是 「 是 」 ！ 若要取得您 /site 與 /siteextensions 資料夾的總大小，您可以使用副檔名為網站 「 Azure Web 應用程式磁碟使用 」。  

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>如何判斷我的網站是否切換到使用本機快取？

如果您使用本機快取功能與臨時環境，交換操作才能完成本機快取接手。 若要查看您的網站是否正在執行針對本機快取，您可以檢查工作程序環境變數`WEBSITE_LOCALCACHE_READY`。 使用在[背景工作程序環境變數](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable)頁面上的指示，以存取 [背景工作程序環境變數上多個執行個體。  

### <a name="i-just-published-new-changes-but-my-web-app-does-not-seem-to-have-them-why"></a>只要發佈新的變更，但我的 web 應用程式似乎不這些內容。 為何？

如果您的 web 應用程式使用本機快取，您需要重新啟動您的網站，以取得最新變更。 不想將變更發佈至生產網站嗎？ 請參閱先前的最佳作法一節中的 [位置] 選項。

### <a name="where-are-my-logs"></a>在哪裡我記錄？

本機快取，與您的記錄與資料資料夾看起來稍有不同。 不過，您的子資料夾的結構會保持相同，但子資料夾的緊貼放置底下的格式] 唯一 VM 識別碼 」 + 時間戳記的子資料夾。

### <a name="i-have-local-cache-enabled-but-my-web-app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>我有本機快取啟用，但我的 web 應用程式仍會重新啟動。 這是為什麼？ 我想的常見的應用程式重新啟動，有助於本機快取。

本機快取，並防止儲存相關的 web 應用程式重新啟動幫助的。 不過，您的 web 應用程式仍無法進行重新啟動 VM 的計劃的基礎結構升級期間。 在您遇到的已啟用的本機快取的整體應用程式重新啟動應該較少。
