<properties
   pageTitle="外部使用者的 Azure Active Directory B2B 共同作業預覽的權杖格式 |Microsoft Azure"
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

# <a name="azure-ad-b2b-collaboration-preview-external-user-token-format"></a>Azure AD B2B 共同作業預覽︰ 外部使用者 token 格式

宣告的標準 Azure AD 權杖所述的[權杖支援和宣告類型](active-directory-token-and-claims.md)的文件上 azure.microsoft.com。

不同的驗證 B2B 共同作業外部使用者的宣告如下所示︰<br/>
- **OID:**的資源租用戶的物件識別碼<br/>
- **TID**︰ 租用戶的資源租用戶的識別碼<br/>
- **發行者**︰ 這是資源租用戶<br/>
- **IDP**︰ 這是家用版的租用戶的使用者<br/>
- **AltSecId**︰ 這是替代安全性識別碼，這是您不透明<br/>

## <a name="related-articles"></a>相關的文章
瀏覽其他文章 Azure AD B2B 共同作業︰

- [什麼是 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
- [運作方式](active-directory-b2b-how-it-works.md)
- [詳細逐步解說](active-directory-b2b-detailed-walkthrough.md)
- [CSV 檔案格式的參照](active-directory-b2b-references-csv-file-format.md)
- [變更外部使用者物件屬性](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [目前的預覽限制](active-directory-b2b-current-preview-limitations.md)
- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
