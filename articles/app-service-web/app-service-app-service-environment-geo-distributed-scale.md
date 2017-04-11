<properties 
    pageTitle="地理分佈的縮放比例與應用程式服務環境" 
    description="瞭解如何水平不按比例縮放使用地理分配流量管理員與應用程式服務環境的應用程式。" 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/07/2016" 
    ms.author="stefsch"/>   

# <a name="geo-distributed-scale-with-app-service-environments"></a>地理分佈的縮放比例與應用程式服務環境

## <a name="overview"></a>概觀 ##
需要更高的小數位數的應用程式案例可以超過單一部署的應用程式可用的計算資源容量。  投票應用程式，運動及 televised 的娛樂事件是所有範例案例需要極高的小數位數。 可以藉由水平縮放應用程式]，查看使用多個進行中的單一的區域，以及區域，處理非常載入需求的應用程式部署符合高的縮放比例需求。

應用程式服務環境是水平延展理想的平台。  一次應用程式服務環境中已選取組態可支援已知的要求工資率，開發人員可以部署其他應用程式服務環境 」 切 」 的方式來達到所要的最大使用量載入容量。

例如假設應用程式服務環境設定上執行應用程式已經過測試處理 20 K 要求秒 （每秒要求數量）。  如果您要的最大使用量載入容量 100 K 每秒要求數量，然後 5 個應用程式服務環境可以建立及設定以確保應用程式可以處理的最大的預計的載入。

客戶通常會 access 應用程式使用自訂 （或 vanity） 的網域，因為開發人員需要應用程式要求分散所有應用程式服務環境執行個體的方法。  完成這項作業的絕佳方式是解決使用[Azure 流量管理員設定檔]的自訂網域[AzureTrafficManagerProfile]。  若要在個別的應用程式服務環境的所有指向，可以設定流量管理員設定檔。  在所有應用程式服務環境根據負載平衡流量管理員設定檔中的設定，流量管理員會自動處理散發的客戶。  這種方式運作，不論是否所有應用程式服務環境部署在單一 Azure 區域中，或已在全球部署跨多個 Azure 區域。

此外，因為客戶 access 應用程式，透過 vanity 網域，也不知道執行應用程式的應用程式服務環境的數字的客戶。  結果開發人員可以快速且輕鬆地新增，並移除，請根據觀察的流量載入的應用程式服務環境。

概念性下圖說明水平延展在單一區域內的三個應用程式服務環境的應用程式。

![概念性架構][ConceptualArchitecture] 

本主題的其餘部分將引導設定的分散式拓撲範例應用程式使用多個應用程式服務環境的相關步驟。

## <a name="planning-the-topology"></a>規劃拓撲 ##
之前所需的分散式應用程式出建置，有助於有提前的幾個部分資訊。

- **應用程式的自訂網域︰** 客戶會用來存取應用程式的自訂網域名稱是什麼？  範例應用程式的自訂網域名稱是*www.scalableasedemo.com*
- **流量管理員網域︰** 需要建立[Azure 流量管理員設定檔]時選擇網域名稱[AzureTrafficManagerProfile]。  此名稱就會結合*trafficmanager.net*後置字元登錄流量管理員所管理的網域項目。  範例應用程式中，選擇名稱是*可調整 ase 示範*。  如此一來管理由流量管理員的完整網域名稱就*可調整 ase demo.trafficmanager.net*。
- **縮放比例應用程式所需的策略︰** 應用程式所需的分佈一個區域中的多個應用程式服務環境？  多個區域？  混合-與比對兩種方法？  決定應該根據客戶的流量會產生，以及如何在應用程式支援的後端基礎結構的其餘部分可以不按比例縮放的期望。  例如，100%的無應用程式，應用程式可以人連線縮放每個 Azure 地區，乘以跨多個 Azure 區域部署的應用程式服務環境中使用多個應用程式服務環境的組合。  15 + 公用 Azure 區域可以選擇從，客戶真正可以建立所需的全球超比例應用程式。  本文所使用的範例應用程式，以單一 Azure 區域 （南部中央適用於美國） 建立三個應用程式服務環境。
- **應用程式服務環境的命名慣例︰** 每個應用程式服務環境需要唯一的名稱。  除了一或兩個應用程式服務環境還是有幫助有的命名慣例，以協助您識別每個應用程式服務環境項目。  範例應用程式的使用簡單的命名慣例。  三種應用程式服務環境名稱是*fe1ase* *fe2ase*，與*fe3ase*。
- **應用程式的命名慣例︰** 將部署的應用程式的多個執行個體，因為每個執行個體的部署的應用程式需要的名稱。  一個知，但很方便的應用程式服務環境功能可以使用跨多個應用程式服務環境的相同的應用程式名稱。  因為每個應用程式服務環境都有唯一的網域後置字元，開發人員可以選擇重複每個環境中使用相同的應用程式名稱。  例如開發人員可以有名稱如下的應用程式︰ *myapp.foo1.p.azurewebsites.net*、 *myapp.foo2.p.azurewebsites.net*、 *myapp.foo3.p.azurewebsites.net*等。 範例應用程式的透過每個應用程式執行個體也都有唯一名稱。  所使用的應用程式執行個體名稱是*webfrontend1* *webfrontend2*，與*webfrontend3*。


## <a name="setting-up-the-traffic-manager-profile"></a>設定流量管理員設定檔 ##
一旦在多個應用程式服務環境部署的應用程式的多個執行個體，個別的應用程式執行個體註冊使用流量管理員。  範例應用程式流量管理員*可調整 ase demo.trafficmanager.net*可路由客戶下列部署的應用程式執行個體的任何需要的設定檔︰

