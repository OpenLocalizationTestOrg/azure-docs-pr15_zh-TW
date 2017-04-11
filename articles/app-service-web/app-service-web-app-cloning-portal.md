<properties
    pageTitle="Web 應用程式複製使用 Azure 入口網站"
    description="瞭解如何複製您的 Web 應用程式，使用 Azure 入口網站的新 Web 應用程式。"
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/08/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-app-cloning-using-azure-portal"></a>Azure 應用程式服務應用程式，請使用 Azure 入口網站#

[Azure 應用程式服務 Web 應用程式](http://go.microsoft.com/fwlink/?LinkId=529714)中的複製功能可讓您輕鬆地複製至新建立的應用程式在不同區域中，或在同一個區域中的現有 web 應用程式。 這樣會啟用部署跨不同區域的數字的應用程式，快速且輕鬆地與客戶。

應用程式複製目前是只支援進階版層應用程式服務方案。 新功能使用相同的限制為 Web 應用程式備份的功能，請參閱[備份 Azure 應用程式服務中的 web 應用程式](web-sites-backup.md)。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 


## <a name="cloning-an-existing-app"></a>複製現有的應用程式 ##

Web 應用程式必須執行**進階**模式中，您才能建立 web 應用程式的複本。

1. 在[Azure 入口網站](https://portal.azure.com/)中，開啟您的 web 應用程式刀。
2. 按一下 [**工具**]。 然後在**工具**刀中，按一下 [**複製應用程式**。

    ![][1]

    > [AZURE.NOTE]
    > 如果 web 應用程式尚未**進階**模式中，您會收到訊息，指出複製應用程式的支援的模式。 此時，您必須選取**升級**的選項。
    
3. 在**複製應用程式**刀提供新的 web 應用程式、 資源] 群組中，及應用程式服務方案的名稱。 使用者也都能選擇是否要複製的來源 web 應用程式設定數字或不。

    ![][2]

4. 按一下 [**建立**] 後的平台會建立來源 web 應用程式複製開始工作。

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>複製現有的應用程式到應用程式服務環境##

在**複製應用程式**刀客戶會有現有的應用程式服務環境中選擇一個應用程式集區。

## <a name="current-restrictions"></a>目前的限制 ##

此功能是目前預覽中，我們正在新增一段時間的新功能，下列清單會於 Azure 入口網站中複製的應用程式目前支援已知的限制︰

- 不會複製 azure 的流量管理員設定
- 不會複製自動縮放設定
- 備份排程設定不會複製
- 不會複製 VNET 設定
- 應用程式的深入見解會無法自動設定目的地 web app
- 不會複製輕鬆驗證設定
- 不會複製 kudu 副檔名
- 秘訣規則不會複製
- 不會複製資料庫內容


### <a name="references"></a>參照 ###
- [使用 PowerShell web 應用程式複製](app-service-web-app-cloning.md)
- [備份 Azure 應用程式服務中的 web 應用程式](web-sites-backup.md)
- [如何建立的應用程式服務環境](app-service-web-how-to-create-an-app-service-environment.md)
- [在應用程式服務環境中建立 web 應用程式](app-service-web-how-to-create-a-web-app-in-an-ase.md)
- [應用程式服務環境簡介](app-service-app-service-environment-intro.md)

<!--Image references-->
[1]: ./media/app-service-web-app-cloning-portal/CloningBlade.png
[2]: ./media/app-service-web-app-cloning-portal/CloneSettings.png