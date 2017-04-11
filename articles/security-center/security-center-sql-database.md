<properties
   pageTitle="Azure 資訊安全中心] 和 [Azure SQL 資料庫服務 |Microsoft Azure"
   description="本文說明資訊安全中心如何協助您保護您的資料庫 Azure SQL 資料庫。"
   services="sql-database"
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
   ms.date="10/18/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-and-azure-sql-database-service"></a>Azure 資訊安全中心] 和 [Azure SQL 資料庫服務

[Azure 資訊安全中心 」](https://azure.microsoft.com/documentation/services/security-center/)可協助您避免偵測到，與回應威脅。 整合式的安全性的監控和原則管理提供您 Azure 訂閱，有助於偵測威脅，否則請忽略和相關事務的安全性解決方案的主要生態系統。

本文說明資訊安全中心如何協助您保護您的資料庫 Azure SQL 資料庫。

## <a name="why-use-security-center"></a>為什麼要使用資訊安全中心？

資訊安全中心 」 可協助您保護提供完善的安全性所有您的伺服器和資料庫的 SQL 資料庫中的資料。 使用資訊安全中心，您可以︰

- 定義 SQL 資料庫加密和稽核的原則。
- 在您的訂閱監控 SQL 資料庫資源的安全性。
- 快速找出並修復安全性問題。
- 整合[Azure SQL 資料庫威脅偵測](../sql-database/sql-database-threat-detection-get-started.md)的通知。

除了協助保護您的 SQL 資料庫的資源，安全中心也會提供安全性監控和管理的 Azure 虛擬機器、 雲端服務，應用程式服務、 虛擬網路等。 深入瞭解資訊安全中心[以下](security-center-intro.md)。

## <a name="prerequisites"></a>必要條件

若要開始與資訊安全中心，您必須使用 Microsoft Azure 的訂閱。 資訊安全中心的免費層啟用與您的訂閱。 如需有關資訊安全中心的空閒和標準層的詳細資訊，請參閱[安全性中心價格](https://azure.microsoft.com/pricing/details/security-center/)。

資訊安全中心支援角色型存取。 若要進一步瞭解 Azure 中的角色型存取控制 (RBAC)，請參閱[Azure Active Directory 角色型存取控制](../active-directory/role-based-access-control-configure.md)。 安全性中心常見問題集提供資訊[安全中心中的權限的處理方式](security-center-faq.md#how-are-permissions-handled-in-azure-security-center)。

## <a name="access-security-center"></a>存取資訊安全中心

您可以存取資訊安全中心從[Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)。 [登入入口網站](https://portal.azure.com/)，選取**資訊安全中心的選項**。

![資訊安全中心] 選項][1]

**資訊安全中心**刀隨即會開啟。
![資訊安全中心刀][2]

## <a name="set-security-policy"></a>設定的安全性原則

安全性原則可定義一組控制項所指定的訂閱或資源群組內的資源的建議。 您可以在資訊安全中心中, 定義訂閱] 或 [資源群組依據貴公司的安全性需求以及的應用程式類型或每個訂閱中資料的敏感度的原則。

您可以設定原則，以顯示 SQL 稽核和 SQL 透明資料加密 (TDE) 的建議。

- 當您開啟**SQL 稽核及威脅偵測**時，建議您為符合規範，進階偵測和調查的目的啟用稽核 Azure 資料庫的存取權的資訊安全中心。
- 當您開啟**SQL 透明資料加密**資訊安全中心建議靜止加密啟用 Azure SQL 資料庫、 相關聯的備份，和交易記錄檔。

若要設定的安全性原則，選取 [資訊安全中心刀**原則**磚。 在**安全性原則**刀中，選取您要啟用安全性原則的訂閱。 選取 [**防止原則**，**開啟您想要使用此訂閱的安全性建議**。
![安全性原則][3]

若要深入瞭解，請參閱[設定安全性原則](security-center-policies.md)。

## <a name="manage-security-recommendation"></a>管理安全性的建議

資訊安全中心定期分析 Azure 資源的安全性狀態。 當資訊安全中心] 會識別潛在安全性漏洞時，它會建立建議。 建議會引導您進行必要的控制項的程序。

您設定的安全性原則後，安全中心分析安全性的狀態來識別潛在的弱點資源。 建議會顯示在每一列代表一個特定的建議的位置以表格的格式。 使用下表的參考，可協助您瞭解 Azure SQL 資料庫]，與每個建議功能如果您將它套用提供建議。 選取 [建議會帶您到說明如何在資訊安全中心實作建議的文件。

| 建議 | 描述 |
| ----- | ----- |
| [啟用 SQL 伺服器上的稽核及威脅偵測](security-center-enable-auditing-on-sql-servers.md) | 建議您開啟稽核與威脅偵測 SQL 資料庫伺服器。 （SQL 資料庫服務。 不包含在您的虛擬機器上執行的 Microsoft SQL Server）。 |
| [啟用稽核及威脅偵測 SQL 資料庫](security-center-enable-auditing-on-sql-databases.md) | 建議您開啟稽核與威脅偵測 SQL 資料庫的資料庫。 （SQL 資料庫服務。 不包含在您的虛擬機器上執行的 Microsoft SQL Server）。 |
| [啟用透明資料加密](security-center-enable-transparent-data-encryption.md) | 建議您啟用 SQL 資料庫加密。 （僅限 SQL 資料庫服務。） |

若要查看 Azure 資源的建議，請選取資訊安全中心刀上的 [**建議**] 方塊。 在 [**建議**刀中，選取 [詳細資料的建議]。 在此範例中，讓我們來選取 [**啟用稽核及威脅偵測 SQL 伺服器上**。

![建議][4]

如下所示，安全中心 」 會顯示您未啟用稽核與威脅偵測 SQL server。 開啟稽核之後，您可以設定威脅偵測設定與電子郵件設定接收的安全性警告。 偵測到的表示資料庫的潛在安全性威脅異常資料庫活動時，威脅偵測會通知您。 通知] 會顯示在 [安全性中心儀表板。
![稽核與威脅偵測][5]

依照 [[快速入門 SQL 資料庫威脅偵測](../sql-database/sql-database-threat-detection-get-started.md)啟用及設定威脅偵測並設定的安全性警訊當偵測異常活動時，所收到的電子郵件清單中的步驟。

若要進一步瞭解建議，請參閱[管理安全性的建議](security-center-recommendations.md)。

## <a name="monitor-security-health"></a>監控安全性運作情況

啟用[安全性原則](security-center-policies.md)的訂閱的資源之後，資訊安全中心會分析您的資源來識別潛在的弱點的安全性。  在 [**資源安全性健康狀況**] 方塊中，您可以檢視您的資源的安全性狀態。 當您在 [**資源安全性健康狀況**] 方塊中按一下 [**資料**] 時，**資料資源**刀會開啟 SQL 建議，例如尚未啟用稽核和透明資料加密的問題。 也都有資料庫的一般狀況的建議。
![資源安全性狀況][6]

若要深入瞭解，請參閱[安全性健康監視](security-center-monitoring.md)。

## <a name="manage-and-respond-to-security-alerts"></a>管理及回應的安全性警告

資訊安全中心會自動收集、 分析，並整合從[Azure SQL 威脅偵測](../sql-database/sql-database-threat-detection-get-started.md)，以及其他 Azure 的資源，偵測威脅並減少誤判記錄資料。 優先順序的安全性提醒的清單所示資訊安全中心 」，以及快速調查的問題及如何修復攻擊建議所需的資訊。

若要查看通知，選取 [**安全性警告**磚上資訊安全中心刀。 在 [**安全性警訊**刀中，選取 [若要進一步瞭解事件觸發提醒和內容，如果您需要補救攻擊採取的步驟，任何通知]。 在此範例中，讓我們選取**潛在 SQL 插入**。
![安全性警告][7]

資訊安全中心如下所示，可提供深入了解觸發警示，適當的目標資源的其他詳細資料的來源 IP 位址及如何修補的建議。
![可能的 SQL 插入][8]

若要深入瞭解，請參閱[管理及回應的安全性警告](security-center-managing-and-responding-alerts.md)。

## <a name="next-steps"></a>後續步驟

- [安全性中心常見問題集](security-center-faq.md)-尋找使用服務的相關的常見問題集。
- [資訊安全中心規劃及作業指南](security-center-planning-and-operations-guide.md)，最佳化您的資訊安全中心使用一組步驟與工作的待會根據您組織的安全性需求和雲端管理模型。
- [安全性中心資料安全性](security-center-data-security.md)– 瞭解如何資訊安全中心 」 會收集，以及處理您 Azure 的資源，包括設定資訊、 中繼資料、 事件記錄檔、 當機傾印檔案，以及更多有關的資料。
- [處理安全性事件](security-center-incident.md)-瞭解如何使用在資訊安全中心的安全性提醒功能來協助您處理安全性事件。

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
