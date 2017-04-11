<properties
    pageTitle="從 Azure Marketplace 建立 web 應用程式 |Microsoft Azure"
    description="瞭解如何使用來建立新的 WordPress web 應用程式從 Azure Marketplace Azure 入口網站。"
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

# <a name="create-a-web-app-from-the-azure-marketplace"></a>建立 web 應用程式從 Azure Marketplace

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Azure Marketplace 讓許多熱門的 web 應用程式開發 Microsoft、 協力廠商及開啟來源軟體計劃。 例如，WordPress、 Umbraco CMS Drupal，等。這些 web 應用程式的內建在大範圍的常用的架構，例如[PHP]此 WordPress 範例、 [.NET]、 [Node.js]、 [Java]，和[Python]等等。 若要建立 web 應用程式從 Azure Marketplace 所需的唯一軟體是您用於[Azure 入口網站]瀏覽器。

在此教學課程中，您將學習如何︰

* 尋找並建立 web 應用程式中 Azure Azure Marketplace 範本為基礎的應用程式服務。
* 設定新的 web 應用程式的 Azure 應用程式服務設定。
* 啟動和管理您的 web 應用程式。

針對此教學課程中，您會部署從 Azure Marketplace WordPress 部落格網站。 當您完成步驟在本教學課程中時，您必須設定您自己 WordPress 網站和雲端中執行。

![範例 WordPress wep 應用程式儀表板][WordPressDashboard1]

您會在此教學課程中部署 WordPress 網站使用 MySQL 資料庫。 如果您想要改為使用資料庫的 [SQL 資料庫，請參閱[專案 Nami]，也可以透過 Azure Marketplace。

> [AZURE.NOTE]
> 若要完成此教學課程中，您需要 Microsoft Azure 帳戶。 如果您沒有帳戶，您可以在[啟動您的 Visual Studio 訂閱權益][activate]或[註冊免費試用版][free trial]。
>
> 如果您想要快速入門 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務]。 從這裡您可以立即短暫入門 web app 中建立應用程式服務，沒有信用卡，而且有沒有承諾。

## <a name="find-and-create-a-web-app-in-azure-app-service"></a>尋找和 Azure 應用程式服務中建立 Web 應用程式

1. [Azure 入口網站]登入。

1. 按一下 [**新增**]。
    
    ![建立新的 Azure 資源][MarketplaceStart]
    
1. 搜尋**WordPress**，，然後按一下 [ **WordPress**。 （如果您想要使用 SQL 資料庫，而不是 MySQL，搜尋**Project Nami**。）

    ![WordPress 市場中搜尋][MarketplaceSearch]
    
1. 讀取之後 WordPress 應用程式的說明，請按一下 [**建立**]。

    ![建立 WordPress web 應用程式][MarketplaceCreate]

## <a name="configure-azure-app-service-settings-for-your-new-web-app"></a>設定新的 Web 應用程式的 Azure 應用程式服務設定

1. 建立新 web 應用程式之後，WordPress 設定刀就會顯示，可用來完成下列步驟︰

    ![設定 WordPress web 應用程式設定][ConfigStart]

1. 在**Web 應用程式**] 方塊中輸入的 web 應用程式的名稱。

    此名稱必須是唯一的 azurewebsites.net 網域，因為 web 應用程式的 URL 會*{名稱}*。 azurewebsites.net。 如果您輸入的名稱不是唯一的一個紅色驚嘆號會出現在 [文字] 方塊中。

    ![設定 WordPress web 應用程式的名稱][ConfigAppName]

1. 如果您有多個訂閱，請選擇您想要使用的項目。 

    ![設定 web 應用程式的訂閱][ConfigSubscription]

1. 選取**資源群組**或建立新的項目。

    如需有關資源群組的詳細資訊，請參閱[Azure 資源管理員概觀][ResourceGroups]。

    ![設定 web 應用程式的 [資源] 群組][ConfigResourceGroup]

1. 選取**應用程式服務方案/位置**，或建立新的項目。

    如需有關應用程式服務方案的詳細資訊，請參閱[Azure 應用程式服務方案概觀][AzureAppServicePlans]。 

    ![設定 web 應用程式的服務計劃][ConfigServicePlan]

1. 按一下**資料庫**]，然後在**新的 MySQL 資料庫**刀設定 MySQL 資料庫中提供必要的值。

    。 輸入新的名稱或保留預設的名稱。

    b。 將**資料庫類型**設為**共用**。

    c。 選擇您選擇的 web 應用程式的相同位置。

    d。 選擇 [價格層]。 在此教學課程-這是免費的最小的連線和磁碟空間-**水星**很好的。

    e。 在**新 MySQL 資料庫**刀中，接受法律合約，然後再按一下**[確定]**。 

    ![設定 web 應用程式的資料庫設定][ConfigDatabase]

1. 在**WordPress**防禦，以接受法律合約，，然後按一下 [**建立**]。 

    ![完成的 web 應用程式設定，並按一下 [確定]][ConfigFinished]

    Azure 應用程式服務會建立 web 應用程式時，通常是一分鐘。 您可以觀看進度鐘形圖示，在入口網站頁面頂端] 即可。

    ![進度指示器][ConfigProgress]

## <a name="launch-and-manage-your-wordpress-web-app"></a>啟動和管理您的 WordPress web 應用程式
    
1. 建立 web 應用程式完成時，瀏覽至您建立的應用程式時，資源群組 Azure 入口網站中，您可以看見 web 應用程式和資料庫。

    電燈泡圖示額外的資源是[應用程式的深入見解][ApplicationInsights]，其中提供監視服務 web 應用程式。

1. 在 [**資源群組**刀中，按一下 [web 應用程式列]。

    ![選取您 WordPress web 應用程式][WordPressSelect]

1. 在 Web 應用程式刀中，按一下 [**瀏覽**]。

    ![瀏覽至您的 WordPress web 應用程式][WordPressBrowse]

1. 如果系統提示您選取 WordPress 部落格的語言，請選取您想要的語言，然後按一下 [**繼續]**。

    ![設定您的 WordPress web 應用程式的語言][WordPressLanguage]

1. 在 [WordPress**歡迎使用**] 頁面，輸入 WordPress，所需的設定資訊，然後再按一下 [**安裝 WordPress**。

    ![設定您的 WordPress web 應用程式][WordPressConfigure]

1. 使用您在 [**歡迎**] 頁面建立的認證登入。  

1. 您的網站 」 儀表板頁面會開啟，並顯示您所提供的資訊。    

    ![檢視 WordPress 儀表板][WordPressDashboard2]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已經看到如何建立及部署從 Azure Marketplace 範例 web 應用程式。

如需有關如何使用應用程式服務 Web 應用程式的詳細資訊，請參閱的連結 （適用於寬的瀏覽器視窗） 頁面的左側或頂端的頁面 （適用於較窄的瀏覽器視窗中）。

如需開發上 Azure WordPress web 應用程式的詳細資訊，請參閱[Azure 應用程式服務的開發 WordPress][WordPressOnAzure]。 

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[Node.js]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[activate]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[free trial]: https://azure.microsoft.com/pricing/free-trial/
[請嘗試應用程式服務]: http://go.microsoft.com/fwlink/?LinkId=523751
[ResourceGroups]: ../resource-group-overview.md
[AzureAppServicePlans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[Azure 入口網站]: https://portal.azure.com/
[專案 Nami]: http://projectnami.org/
[WordPressOnAzure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPressDashboard2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png
