<properties
    pageTitle="Azure Active Directory 身分識別保護詞彙 |Microsoft Azure"
    description="Azure Active Directory 身分識別保護詞彙"
    services="active-directory"
    keywords="azure active directory 身分識別保護]，[管理應用程式、 安全性、 風險、 風險層級、 弱點、 安全性原則、 詞彙的雲端應用程式探索"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory 身分識別保護詞彙 


### <a name="at-risk-user"></a>風險 （使用者）  
具有一個或多個作用中的風險事件的使用者。 

### <a name="atypical-sign-in-location"></a>典型的登入的位置   
登入的不是特定的使用者、 相似的使用者，或租用戶一般的地理位置。

### <a name="azure-ad-identity-protection"></a>Azure AD 身分識別保護    
安全性的可讓您合併的檢視風險事件與可能會影響組織的身分識別的弱點的 Azure Active Directory 模組。

### <a name="conditional-access"></a>條件的存取權  
設定存取資源的安全性原則。 條件存取規則會儲存於 Azure Active Directory，且會評估前授與存取權給資源的 Azure AD。  規則範例包括限制存取權的使用者位置] 裝置狀況或使用者驗證方法。

### <a name="credentials"></a>認證     
包含識別碼及證明的可用來取得本機存取及網路資源的識別的資訊。 憑證範例包括使用者名稱和密碼、 智慧卡，並且憑證。

### <a name="event"></a>事件   
Azure Active Directory 中的活動的記錄。

### <a name="false-positive-risk-event"></a>誤判 （風險事件） 
手動設定身分識別保護使用者，指出風險事件已調查正確已標示為風險事件風險事件狀態。

### <a name="identity"></a>身分識別    
個人或實體，必須使用 [驗證]，根據準則，例如密碼或憑證驗證。

### <a name="identity-risk-event"></a>身分識別風險事件     
已標示為異常身分識別保護，可能表示身分識別已洩露 AAD 事件。

### <a name="ignored-risk-event"></a>忽略 （風險事件）    
手動設定身分識別保護使用者，指出風險事件已關閉而不需採取補救動作風險事件狀態。

### <a name="impossible-travel-from-atypical-locations"></a>無法執行旅遊從典型的位置   
風險觸發事件時的同一個使用者的兩個登集未偵測到，至少一個是來自典型的登入位置，以及登增益集之間的時間小於看實際的旅行之間下列位置的最小的時間。  

###<a name="investigation"></a>調查    
檢閱活動、 記錄和其他風險事件，以決定是否有需要，請補救或補救步驟與相關的相關資訊的程序瞭解如果及身分識別如何已洩露，並瞭解如何使用識別。

### <a name="leaked-credentials"></a>遺漏的認證  

風險觸發事件時，我們研究暗網頁版中張貼公開找到目前的使用者認證 （使用者名稱和密碼）。

### <a name="mitigation"></a>降低風險  
若要限制或排除攻擊可利用識別或裝置不安全的狀態回復的身分識別或裝置動作。 降低無法解決先前的身分識別或裝置相關聯的風險事件。

### <a name="multi-factor-authentication"></a>多重因素驗證 
有兩個或多個驗證方法，其中包含項目會要求使用者驗證方法，憑證。項目使用者知道，例如使用者名稱、 密碼、 或片語。實際的屬性，例如指紋;與個人的屬性，例如個人的簽章。

### <a name="offline-detection"></a>離線偵測   
偵測異常和事件，例如在嘗試登入的風險評估之後，已經發生的事件。

### <a name="policy-condition"></a>原則條件    
安全性原則定義的實體 （群組、 應用程式、 裝置平台、 裝置狀態、 IP 範圍、 使用者用戶端類型） 原則包含或排除它的一部分。

### <a name="policy-rule"></a>原則規則     
安全性原則說明原則，並觸發原則時所採取的動作會觸發的情況下的組件。

### <a name="prevention"></a>防止  
若要防止產生的損害透過濫用組織身分識別或裝置的動作可疑或知道洩漏。 防止動作不保護裝置或身分識別，仍無法解決先前的風險事件。

### <a name="privileged-user"></a>權限 （使用者）
使用者的風險事件，同時永久或暫時系統管理員權限給一或多個資源 Azure Active Directory，例如為全域管理員，計費管理員、 服務管理員、 使用者管理員和密碼管理員。 

###<a name="real-time"></a>即時    
請參閱即時偵測。

