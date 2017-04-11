<properties
   pageTitle="啟用 Azure 資訊安全中心中的透明資料加密 |Microsoft Azure"
   description="這份文件為您示範如何實作 Azure 資訊安全中心建議**啟用透明資料加密**。"
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

# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>啟用 Azure 資訊安全中心中的透明資料加密

Azure 資訊安全中心會建議您在 SQL 資料庫啟用透明資料加密 (TDE)，如果未啟用 TDE。 TDE 會保護您的資料，並可協助您遵守法規加密您的資料庫，相關聯的備份和交易記錄檔，其餘部分中，而不需要變更您的應用程式。 若要瞭解更多請參閱[Azure SQL 資料庫的透明資料加密](https://msdn.microsoft.com/library/dn948096)。

此建議適用於 SQL Azure 服務不包含 SQL 虛擬機器上執行。

> [AZURE.NOTE] 這份文件會使用部署範例介紹服務。  這不是逐步指示。

## <a name="implement-the-recommendation"></a>實作建議

1. 在 [**建議**刀中，選取 [**啟用透明資料加密**]。
![啟用透明資料加密][1]

2. 這會開啟**啟用透明資料加密 SQL 資料庫**刀。 選取 [啟用 TDE 在 SQL 資料庫]。
![選取 [啟用 TDE 在 SQL 資料庫][2]
3. 在**透明資料加密**刀中，選取 [資料加密下的 [**開啟]** ，然後選取上方刀的功能區中的 [**儲存**]。
![開啟 [TDE][3]

  一旦啟用 TDE 上所選的 SQL 資料庫，**加密的狀態**會變更**加密**。    

  ![加密的狀態][4]

## <a name="see-also"></a>另請參閱

本文會展示如何實作資訊安全中心建議 」 啟用透明資料加密]。 若要進一步瞭解 SQL TDE 」 的資訊，請參閱下列各項︰

- [透明資料加密 Azure SQL 資料庫](https://msdn.microsoft.com/library/dn948096)
- [開始使用透明資料加密 (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定您的訂閱 Azure 和資源群組的安全性原則。
- [在 Azure 資訊安全中心管理安全性的建議](security-center-recommendations.md)，了解建議如何協助您保護 Azure 資源。
- [安全性狀況監控 Azure 資訊安全中心 」](security-center-monitoring.md) ，瞭解如何監視 Azure 資源的狀況。
- [管理及回應在 Azure 資訊安全中心的安全性提醒](security-center-managing-and-responding-alerts.md)--瞭解如何管理和回應的安全性警告。
- [監控合作夥伴解決方案與 Azure 資訊安全中心](security-center-partner-solutions.md)，瞭解如何監控您的合作夥伴解決方案的健康狀態。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找常見問題集使用服務。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-取得最新的 Azure 安全性新聞及資訊。

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
