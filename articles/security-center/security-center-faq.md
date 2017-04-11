<properties
   pageTitle="Azure 資訊安全中心常見問題集 (FAQ) |Microsoft Azure"
   description="此常見問題集解答關於 Azure 資訊安全中心的問題。"
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
   ms.date="10/27/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-frequently-asked-questions-faq"></a>Azure 資訊安全中心常見問題集 (faq)

此常見問題集解答 Azure 資訊安全中心，協助您避免、 偵測和回應威脅到增強可見性控制您的 Microsoft Azure 資源的安全性與服務的相關問題。

## <a name="general-questions"></a>一般問題

### <a name="what-is-azure-security-center"></a>什麼是 Azure 資訊安全中心？
Azure 資訊安全中心可協助您避免、 偵測，和回應威脅控制您 Azure 資源的安全性與到增強可見性。 會提供整合式的安全性的監控和原則管理您的訂閱，可協助偵測威脅，否則請忽略，並可搭配使用的安全性解決方案的主要生態系統。

### <a name="how-do-i-get-azure-security-center"></a>我要如何取得 Azure 資訊安全中心？
Azure 資訊安全中心是啟用與您的 Microsoft Azure 訂閱，並從[Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)存取。 （[登入入口網站](https://portal.azure.com)，請選取 [**瀏覽]**，然後向下捲動至**資訊安全中心**）。  

## <a name="billing"></a>帳單

### <a name="how-does-billing-work-for-azure-security-center"></a>Azure 資訊安全中心的帳單的運作方式？
資訊安全中心提供兩層︰ 免費和標準。

免費層可讓您設定的安全性原則及接收安全性警訊與事件，建議將引導您進行必要的控制項的程序。 您也可以監視安全性狀態的 Azure 資源和整合 Azure 訂閱的合作夥伴解決方案與免費層。

標準層提供免費層功能加號進階偵測︰ 威脅智慧與行為分析、 當機分析異常偵測。 使用免費的試用版 90 天的標準層。 若要升級，請選取價格層中[的安全性原則](security-center-policies.md#setting-security-policies-for-subscriptions)。 若要深入瞭解，請參閱[資訊安全中心價格](security-center-pricing.md)。

## <a name="data-collection"></a>資料收集

從您的虛擬機器才能評估安全性狀態、 提供安全性的建議，並通知您威脅資訊安全中心 」 會收集的資料。 當您第一次存取資訊安全中心 」 時，您的訂閱中的所有虛擬機器都啟用資料集合。 資料收集建議，但您可以退出資訊安全中心原則中的 [[停用資料集合](#how-do-i-disable-data-collection)。

### <a name="how-do-i-disable-data-collection"></a>如何停用資料收集？

您可以隨時在安全性原則訂閱來停用**資料集合**。 （[登入 Azure 入口網站](https://portal.azure.com)上，選取 [**瀏覽**、 選取**資訊安全中心 」**，然後選取**原則**。） 當您選取訂閱時，新的刀會開啟，並提供您關閉**資料收集**的選項。 若要移除現有的虛擬機器中的代理程式頂端功能區中選取 [**刪除代理程式**的選項。

> [AZURE.NOTE] 可設定安全性原則 Azure 訂閱層級和資源群組層級，但您必須先選取訂閱關閉資料集合。

### <a name="how-do-i-enable-data-collection"></a>我要如何啟用資料集合？
您可以啟用安全性原則中您 Azure 訂閱資料集合。 若要啟用資料收集，[登入 Azure 入口網站](https://portal.azure.com)上，選取 [**瀏覽****資訊安全中心**]，然後選取選取**原則**。 將**資料集合**設定為 [**開啟**並設定您要放置收集資料的儲存空間帳戶 (請參閱問題 」[我的資料存放？](#where-is-my-data-stored)」)。 啟用**資料集合**時，它會自動收集安全性設定及事件資訊從訂閱中的所有支援虛擬機器。

> [AZURE.NOTE] 可設定安全性原則 Azure 訂閱層級和資源群組層級，但只有訂閱層級時所發生的資料收集設定。

### <a name="what-happens-when-data-collection-is-enabled"></a>啟用資料集合時，會發生什麼情況？
資料收集已啟用透過 Azure 監控代理程式和 Azure 安全性監視副檔名。 Azure 安全性監視副檔名為掃描各種安全性相關設定，並將它傳送到[Windows 事件追蹤](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx)(ETW) 追蹤。 此外，系統會建立的事件記錄項目。  Azure 監控代理程式會讀取事件記錄項目並 ETW 追蹤，並將其複製到您儲存的帳戶進行分析。  這是您設定的安全性原則中的儲存空間帳戶。 如需儲存帳戶的詳細資訊，請參閱問題 」[我的資料存放？](#where-is-my-data-stored)」

### <a name="does-the-monitoring-agent-or-security-monitoring-extension-impact-the-performance-of-my-servers"></a>監控代理人或安全性監視副檔名會影響我選擇的效能？
代理程式與分機號碼會耗費的系統資源 nominal 數量，應該小影響效能。

### <a name="where-is-my-data-stored"></a>我的資料儲存在哪裡？
每個地區必須執行的虛擬機器，您可以選擇從這些虛擬機器中收集的資料儲存位置的儲存空間帳戶。 如此可讓您輕鬆地讓資料保持在相同的地理區域的隱私權與資料主權用途。 您可以選擇訂閱的儲存空間帳戶中的安全性原則。 （[登入 Azure 入口網站](https://portal.azure.com)上，選取 [**瀏覽**、 選取**資訊安全中心 」**，然後選取**原則**。）當您按一下 [訂閱時，隨即會開啟新的刀。 選取 [選取一個區域**選擇儲存帳戶**]。

> [AZURE.NOTE] 可設定安全性原則 Azure 訂閱層級和資源群組層級，但選取的區域，您儲存帳戶時所發生的訂閱等級。

若要瞭解 Azure 儲存和儲存帳戶的詳細資訊，請參閱[儲存文件](https://azure.microsoft.com/documentation/services/storage/)和[相關 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)。

## <a name="using-azure-security-center"></a>使用 Azure 資訊安全中心

### <a name="what-is-a-security-policy"></a>什麼是安全性原則？
安全性原則可定義控制項針對指定的訂閱或資源群組內的資源所建議的設定。 在 Azure 資訊安全中心，您可以定義原則 Azure 訂閱和資源群組依據貴公司的安全性需求以及的應用程式類型或區分大小寫的每個訂閱中的資料。

例如，用於開發或測試資源可能比生產應用程式使用不同的安全性需求。 同樣地，使用規定的資料，例如 PII （個人識別資訊） 應用程式可能會要求較高的安全性。 Azure 資訊安全中心中啟用安全性原則會驅動安全性的建議與監視。 若要進一步瞭解安全性原則，請參閱[監控 Azure 資訊安全中心的安全性狀況](security-center-monitoring.md)。

> [AZURE.NOTE] 若訂閱層級的原則和資源群組層級原則之間的衝突，資源群組層級原則的優先順序。

### <a name="who-can-modify-a-security-policy"></a>誰可以修改安全性原則？
安全性原則會設定為每個訂閱或資源群組。 若要修改的訂閱層級或資源群組層級的安全性原則，您必須擁有者或參與者的該訂閱。

若要瞭解如何設定的安全性原則，請參閱[Azure 資訊安全中心中的設定安全性原則](security-center-policies.md)。

### <a name="what-is-a-security-recommendation"></a>什麼是安全性的建議事項？
Azure 資訊安全中心分析 Azure 資源的安全性狀態。 潛在安全性漏洞識別時，會建立建議。 建議會引導您完成程序設定所需的控制項。 以下是範例︰

- 佈建的反惡意程式碼，以便識別及移除惡意軟體
- 設定[網路安全性群組](../virtual-network/virtual-networks-nsg.md)以及規則來控制流量虛擬機器
- 佈建的 web 應用程式的防火牆，以協助防範攻擊目標 web 應用程式
- 部署遺失系統更新
- 處理 OS 設定不符合建議的比較基準

安全性原則中已啟用的建議如下所示。

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>我可以看到我的 Azure 資源的目前安全性狀態？
**資訊安全中心**刀**資源狀況**磚會顯示您的環境的虛擬機器、 web 應用程式，與其他資源的整體安全性狀態。 如果已識別任何潛在的安全性漏洞，每個資源會有指標顯示。 按一下 [資源健康狀況] 方塊顯示您的資源，並識別位置注意資格，或可能會有問題。

### <a name="what-triggers-a-security-alert"></a>觸發的安全性提醒中？
Azure 資訊安全中心會自動收集、 分析和 fuses Azure 資源、 網路、 與合作夥伴解決方案，例如反惡意程式碼和防火牆記錄資料。 未偵測到威脅，就會建立的安全性提醒中。 範例包括偵測的︰

- 洩漏已知惡意的 IP 位址與通訊的虛擬機器
- 使用 Windows 錯誤報告偵測到的進階惡意程式碼
- 強制攻擊針對虛擬機器
- 整合式的合作夥伴安全性解決方案，例如反惡意程式碼或 Web 應用程式防火牆的安全性警告

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>什麼是威脅偵測並收到上 Microsoft 資訊安全回應中心與 Azure 資訊安全中心之間的差異？
Microsoft 安全性回應中心 (MSRC) 執行選取的安全性監視 Azure 網路和基礎結構，並從協力廠商接收威脅智慧和濫用抱怨。 注意非法或未經授權的廠商已存取客戶資料，或的 Azure 客戶使用不符合的各項的可接受的使用 MSRC 時，安全性事件管理員會通知客戶。 通知通常會發生電子郵件傳送至指定 Azure 資訊安全中心或 Azure 訂閱擁有者，如果未指定安全性連絡人的安全性聯絡人。

資訊安全中心是活動的 Azure 服務的持續監視客戶 Azure 環境，適用於分析，自動偵測大範圍的潛在惡意。 這些偵測呈現為資訊安全中心儀表板中的安全性警告。

### <a name="how-are-permissions-handled-in-azure-security-center"></a>權限是如何處理在 Azure 資訊安全中心？
Azure 資訊安全中心支援角色型存取。 若要進一步瞭解 Azure 中的角色型存取控制 (RBAC)，請參閱[Azure Active Directory 角色型存取控制](../active-directory/role-based-access-control-configure.md)。

當使用者開啟資訊安全中心，只建議和會看到使用者有權存取的資源相關的通知。 這表示使用者只會看到相關的資源，使用者指派給角色的擁有者 」、 「 參與者或 「 讀取訂閱或資源所屬的資源群組的項目。

如果您需要︰

- **編輯安全性原則**，您必須是擁有者或參與者的訂閱。
- **套用建議**] 下，您必須是擁有者或參與者的訂閱。
- **有完善的安全性狀態，在所有您的訂閱**，您必須擁有者，參與者或閱讀程式 （[安全性小組中的 [IT 管理員） 的每個訂閱。
- **有完善資源的安全性狀態**，您必須是資源群組的擁有者，參與者或閱讀程式 (DevOps)。

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Azure 資源監視 Azure 資訊安全中心？
Azure 資訊安全中心監視 Azure 下列資源︰

- 虛擬機器 (Vm) （包括[雲端服務](../cloud-services/cloud-services-choose-me.md)）
- Azure 虛擬網路
- Azure SQL 服務
- 整合式 web 應用程式防火牆 Vm 及[應用程式服務環境](../app-service/app-service-app-service-environments-readme.md)例如 Azure 訂閱的合作夥伴解決方案

## <a name="virtual-machines"></a>虛擬機器

### <a name="what-types-of-virtual-machines-will-be-supported"></a>哪些類型的虛擬機器會支援嗎？
安全性健康監視與建議事項，可使用兩個建立[傳統和資源管理員部署模型](../azure-classic-rm.md)的虛擬機器 (Vm)。

支援 Windows Vm:

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

支援 Linux Vm:

- Ubuntu 版本 12.04，14.04 16.04
- Debian 版本 7 8
- CentOS 版本 6。\*, 7.*
- 紅色角色企業 Linux (RHEL) 版本 6。\*, 7.*
- SUSE Linux Enterprise Server (SLES) 版本 11。\*, 12.*
- Oracle Linux 版本 6。\*, 7.*

在雲端服務中執行的 Vm 也受到支援。 僅限雲端服務的生產監視位置中執行的網頁和背景工作角色。 若要進一步瞭解雲端服務，請參閱[雲端服務的概觀](../cloud-services/cloud-services-choose-me.md)。

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Azure 資訊安全中心為何無法辨識我 Azure VM 上執行的反惡意程式碼解決方案？

Azure 資訊安全中心只有完善透過 Azure 副檔名所安裝的反惡意程式碼。 例如，資訊安全中心無法偵測反惡意程式碼中已預先安裝在您所提供的影像，或如果您在您使用您自己的程序 （例如設定管理系統） 的虛擬機器上安裝反惡意程式碼。

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>為什麼取得郵件 「 遺失掃描資料 」 我 VM？

可能需要一些時間 （通常少於小時） 來填入之後 Azure 資訊安全中心中啟用資料收集掃描資料。 掃描不會填入的 Vm 中停止狀態。

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>為什麼郵件 」 VM 代理程式遺漏？ 」

VM 代理人必須安裝在 Vm，才能啟用資料集合。 預設會安裝 VM 代理程式的部署從 Azure Marketplace 的 Vm。 如何在其他 Vm 安裝 VM 代理程式的資訊，請參閱部落格文章[VM 代理程式及副檔名](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)。
