<properties
   pageTitle="限制存取透過 Azure 資訊安全中心中的網際網路的端點 |Microsoft Azure"
   description="這份文件為您示範如何實作 Azure 資訊安全中心建議**透過網際網路具端點的限制存取**。"
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
   ms.date="10/26/2016"
   ms.author="terrylan"/>

# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>限制存取透過 Azure 資訊安全中心中的網際網路的端點

Azure 資訊安全中心會建議您限制存取透過網際網路的端點，如果您的網路安全性群組 (NSGs) 的任何有一或多個輸入的規則，允許存取 「 任何 」 來源 IP 位址。 開啟 access 「 任何 」，可能會啟用攻擊，存取您的資源。 資訊安全中心會建議您編輯下列輸入的規則，限制存取真的需要存取的來源 IP 位址。

此建議會產生的任何具有 「 任何 」 來源的非網頁連接埠。

> [AZURE.NOTE] 這份文件會使用部署範例介紹服務。 這不是逐步指示。

## <a name="implement-the-recommendation"></a>實作建議

1. 在 [**建議刀**中，選取 [**透過網際網路具端點的限制存取**]。
![限制存取透過網際網路圖示的端點][1]

2. 這會開啟刀**透過網際網路具端點的限制存取**。 此刀會列出的建立的潛在安全性問題的輸入規則的虛擬機器 (Vm)。 選取 [VM]。
![選取 VM][2]

3. **NSG**刀會顯示網路安全性群組資訊、 相關的輸入的規則，以及相關聯的 VM。 選取 [繼續編輯輸入的規則的 [**編輯輸入的規則**]。
![網路安全性群組刀][3]

4. 在**輸入的安全性規則**刀選取要編輯的連入的規則。 在此範例中，讓我們選取**AllowWeb**。
![輸入的安全性規則][4]

  請注意，您也可以選取**預設規則**，請參閱所有 NSGs 所都包含的預設規則的設定。 您無法刪除預設的規則，但因為而指派更低的優先順序，可以將它們覆寫您建立的規則。 進一步瞭解[預設規則](../virtual-network/virtual-networks-nsg.md#default-rules)。
![預設的規則][5]

5. 使**來源**IP 位址或區塊的 IP 位址，請在**AllowWeb**刀中，編輯輸入規則的屬性。 若要深入瞭解輸入規則的屬性，請參閱[NSG 規則](../virtual-network/virtual-networks-nsg.md#nsg-rules)。

  ![編輯輸入的規則][6]

## <a name="see-also"></a>另請參閱

本文將示範如何實作資訊安全中心建議 「 限制存取透過網際網路具結束點 」。 若要進一步瞭解如何啟用 NSGs 和規則，請參閱下列各項︰

- [什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md)
- [如何管理 NSGs 使用 Azure 入口網站](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定您的訂閱 Azure 和資源群組的安全性原則。
- [管理 Azure 資訊安全中心中的安全性建議](security-center-recommendations.md)，了解建議如何協助您保護 Azure 資源。
- [監控 Azure 資訊安全中心的安全性狀況](security-center-monitoring.md)，瞭解如何監視 Azure 資源的狀況。
- [管理及回應在 Azure 資訊安全中心的安全性提醒](security-center-managing-and-responding-alerts.md)--瞭解如何管理和回應的安全性警告。
- [監控合作夥伴解決方案與 Azure 資訊安全中心](security-center-partner-solutions.md)，瞭解如何監控您的合作夥伴解決方案的健康狀態。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找常見問題集使用服務。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-取得最新的 Azure 安全性新聞及資訊。

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png
