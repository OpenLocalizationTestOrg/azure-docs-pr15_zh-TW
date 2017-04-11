<properties
    pageTitle="Azure Active Directory 網域服務︰ 受管理的網域中同步處理 |Microsoft Azure"
    description="瞭解的 Azure Active Directory 網域服務受管理的網域中的同步處理"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Azure AD 網域服務受管理的網域中的同步處理
下圖顯示同步處理如何運作 Azure AD 網域服務中受管理的網域。

![Azure AD 網域服務中的同步處理拓撲](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>從您的內部部署目錄同步處理至您的 Azure AD 租用戶
Azure AD Connect 同步處理用來同步處理使用者帳戶，群組的成員，然後認證雜湊您 Azure AD 租用戶。 屬性的使用者帳戶，例如 UPN 和內部部署 SID （安全性識別碼） 會同步處理。 如果您使用 Azure AD 網域服務，NTLM 和 Kerberos 驗證所需的舊版認證雜湊也會跟著同步處理至您的 Azure AD 租用戶。

如果您設定回寫功能時，發生 Azure AD 目錄中的變更會同步處理至您的內部部署 Active Directory。 比方說，如果您變更您使用 Azure AD 自助密碼變更功能的密碼，變更的密碼會更新您的內部部署中 AD 網域。

> [AZURE.NOTE] 使用最新版 Azure AD Connect 來確認您擁有的所有錯誤修正。


## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>從 Azure AD 租用戶同步處理至您受管理的網域
使用者帳戶與群組成員資格，認證雜湊從同步處理您的 Azure AD 租用戶您 Azure AD 網域服務受管理的網域。 此同步處理程序會自動執行。 您不需要設定、 監控或管理此同步處理程序。 同步處理程序也是其中之一-雙向/單向的。 大部分唯讀除了您建立的任何自訂 Ou 您受管理的網域。 因此，您無法變更使用者屬性、 使用者密碼或受管理的網域中的群組成員資格。 如此一來，是來自您受管理的網域回到您的 Azure AD 租用戶的變更沒有反向同步處理。


## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>從多樹系內部部署環境的同步處理
許多組織有很複雜的內部部署的身分識別的基礎結構，包含多個帳戶樹系。 Azure AD Connect 支援同步處理的使用者、 群組和認證 Azure AD 租用戶來自多樹系環境雜湊。

相反地，您 Azure AD 租用戶是更簡單、 一般命名空間。 若要啟用使用者確實存取 Azure AD 受保護的應用程式，解決 UPN 衝突跨不同樹系中的使用者帳戶。 您 Azure AD 網域服務受管理的網域熊關閉跟您 Azure AD 租用戶。 因此，您會看到一般 OU 結構中的受管理的網域。 所有使用者和群組會都儲存在 「 AADDC 使用者 ' 容器中，無論從這些已同步處理的內部部署網域或樹系。 您設定的階層式 OU 結構內部部署。 不過，您受管理的網域仍會有一個簡單的二維 OU 結構。


## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>排除項目-什麼未同步處理至您受管理的網域
下列的物件或屬性未同步處理至您的 Azure AD 租用戶或您受管理的網域︰

- **排除屬性︰**您可以選擇從內部部署網域使用 Azure AD Connect 同步處理至您的 Azure AD 租用戶時，排除特定屬性。 您受管理的網域無法使用這些排除的屬性。

- **群組原則︰**設定您的內部部署網域中的群組原則未同步處理至您受管理的網域。

- **SYSVOL 共用︰**同樣的內部部署網域 SYSVOL 共用的內容會不同步處理至您受管理的網域。

- **電腦物件︰**電腦物件的電腦加入您的內部部署網域未同步處理至您受管理的網域。 這些電腦沒有使用受管理的網域信任關係，屬於您的內部部署網域只。 在受管理的網域，您可以找到電腦物件僅適用於您有明確網域的受管理的網域的電腦。

- **使用者和群組的 SidHistory 屬性︰**主要的使用者和主要群組 Sid 從您內部部署的網域會同步處理至您受管理的網域。 不過，使用者和群組的現有 SidHistory 屬性未同步處理從您的內部部署網域您受管理的網域。

- **組織單位 (OU) 結構︰**在您的內部部署網域中定義的組織單位不同步處理至您受管理的網域。 您受管理的網域中有兩個內建 Ou。 根據預設，您受管理的網域會有一般 OU 結構。 不過，您可以選擇[建立自訂的 OU 中的受管理的網域](./active-directory-ds-admin-guide-create-ou.md)。


## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>如何特定的屬性有同步處理至您受管理的網域
下表列出一些常見的屬性，並說明如何進行同步處理至您受管理的網域。

| 您受管理的網域中的屬性 | 來源 | 備忘稿 |
|:---|:---|:---|
|UPN|Azure AD 租用戶的使用者的 UPN 屬性|從 Azure AD 租用戶的 UPN 屬性會同步處理到您受管理的網域。 因此，登入您受管理的網域最可靠的方法使用您的 UPN。|
|SAMAccountName|使用者的 mailNickname Azure AD 租用戶屬性或自動產生|Azure AD 租用戶 mailNickname 屬性源自的 SAMAccountName 屬性。 多個使用者帳戶有相同的 mailNickname 屬性，如果 SAMAccountName 會自動產生。 如果使用者的郵件別名或 UPN 前置詞是超過 20 個字元，SAMAccountName 會自動產生滿足 SAMAccountName 屬性 20 字元的限制。|
|密碼|從 Azure AD 租用戶的使用者的密碼|從 Azure AD 租用戶同步處理 NTLM 或 Kerberos 驗證 （也稱為補充認證） 所需的認證雜湊。 如果您的 Azure AD 租用戶已同步處理的租用戶，這些認證來自您內部部署的網域。|
|主要使用者/群組 SID|自動產生|使用者/群組帳戶的主要 SID 位於自動產生的受管理的網域。 這個屬性不符合主要使用者/群組您的內部部署中的物件 SID AD 網域。 這種不符合是因為的受管理的網域是不同的 SID 命名空間比您內部部署的網域。|
|使用者和群組的 SID 歷程記錄|內部部署的主要使用者和群組 SID|使用者和群組您受管理的網域中的 SidHistory 屬性設定為符合對應的主要使用者或群組中您的內部部署網域 SID。 此功能可協助您更輕鬆地增益 shift 內部部署的應用程式的受管理的網域，因為您不需要重新 ACL 資源。|

> [AZURE.NOTE] **登入受管理的網域使用 UPN 格式︰**SAMAccountName 屬性可能會自動產生的某些您受管理的網域中的使用者帳戶。 如果有多名使用者的相同的 mailNickname 屬性，或是使用者過長 UPN 前置詞，這些使用者的 SAMAccountName 可能會自動產生。 因此，SAMAccountName 格式 (例如，「 CONTOSO100\joeuser 」) 不是登入網域可靠的方式。 使用者的自動產生的 SAMAccountName 可能不同於其 UPN 前置字元。 使用 UPN 格式 (例如，'joeuser@contoso100.com')可靠登入的受管理的網域。


## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>從您受管理的網域不同步至您的 Azure AD 租用戶的物件
本文的前一節所述，有從您受管理的網域不同步處理，回到您的 Azure AD 租用戶。 在您受管理的網域，您可以選擇建立[自訂組織單位 (OU)](./active-directory-ds-admin-guide-create-ou.md) 。 此外，您可以建立其他 Ou、 使用者、 群組或這些自訂 Ou 中的服務帳戶。 建立自訂 Ou 中的物件都不會同步處理至您的 Azure AD 租用戶。 這些物件可供使用，只在您受管理的網域。 因此，這些物件不會顯示使用 Azure AD PowerShell cmdlet，Azure AD 圖形 API，或使用 Azure AD 管理使用者介面。


## <a name="related-content"></a>相關的內容
- [功能-Azure AD 網域服務](active-directory-ds-features.md)

- [部署案例-Azure AD 網域服務](active-directory-ds-scenarios.md)

- [Azure AD 網域服務的網路考量](active-directory-ds-networking.md)

- [開始使用 Azure AD 網域服務](active-directory-ds-getting-started.md)
