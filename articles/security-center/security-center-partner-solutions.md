<properties
   pageTitle="管理合作夥伴解決方案在 Azure 資訊安全中心 |Microsoft Azure"
   description="這份文件會引導您如何 Azure 資訊安全中心 」 可讓您監控您的合作夥伴解決方案的健康狀態整合 Azure 訂閱概覽。"
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

# <a name="monitoring-partner-solutions-with-azure-security-center"></a>監控合作夥伴解決方案與 Azure 資訊安全中心

這份文件會引導您如何監控您的合作夥伴解決方案在 Azure 資訊安全中心的健康狀態。

> [AZURE.NOTE] 這份文件會使用部署範例介紹服務。 這不是逐步指示。

## <a name="monitoring-partner-solutions"></a>監控合作夥伴解決方案

**資訊安全中心**刀**合作夥伴解決方案**磚可讓您監控您的合作夥伴解決方案的健康狀態整合 Azure 訂閱概覽。
![合作夥伴解決方案磚][1]

**合作夥伴解決方案**磚顯示合作夥伴解決方案與這些解決方案的摘要工作狀態的數目。

可合作夥伴解決方案**狀態**︰

- 受保護 （綠色）-沒有狀況的問題。
- （紅色）-健康狀況問題需要立即關注的。
- 停止報告 （橘色）-方案已停止報告其健康狀況。
- 未知的保護狀態 （橘色）-健康狀況的解決方案不明，因為失敗的程序的現有的方案中加入新的資源。
- 不會報告 （呈現灰色）-方案尚未報告任何項目，但如果只要已連線且仍部署解決方案的狀態可能未回報。

如果不有任何與您的訂閱整合的解決方案磚會狀態是沒有解決方案。 選取 [**合作夥伴解決方案**磚會讓您開啟**建議**刀部署協力廠商安全性解決方案。
![沒有合作夥伴解決方案][2]

若要檢視您的合作夥伴解決方案的狀況︰

1. 選取 [**合作夥伴解決方案**磚。 刀隨即開啟並顯示您連線至資訊安全中心的合作夥伴解決方案清單。
![合作夥伴解決方案][3]

2. 選取 [合作夥伴解決方案]。 在此範例中，可讓選取**F5 WAF2**解決方案。  隨即會開啟刀，顯示您的狀態合作夥伴解決方案與解決方案的相關聯的資源。 選取**方案主控台**] 以開啟此方案的合作夥伴管理體驗。
![合作夥伴解決方案詳細資料][4]

3. 回到**F5 WAF2**刀，然後選取**連結的應用程式**。 **連結應用程式**刀隨即會開啟。 這裡，您可以進行合作夥伴解決方案連線資源。
![連結至合作夥伴解決方案的資源][5]

## <a name="see-also"></a>另請參閱
在此文件中所採用至資訊安全中心中的 [**合作夥伴解決方案**] 磚。 若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定您的訂閱 Azure 和資源群組的安全性原則。
- [管理 Azure 資訊安全中心中的安全性建議](security-center-recommendations.md)，了解建議如何協助您保護 Azure 資源。
- [監控 Azure 資訊安全中心的安全性狀況](security-center-monitoring.md)，瞭解如何監視 Azure 資源的狀況。
- [管理及回應在 Azure 資訊安全中心的安全性警告](security-center-managing-and-responding-alerts.md)，瞭解如何管理和回應的安全性警告。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找使用服務的相關的常見問題集。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-取得最新的 Azure 安全性新聞及資訊。

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png
