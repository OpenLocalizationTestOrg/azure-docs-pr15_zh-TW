<properties
   pageTitle="套用在 Azure 資訊安全中心系統更新 |Microsoft Azure"
   description="這份文件會顯示如何實作 Azure 資訊安全中心建議**套用系統更新**並**重新啟動後系統更新**。"
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

# <a name="apply-system-updates-in-azure-security-center"></a>套用在 Azure 資訊安全中心系統更新

Azure 資訊安全中心每天監視遺漏的作業系統更新 Windows 和 Linux 的虛擬機器 (Vm)。 資訊安全中心擷取可用的安全性和重大更新的清單從 Windows Update 或 Windows Server 更新 >，依據設定 Windows VM 服務。  資訊安全中心也會檢查最新的更新 Linux 系統中。 如果您 VM 遺漏系統更新，安全中心會建議您套用系統更新

> [AZURE.NOTE] 這份文件會使用部署範例介紹服務。  這不是逐步指示。

## <a name="implement-the-recommendation"></a>實作建議

1. 在 [**建議**刀中，選取 [**套用系統更新**]。
![套用系統更新][1]

2. **套用系統更新**刀隨即開啟並顯示 Vm 遺漏系統更新的清單。 選取 [VM]。
![選取 VM][2]

3. 刀隨即會開啟該 VM 顯示遺失更新的清單。 選取 [系統更新]。 在此範例中，讓我們選取 KB3156016。
![遺漏的安全性更新][3]

4. 請遵循套用遺失更新**安全性更新**刀中的步驟進行。
![安全性更新][4]

## <a name="reboot-after-system-updates"></a>系統更新後重新啟動

5. 返回**建議**刀。 套用系統的更新，稱為**系統更新後重新啟動**後產生新的項目。 這個項目可讓您知道您需要重新啟動完成套用系統更新的程序 VM。
![系統更新後重新啟動][5]

6. 選取 [**系統更新後重新啟動**]。 這會開啟 [**重新啟動已擱置完成系統更新**刀顯示 Vm 您需要重新啟動完成套用系統更新程序的清單。
![重新啟動擱置][6]

重新啟動完成程序 azure VM。

## <a name="see-also"></a>另請參閱

若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定您的訂閱 Azure 和資源群組的安全性原則。
- [在 Azure 資訊安全中心管理安全性的建議](security-center-recommendations.md)，了解建議如何協助您保護 Azure 資源。
- [安全性狀況監控 Azure 資訊安全中心 」](security-center-monitoring.md) ，瞭解如何監視 Azure 資源的狀況。
- [管理及回應在 Azure 資訊安全中心的安全性提醒](security-center-managing-and-responding-alerts.md)--瞭解如何管理和回應的安全性警告。
- [監控合作夥伴解決方案與 Azure 資訊安全中心](security-center-partner-solutions.md)，瞭解如何監控您的合作夥伴解決方案的健康狀態。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找常見問題集使用服務。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-尋找部落格文章瞭解 Azure 安全性和法規遵循。

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/recommendation.png
[2]:./media/security-center-apply-system-updates/select-vm.png
[3]: ./media/security-center-apply-system-updates/missing-security-updates.png
[4]: ./media/security-center-apply-system-updates/security-update.png
[5]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[6]: ./media/security-center-apply-system-updates/restart-pending.png
