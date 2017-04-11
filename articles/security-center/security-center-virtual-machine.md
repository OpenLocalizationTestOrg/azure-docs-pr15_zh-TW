<properties
   pageTitle="Azure 資訊安全中心和 Azure 虛擬機器 |Microsoft Azure"
   description="這份文件可協助您瞭解如何 Azure 資訊安全中心可以保護您 Azure 虛擬機器。"
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-and-azure-virtual-machines"></a>Azure 資訊安全中心和 Azure 虛擬機器

[Azure 資訊安全中心 」](https://azure.microsoft.com/services/security-center/)可協助您避免偵測到，與回應威脅。 整合式的安全性的監控和原則管理提供您 Azure 訂閱，有助於偵測威脅，否則請忽略和相關事務的安全性解決方案的主要生態系統。

本文說明資訊安全中心如何協助您保護您 Azure 虛擬機器 (VM)。

## <a name="why-use-security-center"></a>為什麼要使用資訊安全中心？

資訊安全中心 」 可協助您保護 Azure 虛擬機器資料提供完善虛擬機器的安全性設定。 當資訊安全中心保護您的 Vm 時，將可使用下列功能︰

- 作業系統 (OS) 的建議的設定規則的安全性設定
- 系統安全性和遺失的重大更新
- 端點保護建議
- 磁碟加密驗證
- 弱點評估並修復
- 威脅偵測

除了協助保護您的 Azure Vm，資訊安全中心也會提供安全性監控和管理的雲端服務、 應用程式服務、 虛擬網路等。 

>[AZURE.NOTE] 請參閱[Azure 安全性中心簡介](security-center-intro.md)，若要進一步瞭解 Azure 資訊安全中心。

## <a name="prerequisites"></a>必要條件

若要開始與 Azure 資訊安全中心，您必須知道，考慮下列事項︰

- 您必須具備 Microsoft Azure 的訂閱。 如需詳細資訊安全中心 」 的免費和標準層上，請參閱[安全性中心價格](https://azure.microsoft.com/pricing/details/security-center/)。
- 規劃您的資訊安全中心採用，請參閱[Azure 資訊安全中心規劃及作業指南](security-center-planning-and-operations-guide.md)若要進一步瞭解規劃及作業的考量。
- 瞭解作業系統性的相關資訊，請參閱[Azure 資訊安全中心常見問題集 (FAQ)](security-center-faq.md)。 

## <a name="set-security-policy"></a>設定的安全性原則

必須啟用，該 Azure 資訊安全中心可以收集所提供的建議和警示，會產生所需的資訊會根據您設定的安全性原則，讓資料集合。 在下圖中，您可以看到**資料收集**已**開啟**。

安全性原則可定義控制項針對指定的訂閱或資源群組內的資源所建議的設定。 啟用之前的安全性原則，您必須啟用的資料收集，從您的虛擬機器才能評估安全性狀態、 提供安全性的建議，並通知您威脅資訊安全中心 」 會收集的資料。 在資訊安全中心，您可以定義原則您 Azure 訂閱] 或 [資源群組依據貴公司的安全性需求以及的應用程式類型或區分大小寫的每個訂閱中的資料。 

![安全性原則](./media/security-center-virtual-machine/security-center-virtual-machine-fig1.png)

>[AZURE.NOTE] 若要進一步瞭解每個**防止原則**可用，請參閱[設定安全性原則](security-center-policies.md)文章。

## <a name="manage-security-recommendations"></a>管理安全性的建議

資訊安全中心分析 Azure 資源的安全性狀態。 當資訊安全中心] 會識別潛在安全性漏洞時，它會建立建議。 建議會引導您進行必要的控制項的程序。

設定的安全性原則之後, 資訊安全中心分析您的資源來識別潛在的弱點的安全性狀態。 建議會顯示每一列代表一個特定的建議的位置以表格格式。 下表將提供一些範例的 Azure Vm 和每個會做什麼當您將它套用的建議。 當您選取的建議時，您將會提供說明如何在資訊安全中心實作建議的資訊。

|建議|描述|
|-----|-----|
|[啟用資料收集適用於的訂閱](security-center-enable-data-collection.md)|建議您在您的訂閱中開啟的每一個您的訂閱及所有的虛擬機器 (Vm) 的安全性原則中的資料集合。|
|[修復 OS 弱點](security-center-remediate-os-vulnerabilities.md)|建議您對齊 OS 組態建議的設定規則，例如不允許儲存密碼。|
|[套用系統更新](security-center-apply-system-updates.md)|建議您先部署至 Vm 遺失系統安全與重要更新。|
|[系統更新後重新啟動](security-center-apply-system-updates.md#reboot-after-system-updates)|建議您重新啟動 VM 完成套用系統更新的程序。|
|[安裝端點保護](security-center-install-endpoint-protection.md)|建議您佈建 Vm (Windows Vm) 的反惡意程式碼程式。|
|[解決端點保護狀況通知](security-center-resolve-endpoint-protection-health-alerts.md)|建議您解決端點保護失敗。|
|[啟用 VM 代理程式](security-center-enable-vm-agent.md)|可讓您看到 Vm 需要 VM 代理程式。 VM 代理人必須安裝在 Vm，才能佈建修補程式掃描掃描的比較基準和反惡意程式碼的程式。 預設會安裝 VM 代理程式的部署從 Azure Marketplace 的 Vm。 文章[VM 代理程式 」 和 「 延伸模組-第 2 部分](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)提供如何安裝 VM 代理程式的資訊。|
| [套用磁碟加密](security-center-apply-disk-encryption.md) |建議您加密 VM 磁碟使用 Azure 磁碟加密 （Windows 和 Linux Vm）。 在您 VM OS 和資料區建議加密。|
| [未安裝的弱點評估](security-center-vulnerability-assessment-recommendations.md) | 建議您在您 VM 安裝弱點評估解決方案。 |
| [修復弱點](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | 可讓您看到偵測到安裝在您 VM 弱點評估方案的系統和應用程式弱點。 |

>[AZURE.NOTE] 若要進一步瞭解建議，請參閱[管理安全性的建議](security-center-recommendations.md)文件。

## <a name="monitor-security-health"></a>監控安全性運作情況

啟用[安全性原則](security-center-policies.md)的訂閱的資源之後，資訊安全中心會分析您的資源來識別潛在的弱點的安全性。  您可以檢視您的資源，以及任何**資源安全性狀況**刀問題的安全性狀態。 當您按一下**虛擬機器****資源安全性**狀況磚中時，建議您 Vm 會開啟**虛擬機器**刀。 

![安全性狀況](./media/security-center-virtual-machine/security-center-virtual-machine-fig2.png)

## <a name="manage-and-respond-to-security-alerts"></a>管理及回應的安全性警告

資訊安全中心會自動收集、 分析，並偵測威脅並減少誤判整合資料來自 Azure 資源、 網路和連線的合作夥伴解決方案 （例如防火牆及端點保護解決方案）。 利用各種不同的彙總[偵測功能](security-center-detection-capabilities.md)的資訊安全中心是可以產生優先順序的安全性警訊，可協助您快速調查問題，並提供如何修補可能攻擊的建議。

![安全性警告](./media/security-center-virtual-machine/security-center-virtual-machine-fig3.png)

選取 [安全性提醒中，若要進一步瞭解事件觸發提醒和內容，如果任何，您需要補救攻擊採取的步驟。 依[類型](security-center-alerts-type.md)及日期分組的安全性警告。


## <a name="see-also"></a>另請參閱

若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定您的訂閱 Azure 和資源群組的安全性原則。
- [管理及回應在 Azure 資訊安全中心的安全性提醒](security-center-managing-and-responding-alerts.md)--瞭解如何管理和回應的安全性警告。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找常見問題集使用服務。
