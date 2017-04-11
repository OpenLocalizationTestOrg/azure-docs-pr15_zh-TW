<properties
   pageTitle="了解隱含 OAuth2 授與 Azure Active Directory 中的流程 |Microsoft Azure"
   description="瞭解更多有關隱含 OAuth2 的 Azure Active Directory 的實作授與流量，以及是否適合您的應用程式。"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="vibronet"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="vittorib;bryanla"/>

# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>了解 OAuth2 隱含授與流量的 Azure Active Directory (AD)

OAuth2 隱含授與是昭彰的被授與 OAuth2 規格中的安全性考量的最長的清單。 然後，都是實作 ADAL JS 以及撰寫選項應用程式時的建議。 呢？ 是所有的必須做的取捨︰ 隱含授與結果，是您可以嘗試從瀏覽器中使用透過 JavaScript 網頁 API 的應用程式的最佳方式。

## <a name="what-is-the-oauth2-implicit-grant"></a>什麼是 OAuth2 隱含授與？

[授與 OAuth2 授權程式碼](https://tools.ietf.org/html/rfc6749#section-1.3.1)的典型是授權授與使用兩個不同的端點。 授權端點用於使用者互動階段，藉此授權程式碼。 用戶端然後會使用 token 端點交換存取權杖，和通常的重新整理權杖的程式碼。 Web 應用程式所需展示 token 端點，自己應用程式的認證，以便授權伺服器可以驗證用戶端。

[授與 OAuth2 隱含](https://tools.ietf.org/html/rfc6749#section-1.3.2)的是允許的用戶端，以取得存取權杖 （和 id_token，若是[OpenId 連線](http://openid.net/specs/openid-connect-core-1_0.html)） 直接從沒有連絡 token 端點或驗證用戶端應用程式授權結束點。 此 variant 專為 JavaScript 以在網頁瀏覽器中執行的應用程式︰ 在原始 OAuth2 規格中，權杖會傳回 URI 片段。 權杖位才能 JavaScript 程式碼在用戶端，但可確保他們不會包含在重新導向到伺服器。 透過瀏覽器中傳回權杖會重新導向直接從授權結束點。 它也有消除交叉原點通話，如果 JavaScript 應用程式需要連絡 token 端點是必要的任何需求的優點。

OAuth2 隱含授與的重要特性是例如接續永遠不會傳回的重新整理權杖用戶端。 我們將會看到 [下一步] 區段中，不是必要並想實際上是安全性問題。

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>OAuth2 隱含授與適當的案例

為宣告 OAuth2 規格本身，已想出隱含授與，讓使用者代理程式的應用程式 – 也就是說，在瀏覽器中執行的 JavaScript 應用程式。 這類應用程式定義特性是用於存取的伺服器資源 (通常是 Web API) 和據以更新應用程式 UX JavaScript 程式碼。 考慮如 Gmail 或 Outlook Web Access 應用程式︰ 時頁面的其餘部分則維持不修改顯示新的選取範圍，當您從您的收件匣中選取郵件，變更只訊息視覺效果面板。 這是與傳統重新導向式 Web 應用程式，每個使用者的互動整頁回傳和新的伺服器回應整頁呈現結果的位置。

採用達 JavaScript 基礎的方法的應用程式稱為單一頁面應用程式] 或 SPAs︰ 是，這些應用程式只能作為初始的 [HTML] 頁面和相關聯的 JavaScript，使用所有後續互動的 Web API 呼叫執行透過 JavaScript。 不過，也是相關的隱含流程情況討論 – 混合式部署的解決方法，位置應用程式是最回傳導向，但執行偶爾 JS 電話，不是很。

重新導向應用程式通常是安全的方法無法運作，也 JavaScript 應用程式的 cookie，不過，透過要求。 Cookie 僅適用於他們已產生的同時 JavaScript 通話可能會導向其他網域的網域。 事實上的經常會大小寫︰ 考慮叫用 Microsoft Graph API，Office API Azure API – 從應用程式服務的位置的網域以外的所有位於應用程式。 JavaScript 應用程式持續增加趨勢是不後端，必須在第 3 廠商實作其商務函數的 Web Api 100%。

目前，慣用的保護的來電至 Web API 的方法是使用 OAuth2 承載者權杖方法，每個呼叫伴隨 OAuth2 存取權杖的位置。 Web API 會檢查內送的存取權杖，並找到的必要的範圍，如果其授與存取要求的作業。 隱含流程提供 JavaScript 應用程式，以取得存取權杖 Web API，好方便您機制提供許多優點的 cookie:

- 權杖可靠取得不需要對原始電話 – 交叉強制性的權杖會傳回 URI 保證的權杖是不脫離頁面重新導向的註冊
- JavaScript 應用程式可以取得最多存取權杖需要，最多網頁 api 它們目標-使用網域沒有限制
- Html5 版功能等工作階段或本機存放區授與權杖快取及存留時間管理的完全控制權，而 cookie 管理應用程式不透明
- 存取權杖不容易要求偽造 (CSRF) 攻擊的跨網站

隱含授與流程不會發出重新整理權杖，大多基於安全性理由。 重新整理權杖為尚不存取權杖，授與更多 power 以防它遺漏，因此一般說來最多為範圍。 隱含的流程，在 URL 中傳送權杖，因此攔截的風險高於授權的程式碼授與。

不過，請注意，JavaScript 應用程式另一個機制在其種方式更新存取權杖不重複提示使用者認證。 應用程式可以使用隱藏的 iframe，來執行 Azure AD 授權端點針對新的權杖要求︰ 只要在瀏覽器仍有作用中的工作階段 (讀取︰ 有工作階段 cookie) Azure AD 網域，驗證要求成功發生不需要對使用者的互動。 

此模型授予 JavaScript 應用程式能夠獨立更新存取權杖，甚至取得新的文件的新的 API，（前提是使用者先前同意它們。 如此可避免取得、 維護及保護例如重新整理權杖高值的成品新增的負擔。 成品讓靜音自動更新，Azure AD 工作階段 cookie，是應用程式外進行管理。 這種方法的其他優點是使用者可以從 Azure AD，使用應用程式登入 Azure AD，在任一瀏覽器索引標籤中執行下列任一登出。 這會導致 Azure AD 的工作階段 cookie，刪除和 JavaScript 應用程式會自動遺失的功能來更新權杖簽署查看的使用者。

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>適合隱含授與我的應用程式？

隱含授與提供更多的風險，於其他授權。 您需要注意區域有詳細的記錄 (例如看到[不正確地使用的存取權杖中隱含流程模擬資源擁有者][OAuth2-Spec-Implicit-Misuse]與[OAuth 2.0 威脅模型與安全性考量][OAuth2-Threat-Model-And-Security-Implications])。 然而，較高的風險設定檔是主要是因為用是要讓應用程式的執行作用中的程式碼，由遠端資源的瀏覽器。 如果您計劃] 選項的架構，有沒有後端元件，或想要叫用 JavaScript 透過網頁 API，建議使用隱含流程 token 取得的。

