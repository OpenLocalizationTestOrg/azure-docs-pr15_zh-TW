<properties
    pageTitle="Azure 應用程式服務的企業級 WordPress |Microsoft Azure"
    description="瞭解如何主控 Azure 應用程式服務企業級 WordPress 網站"
    services="app-service\web"
    documentationCenter=""
    authors="sunbuild"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.devlang="php"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="web"
    ms.date="10/24/2016"
    ms.author="sumuth"/>

# <a name="enterprise-class-wordpress-on-azure-app-service"></a>企業級 WordPress Azure 應用程式服務

Azure 應用程式服務會提供可調整且易於使用環境任務要徑、 大型刻度[WordPress] [wordpress]網站。 Microsoft 本身執行企業級網站，例如[Office] [officeblog]和[Bing] [bingblog]部落格。 這份文件會顯示您如何建立和維護企業級、 雲端 WordPress 網站可處理大量的網站訪客使用 Azure 應用程式服務 Web 應用程式。

## <a name="architecture-and-planning"></a>架構及規劃

基本的 WordPress 安裝有只有兩個需求。

* **MySQL 資料庫**-可透過[在 Azure Marketplace ClearDB][cdbnstore]，您可以管理您自己的 MySQL 安裝在 Azure 虛擬機器使用 [ [Windows]或[mysqlwindows]或[Linux][mysqllinux]。

  > [AZURE.NOTE] ClearDB 提供數種 MySQL 設定，每個設定不同的效能特性。 請參閱[Azure 市集][cdbnstore]用透過 Azure 市集，或直接所見[ClearDB 網站](http://www.cleardb.com/pricing.view)上提供的產品的詳細資訊。

* **PHP 5.2.4 或更大**-Azure 應用程式服務目前提供[PHP 版本 5.4 5.5，與 5.6][phpwebsite]。

    > [AZURE.NOTE] 我們建議您永遠在上執行最新版的 PHP，以確定您有最新的安全性修正。

### <a name="basic-deployment"></a>基本的部署

使用基本的需求，您可以建立 Azure 的區域內的基本解決方案。

![Azure web app 和 MySQL 資料庫裝載於單一 Azure 區域][basic-diagram]

雖然這可讓您以擴展您的應用程式建立多個網站的 Web 應用程式執行個體，所有項目被裝載在特定的地理區域中的資料中心。 從這個區域外的訪客可能會看到回應時間緩慢時使用的網站，並向下地區資料中心，因此會應用程式。


### <a name="multi-region-deployment"></a>多區域部署

使用 Azure[流量管理員][trafficmanager]，可能不按比例縮放 WordPress 網站跨多個地理區域時的訪客提供一個 URL。 所有的訪客有透過流量管理員，並會被傳送到根據負載平衡設定的區域。

![Azure web 應用程式]，存放在多個區域中，使用 CDBR 可用性路由器將路由傳送至 MySQL 各區域][multi-region-diagram]

每個地區，WordPress 網站仍會橫跨多個 Web 應用程式的執行個體，但此縮放地區特定;可以在不同低流量的縮放高流量區域。

複製及路由至多個 MySQL 資料庫，其實會使用 ClearDB 的[CDBR 高可用性路由器][ cleardbscale] （顯示左） 或[MySQL 叢集 CGE][cge]。

### <a name="multi-region-deployment-with-media-storage-and-caching"></a>媒體儲存與快取多區域部署
如果網站接受上傳或主機媒體檔案，請使用 Azure Blob 儲存體。 如果您需要快取，請考慮[Redis 快取][rediscache]， [Memcache 雲端](http://azure.microsoft.com/gallery/store/garantiadata/memcached/)、 [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/)，或其中一個[Azure 存放區](http://azure.microsoft.com/gallery/store/)中的其他快取方案。

![裝載於在多個區域中，使用 MySQL，管理快取、 Blob 儲存體，與 CDN CDBR 可用性路由器 Azure web 應用程式][performance-diagram]

Blob 儲存體是地理分佈於目前根據預設，區域，因此您不必擔心複寫所有網站的檔案。 您也可以啟用 Azure[內容分配網路 」 (CDN)] [ cdn] Blob 儲存空間，其分配結束節點往您的訪客的檔案。

### <a name="planning"></a>規劃

#### <a name="additional-requirements"></a>其他需求

若要執行此動作... | 使用此...
------------------------|-----------
**上傳或可以儲存大型檔案** | [使用 Blob 儲存體 WordPress 外掛程式][storageplugin]
**傳送電子郵件** | [SendGrid] [storesendgrid]和[使用 SendGrid WordPress 外掛程式][sendgridplugin]
**自訂網域名稱** | [Azure 應用程式服務中設定自訂網域名稱][customdomain]
**HTTPS** | [啟用 HTTPS Azure 應用程式服務中的 web 應用程式][httpscustomdomain]
**生產前驗證** | [設定臨時環境中 Azure 應用程式服務的 web 應用程式][staging] <p>切換 web app 從臨時生產也會移動 WordPress 設定。 您應該確認所有設定都生產切換分段應用程式之前，先都更新生產應用程式的需求。</p>
**監控與疑難排解** | [啟用診斷記錄的 web 應用程式中 Azure 應用程式服務][log]和[Azure 應用程式服務中的 [監視器 Web 應用程式][monitor]
**部署您的網站** | [部署 Azure 應用程式服務中的 web 應用程式][deploy]

#### <a name="availability-and-disaster-recovery"></a>可用性及損毀的復原

若要執行此動作... | 使用此...
------------------------|-----------
**負載平衡網站**或**地理-發佈網站** | [路由流量與 Azure 流量管理員][trafficmanager]
**備份及還原** | [備份 Azure 應用程式服務中的 web 應用程式][backup]及[還原 Azure 應用程式服務中的 web 應用程式][restore]

#### <a name="performance"></a>效能

在雲端的效能達成主要是透過快取及擴充;不過應視為記憶體、 頻寬，以及其他屬性的主機服務的 Web 應用程式。

若要執行此動作... | 使用此...
------------------------|-----------
**了解應用程式服務執行個體功能** | [定價詳細資料，包括應用程式服務層的功能][websitepricing]
**快取資源** | [Redis 快取][rediscache]， [Memcache 雲端](/gallery/store/garantiadata/memcached/)、 [MemCachier](/gallery/store/memcachier/memcachier/)，或其中一個[Azure 存放區](/gallery/store/)中的其他快取方案
**調整您的應用程式** | [Azure 應用程式服務中調整 web app] [websitescale]及[ClearDB 高的可用性路由][cleardbscale]。 如果您選擇裝載和管理您自己的 MySQL 安裝，您應該考慮[MySQL 叢集 CGE] [cge]的擴充

#### <a name="migration"></a>移轉

有兩種方法可以將現有的 WordPress 網站移轉到 Azure 應用程式服務。

* ** [WordPress 匯出][ export] ** -這會匯出您的部落格，然後可匯入到新的 WordPress 網站上使用[WordPress 匯入工具外掛程式]Azure 應用程式服務內容[import]。

    > [AZURE.NOTE] 雖然這個程序可讓您移轉您的內容，它不會移轉任何增益集、 佈景主題] 或其他自訂作業。 這些必須再次手動安裝。

* **手動移轉** - [備份您的網站][wordpressbackup]與[資料庫][wordpressdbbackup]]，以手動方式將它還原為 Azure 應用程式服務中的 web 應用程式，並相關聯，移轉高度自訂的網站並避免手動安裝外掛程式，佈景主題及其他自訂作業 tedium MySQL 資料庫。

## <a name="step-by-step-instructions"></a>逐步指示

### <a name="create-a-wordpress-site"></a>建立 WordPress 網站

1. 使用[Azure Marketplace] [cdbnstore]識別[架構及規劃](#planning)在區段中，您會裝載您的網站區域中建立 MySQL 資料庫的大小。

2. 依照[建立 Azure 應用程式服務中的 WordPress web 應用程式]中的步驟[createwordpress]建立 WordPress web 應用程式。 時建立 web 應用程式，請選取 [**使用現有的 MySQL 資料庫**，然後選取 [在步驟 1 建立的資料庫。

如果您要移轉現有 WordPress 網站，請參閱[將現有的 WordPress 網站至 Azure](#Migrate-an-existing-WordPress-site-to-Azure)之後建立新的 web 應用程式。

### <a name="migrate-an-existing-wordpress-site-to-azure"></a>將現有的 WordPress 網站移轉到 Azure

[[架構與規劃](#planning)] 區段中所述，有兩種方法可以移轉 WordPress 網站。

* **匯出與匯入**-網站不太多自訂項目，或您只想移動的內容。

* **備份與還原**-網站的自訂作業與您要移動所有項目。

使用其中一個下列各節，將您的網站。

#### <a name="the-export-and-import-method"></a>匯出與匯入的方法

1. 使用 [[匯出 WordPress] [export]匯出您現有的網站。

2. 建立 web 應用程式使用 [[建立 WordPress 網站](#Create-a-new-WordPress-site)] 區段中的步驟。

3. 登入您 WordPress 的網站上的 Web 應用程式，並按一下 [**增益集** -> **新增]**。 搜尋，並安裝， **WordPress 匯入工具**外掛程式。

4. 匯入工具外掛程式安裝完成後，按一下 [**工具]** -> **匯入**，]，然後選取**WordPress**為使用 WordPress 匯入工具外掛程式。

5. 在 [**匯入 WordPress** ] 頁面上，按一下 [**選擇 [檔案**]。 瀏覽至匯出您現有的 WordPress 網站上的 WXR 檔案，然後選擇 [**上傳檔案並匯入**。

6. 按一下 [**送出**]。 系統會提示您匯入成功。

8. 當您完成所有步驟時，以重新啟動您的網站，從 web 應用程式刀[Azure 入口網站][mgmtportal]。

匯入之後網站，您可能需要執行下列步驟，以啟用設定不包含在匯入的檔案。

如果您使用此... | 執行此動作...
------------------ | ----------
**永久連結** | 從新網站的 [WordPress 儀表板中，按一下 [**設定** -> **永久連結**，然後更新永久結構
**圖像/媒體連結** | 若要更新連結至新的位置，請使用 [ [Velvet 則藍色更新 Url 外掛程式][velvet]，搜尋及取代的工具，或以手動方式在您的資料庫
**佈景主題** | 移至 [**外觀** -> **佈景主題**和更新視網站佈景主題
**功能表** | 如果您的佈景主題支援功能表，您的首頁上的連結可能仍會有舊的子目錄中內嵌。 移至 [**外觀** -> **功能表**及更新

#### <a name="the-backup-and-restore-method"></a>備份與還原的方法

1. 備份您的現有 WordPress 網站使用[WordPress 備份]資訊[wordpressbackup]。

2. 備份您現有的資料庫，在[備份您的資料庫]中使用的資訊[wordpressdbbackup]。

3. 建立資料庫及還原的備份。

    1. 從[Azure Marketplace]購買新的資料庫[cdbnstore]，或設定[Windows]上的 MySQL 資料庫[mysqlwindows]或[Linux] [ mysqllinux] VM。

    2. 使用 MySQL 用戶端，例如[MySQL 工作][workbench]、 連線至新的資料庫，及匯入 WordPress 資料庫。

    3. 更新資料庫，變更您的新 Azure 應用程式服務網域的網域項目。 例如，mywordpress.azurewebsites.net。 使用[搜尋及取代 WordPress 資料庫指令碼][searchandreplace]安全地變更所有執行個體。

4. Azure 入口網站中建立 web 應用程式，並發佈 WordPress 備份]。

    1. [Azure 入口網站]中建立 web 應用程式[mgmtportal]以使用**新**的資料庫 -> **Web + 行動** -> **Azure Marketplace** -> **Web 應用程式** -> **Web 應用程式 + SQL** （或**Web 應用程式 + MySQL**）]-> [**建立**。 設定所有必要的設定，以建立空白的 web 應用程式。

    2. 在您 WordPress 的備份，找出**wp config.php**檔案並在編輯器中開啟。 下列項目取代為新 MySQL 資料庫的資訊。

        * **DB_NAME**資料庫的使用者名稱

        * **DB_USER**用來存取資料庫的使用者名稱

        * **DB_PASSWORD**使用者的密碼

        在之後變更這些項目，請儲存並關閉**wp config.php**檔案。

    3. 使用[Azure 應用程式服務中的 web 應用程式部署][deploy]您想要使用，，然後再部署到 web 應用程式 Azure 應用程式服務中的 [WordPress 備份的資訊來啟用部署方法。

5. 已部署 WordPress 網站，您應該可以存取新的網站 （如應用程式服務 web 應用程式） 使用 *。 azurewebsite.net 網站的 URL。

### <a name="configure-your-site"></a>設定您的網站

已建立或移轉 WordPress 網站之後，請使用下列資訊來改善效能或啟用其他功能。

若要執行此動作... | 使用此...
------------- | -----------
**設定應用程式服務方案模式、 大小和縮放比例啟用** | [不按比例縮放 Azure 應用程式服務中的 web 應用程式][websitescale]
**啟用持續的資料庫連線** | 根據預設，WordPress 不使用持續的資料庫連線]，可能會導致要成為經流速控制多個連線後的資料庫的連線。 若要啟用持續的連線，安裝 （持續連線介面卡外掛程式） [https://wordpress.org/plugins/persistent-database-connection-updater/installation/]。 
**提升效能** | <ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">停用 ARR cookie</a> -可改善效能，多個 Web 應用程式執行個體上執行 WordPress 時</p></li><li><p>啟用快取。 <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Redis 快取</a>可利用<a href="https://wordpress.org/plugins/redis-object-cache/">Redis 物件快取 WordPress 外掛程式</a>，或使用其中一個從<a href="/gallery/store/">Azure 市集</a>的其他快取方案使用 （預覽版本）</p></li><li><p>預設會啟用<a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">如何讓 WordPress 運作更快速的 Wincache</a> Wincache Web 應用程式</p></li><li><p><a href="../web-sites-scale/">縮放比例 Azure 應用程式服務中的 web 應用程式</a>和<a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB 高的可用性路由</a>或<a href="http://www.mysql.com/products/cluster/">MySQL 叢集 CGE</a></p></li></ul>
**使用 blob 儲存體** | <ol><li><p><a href="../storage-create-storage-account/">建立 Azure 儲存體帳戶</a></p></li><li><p>瞭解如何<a href="../cdn-how-to-use/">使用內容分配網路 」 (CDN)</a>至地理-發佈二進位大型物件中儲存的資料。</p></li><li><p>安裝和設定<a href="https://wordpress.org/plugins/windows-azure-storage/">WordPress 外掛程式 Azure 儲存空間</a>。</p><p>詳細的安裝及設定外掛程式的資訊，請參閱<a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">使用者指南</a>。</p> </li></ol>
**啟用電子郵件** | 啟用<a href="https://azure.microsoft.com/en-us/marketplace/partners/sendgrid/sendgrid-azure/">SendGrid</a>使用 Azure 存放區。 安裝 WordPress <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified">SendGrid 外掛程式</a>。
**設定自訂網域名稱** | [Azure 應用程式服務中設定自訂網域名稱][customdomain]
**啟用 HTTPS 的自訂網域名稱** | [啟用 HTTPS Azure 應用程式服務中的 web 應用程式][httpscustomdomain]
**負載平衡或地理-發佈您的網站** | [路由流量與 Azure 流量 Manager][trafficmanager]。 如果您使用自訂網域，請參閱[Azure 應用程式服務中的自訂網域名稱設定][customdomain]的流量管理員使用的自訂網域名稱的詳細資訊
**啟用自動的備份** | [備份 Azure 應用程式服務中的 web 應用程式][backup]
**啟用診斷記錄** | [啟用診斷記錄 Azure 應用程式服務中的 web 應用程式][log]

## <a name="next-steps"></a>後續步驟

* [WordPress 最佳化](http://codex.wordpress.org/WordPress_Optimization)

* [將 WordPress 轉換成 Multisite Azure 應用程式服務](web-sites-php-convert-wordpress-multisite.md)

* [ClearDB 升級 Azure 精靈](http://www.cleardb.com/store/azure/upgrade)

* [Web 應用程式中 Azure 應用程式服務的子資料夾中的主機服務 WordPress](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [逐步資訊︰ 建立使用 Azure WordPress 網站](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [主控您現有的 WordPress 部落格上 Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [啟用 WordPress 中的精美永久連結](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [如何移轉和 Azure 應用程式服務上執行您的 WordPress 部落格](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [如何免費執行 WordPress Azure 應用程式服務](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress 上 Azure 在 2 分鐘之內](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [將 WordPress 部落格移至 Azure-第 1 部分︰ 建立 Azure WordPress 部落格](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [將 WordPress 部落格移至 Azure-第 2 部分︰ 傳送您的內容](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [將 WordPress 部落格移至 Azure-第 3 部分︰ 設定您的自訂網域](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [將 WordPress 部落格移至 Azure-第 4 部分︰ 很永久和 URL 重新寫入的規則](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [將 WordPress 部落格移至 Azure-第 5 部分︰ 從子資料夾移到根目錄](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [如何設定 Azure 帳戶中的 WordPress web 應用程式](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [設定上 Azure WordPress propping](http://www.johnpapa.net/wordpress-on-azure/)

* [Azure WordPress 的秘訣](http://www.johnpapa.net/azurecleardbmysql/)

>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

## <a name="whats-changed"></a>變更的項目
* 若要變更的指南，從網站應用程式服務請參閱︰ [Azure 應用程式服務與程式影響現有 Azure 服務](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- URL List -->

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: ../traffic-manager/traffic-manager-overview.md
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: https://azure.microsoft.com/documentation/services/redis-cache/
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]: ../virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md
[mysqllinux]: ../virtual-machines/virtual-machines-linux-classic-mysql-on-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: web-sites-php-web-site-gallery.md
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: web-sites-deploy.md
[posh]: ../powershell-install-configure.md
[Azure CLI]: ../xplat-cli-install.md
[storesendgrid]: https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/
[cdn]: ../cdn/cdn-overview.md
