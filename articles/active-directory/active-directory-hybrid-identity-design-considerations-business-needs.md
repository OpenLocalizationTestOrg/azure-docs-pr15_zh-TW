<properties
    pageTitle="Azure Active Directory 混合式身分識別設計考量-決定身分識別需求 |Microsoft Azure"
    description="找出會引導您定義混合式身分識別設計的需求的公司的業務需求。"
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

# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>決定您混合式部署的身分識別解決方案的身分識別需求
設計混合式身分識別解決方案的第一個步驟是決定充分使用此方案的企業組織的需求。  混合式身分識別啟動支援的角色 （支援所有其他雲端解決方案提供驗證），並前往上提供解除鎖定使用者的新工作負載的新及最感興趣的功能。  這些負載或您想要採用使用者的服務會規定混合式身分識別設計的需求。  這些服務和負載需要運用混合式身分識別兩個內部部署和雲端中。  

您需要執行上的 [若要瞭解什麼是商務這些重要層面要求現在和公司方案供日後使用。 如果您沒有安裝的混合式身分識別設計的長期策略可見性，可能會是，您的方案不會是可調整企業需求變大和變更。   T 他的 [圖表] 下方顯示的混合式身分識別架構和使用者會被解除鎖定的工作負載的範例。 這是只可以解除鎖定及實心混合式身分識別策略與傳遞的所有新可能的範例。 
 
混合式身分識別架構的組件的部分元件![](./media/hybrid-id-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>決定業務需求
每個公司會有不同的需求，即使這些公司屬於相同的產業、 實企業需求可能會視情況而定。 您仍然可以利用最佳作法，從產業，但最後會引導您定義混合式身分識別設計的需求的公司的業務需求。 

請確認回答下列問題，以找出您的業務需求︰

- 您的公司想要剪下 IT 操作成本嗎？
- 您的公司想找的安全性雲端資產 （SaaS 應用程式，基礎結構）？
- 您的公司想要現代化您的 IT 嗎？
  - 是您的使用者，其他行動裝置，且需要 IT 建立不同類型的流量存取不同的資源，以便您 DMZ 到的例外狀況？
  - 您的公司是否有需要發佈到這些新使用者，但並不容易改寫的舊版應用程式？
  - 您的公司是否需要完成所有任務，並將其掌控同時中？
- 您的公司想要保護使用者的身分識別及使運用 Microsoft Azure 安全性專業知識內部部署的專業知識的新工具來降低風險？
- 您的公司嘗試移除內部部署的可怕 」 外部 「 帳戶，並移至雲端他們身在何處不再休眠威脅內您內部部署環境？

## <a name="analyze-on-premises-identity-infrastructure"></a>分析內部部署識別基礎結構
現在，您可以有了解有關您公司的業務需求，必須先評估您內部部署識別基礎結構。 評估是很重要的定義整合您目前的身分識別解決方案雲端身分識別管理系統的技術需求。 請確認回答下列問題︰

- 哪些驗證與授權方案沒有您的公司使用內部部署？ 
- 您的公司目前有任何內部部署的同步處理服務嗎？
- 您的公司是否使用任何協力廠商身分識別提供者 (IdP)？

您也必須知道您的公司可能會有雲端服務。 執行以瞭解目前的整合與您的環境中 SaaS、 IaaS 或 PaaS 模型評估是非常重要。 請確定此評估期間回答下列問題︰
- 您的公司是否有任何整合雲端服務提供者？
- 如果是，使用哪些服務？
- 此整合目前在生產或很試驗嗎？


>[AZURE.NOTE]
如果您沒有有正確對應的所有應用程式，雲端服務，您可以使用雲端應用程式探索工具。 這項工具可將您的 IT 部門提供完善貴組織的所有商務和消費者雲端應用程式。 使您更輕鬆地以發現陰影 IT 組織，包括使用模式和任何使用者存取您的雲端應用程式的詳細資訊。 若要存取這項工具，請移至[https://appdiscovery.azure.com](https://appdiscovery.azure.com/)

## <a name="evaluate-identity-integration-requirements"></a>評估身分識別整合需求
接下來，您要評估的身分識別整合需求。 評估很重要定義使用者驗證的方式組織的目前狀態的外觀如何在雲端，組織可讓授權的方式與使用者體驗，即將的技術需求。 請確認回答下列問題︰

- 將您的組織使用同盟、 標準驗證或兩者？
- 要求是同盟？  由於下列動作︰
 - Kerberos 型 SSO
 - 貴公司有內部部署的應用程式 （[內建內部或第 3 的廠商） 使用 SAML 或類似的同盟功能。
 - 透過智慧卡 MFA。 RSA SecurID 等。
 - 用戶端存取規則的地址下列問題︰
     1. 可以封鎖所有外部存取 Office 365 根據用戶端 IP 位址嗎？
     1. 我可以封鎖所有外部存取 Office 365，除了 Exchange ActiveSync？
     1. 我可以封鎖所有外部存取 Office 365，除了瀏覽器型的應用程式 （OWA，SPO）
     1. 可以封鎖所有外部存取 Office 365 的指定 AD 群組的成員
- 安全性/稽核疑慮
- 現有的投資的同盟驗證
- 我們的組織會使用什麼名稱我們雲端中的網域？
- 組織有自訂網域嗎？
    1. 公用與透過 DNS 輕鬆驗證，則是該網域？
    1. 如果尚未選取，然後您是否已公開網域時，可以用來在 AD 註冊替代的 UPN？
- 是以雲端的方式呈現一致的使用者識別碼？ 
- 組織是否有需要整合雲端服務的應用程式？
- 組織是否有多個網域，並將它們全部使用標準或同盟驗證？

## <a name="evaluate-applications-that-run-in-your-environment"></a>評估您的環境中執行的應用程式
現在，您有了解有關您的內部部署與雲端基礎結構，必須先評估執行這些環境中的應用程式。 評估很重要定義技術需求，以整合至雲端的身分識別管理系統，這些應用程式。 請確認回答下列問題︰

- 將我們的應用程式在哪裡？
- 將使用者存取內部部署的應用程式？  在雲端中？ 或兩者？
- 有現有的應用程式工作負載並將其移至雲端的方案嗎？
- 是否有開發新應用程式會位於 [內部部署或會使用雲端方案驗證嗎？

## <a name="evaluate-user-requirements"></a>評估使用者需求
您也可以評估使用者需求。 評估務必定義所需的上手，並協助使用者時，切換至雲端的步驟。 請確認回答下列問題︰

- 將使用者存取應用程式內部部署？
- 將使用者存取雲端中的應用程式？
- 如何執行一般使用者登入他們的內部部署環境？
- 如何將使用者登入雲端？

>[AZURE.NOTE]
請務必記錄的每個答案的筆記，並瞭解回答原理。 在可用的選項並專業人員/優缺點的每個選項，就會移[判斷應計需求](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)。  依需要回答這些會選取最佳的選項適合您的業務需要。

## <a name="next-steps"></a>後續步驟
[決定目錄同步處理需求](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>另請參閱
[設計考量概觀](active-directory-hybrid-identity-design-considerations-overview.md)