- **webfrontend1.fe1ase.p.azurewebsites.net:** 在第一個應用程式服務環境中部署範例應用程式的執行個體。
- **webfrontend2.fe2ase.p.azurewebsites.net:** 在第二個應用程式服務環境中部署範例應用程式的執行個體。
- **webfrontend3.fe3ase.p.azurewebsites.net:** 在第三個應用程式服務環境中部署範例應用程式的執行個體。

註冊多個 Azure 應用程式服務端點，所有在**同一個**Azure 區域] 中執行最簡單的方法是使用 Powershell [Azure 資源管理員流量管理員支援][ARMTrafficManager]。  

第一步是建立 Azure 流量管理員設定檔。  下列程式碼會顯示範例應用程式的設定檔的建立方式︰

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

請注意*RelativeDnsName*參數設定為*可調整 ase 示範*如何。  這是如何建立以及流量管理員設定檔與相關聯的網域名稱*可調整 ase demo.trafficmanager.net* 。

*TrafficRoutingMethod*參數定義的負載平衡的原則流量管理員用來決定如何客戶負荷分散所有可用的端點。  在此範例中*加權*選擇方法。  這會導致客戶要求正在分散所有註冊的應用程式端點根據每個端點相關聯的相對粗細。 

建立設定檔，每個應用程式執行個體會新增至設定檔與原生 Azure 端點。  下列程式碼會擷取至每個前端 web 應用程式中，參照，並每一個應用程式為流量管理員端點透過*TargetResourceId*參數。


    $webapp1 = Get-AzureRMWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzureRMWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzureRMWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10
    
    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
    
請注意如何有一個通話*新增 AzureTrafficManagerEndpointConfig*每個個別的應用程式執行個體。  在每個 Powershell 命令*TargetResourceId*參數參照三個已部署的應用程式執行個體之一。  流量管理員設定檔會載入分散登錄設定檔中的所有三個端點。

所有三個端點*粗細*參數使用相同的值 (10)。  這會導致流量管理員分配客戶要求所有三個應用程式執行個體以往平均。 


## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>指向應用程式的自訂網域流量管理員網域 ##
必要的最後一個步驟是指向流量管理員網域的應用程式的自訂網域。  範例應用程式中，這表示*可調整 ase demo.trafficmanager.net*指向*www.scalableasedemo.com* 。  需要管理自訂網域的網域註冊機構完成此步驟。  

使用您的註冊機構網域管理工具，CNAME 記錄建立的點流量管理員網域的自訂網域需求。  下圖顯示這個 CNAME 設定看起來像的範例︰

![自訂網域的 CNAME][CNAMEforCustomDomain] 

雖然不包含在此主題，請記住，必須擁有自訂的網域註冊，以及每個個別的應用程式執行個體。  否則如果要求應用程式執行個體，讓您的應用程式沒有註冊使用應用程式的自訂網域，要求將會失敗。  

在此範例中的自訂網域*www.scalableasedemo.com*，而每個應用程式執行個體具有與其相關聯的自訂網域。

![自訂網域][CustomDomain] 

概述註冊 Azure 應用程式服務應用程式的自訂網域，請參閱下列文章，關於[註冊的自訂網域][RegisterCustomDomain]。

## <a name="trying-out-the-distributed-topology"></a>試用分散式拓撲 ##
流量管理員和 DNS 設定的結果是*www.scalableasedemo.com*的要求會透過下列程序︰

1. 在瀏覽器或裝置會使*www.scalableasedemo.com* DNS 查閱
2. 在網域註冊機構的 CNAME 項目會被重新導向至 Azure 流量管理員 DNS 查閱。
3. DNS 查閱是進行*調整 ase demo.trafficmanager.net* Azure 流量管理員 DNS 伺服器的其中之一。
4. 根據負載平衡原則 （ *TrafficRoutingMethod*參數建立流量管理員設定檔時，請使用較舊版本），流量管理員會選取其中一個設定結束點，並返回瀏覽器或裝置中的端點的 FQDN。
5.  由於端點的 FQDN 應用程式服務環境上執行應用程式執行個體的 Url，在瀏覽器或裝置會詢問 Microsoft Azure DNS 伺服器 IP 位址解析 FQDN。 
6. 在瀏覽器或裝置會將 HTTP/S 要求 IP 位址。  
7. 在其中執行在其中一個應用程式服務環境的應用程式執行個體，將會送達要求。

下列主控台圖片顯示成功解決其中一個的三個範例應用程式服務環境 （在此情況下，第二個的三種應用程式服務環境） 上執行應用程式執行個體的範例應用程式的自訂網域的 DNS 搜尋︰

![DNS 查閱][DNSLookup] 

## <a name="additional-links-and-information"></a>其他連結和資訊 ##
所有的文件及如何-的應用程式服務環境都是在[讀我檔案的應用程式服務環境](../app-service/app-service-app-service-environments-readme.md)中使用。

Powershell [Azure 資源管理員流量管理員支援]的相關文件[ARMTrafficManager]。  

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]:  https://azure.microsoft.com/documentation/articles/traffic-manager-manage-profiles/
[ARMTrafficManager]:  https://azure.microsoft.com/documentation/articles/traffic-manager-powershell-arm/
[RegisterCustomDomain]:  https://azure.microsoft.com/en-us/documentation/articles/web-sites-custom-domain-name/


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
