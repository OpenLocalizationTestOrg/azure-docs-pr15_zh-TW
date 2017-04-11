<properties
   pageTitle="Azure 記錄整合 （預覽版本） 整合 Azure 資訊安全中心警示 |Microsoft Azure"
   description="本文可協助您開始使用整合整合 Azure 記錄的資訊安全中心警示。"
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
   ms.date="08/08/2016"
   ms.author="terrylan"/>

# <a name="integrating-security-center-alerts-with-azure-log-integration-preview"></a>整合資訊安全中心警示 Azure 記錄整合 （預覽版本）

許多安全性作業和事件回應小組仰賴安全性資訊及事件管理 (SIEM) 解決方案分類和調查的安全性警告的起點。 整合 Azure 記錄客戶可以同步處理 Azure 資訊安全中心的通知，以及 Azure 診斷和 Azure 稽核記錄，收集記錄分析或 SIEM 解決方案幾乎即時的虛擬機器安全性事件。

Azure 記錄整合搭配 HP 討論 ArcSight Splunk、 IBM QRadar 與其他人。

## <a name="what-logs-can-i-integrate"></a>可以整合哪些記錄？

Azure 產生每個服務的更多的記錄。 這些記錄分類為︰

- **控制/管理記錄**提供完善的 Azure 資源管理員建立、 更新及刪除作業。
- **資料平面記錄**讓完善時使用的 Azure 資源引發的事件。 範例的 Windows 事件記錄檔的安全性，而記錄虛擬機器中的應用程式。

Azure 記錄整合目前支援的整合︰

- Azure VM 記錄
- Azure 稽核記錄
- Azure 資訊安全中心的通知

## <a name="install-azure-log-integration"></a>安裝 Azure 記錄整合

下載[Azure 登整合](https://www.microsoft.com/download/details.aspx?id=53324)。

Azure 記錄整合服務會從其安裝所在的電腦收集遙測資料。  遙測資料收集是︰

- Azure 記錄整合執行期間發生的例外狀況
- 瞭解查詢和處理事件的數目的指標
- 命令列選項正在使用哪個 Azlog.exe 的相關統計資料

> [AZURE.NOTE] 您可以關閉遙測資料的集合，取消核取此選項。

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>整合 Azure 稽核記錄檔和安全性中心的通知

1. 開啟命令提示字元**cd**至**c:\Program Files\Microsoft Azure 記錄整合**。

2. 執行主控 Azure 訂閱 Azure Active Directory (AD) 租用戶中建立的[Azure Active Directory 服務主要](../active-directory/active-directory-application-objects.md) **azlog createazureid**命令。

    系統會提示您 Azure 登入。

    > [AZURE.NOTE] 您必須是訂閱擁有者或訂閱的共同管理員。

    Azure 驗證是透過 Azure AD 完成。  建立以 Azure 記錄整合服務主體會建立會提供從 Azure 訂閱的讀取權限的 Azure AD 身分識別。

3. 執行**azlog 授權<SubscriptionID>**] 命令，將訂閱的閱讀程式存取指派給您在步驟 2 中建立的服務本人。 如果您不指定**SubscriptionID**，然後服務主要會讀取者角色為指定所有的訂閱您具有存取權。

    > [AZURE.NOTE] 如果您將執行 [**授權**] 命令緊接**createazureid** ] 命令，因為有一些延遲之間建立 Azure AD 帳戶時，可供使用的帳戶時，您可能會看到警告。 如果您即將 10 秒後執行**createazureid**命令執行 [**授權**] 命令，然後就不會看到這些警告。

4. 檢查以確認稽核記錄 JSON 檔有下列資料夾︰

  - **c:\Users\azlog\AzureResourceManagerJson**
  - **c:\Users\azlog\AzureResourceManagerJsonLD**

5. 檢查以確認資訊安全中心警示存在很下列資料夾︰

  - **c:\Users\azlog\ AzureSecurityCenterJson**
  - **c:\Users\azlog\AzureSecurityCenterJsonLD**

6. 標準 SIEM 檔案轉寄站連接器指向管道資料 SIEM 執行個體以適當的資料夾。 請參閱[SIEM 設定](https://azsiempublicdrops.blob.core.windows.net/drops/ALL.htm)您的 SIEM 設定。

如果您有 Azure 記錄整合的相關問題，請傳送電子郵件[AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>後續步驟

若要瞭解 Azure 稽核記錄和屬性定義的詳細資訊，請參閱︰

- [稽核作業與資源管理員](../resource-group-audit.md)
- [清單管理中的事件訂閱](https://msdn.microsoft.com/library/azure/dn931934.aspx)-擷取的稽核記錄事件。

若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [管理及回應在 Azure 資訊安全中心的安全性警告](security-center-managing-and-responding-alerts.md)，瞭解如何管理和回應的安全性警告。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找使用服務的相關的常見問題集。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-取得最新的 Azure 安全性新聞及資訊。
