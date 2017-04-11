<properties
    pageTitle="Azure Active Directory 混合式身分識別設計考量-概觀 |Microsoft Azure"
    description="概觀與內容的地圖的混合式身分識別設計考量指南"
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

# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Azure Active Directory 混合式身分識別設計考量

消費者為基礎的裝置 proliferating 公司世界中，且容易採用雲端軟體為-的-服務 (SaaS) 應用程式。 如此一來，容易維護控制使用者的應用程式存取內部資料中心和雲端的平台上。  Microsoft 的身分識別解決方案橫跨內部部署和雲端功能，建立單一使用者身分識別的驗證及所有的資源，隨時隨地的授權。 我們呼叫此混合式身分識別。 有不同的設計及設定選項的 [使用 Microsoft 解決方案的混合式身分識別，然後在某些情況下，可能難以判斷哪個組合最符合貴組織的需求。 此混合式身分識別設計考量指南將協助您瞭解如何設計混合式身分識別解決方案的最適合企業與貴組織的技術需求。  本指南將詳細說明一系列的步驟，您可以依照可協助您設計符合您組織的唯一需求的混合式身分識別解決方案的工作。 在步驟和工作，指南會顯示相關技術及功能開會功能及服務品質 （例如可用性、 延展性、 效能、 管理能力和安全性） 的組織使用的選項層級的需求。 具體來說，混合式身分識別設計考量指南目標是回答下列問題︰ 

- 詢問及回答驅動混合式身分識別特定設計技術或問題網域最符合需求我是否需要什麼問題？
- 設計混合式身分識別解決方案的技術或問題的網域應完成之活動何種順序？ 
- 混合式身分識別技術及設定選項可協助我符合我需求？ 什麼是權衡這些選項之間，好讓我可以在我的商務用選取的最佳選擇？


## <a name="who-is-this-guide-intended-for"></a>誰是本指南供？
 CIO CITO、 人力資源身分識別設計師、 企業架構與 IT 架構負責設計中或大型組織的混合式身分識別解決方案。

## <a name="how-can-this-guide-help-you"></a>如何本指南協助您？ 
您可以使用本指南，瞭解如何設計可以與您目前的內部部署識別方案整合雲端為基礎的身分識別管理系統的混合式身分識別解決方案。 下列圖形顯示範例可讓 IT 來管理整合其目前的 Windows Server Active Directory 方案的系統管理員的混合式身分識別解決方案位於內部部署與 Microsoft Azure Active Directory，讓使用者可以使用單一登入 (SSO) 位於雲端和內部部署的應用程式。

![](./media/hybrid-id-design-considerations/hybridID-example.png)


上述圖例是混合式身分識別解決方案，運用雲端服務以整合內部部署功能，才能提供單一使用者驗證程序的體驗，並促進範例 IT 管理這些資源。 雖然這可能是常見的情況，每個組織的混合式身分識別設計有可能不同的範例中圖 1 因為不同的需求。 本指南可提供一系列的步驟，您可以依照設計符合您組織的唯一需求的混合式身分識別解決方案的工作。 下列步驟和工作，整個指南會提供相關技術及功能選項可用您符合功能及服務組織的品質等級需求。

**假設**︰ 您的 Windows Server、 Active Directory 網域服務與 Azure Active Directory 經驗。 在此文件中，我們假設您正在尋找如何這些解決方案可以符合您的業務需求自己或中整合式解決方案。

## <a name="design-considerations-overview"></a>設計考量概觀
這份文件會提供一組步驟及工作，您可以依照設計最符合您需求的混合式身分識別解決方案。 步驟會以排序的順序呈現。 您將瞭解在稍後步驟中的設計考量可能會要求您變更的做出先前的步驟，不過，由於衝突的設計選項。 每個想要通知您在文件的潛在設計衝突。 

您將會送達最佳重複次數以將合併的文件內的考量所有必要的步驟之後，才符合您需求的設計。 

| 混合式身分識別階段                                             | 主題清單                                                                                                                                                                                       |
|-------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 判斷身分識別的需求                                   | [決定業務需求](active-directory-hybrid-identity-design-considerations-business-needs.md)<br> [決定目錄同步處理需求](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [決定多重因素驗證需求](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [定義混合式身分識別採用策略](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)                       |
| 加強資料安全性強式的身分識別解決方案的計劃 | [決定資料保護需求](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [決定內容管理需求](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [決定存取控制需求](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [決定應計需求](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [定義資料保護策略](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)  |
| 規劃混合式身分識別生命週期                                | [決定混合式身分識別管理工作](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [同步處理管理](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [決定混合式身分識別管理採用策略](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |     


##<a name="download-this-guide"></a>下載本指南
您可以從[Technet 庫](https://gallery.technet.microsoft.com/Azure-Hybrid-Identity-b06c8288)下載指南 pdf 版本混合式身分識別設計考量。 

                                                             
