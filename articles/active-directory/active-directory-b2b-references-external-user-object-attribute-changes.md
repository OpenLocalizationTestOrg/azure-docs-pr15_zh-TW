<properties
   pageTitle="Azure Active Directory B2B 共同作業預覽的外部使用者物件屬性變更 |Microsoft Azure"
   description="Azure Active Directory B2B 啟用選擇性地存取您公司的應用程式的商業夥伴支援您跨公司的關聯"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-external-user-object-attribute-changes"></a>Azure AD B2B 共同作業預覽︰ 變更外部使用者物件屬性

Azure AD 目錄中的每個使用者會以使用者物件。 使用者物件中 Azure AD 期經歷的各種屬性變更 B2B 共同作業的階段邀請兌換流程。 使用者物件，代表合作夥伴使用者在目錄中的屬性變更，兌換的時間，當合作夥伴使用者按一下邀請電子郵件中的連結。 特別是︰

- 已填妥**SignInName**和**AltSecId**屬性
- 從使用者主體名稱的**DisplayName**屬性變更(user_fabrikam.com#EXT#@contoso.com)登入名稱(user@fabrikam.com)

Azure AD 中追蹤這些屬性可協助您排解合作夥伴使用者是具有兌換其 B2B 共同作業的邀請。

## <a name="related-articles"></a>相關的文章
瀏覽其他文章 Azure AD B2B 共同作業︰

- [什麼是 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
- [運作方式](active-directory-b2b-how-it-works.md)
- [詳細逐步解說](active-directory-b2b-detailed-walkthrough.md)
- [CSV 檔案格式的參照](active-directory-b2b-references-csv-file-format.md)
- [外部使用者 token 格式](active-directory-b2b-references-external-user-token-format.md)
- [目前的預覽限制](active-directory-b2b-current-preview-limitations.md)
- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
