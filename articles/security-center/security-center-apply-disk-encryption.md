<properties
   pageTitle="套用 Azure 資訊安全中心中的磁碟加密 |Microsoft Azure"
   description="這份文件為您示範如何實作 Azure 資訊安全中心建議**套用磁碟加密**。"
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

# <a name="apply-disk-encryption-in-azure-security-center"></a>套用 Azure 資訊安全中心中的磁碟加密

Azure 資訊安全中心會建議您套用磁碟加密，如果您有 Windows 或 Linux VM 不使用 Azure 磁碟加密加密的磁碟。 磁碟加密可讓您加密 Windows 和 Linux IaaS VM 磁碟。  在您 VM OS 和資料區建議加密。


磁碟加密運用業界標準[BitLocker](https://technet.microsoft.com/library/cc732774.aspx)功能的 Windows 和[DM 窖](https://en.wikipedia.org/wiki/Dm-crypt)功能的 Linux 提供 OS 和資料來協助保護及保護您的資料並符合您組織的安全性和法規遵循承諾加密。 [Azure 鍵保存庫](https://azure.microsoft.com/documentation/services/key-vault/)可協助您控制與管理磁碟加密金鑰和金鑰保存庫訂閱，同時確保 VM 磁碟中的所有資料已都加密，在您[Azure 儲存體](https://azure.microsoft.com/documentation/services/storage/)中的其餘部分中的機密資料整合磁碟加密。

> [AZURE.NOTE] 下列 Windows 伺服器作業系統-Windows Server 2008 R2、 Windows Server 2012 及 Windows Server 2012 R2 支援 azure 磁碟加密。 磁碟加密支援下列 Linux 伺服器作業系統-Ubuntu、 CentOS、 SUSE 和 SUSE Linux Enterprise Server (SLES)。

## <a name="implement-the-recommendation"></a>實作建議

1. 在 [**建議**刀中，選取 [**套用磁碟加密**]。
2. 在**套用磁碟加密**刀中，您會看到的 Vm 磁碟加密建議的清單。
3. 依照指示將加密套用至這些 Vm。

![][1]

若要加密 Azure 虛擬機器中的資訊安全中心識別為需要加密，我們建議下列步驟︰

- 安裝和設定 PowerShell 的 Azure。 這會讓您執行的 PowerShell 命令，才能設定為加密 Azure 虛擬機器必要的先決條件。
- 取得，並執行 Azure 磁碟加密的先決條件 Azure PowerShell 指令碼。
- 加密虛擬機器。

[加密 Azure 虛擬機器](security-center-disk-encryption.md)會引導您執行這些步驟。  本主題假設您使用的 Windows 10 用戶端電腦從您將會設定磁碟加密。

有許多種方法可以用於設定必要條件及設定加密的 Azure 虛擬機器。 如果您已在 PowerShell 的 Azure 或 Azure CLI 完善部分，您可能會想要使用替代方案。 若要深入瞭解這些其他方法，請參閱[Azure 磁碟加密](../security/azure-security-disk-encryption.md)。



## <a name="see-also"></a>另請參閱

這份文件會示範如何實作資訊安全中心建議 「 套用磁碟加密]。 若要進一步瞭解磁碟加密，請參閱下列各項︰

- [加密和 Azure 金鑰保存庫使用金鑰管理](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/)（視訊 36 min 39 秒），瞭解如何使用 IaaS Vm 和 Azure 金鑰保存庫的磁碟加密管理協助保護及保護您的資料。
- [加密 Azure 虛擬機器](security-center-disk-encryption.md)（文件），瞭解如何加密 Azure 虛擬機器。
- [Azure 磁碟加密](../security/azure-security-disk-encryption.md)（文件），瞭解如何啟用 for Windows 和 Linux Vm 磁碟加密。

若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定安全性原則。
- [安全性狀況監控 Azure 資訊安全中心 」](security-center-monitoring.md) ，瞭解如何監視 Azure 資源的狀況。
- [管理及回應在 Azure 資訊安全中心的安全性提醒](security-center-managing-and-responding-alerts.md)--瞭解如何管理和回應的安全性警告。
- [在 Azure 資訊安全中心管理安全性的建議](security-center-recommendations.md)，了解建議如何協助您保護 Azure 資源。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找常見問題集使用服務。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-尋找部落格文章瞭解 Azure 安全性和法規遵循。



<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
