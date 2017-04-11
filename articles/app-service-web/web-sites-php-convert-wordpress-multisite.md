<properties 
    pageTitle="將 WordPress 轉換成 Multisite Azure 應用程式服務" 
    description="瞭解如何採取透過 Azure 中庫中建立的現有 WordPress web 應用程式，並將它轉換成 WordPress Multisite" 
    services="app-service\web" 
    documentationCenter="php" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>



# <a name="convert-wordpress-to-multisite-in-azure-app-service"></a>將 WordPress 轉換成 Multisite Azure 應用程式服務

## <a name="overview"></a>概觀

*[在精靈 Lobaugh]以[ben-lobaugh]， [Microsoft 開啟技術 Inc.][ms-open-tech]*

在本教學課程中，您將學習如何透過 Azure 中庫中建立的現有 WordPress web 應用程式並將其轉換成 WordPress Multisite 安裝。 此外，您將學習如何在您安裝的子網站的每個指定自訂網域。

假設您有 WordPress 的現有安裝。 如果您沒有，請依照[建立 WordPress 網站從 Azure 中圖庫]中提供的指導方針[website-from-gallery]。

轉換的現有 WordPress 通常是相當簡單的單一網站安裝 Multisite 和許多初始步驟來自直接[建立的網路][ wordpress-codex-create-a-network] [WordPress Codex](http://codex.wordpress.org)上的頁面。

我們開始吧。

## <a name="allow-multisite"></a>允許 Multisite

您必須先啟用透過 Multisite`wp-config.php`檔案與**WP\_允許\_MULTISITE**常數。 若要編輯您的 web 應用程式檔案的兩種方法︰ 第一個是透過 FTP，以及透過給第二個。 如果您不熟悉如何設定這兩種方法，請參閱下列教學課程︰

* [MySQL 與 FTP PHP 網站][website-w-mysql-and-ftp-ftp-setup]

* [MySQL 與給 PHP 網站][website-w-mysql-and-git-git-setup]

開啟`wp-config.php`檔案與您所選擇的編輯器] 和 [新增上面下列`/* That's all, stop editing! Happy blogging. */`線條。

    /* Multisite */

    define( 'WP_ALLOW_MULTISITE', true );

請務必儲存檔案，並將它上傳至伺服器 ！

## <a name="network-setup"></a>網路設定

登入中*wp 管理員*區域，您的 web 應用程式，您應該會看到稱為**網路設定**的 [**工具**] 功能表下的新項目。 按一下 [**網路設定]** ，然後填入您的網路的詳細資料。

![網路設定畫面][wordpress-network-setup]

本教學課程中使用*子目錄*網站結構描述，因為它應該一律可以運作，而且我們將會設定為每一個子網站的自訂網域稍後的教學課程。 不過，應該能夠子網域安裝如果您將對應透過[Azure 入口網站](https://portal.azure.com)及設定萬用字元 DNS 網域正確的設定。

如需有關的子網域與子目錄安裝查看[類型的多站台網路][ wordpress-codex-types-of-networks] WordPress Codex 文章。

## <a name="enable-the-network"></a>啟用網路

網路現在已設定在資料庫中，但有一個剩餘的步驟，以啟用網路功能。 完成`wp-config.php`設定，並確保`web.config`正確路由每個網站。


按一下 [*網路設定*] 頁面上的 [**安裝**] 按鈕之後, WordPress 將嘗試更新`wp-config.php`和`web.config`檔案。 不過，您應該一律檢查以確保更新已成功的檔案。 如果沒有，此畫面會為您提供必要的更新。 編輯並儲存檔案。


後進行的更新您必須登出再登回 wp 管理員儀表板。

現在應該額外的功能表上 [列標籤的**「 我的網站**管理員。 此功能表可讓您控制您新的網路，透過**網路管理員**儀表板。

## <a name="adding-custom-domains"></a>新增自訂網域

[WordPress 記憶網域對應][wordpress-plugin-wordpress-mu-domain-mapping]外掛程式可讓您輕鬆地將自訂網域新增至您的網路中的任何網站。 為了讓外掛程式正常運作，您需要執行一些額外的安裝在入口網站，並在您的網域註冊機構。

## <a name="enable-domain-mapping-to-the-web-app"></a>啟用網域對應至 web 應用程式

**免費**的[應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714)方案模式不支援的自訂網域新增至 Web 應用程式。 您需要切換至 [**共用**] 或 [**標準**] 模式。 若要執行這項操作︰

* 登入 Azure 入口網站，並找出您的 web 應用程式。 
* 按一下 [**設定**] 中，**不按比例縮放**] 索引標籤上。
* 選取 [**一般**] 下的 [*共用*] 或 [*標準*
* 按一下 [**儲存**]

您可能會收到訊息，詢問您確認變更，並確認您的 web 應用程式現在可能需支付費用中，使用情況和您設定的設定而定。

花幾秒鐘處理新的設定，因此現在可以開始設定您的網域的好時機。

## <a name="verify-your-domain"></a>驗證您的網域

Azure Web 應用程式可讓您將對應至網站的網域之前，您必須驗證您擁有的授權對應網域。 若要這麼做，您必須將新的 CNAME 記錄新增至您的 DNS 項目。

* 登入您的網域 DNS 管理員
* 建立新的 CNAME *awverify*
* 指向*awverify *awverify* 。YOUR_DOMAIN.azurewebsites.net*

可能需要一些時間 DNS 變更要進入完整的效果，因此如果下列步驟無效，請請一杯咖啡，然後完假回來後再試。

## <a name="add-the-domain-to-the-web-app"></a>將網域新增至 web 應用程式

返回您的 web 應用程式，透過 Azure 入口網站，按一下 [**設定**]，再按一下 [ **SSL] 和自訂網域**。

當*SSL 設定*顯示時，您會看到您會在其中輸入您要指派給您的 web 應用程式的所有網域的欄位。 如果此處未列出的網域，就會無法使用的對應內 WordPress，不論如何網域 DNS 的設定。

![管理自訂網域] 對話方塊][wordpress-manage-domains]

在 [文字] 方塊中輸入您的網域，Azure 將確認您先前建立的 CNAME 記錄。 如果沒有完全散佈 DNS，就會出現紅色指示器。 如果已順利完成，您會看到綠色核取記號。 

請注意] 對話方塊的底部列出的 IP 位址。 您會需要此設定 A 記錄，您的網域。

## <a name="setup-the-domain-a-record"></a>設定的網域一筆記錄

如果其他步驟成功，您現在可能會將網域指派給 DNS A 記錄透過 Azure web 應用程式。 

請務必請注意以下 Azure web 應用程式接受 CNAME 和記錄，不過您*必須*使用 A 記錄，以啟用適當的網域對應。 CNAME 不能轉接到另一個 CNAME，這是什麼 Azure 為您建立的 YOUR_DOMAIN.azurewebsites.net。

使用上述步驟中的 IP 位址，請返回您的 DNS 管理員，並設定 A 記錄以指向該 IP。


## <a name="install-and-setup-the-plugin"></a>安裝及設定外掛程式

WordPress Multisite 目前沒有內建的方法，將對應的自訂網域。 然而，有一部分[WordPress 記憶網域對應]外掛程式[wordpress-plugin-wordpress-mu-domain-mapping]的新增功能。 登入您的網站的網路管理員部分，並安裝**WordPress 記憶網域對應**的外掛程式。

安裝和啟動外掛程式，請造訪**設定**後 > 設定外掛程式的**網域對應**。 在第一個文字方塊中，[*伺服器 IP 位址*] 中，輸入您用來設定 A 記錄之網域的 IP 位址。 設定您想要的任何*網域選項*（預設值是通常正常），按一下 [**儲存**]。

## <a name="map-the-domain"></a>對應網域

請造訪**儀表板**您想要對應至該網域的網站。 按一下 [**工具]** > **網域對應**] 文字方塊中輸入新的網域，並按一下 [**新增**]。

