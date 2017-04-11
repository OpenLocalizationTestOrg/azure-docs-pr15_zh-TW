<properties
   pageTitle="提供於 Azure 資訊安全中心的安全性連絡人詳細資料 |Microsoft Azure"
   description="這份文件會顯示如何提供安全性 Azure 資訊安全中心中的連絡人詳細資料。"
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

# <a name="provide-security-contact-details-in-azure-security-center"></a>提供於 Azure 資訊安全中心的安全性連絡人詳細資料

Azure 資訊安全中心會建議您 Azure 訂閱提供安全性連絡人詳細資料，如果您未。 Microsoft 會使用這項資料，如果 Microsoft 安全性回應中心 (MSRC) 探索非法或未經授權的廠商已存取您的客戶資料，請連絡您。 MSRC 執行選取的安全性監視 Azure 網路和基礎結構，並從協力廠商接收威脅智慧和濫用抱怨。

上的第一個每日的項目提醒和僅適用於 [高重要性通知傳送電子郵件通知。 只能訂閱原則設定電子郵件喜好設定。 資源群組內的訂閱將會繼承這些設定。

> [AZURE.NOTE] 這份文件會使用部署範例介紹服務。  這不是逐步指示。

## <a name="implement-the-recommendation"></a>實作建議

1. 在 [**建議**刀中，選取 [**提供安全性連絡詳細資料**]。
![提供安全性連絡人][1]

2. 這會開啟刀**提供安全性連絡詳細資料**。 選取 Azure 訂閱，可提供連絡人的資訊。
![提供安全性連絡人詳細資料][2]

3. 第二個**提供安全性連絡人詳細資料**的刀隨即會開啟。

  - 輸入安全連絡人的電子郵件地址或地址，並以逗號分隔。 沒有限制您可以輸入的電子郵件地址的數目。
  - 輸入一個連絡人的國際電話號碼。
  - 若要收到關於高重要性通知的電子郵件，請開啟**傳送給我的電子郵件的相關提醒**的選項。
  - 在未來，您必須訂閱的擁有者傳送電子郵件通知的選項。 目前，此選項會呈現灰色。
  - 選取**[確定]**以套用至您的訂閱的安全性連絡人資訊。

## <a name="see-also"></a>另請參閱

若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定您的訂閱 Azure 和資源群組的安全性原則。
- [在 Azure 資訊安全中心管理安全性的建議](security-center-recommendations.md)，了解建議如何協助您保護 Azure 資源。
- [安全性狀況監控 Azure 資訊安全中心 」](security-center-monitoring.md) ，瞭解如何監視 Azure 資源的狀況。
- [管理及回應在 Azure 資訊安全中心的安全性提醒](security-center-managing-and-responding-alerts.md)--瞭解如何管理和回應的安全性警告。
- [監控合作夥伴解決方案與 Azure 資訊安全中心](security-center-partner-solutions.md)，瞭解如何監控您的合作夥伴解決方案的健康狀態。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找常見問題集使用服務。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-取得最新的 Azure 安全性新聞及資訊。

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
