<properties
   pageTitle="保護您的應用程式在 Azure 資訊安全中心 |Microsoft Azure"
   description="此文件地址，建議在 Azure 資訊安全中心可協助您保護 Azure 應用程式，並保持留存安全性原則。"
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# <a name="protecting-your-applications-in-azure-security-center"></a>保護您的應用程式在 Azure 資訊安全中心

Azure 資訊安全中心分析 Azure 資源的安全性狀態。 當資訊安全中心] 會識別潛在安全性漏洞時，它會建立引導您進行必要的控制項的程序的建議。  建議適用於 Azure 的資源類型︰ 虛擬機器 (Vm)，網路、 SQL 和應用程式。

本文適用於應用程式的建議。  應用程式的建議中心周圍的 web 應用程式防火牆部署]。  使用下的表的參考，可協助您瞭解可用的應用程式建議，以及每個當您將它套用會進行的工作。

## <a name="available-application-recommendations"></a>可用的應用程式的建議

|建議|描述|
|-----|-----|
|[新增 web 應用程式防火牆](security-center-add-web-application-firewall.md)|建議您部署 web 端點的 web 應用程式防火牆 (WAF)。 您可以藉由新增到您現有的 WAF 部署這些應用程式來保護資訊安全中心中的多個 web 應用程式。 部署至不同的虛擬網路需要 WAF 就 （使用資源管理員部署模型建立）。 （建立使用傳統的部署模型） WAF 就會限制使用網路安全性群組。 這項支援會在未來延伸至 （傳統） 的 WAF 應用裝置完全自訂部署。|
|[完成應用程式保護](security-center-add-web-application-firewall.md#finalize-application-protection)|若要完成 WAF 的設定，您必須路徑流量，以便 WAF 應用裝置。 追蹤此建議將完成所需的設定變更。|

## <a name="see-also"></a>另請參閱

若要進一步瞭解套用至其他 Azure 的資源類型的建議，請參閱下列各項︰

- [保護您的虛擬機器中 Azure 資訊安全中心](security-center-virtual-machine-recommendations.md)
- [保護您的網路中 Azure 資訊安全中心](security-center-network-recommendations.md)
- [保護您的 SQL Azure 服務於 Azure 資訊安全中心](security-center-sql-service-recommendations.md)

若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定您的訂閱 Azure 和資源群組的安全性原則。
- [管理及回應在 Azure 資訊安全中心的安全性提醒](security-center-managing-and-responding-alerts.md)--瞭解如何管理和回應的安全性警告。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找常見問題集使用服務。
