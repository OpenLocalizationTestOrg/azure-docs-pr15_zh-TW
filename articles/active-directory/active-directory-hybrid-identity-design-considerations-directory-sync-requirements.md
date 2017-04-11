<properties
    pageTitle="Azure Active Directory 混合式身分識別設計考量-決定目錄同步處理需求 |Microsoft Azure"
    description="識別哪些需求所需的同步處理之間的所有使用者在 = 部署和雲端企業版。"
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

# <a name="determine-directory-synchronization-requirements"></a>決定目錄同步處理需求
同步處理是提供使用者在他們的內部部署識別為基礎的雲端身分識別。 驗證或同盟的驗證時將使用同步處理的帳戶，使用者將仍需要有在雲端的身分識別。  此身分識別需要維護和定期更新。  更新可能需要多個表單標題變更密碼的變更。  

首先評估組織內部部署識別方案與使用者需求。 評估很重要定義會如何建立和維護雲端中使用者身分識別的技術需求。  大部分的組織，Active Directory 是內部部署，並將內部部署目錄的使用者會從同步處理，不過在某些情況下並不是大小寫。  

請確認回答下列問題︰


- 您有一個 AD 樹系、 多個或無嗎？
 - 多少 Azure AD 目錄將您要同步處理到？
 
    1. 您目前使用篩選？
    2. 您有多個 Azure AD Connect 伺服器計劃嗎？
  
- 目前是否有同步處理工具內部部署？
  - 如果是的沒有您的使用者，如果使用者有虛擬目錄/整合的身分識別嗎？
- 您是否有任何其他內部部署的目錄，您想要同步處理 （例如 LDAP 目錄，HR 資料庫等）？
  - 您將會執行任何 GALSync 嗎？
  - 什麼是 upn，請在您的組織中的目前狀態？ 
  - 您有不同的使用者驗證的目錄嗎？
  - 您的公司是否使用 Microsoft Exchange？
    - 他們計劃的 exchange 混合式部署？

現在，您可以有了解有關您同步處理的需求，您需要判斷哪種工具是正確的項目，以符合這些需求。  Microsoft 提供數種工具完成目錄整合及同步處理。  請參閱[混合式身分識別目錄整合工具比較表](active-directory-hybrid-identity-design-considerations-tools-comparison.md)，如需詳細資訊。 
   
現在您擁有您的同步處理的需求和完成此貴公司的工具，您需要評估使用這些目錄服務應用程式。 評估很重要定義技術需求，以整合至雲端這些應用程式。 請確認回答下列問題︰

- 將這些應用程式移至雲端並使用目錄嗎？
- 有需要讓這些應用程式可以成功地使用同步處理至雲端的特殊屬性嗎？
- 這些應用程式會需要重新寫入善用雲端授權嗎？
- 這些應用程式會繼續 live 時使用雲端身分識別的使用者存取的內部部署？

您也需要判斷安全性需求及限制目錄同步處理。 評估很重要取得所需建立和維護雲端中的使用者的身分識別的需求的清單。 請確認回答下列問題︰

- 在同步處理伺服器都位於？
- 它會加入網域？
- 將伺服器位於限制的網路有防火牆，例如 DMZ 嗎？
  - 將您能夠開啟所需的防火牆連接埠支援同步處理嗎？
- 您是否已損壞復原計畫同步處理伺服器？
- 您有您想要與同步處理所有的樹系的正確權限的帳戶嗎？
 - 如果您的公司不知道此問題的答案，請檢閱[安裝 Azure Active Directory 同步處理服務](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService)文件中的 [] 區段中 [權限的密碼同步處理]，然後決定如果您已經擁有下列權限的帳戶，或如果您需要建立一個。
- 如果您有多方面樹系同步處理將會取得每個樹系同步處理伺服器？
 
>[AZURE.NOTE]
請務必記錄的每個答案的筆記，並瞭解答案原理。 [判斷應計需求](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)會超過可用的選項。 依需要回答這些會選取最佳的選項適合您的業務需要。

## <a name="next-steps"></a>後續步驟
[決定多重因素驗證需求](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>另請參閱
[設計考量概觀](active-directory-hybrid-identity-design-considerations-overview.md)
