<properties
    pageTitle="Azure Active Directory 條件存取常見問題集 |Microsoft Azure"
    description="常見問題條件的存取權 "
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-conditional-access-faq"></a>Azure Active Directory 條件存取常見問題集

## <a name="which-applications-work-with-conditional-access-policies"></a>與條件存取原則搭配使用的應用程式？

**A:**請參閱[條件存取苦頭應用程式支援](active-directory-conditional-access-supported-apps.md)主題。

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>條件存取原則強制執行 B2B 共同作業和來賓使用者？

**A:**B2B 共同作業的使用者，會強制執行原則。 不過，在某些情況下，使用者可能無法滿足原則的需求，如果，例如組織不支援多重因素驗證。 

SharePoint 來賓使用者的目前不強制原則。 在 SharePoint 維護來賓關聯。 來賓使用者帳戶沒有存取原則，驗證伺服器。 來賓存取可以在 SharePoint 中加以管理。

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>會在 SharePoint Online 原則也適用於商務用 OneDrive？

**A:**[是]。
 
## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>為何不能將原則在用戶端應用程式，例如 Word 或 Outlook？

**A:**條件存取原則設定存取服務的需求，且執行時，該服務將會驗證。 直接在用戶端應用程式中; 沒有設定原則相反地，它會套用到服務呼叫時。 例如 [用戶端呼叫 SharePoint 適用於 SharePoint 上設定的原則和原則設定 Exchange 適用於 Outlook。


## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>要將條件存取原則套用至服務帳戶？

**A:**條件存取原則套用到所有的使用者帳戶。 這包含用來做為服務帳戶的使用者帳戶。 在許多情況下，自動執行的服務帳戶不是無法滿足原則。 這是，例如大小寫，需要 MFA 時。 在下列情況下，可以從原則，使用條件存取原則管理設定排除服務帳戶。 進一步瞭解以下使用者套用原則。
