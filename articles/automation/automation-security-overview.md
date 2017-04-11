<properties
   pageTitle="Azure 自動化安全性 |Microsoft Azure"
   description="本文提供 Azure 自動化自動化帳戶自動化的安全性和可用的不同的驗證方法的概觀。"
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="自動化安全性]，[安全的自動化" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/29/2016"
   ms.author="magoedte" />

# <a name="azure-automation-security"></a>Azure 自動化的安全性
Azure 自動化可讓您的自動化工作針對資源 Azure 於內部部署，並使用其他雲端提供者例如 Amazon Web 服務 (AWS)。  為了讓 runbook 執行其所需的動作，它必須有權限安全地存取訂閱中所需的最低權限的 [資源。  
本文將討論各種不同的驗證案例支援 Azure 自動化，並會告訴您如何開始根據環境或管理所需的環境。  

## <a name="automation-account-overview"></a>自動化帳戶概觀
當您第一次啟動 Azure 自動化時，您必須建立至少有一個自動化帳戶。 自動化帳戶可讓您找出您的自動化資源 runbooks、 資產 （設定） 從其他自動化帳戶所包含的資源。 您可以使用自動化帳戶至分成個別的邏輯環境中的資源。 例如，您可能會使用一個帳戶開發、 另一個用於生產，以及另一個用於您的內部部署環境。  Azure 自動化帳戶是您的 Microsoft 帳戶或帳戶所建立 Azure 訂閱中的不同。

針對每個自動化帳戶的自動化資源與單一 Azure 區域，相關聯，但自動化帳戶管理任何區域中的資源。 在不同區域中建立自動化帳戶的主要原因就是您是否需要資料和資源隔離特定區域的原則。

>[AZURE.NOTE]自動化帳戶，並包含的資源 Azure 入口網站中建立，無法存取 Azure 傳統入口網站中。 如果您想要管理這些帳戶或使用 Windows PowerShell 的資源，您必須使用 Azure 資源管理員模組。

使用 Azure Active Directory 組織的身分識別認證型驗證的 Azure 必須驗證所有您對使用中 Azure 自動化 Azure 資源管理員和 Azure cmdlet 的資源執行的工作。  憑證驗證原始的驗證方法，使用 Azure 服務管理模式時，但很複雜，安裝程式。  Azure 驗證與 Azure AD 使用者已引入上的一步] 在 2014 年不只簡化設定驗證帳戶，但也支援的功能與單一使用者帳戶的與傳統的資源和 Azure 資源管理員合作，非互動方式進行 Azure 的程序。   

目前當您建立新的自動化帳戶 Azure 入口網站中時，它會自動建立︰

-  執行以建立新的服務主要 Azure Active Directory 憑證，並將指派參與者的角色型存取控制 (RBAC)，將會用來管理資源管理員資源使用 runbooks 帳戶。
-  傳統執行為帳戶上傳管理憑證，會用來管理 Azure 服務管理或使用 runbooks 傳統的資源。  

使用與來授與 Azure AD 使用者帳戶並執行為帳戶]，允許的動作並驗證該服務的主要 Azure 資源管理員角色型存取控制。  請閱讀[Azure 自動化文件中的角色型存取控制](../automation/automation-role-based-access-control.md)的詳細資訊，協助開發管理自動化權限模型。  

在混合式 Runbook 工作者在您的資料中心或針對運算中 AWS 服務上執行的 Runbooks 無法使用相同的方法，通常用來 runbooks 驗證 Azure 資源。  這是因為這些資源執行的 Azure 以外，因此，需要驗證的資源，他們會在本機存取自動化中定義自己安全性認證。  

## <a name="authentication-methods"></a>驗證方法

下表摘要列出每個環境 Azure 自動化與本文說明如何設定驗證您 runbooks 支援不同的驗證方法。

方法  |  環境  | 文件
----------|----------|----------
Azure AD 使用者帳戶 | Azure 資源管理員和 Azure 服務管理 | [Azure AD 使用者帳戶驗證方法 Runbooks](../automation/automation-sec-configure-aduser-account.md)
為帳戶 azure 執行 | Azure 資源管理員 | [Azure 執行為帳戶驗證方法 Runbooks](../automation/automation-sec-configure-azure-runas-account.md)
為帳戶 azure 傳統執行 | Azure 服務管理 | [Azure 執行為帳戶驗證方法 Runbooks](../automation/automation-sec-configure-azure-runas-account.md)
Windows 驗證 | 內部部署資料中心 | [驗證混合式 Runbook 工作者 Runbooks](../automation/automation-hybrid-runbook-worker.md)
AWS 認證 | Amazon Web 服務 | [驗證 Runbooks 與 Amazon Web 服務 (AWS)](../automation/automation-sec-configure-aws-account.md)



