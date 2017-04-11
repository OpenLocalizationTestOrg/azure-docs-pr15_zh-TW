<properties
   pageTitle="開始使用 Azure 記錄整合 |Microsoft Azure"
   description="瞭解如何安裝 Azure 記錄整合服務，以及整合從 Azure 儲存空間、 Azure 稽核記錄和 Azure 資訊安全中心警示的記錄。"
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# <a name="get-started-with-azure-log-integration-preview"></a>開始使用 Azure 記錄整合 （預覽版本）

Azure 記錄整合可讓您從 Azure 資源的原始記錄整合內部部署安全性資訊及事件管理 (SIEM) 系統。 此整合提供整合儀表板的所有的資產，內部部署或雲端，以便您可以彙總、 相互關聯、 分析和相關聯的應用程式的安全性事件的提醒。

本教學課程中會引導您如何安裝 Azure 記錄整合，以及整合記錄的 Azure 儲存體] Azure 稽核記錄，以及 Azure 資訊安全中心的通知。 估計的時間才能完成此教學課程是一小時。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程中，您必須具備下列項目︰

- 電腦 (內部部署或雲端) 安裝 Azure 記錄整合服務。 這台電腦都必須執行 64 位元的 Windows 作業系統.net 4.5.1 安裝。 這台電腦稱為**Azlog 積分**。
- Azure 訂閱。 如果您沒有其中一個，您可以註冊[免費的帳戶](https://azure.microsoft.com/free/)。
- Azure 診斷啟用 Azure 虛擬機器 (Vm)。 若要啟用診斷雲端服務，請參閱[Azure 雲端服務中的 [啟用 Azure 診斷](../cloud-services/cloud-services-dotnet-diagnostics.md)。 若要啟用診斷程式的執行 Windows Azure VM，請參閱[使用 PowerShell 來啟用 Azure 虛擬機器執行 Windows 中的診斷程式](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)。
- 從 Azlog 積分 Azure 儲存空間，以驗證和 Azure 訂閱授權的連線。
- Azure VM 記錄 SIEM 代理程式 （例如，Splunk 通用轉寄站、 HP 討論 ArcSight Windows 事件行程代理程式或 IBM QRadar WinCollect） 必須安裝 Azlog 積分上。

## <a name="deployment-considerations"></a>部署考量

如果事件音量高，您可以執行多個 Azlog 積分執行個體。 負載平衡*(WAD)*在 Windows 版的 Azure 診斷儲存帳戶和訂閱提供的執行個體數目應該根據您的容量。

8 個處理器 （核心） 在電腦上，Azlog 積分一個執行個體可以處理關於 24 百萬事件每日 (~1M/hour)。

4 個處理器 （核心） 上一個執行個體 Azlog 積分可以處理 15 萬事件每日 (~62.5K/hour)。

## <a name="install-azure-log-integration"></a>安裝 Azure 記錄整合

下載[Azure 登整合](https://www.microsoft.com/download/details.aspx?id=53324)。

Azure 記錄整合服務會從其安裝所在的電腦收集遙測資料。  遙測資料收集是︰

- Azure 記錄整合執行期間發生的例外狀況
- 瞭解查詢和處理事件的數目的指標
- 命令列選項正在使用哪個 Azlog.exe 的相關統計資料

> [AZURE.NOTE] 您可以關閉遙測資料的集合，取消核取此選項。

## <a name="integrate-azure-vm-logs-from-your-azure-diagnostics-storage-accounts"></a>整合從 Azure 診斷儲存帳戶 Azure VM 記錄

1. 檢查以確保您 WAD 儲存的帳戶收集記錄，再繼續您 Azure 記錄整合上方所列的先決條件。 如果您 WAD 儲存的帳戶不收集記錄，並執行下列步驟。

2. 開啟命令提示字元**cd**至**c:\Program Files\Microsoft Azure 記錄整合**。

3. 執行命令

        azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>

      取代 StorageAccountName 設為接收來自您 VM 診斷事件 Azure 儲存體帳戶名稱。

        azlog source add azlogtest WAD azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==

      如果您想要顯示在事件 XML 的訂閱識別碼，加入好記的名稱的訂閱識別碼︰

        azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>

4. 等候 30 到 60 分鐘 （它可能會花小時），然後檢視會提取從儲存的帳戶的事件。 若要檢視，請開啟**事件檢視器] > Windows 記錄] > 轉寄事件**Azlog 積分上。

5. 請確定您的電腦上安裝的標準 SIEM 連接器的設定從 [**轉寄事件**] 資料夾中挑選 [事件和管道這些您 SIEM 執行個體。 檢閱設定，並查看整合記錄 SIEM 特定設定。

## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>如果資料未顯示在 [轉寄事件資料夾嗎？

如果小時後資料未顯示在 [**轉寄事件**] 資料夾，然後︰

1. 檢查電腦，並確認其可存取 Azure。 若要測試連線，請嘗試從瀏覽器中開啟[Azure 入口網站](http://portal.azure.com)。

2. 請確定使用者帳戶**azlog**在資料夾**users\azlog**中有 [寫入] 權限。

3. 請確定儲存帳戶新增**azlog 來源新增**的命令列執行**azlog 來源清單**中的 [命令] 時。
4. 移至 [**事件檢視器 > Windows 記錄] > 應用程式**若要查看是否有任何錯誤報告從 Azure 記錄的整合。

如果您仍然沒有看到 [事件，然後︰

1. 下載[Microsoft Azure 儲存檔案總管](http://storageexplorer.com/)。

2. 連線至新增命令**azlog 來源新增**儲存空間帳戶。

3. 在 Microsoft Azure 儲存的檔案總管，瀏覽至**WADWindowsEventLogsTable**若要查看是否有任何資料的資料表。 如果沒有出現，然後在 VM 診斷設定不正確。

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>整合 Azure 稽核記錄，以及資訊安全中心警示

1. 開啟命令提示字元**cd**至**c:\Program Files\Microsoft Azure 記錄整合**。

2. 執行命令

        azlog createazureid

      這個命令會提示您輸入您的 Azure 登入。 然後，] 命令會在主控 Azure 訂閱登入的使用者是以系統管理員，共同管理員或擁有者 Azure AD 租用戶中建立[Azure Active Directory 服務主要](../active-directory/active-directory-application-objects.md)。 登入的使用者是否只來賓使用者 Azure AD 租用戶中的，[] 命令將會失敗。 Azure 驗證是透過 Azure Active Directory (AD)。  建立以 Azlog 整合服務主要建立會提供從 Azure 訂閱的讀取權限的 Azure AD 身分識別。

3. 執行命令

        azlog authorize <SubscriptionID>

      這會指派在步驟 2 中建立的閱讀程式存取本金服務訂閱。 如果您不指定 SubscriptionID，則會指派所有的訂閱，您有任何存取服務本金讀者角色。

        azlog authorize 0ee9d577-9bc4-4a32-a4e8-c29981025328

      > [AZURE.NOTE] 如果您執行緊接**createazureid** ] 命令的 [**授權**] 命令，您可能會看到警告。 有一些延遲之間建立 Azure AD 帳戶時，帳戶時可供使用。 如果您即將 10 秒後執行**createazureid**命令執行 [**授權**] 命令，然後就不會看到這些警告。

4. 檢查以確認稽核記錄 JSON 檔有下列資料夾︰

  - **c:\Users\azlog\AzureResourceManagerJson**
  - **c:\Users\azlog\AzureResourceManagerJsonLD**

5. 檢查以確認資訊安全中心警示存在很下列資料夾︰

  - **c:\Users\azlog\ AzureSecurityCenterJson**
  - **c:\Users\azlog\AzureSecurityCenterJsonLD**

6. 標準 SIEM 檔案轉寄站連接器指向管道資料 SIEM 執行個體以適當的資料夾。 您可能需要根據您使用的 SIEM 產品的某些欄位對應。

如果您有 Azure 記錄整合的相關問題，請傳送電子郵件[AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您學到如何安裝 Azure 記錄整合，以及整合從 Azure 儲存空間的記錄。 若要深入瞭解，請參閱下列各項︰

- [Microsoft Azure 記錄整合 Azure 記錄 （預覽版本）](https://www.microsoft.com/download/details.aspx?id=53324) – 下載詳細資料、 系統需求和 Azure 記錄整合的安裝指示的中心。
- [Azure 記錄整合簡介](security-azure-log-integration-overview.md)– 這份文件會向您介紹 Azure 記錄整合、 其主要的功能及其運作方式。
- [合作夥伴設定步驟](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/)– 此部落格文章說明如何設定使用合作夥伴解決方案 Splunk HP 討論 ArcSight，與 IBM QRadar Azure 記錄整合。
- [Azure 記錄整合常見問題集 (faq)](security-azure-log-integration-faq.md) -此常見問題集解答關於 Azure 記錄整合的問題。
- [Azure 的拷貝資訊安全中心警示登整合](../security-center/security-center-integrating-alerts-with-log-integration.md)– 這份文件會顯示您如何同步處理資訊安全中心的通知，以及收集 Azure 診斷和 Azure 稽核記錄，以與記錄分析或 SIEM 解決方案的虛擬機器安全性事件。
- [Azure 診斷和 Azure 稽核記錄的新功能](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/)-此部落格文章會向您介紹 Azure 稽核記錄和其他功能，協助您獲得深入見解 Azure 資源的作業。