根據預設，會自動產生網站網域重寫新網域]。 如果您想讓所有流量傳送至新的網域，核取之前儲存的*主要網域此部落格*] 方塊。 您可以新增的網域數量至網站，但僅有一個可以主要。

## <a name="do-it-again"></a>一次

Azure Web 應用程式可讓您將數量的網域新增至 web 應用程式。 若要新增另一個網域您將需要執行**驗證網域**及**設定網域記錄**的節，針對每個網域。  

>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

## <a name="whats-changed"></a>變更的項目
* 若要變更的指南，從網站應用程式服務請參閱︰ [Azure 應用程式服務與程式影響現有 Azure 服務](http://go.microsoft.com/fwlink/?LinkId=529714)

[ben-lobaugh]: http://ben.lobaugh.net
[ms-open-tech]: http://msopentech.com
[website-from-gallery]: https://www.windowsazure.com/develop/php/tutorials/website-from-gallery/
[wordpress-codex-create-a-network]: http://codex.wordpress.org/Create_A_Network
[website-w-mysql-and-ftp-ftp-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
[website-w-mysql-and-git-git-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-git/#header-1
[wordpress-network-setup]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
[wordpress-codex-types-of-networks]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
[wordpress-plugin-wordpress-mu-domain-mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/

[wordpress-manage-domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png

 
