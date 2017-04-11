<properties
   pageTitle="Microsoft Azure IaaS 上的軟體更新的最佳作法 |Microsoft Azure"
   description="本文提供 Microsoft Azure IaaS 環境中的軟體更新的最佳作法的集合。  適用於 IT 專業人員和處理安全性分析師變更控制項，每日，包括負責組織的安全性和法規遵循努力的軟體更新與資產管理。"
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="best-practices-for-software-updates-on-microsoft-azure-iaas"></a>Microsoft Azure IaaS 上的軟體更新的最佳作法

在探究之前討論的任何類型的 Azure [IaaS](https://azure.microsoft.com/overview/what-is-iaas/)環境的最佳作法，請務必瞭解案例的是不知道您管理軟體更新和責任。 下圖應能助您瞭解這些界限︰

![雲端模型和 responsabilities](./media/azure-security-best-practices-software-updates-iaas/sec-cloudstack-new.png)

最左邊的欄顯示七 （定義在以下區段） 的職責必須考量組織，這些都參與安全性與隱私權運算的環境。
 
資料分類與責任和用戶端與端點保護只在網域的客戶的責任及職責的實體、 主機和網路數 PaaS 和 SaaS 模型中的雲端服務提供者的網域。 

剩餘的職責的客戶共用和雲端服務提供者。 某些職責需要 CSP 和客戶管理責任放在一起，其中包括稽核的網域。 例如，請考慮身分識別與使用 Azure Active Directory 服務; 時，存取 [管理設定多重因素驗證等服務的客戶，最但確保有效的功能是由 Microsoft Azure。

> [AZURE.NOTE] 更多雲端中的共用職責的詳細資訊，請參閱[共用雲端運算的職責](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf) 

這些相同的原則套用在您的公司位置使用 Azure IaaS Vm 的通訊和內部部署資源，如下圖所示的混合式案例。

![Microsoft Azure 一般的混合式案例](./media/azure-security-best-practices-software-updates-iaas/sec-azconnectonpre.png)

## <a name="initial-assessment"></a>初始評估

即使您的公司已經更新管理系統而位置中已經有軟體更新原則，請務必經常回到先前的原則評估並根據您目前的需求進行更新。 這表示必須熟悉您公司內的資源的目前狀態。 若要達到此狀態，您必須知道︰

-   在 [企業中的實體和虛擬電腦。

-   作業系統，在每個這些實體和虛擬電腦上執行的版本。

-   每個 （service pack 版本、 軟體更新和其他修改） 的電腦上目前安裝的軟體更新。

-   在您的企業版中的每一部電腦執行函數。

-   應用程式，並在每一部電腦上執行的程式。

-   每一部電腦的擁有權 」 與 「 連絡人資訊。

-   資產呈現您的環境和其相對的值，以判斷哪些區域需要最注意並保護。

-   已知的安全性問題與處理程序企業有在識別新的安全性問題或變更安全性層級的位置。

-   已部署來保護您的環境的措施。

您應該定期，更新這項資訊，可以使用這些程序中您的軟體更新管理很。

## <a name="establish-a-baseline"></a>建立 [比較基準

軟體更新管理程序很重要的一部分建立作業系統版本、 應用程式] 及硬體的電腦上的初始標準安裝在您的企業版;這些稱為比較基準。 比較基準是產品或特定點建立時間系統的設定。 應用程式或作業系統比較基準，例如，提供重建電腦或服務特定狀態的能力。

比較基準提供的基礎來尋找和修正可能發生的問題，並簡化軟體更新管理程序，以減少軟體更新，您必須在企業中部署及增加監控法規遵循的能力。

執行後的企業初始稽核，您應該使用的從定義生產環境中的 IT 元件操作比較基準稽核取得資訊。 比較基準的數字，可能需要根據的硬體與軟體部署到不同的類型而定。

例如，某些伺服器需要若要防止執行 Windows Server 2012 時，他們會進入關機程序時首行凸軟體更新。 這些伺服器的比較基準應該包含此軟體更新。

在大型組織，通常是幫助分成資產類別中的企業中的電腦，並將每個類別保留在標準的比較基準，使用相同的軟體和軟體更新版本。 然後，您可以使用這些資產中的類別排列優先順序軟體更新通訊群組。

## <a name="subscribe-to-the-appropriate-software-update-notification-services"></a>訂閱適當的軟體更新通知服務

您在企業中執行使用初始稽核的軟體之後，您應該判斷收到通知，新的軟體更新的每個軟體產品及版本的最佳方法。 根據 「 軟體產品的最佳的通知方法可能電子郵件通知、 網站或電腦的出版物。

例如，Microsoft 安全性回應中心 (MSRC) 回應 Microsoft 產品的相關的所有安全性相關問題，並提供 Microsoft 安全性公告服務，免費的電子郵件通知的新發現的弱點和地址這些弱點所發行軟體更新。 您可以在 http://www.microsoft.com/technet/security/bulletin/notify.mspx 這項服務訂閱。

## <a name="software-update-considerations"></a>軟體更新考量

您在企業中執行使用初始稽核的軟體之後，您應該判斷設定您的軟體更新管理系統 」，這取決於您使用的軟體更新管理系統需求。 閱讀[與 Windows Server 更新服務的最佳作法](https://technet.microsoft.com/library/Cc708536)wsus，系統管理中心閱讀[軟體更新組態管理員中的規劃](https://technet.microsoft.com/library/gg712696)。

然而，有一些您可以套用無論追蹤您使用的各節中所示的解決方案的最佳作法和一般的考量事項。

### <a name="setting-up-the-environment"></a>設定環境

請考慮下列作法時計劃要安裝軟體更新管理環境︰

-   **建立生產軟體更新集合根據穩定的準則**︰ 一般而言，使用穩定準則來建立您的軟體更新庫存和通訊群組的集合，有助於簡化所有階段的軟體更新管理程序。 已安裝的用戶端作業系統版本和 service pack 層級、 系統角色或目標組織，可以包含穩定的準則。

-   **建立生產前集合，包括參考電腦**︰ 生產前集合應該包含代表作業系統版本、 一行商業軟體，以及企業中執行的其他軟體的設定。

您也必須考量位置軟體更新伺服器都位於，如果它就會在雲端中的 Azure IaaS 基礎結構，或會內部部署。 這是流量的很重要的決策，因為您需要進行評估的內部部署資源和 Azure 基礎結構之間。 如需有關如何將您的內部部署基礎結構連線到 Azure 閱讀[連線到 Microsoft Azure 虛擬網路內部部署網路](https://technet.microsoft.com/library/Dn786406.aspx)。

根據您目前的基礎架構與您目前正在使用的軟體更新系統而異也會決定都位於更新伺服器的設計選項。 Wsus 讀取[您的組織中部署 Windows Server 更新服務](https://technet.microsoft.com/library/hh852340.aspx)，為系統管理中心設定管理員讀取[規劃網站與組態管理員中的階層](https://technet.microsoft.com/library/Gg712681.aspx)。

### <a name="backup"></a>備份

定期備份重視不僅適用於軟體更新管理的平台本身也會更新的伺服器。 需要有，[變更管理程序](https://technet.microsoft.com/library/cc543216.aspx)的組織 IT 左右對齊為什麼需要更新伺服器、 估計停機時間和可能影響的原因。 若要確保您有就地復原設定，以避免更新失敗，請務必定期備份系統。

Azure IaaS 一些備份選項包括︰

-   [Azure IaaS 工作量保護資料保護管理員](https://azure.microsoft.com/blog/2014/09/08/azure-iaas-workload-protection-using-data-protection-manager/)

-   [備份 Azure 虛擬機器](../backup/backup-azure-vms.md)

### <a name="monitoring"></a>監控

您應該執行一般報表監控數遺失或不完整狀態，每個授權的軟體更新安裝的更新或更新。 同樣的報表的未獲授權的軟體更新可以協助您更容易部署決策。

您也必須考量下列工作︰

-   進行您公司內的所有電腦的適用和已安裝安全性更新稽核。

-   授權及部署更新到適當的電腦。

-   追蹤庫存及更新安裝狀態和進度您公司內的所有電腦。

此外到一般解釋本文中，您也必須考量每個產品的最佳的考量練習，例如︰ 如果您有 VM 與 SQL Server Azure 中時，請確定您追蹤該產品的軟體更新建議。

## <a name="next-steps"></a>後續步驟

您可以使用本文所述的指導方針，協助您判斷軟體更新虛擬機器中 Azure IaaS 最佳的選項。 軟體更新的最佳作法 Azure IaaS 與傳統資料中心之間有許多相同之處，因此建議您先評估目前的軟體更新原則，以包含 Azure Vm 與整體的軟體更新程序中包含的相關的最佳作法，從這份文件。
