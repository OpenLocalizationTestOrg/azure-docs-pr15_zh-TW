<properties
   pageTitle="新增下一步產生防火牆在 Azure 資訊安全中心 |Microsoft Azure"
   description="這份文件會顯示如何實作**新增下一步產生防火牆**及**傳送 traffice NGFW 僅透過**Azure 資訊安全中心建議。"
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

# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>新增下一步產生防火牆在 Azure 資訊安全中心

Azure 資訊安全中心可能建議您從 Microsoft 合作夥伴新增下一個產生防火牆 (NGFW)，以提升您的安全性保護。 這份文件會引導您執行此動作的範例。

> [AZURE.NOTE] 這份文件會使用部署範例介紹服務。  這不是逐步指示。

## <a name="implement-the-recommendation"></a>實作建議

1. 在**建議**刀中，選取 [**新增下一步產生防火牆**。
![新增下一步產生防火牆][1]

2. 在 [**新增下一步產生防火牆**刀中，選取 [端點]。
![選取端點][2]

3. 第二個**新增下一步產生防火牆**刀隨即會開啟。 您可以選擇要使用現有的方案，如果有的話，或您可以建立新的項目。 在此範例中有任何現有的解決方案，好讓我們會建立新的 NGFW。
![建立新的下一個產生防火牆][3]

4. 若要建立新的 NGFW，選取 [整合式協力廠商的清單中的 [方案]。 我們會在此範例中，選取 [**檢查點**。
![選取 [下一步產生防火牆方案][4]

5. **檢查點**刀隨即會開啟提供您的合作夥伴解決方案的相關資訊。 您可以選取 [**建立**資訊刀中。
![防火牆資訊刀][5]

6. **建立虛擬機器**刀隨即會開啟。 這裡，您可以輸入會執行 NGFW 虛擬機器 (VM) 向上微調所需的資訊。 請依照下列步驟，並提供必要的 NGFW 資訊。 選取 [確定] 套用。
![建立執行 NGFW 虛擬機器][6]

## <a name="route-traffic-through-ngfw-only"></a>路由流量至 NGFW 只

返回**建議**刀。 您新增資訊安全中心，透過 NGFW 呼叫**NGFW 僅透過路由流量**後產生新的項目。 如果您有安裝您 NGFW 透過資訊安全中心 」，則會建立此建議。 如果您有網際網路的結束點，安全中心會建議您設定您透過您 NGFW vm 強制輸入的流量的網路安全性群組規則。

1. 在**建議刀**中，選取 [**只 NGFW 路由流量**。
![路由流量至 NGFW 只][7]

2. 這會開啟**路由流量透過 NGFW 僅**會列出您可以將流量路由傳送的 Vm 刀。 從清單中選取 VM。
![選取 VM][8]

3. 選取 vm 刀隨即開啟，顯示相關的輸入的規則。 說明為您提供可能的下一個步驟的詳細資訊。 選取 [繼續編輯輸入的規則的 [**編輯輸入的規則**]。 預期會的**來源**不設定為**任何**NGFW 與連結的網際網路的端點。 若要深入瞭解輸入規則的屬性，請參閱[NSG 規則](../virtual-network/virtual-networks-nsg.md#nsg-rules)。
![設定規則，限制存取][9]
![編輯輸入的規則][10]

## <a name="see-also"></a>另請參閱

這份文件會示範如何實作資訊安全中心建議 」 新增下一步產生防火牆 」。 若要瞭解 NGFWs 和檢查點合作夥伴解決方案的詳細資訊，請參閱下列各項︰

- [下一步] 產生防火牆](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
- [檢查點 vSEC](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定安全性原則。
- [在 Azure 資訊安全中心管理安全性的建議](security-center-recommendations.md)，了解建議如何協助您保護 Azure 資源。
- [安全性狀況監控 Azure 資訊安全中心 」](security-center-monitoring.md) ，瞭解如何監視 Azure 資源的狀況。
- [管理及回應在 Azure 資訊安全中心的安全性提醒](security-center-managing-and-responding-alerts.md)--瞭解如何管理和回應的安全性警告。
- [監控合作夥伴解決方案與 Azure 資訊安全中心](security-center-partner-solutions.md)，瞭解如何監控您的合作夥伴解決方案的健康狀態。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找常見問題集使用服務。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-尋找部落格文章瞭解 Azure 安全性和法規遵循。

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png
