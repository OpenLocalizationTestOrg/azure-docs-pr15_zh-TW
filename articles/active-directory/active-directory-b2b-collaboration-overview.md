<properties
   pageTitle="Azure Active Directory B2B 共同作業 |Microsoft Azure"
   description="Azure Active Directory B2B 共同作業可讓商業夥伴存取您公司的應用程式，與每個單一 Azure AD 表示使用者帳戶"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B 共同作業

Azure Active Directory (Azure AD) B2B 共同作業，可讓您從協力廠商受管理的身分識別，您公司的應用程式啟用存取。 您可以邀請和授權使用者存取您的資源的合作夥伴，以建立跨公司的關聯。 複雜度會降低，因為每一家公司會一次與 Azure Active Directory 和每個使用者是以單一 Azure AD 帳戶。 如果您的商業夥伴管理自己的帳戶中的 Azure AD 因為合作夥伴使用者終止從他們的企業組織，並將無法透過內部目錄中的成員資格的非預期的 access 已撤銷存取權，就會增加安全性。 沒有 Azure AD 的商業夥伴 B2B 共同作業具有精簡註冊體驗︰ 您企業的合作夥伴，才能提供 Azure AD 帳戶。

-   您的業務合作夥伴使用自己登入認證，如此您從管理外部的合作夥伴的目錄，及移除存取，當使用者離開合作夥伴組織時需要。

-   您可以管理存取至您的應用程式，而您的業務合作夥伴帳戶生命週期。 這表示，例如，您可以不需要求您企業的合作夥伴執行任何動作的 IT 部門撤銷存取權。

## <a name="capabilities"></a>功能

B2B 共同作業可簡化管理，並且改進合作夥伴存取公司的資源，包括 SaaS 應用程式，例如 Office 365、 Salesforce、 Azure 服務與每一個 mobile 雲端和內部部署宣告注意的應用程式的安全性。 企業可以將安全性原則套用至合作夥伴存取並 B2B 共同作業可讓合作夥伴管理自己的帳戶。

即使他們沒有自己的 Azure Active Directory 透過電子郵件驗證程序，簡化註冊協力廠商的各種共同作業輕鬆設定的 azure Active Directory B2B。 您可以輕鬆也若要維持沒有外部目錄或每合作夥伴同盟設定。

## <a name="b2b-collaboration-process"></a>B2B 共同作業的程序

1. Azure AD B2B 共同作業 」 可讓公司系統管理員，以邀請授權的外部使用者的一組 B2B 共同作業入口網站上傳逗點分隔值 (CSV) 檔案的超過 2000年的線條。

  ![CSV 檔案上傳] 對話方塊](./media/active-directory-b2b-collaboration-overview/upload-csv.png)

2. 入口網站會傳送電子郵件邀請這些外部使用者。

3. 邀請的使用者以 Microsoft （Azure AD 中管理），登入現有的公司帳戶或 Azure AD 中取得新的公司帳戶。

4. 登入之後，使用者會被重新導向至已與其共用的應用程式。

目前不支援消費者電子郵件地址 （例如 Gmail 或[*comcast.net*](http://comcast.net/)） 的邀請。

如需 B2B 共同作業的運作方式的詳細資訊，請參閱[這段影片](http://aka.ms/aadshowb2b)。

## <a name="next-steps"></a>後續步驟
瀏覽其他文章 Azure AD B2B 共同作業。

- [什麼是 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
- [運作方式](active-directory-b2b-how-it-works.md)
- [詳細逐步解說](active-directory-b2b-detailed-walkthrough.md)
- [CSV 檔案格式的參照](active-directory-b2b-references-csv-file-format.md)
- [外部使用者 token 格式](active-directory-b2b-references-external-user-token-format.md)
- [變更外部使用者物件屬性](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [目前的預覽限制](active-directory-b2b-current-preview-limitations.md)
- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