如果您的應用程式的原生的用戶端，隱含流程無法很棒的調整大小。 Azure AD 工作階段 cookie 的原生的用戶端的內容中不存在 deprives 維護的時間有多長存在的工作階段的表示您的應用程式。 取得存取權杖新資源時，這表示您的應用程式將重複會提示使用者。

如果您開發的 Web 應用程式，包括後端，以及使用從其後端的程式碼的 API，隱含流程也不是適合。 其他授權可讓您更多。 例如，OAuth2 用戶端認證授與提供能夠取得反映指派給應用程式本身，而不是使用者委派的權限的權杖。 代表用戶端能夠大資源以程式設計方式存取偶數使用者不積極進行工作階段，依此類推。 不只如此，但這類授與讓較高的安全性保證。 舉例來說，存取權杖永不傳輸透過使用者瀏覽器，他們不儲存在瀏覽器歷程記錄的風險等等。 用戶端應用程式在要求權杖時，也可以執行強大的驗證。

## <a name="next-steps"></a>後續步驟

- 針對開發人員資源的完整清單，包括參考資訊的通訊協定與 OAuth2 授權授與 Azure AD 流支援，請參閱[Azure AD 開發人員指南][AAD-Developers-Guide]
- 請參閱[如何將整合應用程式與 Azure AD]  [ACOM-How-To-Integrate]其他深度應用程式整合程序。

<!--Image references-->

<!--Reference style links in use-->
[AAD-Developers-Guide]: active-directory-developers-guide.md
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: active-directory-how-to-integrate.md
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16 
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

