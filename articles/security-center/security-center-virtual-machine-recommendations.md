<properties
   pageTitle="保護您的虛擬機器中 Azure 資訊安全中心 |Microsoft Azure"
   description="此文件地址，建議在 Azure 資訊安全中心可協助您保護您的虛擬機器和保持留存安全性原則。"
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
   ms.date="09/25/2016"
   ms.author="terrylan"/>

# <a name="protecting-your-virtual-machines-in-azure-security-center"></a>保護您的虛擬機器中 Azure 資訊安全中心

Azure 資訊安全中心分析 Azure 資源的安全性狀態。 當資訊安全中心] 會識別潛在安全性漏洞時，它會建立引導您進行必要的控制項的程序的建議。  建議適用於 Azure 的資源類型︰ 虛擬機器 (Vm)，網路、 SQL 和應用程式。

本文適用於 Vm 的建議。  資料收集，套用系統的更新，佈建反惡意程式碼，周圍的 VM 建議中心加密 VM 磁碟，及其他內容。  使用下的表的參考，可協助您瞭解可用的 VM 建議，以及每個當您將它套用會進行的工作。

## <a name="available-vm-recommendations"></a>可用的 VM 建議

|建議|描述|
|-----|-----|
|[啟用資料收集適用於的訂閱](security-center-enable-data-collection.md)|建議您在您的訂閱中開啟的每一個您的訂閱及所有的虛擬機器 (Vm) 的安全性原則中的資料集合。|
|[修復 OS 弱點](security-center-remediate-os-vulnerabilities.md)|建議您對齊 OS 組態建議的設定規則，例如不允許儲存密碼。|
|[套用系統更新](security-center-apply-system-updates.md)|建議您先部署至 Vm 遺失系統安全與重要更新。|
|[系統更新後重新啟動](security-center-apply-system-updates.md#reboot-after-system-updates)|建議您重新啟動 VM 完成套用系統更新的程序。|
|[安裝端點保護](security-center-install-endpoint-protection.md)|建議您佈建 Vm (Windows Vm) 的反惡意程式碼程式。|
|[解決端點保護狀況通知](security-center-resolve-endpoint-protection-health-alerts.md)|建議您解決端點保護失敗。|
|[啟用 VM 代理程式](security-center-enable-vm-agent.md)|可讓您看到 Vm 需要 VM 代理程式。 VM 代理人必須安裝在 Vm，才能佈建修補程式掃描掃描的比較基準和反惡意程式碼的程式。 預設會安裝 VM 代理程式的部署從 Azure Marketplace 的 Vm。 文章[VM 代理程式 」 和 「 延伸模組-第 2 部分](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)提供如何安裝 VM 代理程式的資訊。|
| [套用磁碟加密](security-center-apply-disk-encryption.md) |建議您加密 VM 磁碟使用 Azure 磁碟加密 （Windows 和 Linux Vm）。 在您 VM OS 和資料區建議加密。|
| [更新作業系統版本](security-center-update-os-version.md) | 建議您可用的最新版本的雲端服務的更新作業系統 (OS) 版本，您的作業系統系列。  若要進一步瞭解雲端服務，請參閱[雲端服務的概觀](../cloud-services/cloud-services-choose-me.md)。 |
| [未安裝的弱點評估](security-center-vulnerability-assessment-recommendations.md) | 建議您在您 VM 安裝弱點評估解決方案。 |
| [修復弱點](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | 可讓您看到偵測到安裝在您 VM 弱點評估方案的系統和應用程式弱點。 |

## <a name="see-also"></a>另請參閱

若要進一步瞭解套用至其他 Azure 的資源類型的建議，請參閱下列各項︰

- [保護您的應用程式在 Azure 資訊安全中心](security-center-application-recommendations.md)
- [保護您的網路中 Azure 資訊安全中心](security-center-network-recommendations.md)
- [保護您的 SQL Azure 服務於 Azure 資訊安全中心](security-center-sql-service-recommendations.md)

若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定您的訂閱 Azure 和資源群組的安全性原則。
- [管理及回應在 Azure 資訊安全中心的安全性提醒](security-center-managing-and-responding-alerts.md)--瞭解如何管理和回應的安全性警告。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找常見問題集使用服務。
