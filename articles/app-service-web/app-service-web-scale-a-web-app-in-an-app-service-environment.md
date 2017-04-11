<properties 
    pageTitle="如何不按比例縮放的應用程式服務環境中的應用程式" 
    description="在應用程式服務的環境中縮放應用程式" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="stefsch" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="ccompy"/>

# <a name="scaling-apps-in-an-app-service-environment"></a>在應用程式服務的環境中的縮放比例應用程式 #

Azure 應用程式服務中有正常三個項目您可以在不按比例縮放︰

- 價格計劃
- 工作者大小 
- 執行個體數目。

在 ASE 中有不需要選取或變更的價格的方案。  以功能已經是寶貴定價功能。  

提供工作者大小解答 ASE 管理員可以指定要用於每個工作者集區計算資源的大小。  這表示有第 4 頁計算資源的工作者集區 1 和背景工作集區 2 與 P1 計算資源，視需要。  他們沒有大小的順序。  如需詳細資訊的大小，以及其價格，請參閱在文件[Azure 應用程式服務價格][AppServicePricing]。  這是應用程式服務環境中保留 web 應用程式與應用程式服務方案的縮放比例選項︰

- 背景工作集區選取範圍
- 執行個體數目

變更項目是透過適當的 UI，顯示您 ASE 裝載於應用程式服務計劃的完成。  

![][1]

您無法擴充您 ASP 除了您 ASP 位於背景工作集區中使用計算資源的數目。  如果您需要計算中該背景工作集區的資源您需要取得您要將他們新增的 ASE 系統管理員。  資訊重新設定您的 ASE 閱讀以下資訊︰[如何設定應用程式服務環境][HowtoConfigureASE]。  您也可以利用新增容量根據排程或指標 ASE 自動調整大小功能。  設定自動調整大小的 ASE 的更多詳細資料環境本身請參閱[如何設定自動調整大小的應用程式服務環境][ASEAutoscale]。

您可以建立多個應用程式使用不同的背景工作集區，從計算資源的服務方案，或者您可以使用相同的背景工作集區。  如果您沒有 (10) 可計算資源工作者集區 1 中，您可以選擇建立一個使用 (6) 計算資源的應用程式服務計劃和第二個應用程式服務規劃的範例使用 (4) 的計算資源。

### <a name="scaling-the-number-of-instances"></a>按比例縮放執行個體的數目 ###

當您第一次建立 web 應用程式的應用程式服務環境中開始 1 的執行個體。  您可以再延伸為應用程式提供其他計算資源的其他執行個體。   

如果您 ASE 有足夠的容量這是很簡單。  移至您的應用程式服務規劃保留您想要不按比例縮放，然後選取 [縮放比例的網站。  這會開啟您可以手動設定您的 ASP 的比例或您 asp 設定自動調整大小規則在 ui。  手動縮放應用程式只將***依比例縮放***為***手動輸入執行個體字數統計***。  從這裡開始將滑桿拖曳至所要的數量或在滑桿旁邊的方塊中輸入。  

![][2] 

自動調整大小的規則中 ASE ASP 運作方式相同以正常方式。  您可以選取 [***依比例***下***CPU 百分比***，並根據 CPU 百分比您 asp 建立自動調整大小的規則，或您可以建立更複雜的規則使用***排程和效能的規則***。  若要查看完整的詳細資料設定自動調整大小使用指南[Azure 應用程式服務中的應用程式不按比例縮放]的以下[AppScale]。 


### <a name="worker-pool-selection"></a>背景工作集區選取範圍 ###

如先前所述，從 ASP UI 存取工作資料庫選取範圍。  開啟您想要不按比例縮放，然後選取 [背景工作集區 ASP 刀。  您會看到所有您已設定您的應用程式服務環境中的背景工作集區。  如果您只有一個背景工作集區然後您只會看到一列的資料庫。  若要變更您 ASP 位於哪些工作者資料庫，您只要選取您想要將應用程式服務計劃 」，移到背景工作集區。  

![][3]

前移動到另一個背景工作集區您 ASP 很重要，請確定您將會有您 asp 適當的容量。  在清單中的 [背景工作集區，不只會工作者集區名稱列，但您也可以查看多少工作人員都可在該背景工作集區中。  請確定有足夠的執行個體使用包含您應用程式服務方案。  如果您需要更多計算中您要移至背景工作集區的資源，然後取得您要將他們新增的 ASE 系統管理員。  

> [AZURE.NOTE] 移動一個工作者資料庫中的 ASP 會導致冷啟動該 ASP 中的應用程式。  這可能會要求您的應用程式為低溫開始新的計算資源時，請執行速度緩慢。  可避免低溫開始使用[應用程式暖功能設定][ AppWarmup] Azure 應用程式服務。  本文所述的應用程式的初始化模組會因為初始化程序也叫用應用程式正在低溫開始新的計算資源時，也適合低溫開始。 

## <a name="getting-started"></a>快速入門

若要開始使用應用程式服務環境，請參閱[如何以建立應用程式服務環境][HowtoCreateASE]

如需有關 Azure 應用程式服務平台的詳細資訊，請參閱[Azure 應用程式服務][AzureAppService]。

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[AppScale]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[AppWarmup]: http://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