###<a name="real-time-detection"></a>即時偵測  
若要繼續允許異常偵測並評估的事件，例如 [登入嘗試在事件發生前的風險。

### <a name="remediated-risk-event"></a>在於 （風險事件）     
自動設定的身分識別保護]，表示這類風險事件使用標準補救動作在於風險事件風險事件狀態。 例如時重設使用者的密碼時，, 指出先前的密碼已洩露許多風險事件會自動在於。

### <a name="remediation"></a>修復     
安全性身分識別或裝置先前可疑或洩漏已知的動作。 補救動作會的身分識別或裝置還原為安全的狀態，以及解決先前的身分識別或裝置相關聯的風險事件。

### <a name="resolved-risk-event"></a>解決 （風險事件）   
手動設定身分識別保護使用者風險事件狀態，指出使用者進行外部身分識別保護適當修復動作，應考慮風險事件已關閉。

###<a name="risk-event-status"></a>風險事件狀態    
風險事件的屬性時，指出是否事件已啟動，且如果關閉，關閉的原因。

###<a name="risk-event-type"></a>風險事件類型  
風險事件，表示類型異常造成視為高風險事件的類別。

###<a name="risk-level-risk-event"></a>風險層級 （風險事件）  
他們需要組織降低風險的風險事件來協助決定解決方案的優先順序的身分識別保護使用者嚴重性表示 （[高]、 [中] 或 [低）。 

###<a name="risk-level-sign-in"></a>風險層級 （登入） 
機率，用於指定登入，其他人正嘗試使用使用者的身分識別表示 （[高]、 [中] 或 [低）。

###<a name="risk-level-user-compromise"></a>風險層級 （使用者洩漏） 
已遭到盜用身分識別的可能性表示 （[高]、 [中] 或 [低）。

### <a name="risk-level-vulnerability"></a>風險層級 （弱點）  
他們需要組織降低風險，協助排列優先順序動作的身分識別保護使用者弱點的嚴重性表示 （[高]、 [中] 或 [低）。

### <a name="secure-identity"></a>安全 （識別）   
進行修復動作，例如變更密碼或還原追求狀態可能會識別因電腦。

### <a name="security-policy"></a>安全性原則
原則規則和條件的集合。 可以套用原則，例如使用者、 群組、 應用程式、 裝置、 裝置平台、 裝置狀態、 IP 範圍及 Auth2.0 用戶端類型的項目。 啟用原則時，則會評估時包含在原則的實體發出的權杖資源。

### <a name="sign-in-v"></a>登入 (v) 
若要進行驗證身分識別 Azure Active Directory 中。

### <a name="sign-in-n"></a>登入 (n) 
程序或驗證身分識別的 Azure Active Directory 與事件擷取這項作業的動作。

###<a name="sign-in-from-anonymous-ip-address"></a>登入匿名 IP 位址    
風險事件觸發之後順利登入已被識別為匿名 proxy IP 位址的 IP 位址。

###<a name="sign-in-from-infected-device"></a>登入從受感染的裝置 
風險觸發事件時登入來自於也就是一個或多個危害裝置，主動正在嘗試與傀儡伺服器通訊所使用的 IP 位址。

###<a name="sign-in-from-ip-address-with-suspicious-activity"></a>使用登入的 IP 位址可疑的活動 
順利登入的 IP 位址大量失敗的登入與跨多個使用者帳戶在一段時間之後，就會觸發風險事件。

###<a name="sign-in-from-unfamiliar-location"></a>登入從熟悉的位置 
當使用者順利登入新的位置 （IP、 經緯度和 ASN） 從觸發風險事件。

###<a name="sign-in-risk"></a>登入風險     
請參閱風險層級 （登入）

###<a name="sign-in-risk-policy"></a>登入風險原則  
評估特定的登入的風險，並套用降低根據預先定義的條件和規則條件存取原則。

###<a name="user-compromise-risk"></a>使用者洩漏的風險     
請參閱風險層級 （使用者洩漏）

###<a name="user-risk"></a>使用者風險    
請參閱風險層級 （使用者洩漏）。

###<a name="user-risk-policy"></a>使用者風險原則     
根據登入，並套用降低根據預先定義的條件和規則條件存取原則。

###<a name="users-flagged-for-risk"></a>標幟為風險的使用者   
有風險事件，也就是 [作用中] 或 [在於的使用者

###<a name="vulnerability"></a>階段    
設定或讓目錄容易利用 Azure Active Directory 中的條件或威脅。


## <a name="see-also"></a>另請參閱

- [Azure Active Directory 身分識別保護](active-directory-identityprotection.md)
