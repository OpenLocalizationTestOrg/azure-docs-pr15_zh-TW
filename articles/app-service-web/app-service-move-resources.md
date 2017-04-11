<properties
    pageTitle="將 Web 應用程式資源移到另一個資源群組"
    description="說明位置您可以 Web 應用程式與應用程式服務從某個資源群組移到另一個的案例。"
    services="app-service"
    documentationCenter=""
    authors="ZainRizvi"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/04/2016"
    ms.author="zarizvi"/>
    
# <a name="supported-move-configurations"></a>支援的移動設定

您可以移動 Azure Web 應用程式使用[ARM 移動資源 Api](../resource-group-move-resources.md)的資源。

Azure Web 應用程式目前支援下列移動案例︰

* 資源群組 （web 應用程式、 應用程式服務方案，請與憑證） 的整個內容移動到另一個資源群組 
    * 注意︰ 目的地資源群組不包含在這個案例中的任何 Microsoft.Web 資源
* 將個別的 web 應用程式移至不同的資源] 群組中，，時仍其裝載在其目前應用程式服務的計劃 （保持舊的 [資源] 群組中的應用程式服務方案）
