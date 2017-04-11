<properties 
    pageTitle="如何建立的應用程式服務環境" 
    description="建立應用程式服務環境流程描述" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="stefsch" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/22/2016" 
    ms.author="ccompy"/>

# <a name="how-to-create-an-app-service-environment"></a>如何建立的應用程式服務環境 #

### <a name="overview"></a>概觀 ###

應用程式服務環境 (ASE) 所提供的不是用於多租用戶戳記增強型的設定功能的 Azure 應用程式服務的進階版服務] 選項。  基本上 ASE 功能會將客戶的虛擬網路部署 Azure 應用程式服務。  若要取得更深入瞭解閱讀的[什麼是應用程式服務環境]的應用程式服務環境所提供的功能[WhatisASE]文件。

### <a name="before-you-create-your-ase"></a>建立您 ASE 之前 ###

請務必留意的您無法變更的項目。  建立後，您無法變更您 ASE 瞭解這些方面有︰

- 位置
- 訂閱
- 資源群組
- 使用 VNet
- 使用子網路 
- 子網路大小

挑選 VNet，以及指定子網路，請確定時夠大，以納入其間的任何未來的成長。  

### <a name="creating-an-app-service-environment"></a>建立應用程式服務環境 ###

有兩種方式存取 ASE 建立使用者介面。  可以找到搜尋中 Azure 服務商場***應用程式服務環境***，或透過新增網頁]-> [+ Mobile]-> [應用程式服務環境。  若要建立 ASE:

1. 提供您 ASE 的名稱。  指定 ASE 的名稱會用於 ASE 中建立的應用程式。  如果名稱 ASE appsvcenvdemo 就會在子網域名稱。*appsvcenvdemo.p.azurewebsites.net*。  如果您因此建立名為*mytestapp* ，然後就會在*mytestapp.appsvcenvdemo.p.azurewebsites.net*定址應用程式。  您無法在 [名稱] 您 ASE 使用空格。  如果您在名稱中使用大寫字元的網域名稱會是該名稱的總小寫版本。  如果您是使用 ILB 然後 ASE 名稱不是您的子網域，但改為明確指出 ASE 建立期間

    ![][1]

2. 選取您的訂閱。  您 ASE 所用的訂閱是也會以建立該 ASE 中的所有應用程式。  您無法將您 ASE 放在位於另一個訂閱 VNet

3. 選取或指定新的資源群組]。  資源群組用於您 ASE 必須相同用於您 VNet。  如果您選取現有的 VNet 您 ASE 資源群組選取項目就會更新以反映您 VNet。

    ![][2]

4. 您選妥虛擬網路和位置。  您可以選擇建立新的 VNet 或選取現有的 VNet。  如果您選取新的 VNet 然後您可以指定名稱與位置。 新 VNet 會有地址範圍 192.268.250.0/23 和名為**預設**的定義為 192.168.250.0/24 子網路。  您可以也只選取現有的傳統或資源管理員 VNet。  如果您 ASE 可以直接從網際網路 （外部） 存取或使用內部負載平衡器 (ILB)，則會決定 VIP 類型選取範圍。  若要瞭解更多相關資訊閱讀[使用應用程式服務環境內部負載平衡器][ILBASE]。  如果您選取外部 VIP 類型，您可以選取外部系統會建立包含 IPSSL 用途的多少 IP 位址。  如果您選取 [內部您需要指定會使用您 ASE 的子網域。  ASEs 才能部署到 （也就是使用*其中一個*公用位址範圍，*或*RFC1918 地址空格的虛擬網路 私人的地址）。  若要使用公用範圍虛擬網路，請您必須建立提前 VNet。  當您選取現有的 VNet 時，您必須建立一個新的子網路 ASE 建立期間。  **您無法使用的預先建立的子網路，在入口網站。 如果您建立使用資源管理員範本您 ASE，您可以使用現有的子網路建立 ASE。**  若要從範本使用這裡的資訊，[建立應用程式服務環境從範本]建立 ASE[ ILBAseTemplate] ，以下是，[建立範本的 ILB 應用程式服務環境][ASEfromTemplate]。

### <a name="details"></a>詳細資料 ###

2 使用正面與結束和 2 工作者被建立 ASE。  使用正面與結束作為 HTTP/HTTPS 結束點，然後傳送流量工作者會裝載您的應用程式的角色。   您可以調整數量 ASE 建立後，可以偶數設定這些資源集區上的 [自動調整大小規則]。  如需詳細資訊，手動調整，管理及監視的應用程式服務環境請前往這裡︰[如何設定應用程式服務環境][ASEConfig] 

只有一個 ASE 可以存在於 ASE 所使用的子網路。  子網路無法用於 ASE 以外的任何項目

### <a name="after-app-service-environment-creation"></a>在應用程式服務環境建立之後 ###

ASE 建立之後，您可以調整︰

- 前結束的數量 (最小︰ 2)
- 同事的數量 (最小︰ 2)
- 適用於 IP SSL 的 IP 位址的數量
- 計算前結束或同事所使用的資源大小 （前端最小的大小為 P2）


有手動的縮放比例、 管理和監視的應用程式服務環境以下周圍的更多詳細資料︰[如何設定應用程式服務環境][ASEConfig] 

如需的資訊自動縮放有以下指南︰[如何設定自動調整大小的應用程式服務環境][ASEAutoscale]

有其他不適用於儲存資料庫等自訂的相依性。  這些是由 Azure，系統會隨附。  系統儲存空間支援 500 GB 整個應用程式服務環境，並視需要由系統的小數位數 Azure 調整資料庫。


## <a name="getting-started"></a>快速入門
所有的文件及如何-的應用程式服務環境都是在[讀我檔案的應用程式服務環境](../app-service/app-service-app-service-environments-readme.md)中使用。

若要開始使用應用程式服務環境，請參閱[應用程式服務環境簡介][WhatisASE]

如需有關 Azure 應用程式服務平台的詳細資訊，請參閱[Azure 應用程式服務][AzureAppService]。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
[ILBAseTemplate]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-create-ilb-ase-resourcemanager/
