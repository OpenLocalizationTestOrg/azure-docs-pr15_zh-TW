<properties
    pageTitle="Azure Active Directory 混合式身分識別設計考量-決定多重因素驗證需求"
    description="使用條件存取控制項，Azure Active Directory 檢查您選擇 [驗證使用者時，才能存取應用程式的特定條件。 一旦符合這些條件，使用者就是驗證，允許存取應用程式。"
    documentationCenter=""
    services="active-directory"
    authors="femila"
    manager="billmath"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>決定您混合式部署的身分識別解決方案的多重因素驗證需求

全球行動性，與使用者存取資料和應用程式在雲端，然後從任何裝置的保護這項資訊皆已成為最重要。  每天有相關的安全性漏洞新標題。  雖然並不保證針對這類遭侵害等，多重因素驗證提供額外的安全性，以協助防範這些遭侵害等層級。
首先評估多重因素驗證的組織需求。 就組織想要什麼安全。  評估很重要定義設定，並啟用多因素驗證的組織使用者的技術需求。

>[AZURE.NOTE]
如果您不熟悉 MFA 和功能，強烈建議您先閱讀，請參閱[什麼是 Azure 多重因素驗證？](../multi-factor-authentication/multi-factor-authentication.md)在於繼續閱讀本節。

請確認回答下列動作︰

- 您的公司試著保護 Microsoft 應用程式？ 
- 這些應用程式發佈的方式？
- 您的公司是否提供遠端存取]，讓員工存取內部部署的應用程式？

如果是的何種遠端存取？您也需要評估都位於存取這些應用程式的使用者。 評估是另一個重要的步驟，以定義適當的多重因素驗證策略。 請確認回答下列問題︰

- 在使用者將會位於？
- 他們可以位於任何位置嗎？
- 您的公司是否想建立限制根據使用者的位置？

只要您瞭解這些需求，請務必也評估多重因素驗證的使用者的需求。 評估是很重要，因為它會定義推出多重因素驗證的需求。 請確認回答下列問題︰

- 熟悉使用者多重因素驗證嗎？
- 某些使用必須提供額外的驗證嗎？  
 - 如果是的任何時間] 時即將從外部網路或存取特定應用程式]，或其他條件下嗎？
- 使用者需要訓練如何設定和實作多重因素驗證嗎？
- 什麼是與貴公司想要啟用多因素驗證的使用者主要的狀況？

上一個問題後，您可以瞭解是否已執行多因素驗證內部部署。 評估很重要定義設定，並啟用多因素驗證的組織使用者的技術需求。 請確認回答下列問題︰

- 您的公司是否需要保護 MFA 有權限的帳戶？
- 您的公司是否需要啟用 MFA 法規遵循的原因，特定應用程式？
- 您的公司是否需要這些應用程式或只有系統管理員的所有符合資格的使用者啟用 MFA？
- 您需要有 MFA 永遠啟用 」 或 「 只當使用者的登入公司網路以外？


## <a name="next-steps"></a>後續步驟
[定義混合式身分識別採用策略](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)


## <a name="see-also"></a>另請參閱
[設計考量概觀](active-directory-hybrid-identity-design-considerations-overview.md)
