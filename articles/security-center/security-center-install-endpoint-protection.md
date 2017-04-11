<properties
   pageTitle="端點保護安裝 Azure 資訊安全中心 |Microsoft Azure"
   description="這份文件為您示範如何實作 Azure 資訊安全中心建議**安裝端點保護**。"
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
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="install-endpoint-protection-in-azure-security-center"></a>端點保護安裝 Azure 資訊安全中心

Azure 資訊安全中心會建議您 Azure 虛擬機器 (Vm) 提供的反惡意程式碼的程式，如果未啟用反惡意程式碼。 此建議只適用於 Windows Vm。

> [AZURE.NOTE] 這份文件會使用部署範例介紹服務。  這不是逐步指示。

## <a name="implement-the-recommendation"></a>實作建議

1. 在 [**建議**刀中，選取 [**安裝端點保護**]。
![選取 [安裝端點保護][1]

2. **安裝端點保護**刀隨即會開啟反惡意程式碼啟用不顯示 Vm 的清單。 從清單中選取您想要在安裝反惡意程式碼，按一下 [**安裝在 Vm**Vm。
![選取 Vm 上安裝反惡意程式碼][2]

3. **選取端點保護**刀會開啟，讓您選取您想要使用的反惡意程式碼解決方案。 在此範例中，讓我們選取**Microsoft 反惡意程式碼**。
![選取端點保護][3]

4. 反惡意程式碼解決方案的其他資訊會顯示。 選取 [**建立**]。
![建立反惡意程式碼解決方案][4]

5. 在**新增副檔名**刀中，輸入所需的設定，然後選取**[確定]**。 若要深入瞭解設定的設定，請參閱[預設與自訂反惡意程式碼設定](../security/azure-security-antimalware.md#default-and-custom-antimalware-configuration)。

[Microsoft 反惡意程式碼](../azure-security-antimalware.md)現在上選取 Vm 作用中。

## <a name="see-also"></a>另請參閱

本文會展示如何實作資訊安全中心建議 「 安裝端點保護 」。 若要進一步瞭解如何啟用 Azure 中的反惡意程式碼程式，請參閱下列各項︰

- [Microsoft 反惡意程式碼的雲端服務與虛擬機器](../azure-security-antimalware.md)，瞭解如何部署 Microsoft 反惡意程式碼。

若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定安全性原則。
- [在 Azure 資訊安全中心管理安全性的建議](security-center-recommendations.md)，了解建議如何協助您保護 Azure 資源。
- [安全性狀況監控 Azure 資訊安全中心 」](security-center-monitoring.md) ，瞭解如何監視 Azure 資源的狀況。
- [管理及回應在 Azure 資訊安全中心的安全性提醒](security-center-managing-and-responding-alerts.md)--瞭解如何管理和回應的安全性警告。
- [監控合作夥伴解決方案與 Azure 資訊安全中心](security-center-partner-solutions.md)，瞭解如何監控您的合作夥伴解決方案的健康狀態。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找常見問題集使用服務。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-尋找部落格文章瞭解 Azure 安全性和法規遵循。

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/select-install-endpoint-protection.png
[2]:./media/security-center-install-endpoint-protection/install-endpoint-protection-blade.png
[3]:./media/security-center-install-endpoint-protection/select-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/create-antimalware-solution.png
