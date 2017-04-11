<properties 
    pageTitle="管理 web 應用程式中 Azure 應用程式服務" 
    description="管理 web 應用程式中 Azure 應用程式服務的資源的連結。" 
    services="app-service\web" 
    documentationCenter="" 
    authors="erikre" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="rachelap"/>

# <a name="manage-a-web-app-in-azure-app-service"></a>管理 web 應用程式中 Azure 應用程式服務

本主題包含管理[Azure 應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714)中的 web 應用程式的資源的連結。 管理包括所有的工作，讓您執行更順暢的 web 應用程式。 

Web 應用程式的存留時間，您將會執行不同的管理工作，當您初次部署從移到正常作業、 維護及更新。

Azure 入口網站中可以執行許多 web 應用程式管理工作。

## <a name="before-you-deploy-your-web-app-to-production"></a>將您的 web 應用程式部署到生產之前

### <a name="choose-a-tier"></a>選擇層

Azure 應用程式服務提供五個層級︰ 空閒、 共用、 基本、 標準和進階版。 功能和價格的每一層的相關資訊，請參閱[定價詳細資料](/pricing/details/app-service/)。 

- [應用程式服務方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)可讓您群組底下的同一層的多個 web 應用程式。
- 您永遠可以[切換層](web-sites-scale.md)之後建立 web 應用程式。

### <a name="configuration"></a>設定

使用[Azure 入口網站](https://portal.azure.com/)設定不同的設定選項。 如需詳細資訊，請參閱[Azure 應用程式服務中的 [設定 web 應用程式](web-sites-configure.md)。 以下是快速的檢查清單︰

- 如有需要請選取**執行階段版本**.NET、 PHP、 Java，或 Python。
- 如果您的 web 應用程式使用 WebSocket 通訊協定，請啟用**WebSockets** 。 （包括使用[ASP.NET SignalR](http://www.asp.net/signalr)或[socket.io](web-sites-nodejs-chat-app-socketio.md)的應用程式）。
- 您執行的連續的 web 工作嗎？ 如果是這樣，啟用**永遠上**。
- 設定**預設文件**，例如 index.html。

除了這些基本設定的設定，您可能會想要設定下列項目︰

- **安全通訊端層 (SSL)**加密。 若要使用 SSL 使用自訂網域名稱，您必須取得 SSL 憑證，並設定您的 web 應用程式使用。 請參閱[啟用 HTTPS web 應用程式中 Azure 應用程式服務](web-sites-configure-ssl-certificate.md)。
- **自訂網域名稱。** 您的 web 應用程式會自動有 azurewebsites.net 底下的子網域。 您可以建立關聯的自訂網域名稱，例如 contoso.com。 請參閱[設定 Azure 應用程式服務中的自訂網域名稱](web-sites-custom-domain-name.md)。

特定語言設定︰

- **PHP**︰[設定 PHP Azure 應用程式服務 Web 應用程式中的](web-sites-php-configure.md)。
- **Python**︰[設定 Python Azure 應用程式服務 Web 應用程式](web-sites-python-configure.md)


## <a name="while-your-web-app-is-running"></a>當正在執行您的 web 應用程式

執行您的 web 應用程式時，您會想要確定可用，但它縮放以符合使用者流量。 您可能也需要疑難排解錯誤。

### <a name="monitoring"></a>監控

- 透過 Azure 入口網站中，您可以[新增效能指標](web-sites-monitor.md)CPU 使用率等的用戶端要求的數字。
- 流量來回應的[縮放比例 web 應用程式](web-sites-scale.md)。 根據您層中，您可以調整 Vm 數及/或 VM 執行個體的大小。 在 [一般] 及 [進階版的階層，您也可以設定自動縮放，讓您的 web app 自動調整固定的排程，或在載入的回應。  
 
### <a name="backups"></a>備份

- 設定[自動備份](web-sites-backup.md)的 web 應用程式。 進一步瞭解在[這段影片](https://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/)中的備份。
- 深入瞭解[資料庫復原](../sql-database/sql-database-business-continuity.md)Azure SQL 資料庫中的選項。

### <a name="troubleshooting"></a>疑難排解

- 如果發生錯誤，您可以[在 Visual Studio 中的疑難排解](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)，請使用診斷記錄和雲端中的即時偵錯。 
- 外部 Visual Studio 中，有幾種方式可以收集診斷記錄。 請參閱[啟用診斷記錄 Azure 應用程式服務中的 web 應用程式](web-sites-enable-diagnostic-log.md)。
- 對於 Node.js 應用程式，請參閱[如何偵錯 Node.js web app 中 Azure 應用程式服務](web-sites-nodejs-debug.md)。

### <a name="restoring-data"></a>還原的資料

- [還原](web-sites-restore.md)先前備份的 web 應用程式。


## <a name="when-you-update-your-web-app"></a>當您更新您的 web 應用程式

如果您有沒有啟用自動備份，您可以建立[手動備份](web-sites-backup.md)。

請考慮使用[分段部署](web-sites-staged-publishing.md)。 這個選項可讓您執行並排顯示的暫存部署發佈更新生產部署。 

如果您使用 Visual Studio 小組服務，您可以設定從來源控制連續部署︰

- [使用小組 Foundation 版本控制 (TFVC)](../cloud-services/cloud-services-continuous-delivery-use-vso.md) 
- [使用給](../cloud-services/cloud-services-continuous-delivery-use-vso-git.md)
 
<!-- Anchors. -->

[Before you deploy your site to production]: #before-you-deploy-your-site-to-production
[While your website is running]: #while-your-website-is-running
[When you update your website]: #when-you-update-your-website

  
