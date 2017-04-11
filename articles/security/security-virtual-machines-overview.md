<properties
   pageTitle="Azure 虛擬機器安全性概觀 |Microsoft Azure"
   description=" Azure 虛擬機器讓您虛擬化的彈性，而不需購買及維護執行虛擬機器的實體硬體。  本文提供的核心概觀 Azure 安全性功能，可供使用 Azure 虛擬機器。 "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="azure-virtual-machines-security-overview"></a>Azure 虛擬機器安全性概觀

Azure 虛擬機器可讓您部署大範圍的運算解決方案敏捷式的方式。 支援 Microsoft Windows、 Linux、 Microsoft SQL Server、 Oracle、 IBM、 SAP 和 Azure BizTalk 服務，您可以部署任何工作量及在幾乎任何作業系統上的任何語言。

Azure 虛擬機器可讓您虛擬化的彈性，而不需購買及維護執行虛擬機器的實體硬體。  您可以建立並部署您的應用程式，確保您的資料是受到保護，我們強烈安全的資料中心的安全。

使用 Azure，您可以建立安全的、 標準解決方案的︰

- 保護您的虛擬機器病毒和惡意程式碼
- 加密機密資料
- 安全的網路流量
- 找出並偵測威脅
- 符合規範要求

本文的目標是核心的提供概觀 Azure 安全性功能，可供虛擬機器。 我們也會提供授與每項功能的詳細資料，因此您可以瞭解更多的文件的連結。  

本文涵蓋核心 Azure 虛擬機器安全性功能︰

- 反惡意程式碼
- 硬體安全性模組
- 虛擬機器磁碟加密
- 虛擬機器備份
- Azure 網站復原
- 虛擬網路
- 安全性原則管理與報告
- 法規遵循

## <a name="antimalware"></a>反惡意程式碼

您可以使用 Azure 反惡意程式碼軟體，例如 Microsoft、 Symantec、 趨勢微、 McAfee 及 Kaspersky 安全性廠商惡意檔案、 廣告軟體及其他威脅保護您的虛擬機器。 請參閱詳細了解下方的章節，尋找合作夥伴文章的解決方案。

Microsoft 反惡意程式碼 Azure 雲端服務和虛擬機器是有助於識別和移除的病毒、 間諜軟體及其他惡意軟體的即時保護功能。  Microsoft 反惡意程式碼會提供可設定已知惡意或有害軟體嘗試安裝本身或 Azure 系統上執行時的通知。

