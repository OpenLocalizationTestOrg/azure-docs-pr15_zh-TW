
<properties
    pageTitle="Azure Active Directory 混合式身分識別設計考量-決定存取控制需求 |Microsoft Azure"
    description="說明為核心身分識別，並且識別混合環境中的使用者適用資源的存取要求。"
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>決定存取控制需求混合式身分識別解決方案
組織設計其混合式身分識別解決方案時，也可以使用這個機會 access 的資源，其計劃以供使用者的需求。 資料存取跨所有的四大身分識別，也就是︰

- 管理
- 驗證
- 授權
- 稽核

驗證與授權中更多詳細資料，將涵蓋的分節、 管理及稽核是混合式身分識別生命週期的一部分。 如需這些功能的相關資訊，請閱讀[判斷混合式身分識別管理工作](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)。

>[AZURE.NOTE]
如需有關這些核心的每一個閱讀[四個核心的身分識別-身分識別管理中的混合式 IT](http://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) 。

## <a name="authentication-and-authorization"></a>驗證與授權
有不同的驗證及授權的案例，這些案例會有特定的需求，必須完成公司前往採用混合式身分識別解決方案。 案例企業 (B2B) 通訊可以 IT 管理員新增額外的挑戰，因為他們必須以確保組織所使用的驗證及授權方法可以進行通訊與其商務合作夥伴。 在設計過程中的驗證及授權需求，請確定會回答下列問題︰

- 將您的組織驗證和授權位於其身分識別管理系統的使用者嗎？
 - 有任何 B2B 案例的方案嗎？
 - 如果是的您已經知道哪些通訊協定 （SAML、 OAuth、 Kerberos、 權杖或憑證） 用於連接兩個企業嗎？
- 是否要採用支援混合式部署識別方案這些通訊協定？

若要考慮的另一項重點是都位於使用使用者與合作夥伴驗證存放庫與要使用的系統管理模型。 請考慮下列兩個核心選項︰
- 集中式︰ 此模型中的使用者認證、 原則和管理可集中內部部署或雲端中。
- 混合式︰ 此模型中的使用者認證、 原則管理都可集中內部部署和複製雲端中。

您想要回答下列問題，以找出所在的身分識別管理系統 」 和 「 系統管理模式]，使用您的組織會採用的模型會根據其商務需求而有所不同:

- 您的組織目前沒有身分識別管理內部部署？
 - 如果是的是否他們計劃，讓它？
 - 有任何法規或相容性需求，您的組織必須追蹤該即表示身分識別管理系統的所在位置嗎？
- 沒有您的組織使用單一登入應用程式位於內部部署或雲端中？
 - 如果是的採用混合式身分識別模型的影響此程序？

## <a name="access-control"></a>存取控制
雖然驗證與授權核心元素，以啟用透過使用者驗證的公司資料的存取權，也很重要來控制存取這些使用者，將 access 系統管理員的層級會有在他們所管理的資源層級。 混合式身分識別方案必須提供的資源，委派角色基底存取控制精緻的存取。 請確定下列問題的回答有關存取控制︰

- 您的公司是否有多個使用者提高權限與管理您的身分識別系統？
 - 如果是的每位使用者是否需要相同的存取層級？
- 您公司所需委派管理的特定資源的使用者存取權嗎？
 - 如果是的頻率發生這種情況？
- 您公司所需整合內部部署與雲端之間的存取控制功能資源？
- 您公司所需限制存取根據某些條件的資源？
- 貴公司有需要自訂 control 一些資源存取的任何應用程式？
 - 如果是的這些應用程式的位置 (內部部署或雲端)？
 - 如果是的其中會那些目標資源位於 (內部部署或雲端)？

>[AZURE.NOTE]
請務必記錄的每個答案的筆記，並瞭解答案原理。 在可用的選項並優點/缺點每個選項，就會移[定義資料保護策略](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)。  您將回答的問題，以選取最佳的選項適合您的業務需求。

## <a name="next-steps"></a>後續步驟

[決定應計需求](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>另請參閱
[設計考量概觀](active-directory-hybrid-identity-design-considerations-overview.md)
