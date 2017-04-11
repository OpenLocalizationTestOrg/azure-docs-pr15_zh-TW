<properties
   pageTitle="Azure Active Directory B2B 共同作業的目前預覽限制 |Microsoft Azure"
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
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-current-preview-limitations"></a>Azure AD B2B 共同作業預覽︰ 目前預覽限制

- 多重因素驗證 (MFA) 不支援的外部使用者。 例如，如果 Contoso 有 MFA，但不會合作夥伴組織，然後合作夥伴組織無法授與使用者 MFA 透過 B2B 共同作業。
- 僅透過 CSV; 可能會出現邀請不支援個別的邀請與 API 的存取權。
- 僅限 Azure AD 全域管理員可以上傳.csv 檔案。
- 目前不支援消費者電子郵件地址 （例如 hotmail.com、 Gmail.com 或 comcast.net） 的邀請。
- 外部使用者存取內部部署的應用程式未測試。
- 外部使用者不會自動時會清除實際的使用者會從他們的目錄中刪除。
- 不支援的通訊群組清單的邀請。
- 經由 CSV 可以上傳的 2000 記錄的最大值。

## <a name="related-articles"></a>相關的文章
瀏覽其他文章 Azure AD B2B 共同作業︰

- [什麼是 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
- [運作方式](active-directory-b2b-how-it-works.md)
- [詳細逐步解說](active-directory-b2b-detailed-walkthrough.md)
- [CSV 檔案格式的參照](active-directory-b2b-references-csv-file-format.md)
- [外部使用者 token 格式](active-directory-b2b-references-external-user-token-format.md)
- [變更外部使用者物件屬性](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
