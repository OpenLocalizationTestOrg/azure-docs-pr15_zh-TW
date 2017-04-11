<properties
   pageTitle="解決 Azure 資訊安全中心中的端點保護狀況警示 |Microsoft Azure"
   description="這份文件會顯示如何實作 Azure 資訊安全中心建議**解決端點保護狀況通知**。"
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

# <a name="resolve-endpoint-protection-health-alerts-in-azure-security-center"></a>解決 Azure 資訊安全中心中的端點保護狀況通知

Azure 資訊安全中心會建議您解決偵測到的端點保護狀況通知。  資訊安全中心 」 可讓您看到的虛擬機器 (Vm) 有端點保護失敗及多少失敗。

> [AZURE.NOTE] 這份文件會使用部署範例介紹服務。 這不是逐步指示。

## <a name="implement-the-recommendation"></a>實作建議

1. 在 [**建議刀**中，選取 [**解決端點保護狀況通知**]。
![解決端點保護狀況通知][1]

2. 這會開啟刀**端點保護失敗**列出每個 VM 失敗與失敗次數 Vm。 從清單中選取 VM。
![端點保護失敗][2]

3. **失敗清單**刀隨即會開啟所選 vm，顯示失敗的清單。 從清單中，若要深入瞭解選取失敗。 這會開啟刀選取失敗的相關資訊。
![失敗清單][3]
  ![失敗事件][4]

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
[1]: ./media/security-center-resolve-endpoint-protection/resolve-endpoint-protection.png
[2]: ./media/security-center-resolve-endpoint-protection/endpoint-protection-failure.png
[3]: ./media/security-center-resolve-endpoint-protection/failure-list.png
[4]: ./media/security-center-resolve-endpoint-protection/failure-event.png
