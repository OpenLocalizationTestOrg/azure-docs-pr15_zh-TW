<properties
   pageTitle="Azure AD B2B 共同作業預覽︰ 運作方式 |Microsoft Azure"
   description="說明如何 Azure Active Directory B2B 共同作業啟用選擇性地存取您公司的應用程式的商業夥伴支援您跨公司的關聯"
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

# <a name="azure-ad-b2b-collaboration-preview-how-it-works"></a>Azure AD B2B 共同作業預覽︰ 運作方式
Azure AD B2B 共同作業為基礎的邀請，並兌換模型。 您提供您想要使用，以及您想要使用的應用程式的合作對象的電子郵件地址。 Azure AD 傳送給他們的電子郵件邀請] 連結。 合作夥伴使用者遵循連結，並會提示您登入使用其 Azure AD 帳戶或註冊為新的 Azure AD 帳戶。

1. 您的系統管理員邀請合作夥伴使用者上傳使用 Azure 入口網站的[結構化的.csv 檔案](active-directory-b2b-references-csv-file-format.md)。
2. 入口網站傳送邀請給這些合作夥伴使用者的電子郵件。
3. 合作夥伴使用者按一下電子郵件中的連結，並使用 （如果已在 Azure AD） 其公司認證，登入，或以 Azure AD B2B 共同作業使用者身分登入提示。
4. 合作夥伴使用者重新導向至應用程式，受邀，他們現在有權存取的位置。

## <a name="directory-operations"></a>目錄作業
合作夥伴使用者存在於 Azure AD 為外部使用者。 這表示您的系統管理員可以佈建授權、 指派給群組的成員資格，並進一步授與存取透過 Azure 入口網站，或您的公司中的使用者就像是使用 PowerShell 的 Azure 公司應用程式。

付費 Azure AD 時訂閱 （Basic 或進階版），則不需要使用 Azure AD B2B，是否已付費的租用戶 Azure AD 訂閱 （Basic 或進階） 會收到下列的其他優點︰

 - 管理員可以將群組指派給應用程式，提供更容易管理受邀的使用者存取權。
 - 管理租用戶商標用來建立的邀請電子郵件與贖回體驗，提供更多內容受邀合作夥伴使用者。

## <a name="related-articles"></a>相關的文章
 瀏覽其他文章 Azure AD B2B 共同作業

 - [什麼是 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
 - [詳細逐步解說](active-directory-b2b-detailed-walkthrough.md)
 - [CSV 檔案格式的參照](active-directory-b2b-references-csv-file-format.md)
 - [外部使用者 token 格式](active-directory-b2b-references-external-user-token-format.md)
 - [變更外部使用者物件屬性](active-directory-b2b-references-external-user-object-attribute-changes.md)
 - [目前的預覽限制](active-directory-b2b-current-preview-limitations.md)
 - [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
