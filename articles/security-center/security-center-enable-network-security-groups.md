<properties
   pageTitle="啟用網路中的安全性群組 Azure 資訊安全中心 |Microsoft Azure"
   description="這份文件為您示範如何實作 Azure 資訊安全中心建議**啟用網路安全性群組**。"
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
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="enable-network-security-groups-in-azure-security-center"></a>啟用網路中的安全性群組 Azure 資訊安全中心

Azure 資訊安全中心會建議您啟用網路安全性群組 (NSG)，如果其中一個尚未啟動。 NSGs 包含允許或拒絕網路流量至您 VM 中執行個體虛擬網路存取控制清單 (ACL) 規則清單。 NSGs 可與子網路或子網路中的個別 VM 執行個體相關聯。 NSG 與子網路相關聯時，ACL 規則套用至子網路中的所有 VM 執行個體。 此外，可限制個別 vm 流量進一步建立關聯，直接對該 VM NSG。 若要瞭解更多，請參閱[什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md)

如果您沒有啟用 NSGs，資訊安全中心 」 會顯示兩個建議您︰ 啟用網路安全性群組在子網路和啟用網路安全性群組虛擬機器上。 您選擇的層級、 子網路或 VM，若要套用 NSGs。


> [AZURE.NOTE] 這份文件會使用部署範例介紹服務。  這不是逐步指示。

## <a name="implement-the-recommendation"></a>實作建議

1. 在**建議**刀中，選取**啟用網路安全性群組**的子網路或虛擬機器上。
![啟用網路安全性群組][1]

2. 這會開啟刀**設定遺失網路安全性群組**子網路或虛擬機器，根據您所選取的建議。 選取子網路或虛擬機器上設定 NSG。

  ![設定 NSG 子網路][2]

  ![針對 VM 設定 NSG][3]
3. **選擇網路安全性群組**刀選取現有的 NSG 或選取 [建立新的 NSG。

  ![選擇 [網路安全性群組][4]

如果您建立新的 NSG，請遵循[管理 NSGs 使用 Azure 入口網站的方式](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)來建立 NSG 並設定安全性規則中的步驟進行。

## <a name="see-also"></a>另請參閱

本文將示範如何實作子網路或虛擬機器資訊安全中心建議 「 啟用網路安全性群組 」。 若要進一步瞭解如何啟用 NSGs，請參閱下列各項︰

- [什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md)
- [如何管理 NSGs 使用 Azure 入口網站](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定您的訂閱 Azure 和資源群組的安全性原則。
- [在 Azure 資訊安全中心管理安全性的建議](security-center-recommendations.md)，了解建議如何協助您保護 Azure 資源。
- [安全性狀況監控 Azure 資訊安全中心 」](security-center-monitoring.md) ，瞭解如何監視 Azure 資源的狀況。
- [管理及回應在 Azure 資訊安全中心的安全性提醒](security-center-managing-and-responding-alerts.md)--瞭解如何管理和回應的安全性警告。
- [監控合作夥伴解決方案與 Azure 資訊安全中心](security-center-partner-solutions.md)，瞭解如何監控您的合作夥伴解決方案的健康狀態。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找常見問題集使用服務。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-取得最新的 Azure 安全性新聞及資訊。

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
