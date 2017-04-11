<properties
   pageTitle="如何應用程式會新增至 Azure Active Directory。"
   description="本文將說明如何新增應用程式的 Azure Active Directory 執行個體。"
   services="active-directory"
   documentationCenter=""
   authors="shoatman"
   manager="kbrint"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="02/09/2016"
      ms.author="shoatman"/>

# <a name="how-and-why-applications-are-added-to-azure-ad"></a>應用程式如何以及為何會新增至 Azure AD

一開始令人費解的項目時檢視應用程式清單中的 Azure Active Directory 執行個體的其中一個瞭解應用程式的來源，以及為何有。  本文將提供應用程式如何在目錄中表示與可協助您瞭解如何應用程式已預先可以在您的目錄中的內容為您提供的高層級概觀。

## <a name="what-services-does-azure-ad-provide-to-applications"></a>Azure AD 並未提供哪些服務應用程式？

應用程式會新增至 Azure AD 運用一或多個它所提供的服務。  這些服務包括︰

* 驗證應用程式和授權
* 使用者驗證與授權
* 單一登入 (SSO) 使用同盟或密碼
* 使用者佈建與同步處理
* 角色型存取控制;使用目錄以定義執行角色的應用程式角色依據應用程式中的授權檢查。
* oAuth 授權服務 （由 Office 365 和其他 Microsoft 應用程式授權 Api/資源的存取權。）
* 發佈應用程式與 proxy;發佈到網際網路私人的網路應用程式

## <a name="how-are-applications-represented-in-the-directory"></a>應用程式代表目錄中的？

在使用 2 物件 Azure AD 表示應用程式︰ 應用程式物件和服務主體的物件。  一個應用程式物件，在 [常用] 中註冊 / 「 擁有者 」 或 「 發佈 」 目錄及一或多服務主體代表其運作每個目錄中的應用程式的物件。  

應用程式物件描述 Azure AD 應用程式 （多租用戶服務），可能會包含下列任何一項: (*請注意*︰ 這不是完整的清單。)

