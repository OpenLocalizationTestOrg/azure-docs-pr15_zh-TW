<properties
   pageTitle="保護您的網路中 Azure 資訊安全中心 |Microsoft Azure"
   description="此文件地址，建議在 Azure 資訊安全中心可協助您保護 Azure 網路，並保持留存安全性原則。"
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

# <a name="protecting-your-network-in-azure-security-center"></a>保護您的網路中 Azure 資訊安全中心

Azure 資訊安全中心分析 Azure 資源的安全性狀態。 當資訊安全中心] 會識別潛在安全性漏洞時，它會建立引導您進行必要的控制項的程序的建議。  建議適用於 Azure 的資源類型︰ 虛擬機器 (Vm)，網路、 SQL 和應用程式。

本文適用於您的網路的建議。  下一步產生防火牆、 網路安全性群組、 設定輸入的傳輸規則，以及更多的網路建議中心。  使用下的表的參考，可協助您瞭解可用的網路建議，以及每個當您將它套用會進行的工作。

## <a name="available-network-recommendations"></a>可用的網路建議

|建議|描述|
|-----|-----|
|[新增下一步產生防火牆](security-center-add-next-generation-firewall.md)|建議您從 Microsoft 合作夥伴新增下一步產生防火牆 (NGFW)，以提升您的安全性保護。|
|[路由流量至 NGFW 只](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only)|建議您先設定您透過您 NGFW vm 強制輸入的流量的網路安全性群組 (NSG) 規則。|
|[在子網路或虛擬機器上啟用網路安全性群組](security-center-enable-network-security-groups.md)|建議您子網路或 Vm 上啟用 NSGs。|
|[限制存取透過網際網路圖示的端點](security-center-restrict-access-through-internet-facing-endpoints.md)|建議您在針對 NSGs 設定輸入的傳輸規則。|

## <a name="see-also"></a>另請參閱

若要進一步瞭解套用至其他 Azure 的資源類型的建議，請參閱下列各項︰

- [保護您的虛擬機器中 Azure 資訊安全中心](security-center-virtual-machine-recommendations.md)
- [保護您的應用程式在 Azure 資訊安全中心](security-center-application-recommendations.md)
- [保護您的 SQL Azure 服務於 Azure 資訊安全中心](security-center-sql-service-recommendations.md)

若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定您的訂閱 Azure 和資源群組的安全性原則。
- [管理及回應在 Azure 資訊安全中心的安全性提醒](security-center-managing-and-responding-alerts.md)--瞭解如何管理和回應的安全性警告。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找常見問題集使用服務。
