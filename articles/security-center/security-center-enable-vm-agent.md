<properties
   pageTitle="啟用 VM 代理程式在 Azure 資訊安全中心 |Microsoft Azure"
   description="這份文件為您示範如何實作 Azure 資訊安全中心建議**啟用 VM 代理程式**。"
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
   ms.date="10/17/2016"
   ms.author="terrylan"/>

# <a name="enable-vm-agent-in-azure-security-center"></a>啟用在 Azure 資訊安全中心 VM 代理程式

若要[啟用資料收集](security-center-enable-data-collection.md)順序 VM 代理程式必須安裝虛擬機器 (Vm) 上。  Azure 資訊安全中心 」 可讓您以查看哪些 Vm 需要 VM 代理程式，並會建議您啟用這些 Vm VM 代理程式。

預設會安裝 VM 代理程式的部署從 Azure Marketplace 的 Vm。 文章[VM 代理程式 」 和 「 延伸模組-第 2 部分](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)提供如何安裝 VM 代理程式的資訊。


> [AZURE.NOTE] 這份文件會使用部署範例介紹服務。 這不是逐步指示。

## <a name="implement-the-recommendation"></a>實作建議

1. 在 [**建議刀**中，選取 [**啟用 VM 代理程式**]。
![啟用 VM 代理程式][1]

2. 這會開啟**VM 代理程式會遺失或不回應**刀。 此刀列出 Vm 需要 VM 代理程式。 依照指示安裝 VM 代理程式刀上。
![未顯示 VM 代理程式][2]

## <a name="see-also"></a>另請參閱

若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定您的訂閱 Azure 和資源群組的安全性原則。
- [管理 Azure 資訊安全中心中的安全性建議](security-center-recommendations.md)，了解建議如何協助您保護 Azure 資源。
- [監控 Azure 資訊安全中心的安全性狀況](security-center-monitoring.md)，瞭解如何監視 Azure 資源的狀況。
- [管理及回應在 Azure 資訊安全中心的安全性提醒](security-center-managing-and-responding-alerts.md)--瞭解如何管理和回應的安全性警告。
- [監控合作夥伴解決方案與 Azure 資訊安全中心](security-center-partner-solutions.md)，瞭解如何監控您的合作夥伴解決方案的健康狀態。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找常見問題集使用服務。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-取得最新的 Azure 安全性新聞及資訊。

<!--Image references-->
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png