* 名稱、 標誌和 Publisher
* 機密資料 （用來驗證應用程式對稱及/或非對稱式鍵）
* API 相依性 (oAuth)
* Api/資源/範圍發佈 (oAuth)
* 應用程式的角色 (RBAC)
* SSO 中繼資料] 及 [設定 (SSO)
* 佈建中繼資料] 及 [設定使用者
* Proxy 中繼資料和設定

服務主要是每個目錄，應用程式可包括其主目錄中的應用程式的一筆記錄。  服務原則︰

* 透過 [應用程式識別碼] 屬性的應用程式物件參照
* 記錄本機使用者和群組應用程式角色指派
* 記錄本機的使用者和管理員權限授與應用程式
    * 例如︰ 若要存取特定的使用者電子郵件應用程式的權限
* 記錄本機的原則，包括條件存取原則
* 記錄本機替代本機設定應用程式
    * 宣告轉換規則
    * （使用者佈建） 的屬性對應
    * （如果應用程式支援的自訂角色） 租用戶特定應用程式的角色
    * 名稱/標誌

### <a name="a-diagram-of-application-objects-and-service-principals-across-directories"></a>應用程式物件在目錄服務原則的圖表

![說明如何應用程式物件和服務主體 Azure AD 執行個體的現有圖表。][apps_service_principals_directory]

您可以看到上圖中。  Microsoft 保留兩個目錄內部 （左側） 用來發佈應用程式。

* 一個 Microsoft 應用程式 （Microsoft 服務目錄）
* 其中一個預先整合第 3 廠商應用程式 （應用程式庫目錄）

應用程式的發行者/廠商與 Azure AD 整合需要有發行的目錄。  （某些 SAAS 目錄）。

將您自己新增的應用程式包括︰

* 應用程式的您開發 （整合式 AAD）
* 應用程式，您連線的單一登入
* 您所發行的應用程式使用 Azure AD 應用程式 proxy。

### <a name="a-couple-of-notes-and-exceptions"></a>有幾種備忘稿和例外狀況

* 並非所有服務原則回都指向應用程式物件。  吧？ Azure AD 原先建置應用程式所提供的服務已更多限制，服務主要已經足夠建立應用程式識別。  原始的服務主要是在圖案中的 Windows Server Active Directory 服務帳戶深入瞭解。  因此的仍然可以建立不需要先建立應用程式物件使用 PowerShell 的 Azure AD 的服務原則。  圖形 API 建立服務本金之前需要應用程式物件。
* 上述資訊而非所有目前是以程式設計方式公開。  以下是只提供使用者介面中︰
    * 宣告轉換規則
    * （使用者佈建） 的屬性對應
* 如需更多的詳細資訊的服務本金和應用程式的物件，請參閱 Azure AD 圖形 REST API 參考文件。  *提示*︰ Azure AD 圖形 API 文件是目前提供的 Azure AD 結構描述參照至最接近的項目。  
    * [應用程式](https://msdn.microsoft.com/library/azure/dn151677.aspx)
    * [服務原則](https://msdn.microsoft.com/library/azure/dn194452.aspx)


## <a name="how-are-apps-added-to-my-azure-ad-instance"></a>應用程式新增到我的 Azure AD 執行個體的？
有多種方式可以加入 Azure AD 應用程式︰

* 從[Azure Active Directory 應用程式庫](https://azure.microsoft.com/updates/azure-active-directory-over-1000-apps/)新增應用程式
* 登向上/到第 3 與 Azure Active Directory 整合的廠商應用程式 (例如︰ [Smartsheet](https://app.smartsheet.com/b/home)或[DocuSign](https://www.docusign.net/member/MemberLogin.aspx))
    * 向上鍵/在使用者登過程中會要求提供應用程式的權限，來存取他們的設定檔和其他權限。  若要授與同意的第一個人員會使服務主要代表應用程式新增至目錄。
* 向上鍵/到 Microsoft 線上服務，例如[Office 365](http://products.office.com/)登入
    * 當您 Office 365 訂閱，或開始的試用代表的各項服務所傳送的所有功能與 Office 365 相關聯的目錄中建立一或多個服務原則。
    * 部分 Office 365 服務，例如 SharePoint 會建立在進行允許包括工作流程元件間的安全通訊服務原則。
* Azure 管理入口網站，請參閱新增您在開發應用程式︰ https://msdn.microsoft.com/library/azure/dn132599.aspx
* 新增您在開發使用 Visual Studio 應用程式請參閱︰
    * [ASP.Net 驗證方法](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)
    * [已連線的服務]](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)
* 新增應用程式，請透過使用[Azure AD 應用程式 Proxy](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* 連線單一登入使用 SAML 或密碼 SSO 應用的程式
* 許多其他包括中 Azure 的各種開發人員體驗/集和 API 總管體驗跨開發人員中心

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>誰有權限可新增至我的 Azure AD 執行個體的應用程式？

只有全域管理員可以︰

* 從 Azure AD 應用程式庫 （預先整合第 3 廠商應用程式） 新增應用程式
* 發佈應用程式使用 Azure AD 應用程式 Proxy

在您的目錄中的所有使用者都有權限新增開發的應用程式，並排除或限制的應用程式在他們共用/授與存取其組織資料。  *請記得在使用者的登入向上/應用程式，並授與權限可能會導致所建立的服務主要。*

這可能會最初音效指派，但請記住下列︰

* 應用程式已能夠針對使用者進行驗證，許多年運用 Windows Server Active Directory，而不需要的應用程式，在目錄中註冊/錄製。  現在組織會有改良完全多少應用程式使用的目錄與苦頭 for 的可見性。
* 不是需要管理員導向應用程式發佈/註冊程序。  使用 Active Directory Federation Services 很可能已新增為信賴代表開發人員的應用程式的系統管理員。  現在開發人員可以自助。
* 登入/最商業用途使用自己的組織帳戶的應用程式中的使用者是一種很好的項目。  隨後離開組織，就無法存取其在應用程式中使用的帳戶。
* 有什麼資料已共用的應用程式是好了一筆記錄。  資料是比以前更可移動，有哪些應用程式共用者資料清除記錄是有用。
* Azure AD 用於 oAuth 的應用程式會決定完全哪些權限的使用者可以授與應用程式和權限要求同意系統管理員。  它應而不說只有管理員可以同意較大的範圍及更重要的權限。
* 新增及允許存取其資料的應用程式的使用者稽核的事件，讓您可以檢視稽核報告 Azure 安全性群組入口網站中，決定如何新增應用程式目錄。

**附註︰***Microsoft 本身具有作業系統，現在有許多月使用預設的設定。*

所有的說可以防止目錄中的使用者新增應用程式並從行使排除或限制的資訊上他們與共用應用程式修改 Azure 管理入口網站中的 [目錄設定。  在目錄中的 「 設定 」 索引標籤上的 [Azure 管理入口網站中可以存取下列設定。

![設定整合式應用程式設定 UI 的螢幕擷取畫面][app_settings]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟

進一步瞭解如何新增應用程式，以 Azure AD 以及如何設定服務應用程式。

* 開發人員︰ 可讓您[瞭解如何將整合 AAD 應用程式](https://msdn.microsoft.com/library/azure/dn151122.aspx)
* 開發人員︰[在 Github 的 Azure Active Directory 整合應用程式的程式碼的檢閱範例](https://github.com/AzureADSamples)
* 開發人員和 IT 專業人員︰[檢閱 Azure Active Directory 圖表 API 的 REST API 文件](https://msdn.microsoft.com/library/azure/hh974478.aspx)
* IT 專業人員︰ 可讓您[瞭解如何使用應用程式庫中的 Azure Active Directory 預先整合式應用程式](https://msdn.microsoft.com/library/azure/dn308590.aspx)
* IT 專業人員︰[尋找設定特定的預先整合式應用程式的教學課程](https://msdn.microsoft.com/library/azure/dn893637.aspx)
* IT 專業人員︰[了解如何發佈應用程式使用 Azure Active Directory 應用程式 Proxy](https://msdn.microsoft.com/library/azure/dn768219.aspx)

## <a name="see-also"></a>另請參閱

- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)

<!--Image references-->
[apps_service_principals_directory]:media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
[app_settings]:media/active-directory-how-applications-are-added/IntegratedAppSettings.jpg