Microsoft 反惡意程式碼是單一代理程式的解決方案的應用程式與租用戶的環境中，設計不會出現人力互動背景中執行。 您可以部署保護根據您的應用程式工作負載，與 [基本安全依預設的需求或進階自訂設定，包括監控反惡意程式碼。

當您部署，並啟用 Microsoft 反惡意程式碼時，核心的可用功能如下︰

- 即時保護-監視器在雲端服務，然後在虛擬機器偵測並封鎖惡意程式碼執行的活動。
- 定期排程掃描-執行目標掃描偵測惡意程式碼，包括執行程式。
- 惡意程式碼補救-自動會偵測到惡意程式碼，例如刪除或隔離惡意檔案及清除惡意登錄項目採取動作。
- 簽章更新-自動安裝最新的保護簽名 （病毒定義） 以確保保護是最新的預定的頻率。
- 反惡意程式碼引擎更新 – 自動更新 Microsoft 反惡意程式碼引擎。
- 反惡意程式碼的平台更新 – 自動更新 Microsoft 反惡意程式碼的平台。
- 作用中的保護-偵測到的威脅和可疑的資源，以確保快速回應的相關報告 Azure 遙測中繼資料，並啟用即時同步的簽章傳送到 Microsoft 作用中保護系統 （地圖）。
- 範例報告-提供和報表範例以協助改善服務，並啟用疑難排解 Microsoft 反惡意程式碼服務。
- 排除項目-可讓應用程式和服務系統管理員設定的特定檔案，處理程序，並排除保護與效能和其他原因掃描磁碟機。
- 反惡意程式碼事件集合-記錄反惡意程式碼服務健康狀況可疑的活動，與作業系統的事件記錄檔中的修復作業，並收集其客戶的 Azure 儲存體帳戶。

深入瞭解︰ 若要深入瞭解保護您的虛擬機器反惡意程式碼軟體，請參閱︰

- [Microsoft 反惡意程式碼 Azure 雲端服務和虛擬機器](../security/azure-security-antimalware.md)
- [Azure 虛擬機器上的部署反惡意程式碼解決方案](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
- [如何安裝和設定趨勢微深層安全性為 Windows VM 的服務](../virtual-machines/virtual-machines-windows-classic-install-trend.md)
- [如何安裝和設定 Windows VM Symantec 端點保護](../virtual-machines/virtual-machines-windows-classic-install-symantec.md)
- [新的反惡意程式碼選項，來保護 Azure 虛擬機器 – McAfee 端點保護](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)
- [在 Azure Marketplace 的安全性解決方案](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>硬體安全性模組

加密和驗證並未改善安全性除非金鑰受到保護。 您可以將它們儲存在 Azure 金鑰保存庫，以簡化重要的機密資料與索引鍵的安全性與管理。 索引鍵保存庫提供儲存在 [硬體安全性模組 (Hsm) 索引鍵經過認證、 可 FIPS 140-2 層級 2 標準的選項。 SQL Server 加密金鑰備份或[透明資料加密](https://msdn.microsoft.com/library/bb934049.aspx)可以所有儲存在金鑰保存庫任何鍵或從應用程式的密碼。 權限和存取這些受保護的項目會透過[Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)來管理。

深入瞭解︰

- [什麼是 Azure 金鑰保存庫？](../key-vault/key-vault-whatis.md)
- [Azure 鍵保存庫快速入門](../key-vault/key-vault-get-started.md)
- [Azure 鍵保存庫部落格](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>虛擬機器磁碟加密

Azure 磁碟加密是新功能，可讓您加密 Windows 和 Linux Azure 虛擬機器磁碟。 Azure 磁碟加密使用 Windows 的業界標準[BitLocker](https://technet.microsoft.com/library/cc732774.aspx)功能和 Linux 的[dm 窖](https://en.wikipedia.org/wiki/Dm-crypt)功能提供大量加密 OS 和資料磁碟。

Azure 鍵保存庫，可協助您控制與管理磁碟加密金鑰和金鑰保存庫訂閱，並確保在您 Azure 儲存體中其餘的加密磁碟虛擬機器中的所有資料中的機密資料整合解決方案。

深入瞭解︰

- [Windows 和 Linux IaaS Vm azure 磁碟加密](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)
- [Azure 磁碟加密 Linux 和 Windows 虛擬機器](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)
- [加密虛擬機器](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>虛擬機器備份

Azure 備份為保護您的應用程式的資料零資金投資與最小的操作成本的可調整解決方案。 應用程式錯誤可能會損毀您的資料，並人力錯誤可能會引入應用程式錯誤。 使用 Azure 備份您執行 Windows 和 Linux 的虛擬機器受保護狀態。

深入瞭解︰

- [什麼是 Azure 備份？](../backup/backup-introduction-to-azure-backup.md)
- [Azure 備份學習路徑](https://azure.microsoft.com/documentation/learning-paths/backup/)
- [Azure 備份服務-常見問題集](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure 網站復原

貴組織的 BCDR 策略很重要的一部分是找出如何讓公司的工作量及應用程式，然後執行計劃和尚未計劃中斷問題發生時。 Azure 網站復原可協助協調複寫、 錯誤移轉及復原的工作量及應用程式，使其可從第二個位置如果您的主要位置當機。

網站修復︰

- **Simplifies BCDR 策略**— 網站復原可讓您輕鬆地處理複寫、 錯誤移轉及復原的多個商務工作負載及應用程式從單一位置。 網站復原協調複寫和容錯移轉，但不會攔截您應用程式的資料或有任何資訊。
- **提供非常有彈性的試驗**，使用網站復原，您可以複製 HYPER-V 虛擬機器、 VMware 虛擬機器，以及 Windows/Linux 實體伺服器上執行的工作量。
- **支援錯誤移轉及復原**— 網站復原提供支援損毀復原切入，而不會影響生產環境中測試容錯移轉。 您也可以執行計劃的容錯移轉與預期中斷，零資料遺失或意外的錯誤後移轉與的未預期的損毀的最小的資料遺失 （根據複寫頻率）。 後移轉後，您可以回復您主要的網站。 網站復原提供修復計劃，包括指令碼和 Azure 自動化活頁簿，讓您可以自訂錯誤移轉及復原多層應用程式。
- **消除次要資料中心**，您可以複製至內部部署的次要網站，或 Azure。 Azure 作為目的地用損毀修復排除的成本和複雜度維護次要網站。 複製的資料會儲存於 Azure 儲存體。
- **將現有的 BCDR 技術**，網站復原合作夥伴與其他應用程式 BCDR 功能。 例如，您可以使用網站復原保護公司的工作負載的 SQL Server 後端。 這包含原生支援的 SQL Server AlwaysOn 管理可用性群組的容錯移轉。

深入瞭解︰

- [什麼是 Azure 網站復原？](../site-recovery/site-recovery-overview.md)
- [Azure 網站復原如何運作？](../site-recovery/site-recovery-components.md)
- [什麼負載受到 Azure 網站復原嗎？](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>虛擬網路

虛擬機器需要網路連線。 若要支援這項需求，Azure 需要連線到 Azure 虛擬網路的虛擬機器。 Azure 虛擬網路是使用實體 Azure 網路布料的轉印圖樣建立邏輯建構。 每個邏輯 Azure 虛擬網路是隔離的所有其他 Azure 虛擬網路。 此隔離有助於確保您部署中的網路流量無法存取其他 Microsoft Azure 客戶。

深入瞭解︰

- [Azure 網路安全性概觀](security-network-overview.md)
- [虛擬網路概觀](../virtual-network/virtual-networks-overview.md)
- [網路功能和合作關係企業案例](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>安全性原則管理與報告

Azure 資訊安全中心 」 可協助您避免偵測到，與回應威脅，，並提供您增加完善，並控制，您 Azure 資源的安全性。 整合式的安全性的監控和原則管理提供您 Azure 訂閱，有助於偵測威脅，否則請忽略和相關事務的安全性解決方案的主要生態系統。

Azure 資訊安全中心 」 可協助您最佳化，並監控透過虛擬機器安全︰

- 例如提供虛擬機器[安全性的建議](../security-center/security-center-recommendations.md)套用系統更新、 設定 Acl 端點、 啟用反惡意程式碼，啟用網路安全性群組，並套用磁碟加密。
- 監控您的虛擬機器中的狀態

深入瞭解︰

- [Azure 安全性中心簡介](../security-center/security-center-intro.md)
- [Azure 資訊安全中心常見問題集](../security-center/security-center-faq.md)
- [Azure 資訊安全中心規劃及作業](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>法規遵循

Azure 虛擬機器已經 FISMA、 FedRAMP、 HIPAA、 PCI DSS 階層 1 及其他索引鍵的規範程式的認證。 這個憑證容易符合規範要求您自己 Azure 應用程式和您的公司地址大範圍的國內及國際法規要求。

深入瞭解︰

- [Microsoft 信任中心︰ 法規遵循](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)
- [信任的雲端︰ Microsoft Azure 安全性、 隱私權和法規遵循](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)
