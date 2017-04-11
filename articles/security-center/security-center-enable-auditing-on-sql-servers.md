<properties
   pageTitle="在 SQL Azure 資訊安全中心中的伺服器上啟用稽核 |Microsoft Azure"
   description="這份文件會顯示如何實作**啟用稽核 SQL server**Azure 資訊安全中心建議。"
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

# <a name="enable-auditing-on-sql-servers-in-azure-security-center"></a>在 SQL Azure 資訊安全中心中的伺服器上啟用稽核

Azure 資訊安全中心會建議您開啟如果稽核尚未啟用稽核 Azure SQL 伺服器上的所有資料庫。 稽核，可協助您維護法規遵循、 瞭解資料庫活動和掌握差異和異常可能表示商務問題或假設安全性衝突。

您已開啟稽核您可以設定威脅偵測設定與電子郵件收到的安全性警告。 威脅偵測偵測指出資料庫的潛在安全性威脅異常資料庫活動。 這可讓您偵測和回應潛在威脅發生。

此建議適用於 SQL Azure 服務不包括中 Azure 基礎結構服務 (Azure IaaS) 虛擬機器上執行的 SQL Server。

> [AZURE.NOTE] 這份文件會使用部署範例介紹服務。  這不是逐步指示。

## <a name="implement-the-recommendation"></a>實作建議

1. 在 [**建議**刀中，選取 [**啟用稽核 SQL 伺服器上**]。  這會開啟**SQL 伺服器上啟用稽核**刀。
![在 SQL server 上啟用稽核][1]

2. 選取 [SQL server 上啟用稽核。 這會開啟**稽核設定**刀。
![稽核設定][2]
3. 在**稽核設定**刀中，選取 [**稽核**底下的**[開啟**]。
![開啟稽核設定][3]

4. 請依照在[開始使用 SQL 資料庫稽核](../sql-database/sql-database-auditing-get-started.md)設定儲存儲存您的稽核記錄。 收集資料的訂閱的儲存空間帳戶是預設儲存的帳戶。

5. 依照 [[快速入門 SQL 資料庫威脅偵測](../sql-database/sql-database-threat-detection-get-started.md)啟用及設定威脅偵測並設定的安全性警訊當偵測異常活動時，所收到的電子郵件清單中的步驟。

## <a name="see-also"></a>另請參閱

本文會展示如何實作資訊安全中心建議 」 啟用稽核 SQL 伺服器上 」。 若要進一步瞭解如何保護您的 SQL 資料庫，請參閱下列各項︰

- [保護您的 SQL 資料庫](../sql-database/sql-database-security.md)

若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定您的訂閱 Azure 和資源群組的安全性原則。
- [在 Azure 資訊安全中心管理安全性的建議](security-center-recommendations.md)，了解建議如何協助您保護 Azure 資源。
- [安全性狀況監控 Azure 資訊安全中心 」](security-center-monitoring.md) ，瞭解如何監視 Azure 資源的狀況。
- [管理及回應在 Azure 資訊安全中心的安全性提醒](security-center-managing-and-responding-alerts.md)--瞭解如何管理和回應的安全性警告。
- [監控合作夥伴解決方案與 Azure 資訊安全中心](security-center-partner-solutions.md)，瞭解如何監控您的合作夥伴解決方案的健康狀態。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找常見問題集使用服務。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-取得最新的 Azure 安全性新聞及資訊。

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing-on-sql-servers.png
[2]:./media/security-center-enable-auditing-on-sql-server/enable-auditing.png
[3]: ./media/security-center-enable-auditing-on-sql-server/auditing-settings-blade.png
