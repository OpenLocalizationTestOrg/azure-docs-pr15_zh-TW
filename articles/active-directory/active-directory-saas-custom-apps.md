<properties 
    pageTitle="設定單一登入 Azure Active Directory 應用程式組件庫中的應用程式 |Microsoft Azure" 
    description="瞭解如何以自助連線到使用 SAML 和密碼以 SSO 的 Azure Active Directory 的應用程式" 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="02/09/2016" 
    ms.author="asmalser" />

#<a name="configuring-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>設定單一登入 Azure Active Directory 應用程式組件庫中的應用程式

本文是此功能可讓系統管理員設定單一登入應用程式沒有顯示在 Azure Active Directory 應用程式組件庫*不需要撰寫程式碼*。 這項功能在 2015 年 11 月 18 年技術預覽發行和所包含的[Azure Active Directory 進階版](active-directory-editions.md)。 如果您改用尋找如何與程式碼透過 Azure AD 整合自訂應用程式開發人員指南，請參閱[Azure ad 驗證案例](active-directory-authentication-scenarios.md)。

Azure Active Directory 應用程式庫提供含有已知會支援單一登入與 Azure Active Directory 的表單，[本文](active-directory-appssoaccess-whatis.md)所述的應用程式的清單。 當您 （為您組織中 IT 專家或系統積分） 找到您要連線的應用程式時，您可以開始使用，請遵循逐步指示呈現 Azure 管理入口網站中啟用單一登入。

[Azure Active Directory 進階版](active-directory-editions.md)授權客戶，也可以取得這些額外功能︰

* 任何支援 SAML 2.0 身分識別提供者 （SP 啟動或 IdP 啟動） 應用程式的自助整合
* 有 html 登入頁面使用[密碼為基礎的 SSO](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)任何 web 應用程式的自助整合
* 用於 SCIM 通訊協定使用者佈建 （[此處所述](active-directory-scim-provisioning.md)） 的應用程式的自助連線
* 若要將連結新增至[Office 365 應用程式啟動器](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/)或[Azure AD 存取面板](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)中的任何應用程式的能力

這可以包含不只 SaaS 應用程式的使用，但有不尚未被上-boarded Azure AD 應用程式庫，但您的組織有部署至伺服器您控制，請在雲端或內部部署中的第三方 web 應用程式。

這些功能，也就是*應用程式整合範本*，提供的支援 SAML、 SCIM 或表單型驗證]，並包含有彈性的選項和設定與的應用程式的相容性的應用程式的標準的連接點。 

##<a name="adding-an-unlisted-application"></a>新增未列出應用程式

若要連線的應用程式會使用的應用程式整合範本，登入使用 Azure Active Directory 系統管理員帳戶，Azure 管理入口網站，瀏覽至**Active Directory > [目錄] > 應用程式**區段中，選取 [**新增**]，然後按一下 [**新增應用程式，從圖庫**。 

![][1]

在 [應用程式] 庫中，您可以新增未列出應用程式的左側，或選取 [**新增未列出應用程式**] 連結會顯示在搜尋結果中，如果找不到您想要的應用程式的使用**自訂**類別。 輸入您的應用程式的名稱之後，您可以設定單一登入選項和行為。 

**快速秘訣**︰ 最佳作法是，請檢查是否應用程式已經存在的應用程式庫中使用搜尋功能。 如果找不到應用程式，描述提及 」 單一登入]，然後應用程式已經支援同盟單一登入。 

![][2]

這種方式新增應用程式提供可供預先整合式應用程式項目非常類似的情況。 若要開始，請選取 [**設定單一登入**。 下一個畫面中會顯示下列三個選項，以設定單一登入，下列各節所述。

![][3]


##<a name="azure-ad-single-sign-on"></a>Azure AD 單一登入

選取此選項，以設定 SAML 式驗證應用程式。 這需要的應用程式支援 SAML 2.0，您應該收集資訊如何使用 SAML 功能在繼續之前的應用程式。 選取 [**下一步**之後, 會提示您輸入對應至應用程式的 SAML 端點的三個不同的 Url。 

![][4]
 
以下是︰

* **登入 URL (SP 啟動只)** – 使用者移至 [登入這個應用程式的位置。 如果應用程式設定為執行服務提供者啟動的單一登入，然後當使用者瀏覽此 URL，服務提供者就會執行必要的重新導向至 Azure AD 驗證，並在使用者登入。 如果已填入此欄位，Azure AD 會啟動從 Office 365 和 Azure AD 存取面板應用程式使用這個 URL。 如果此欄位是 ommited，然後 Azure AD 會改為執行身分識別提供者-發起的登入應用程式時啟動從 Office 365，Azure AD 存取] 面板中，或從 Azure AD 單一登入 URL (copiable 從 [儀表板] 索引標籤)。

* 正在設定**發行者 URL** URL 應該唯一識別的單一登的應用程式的發行者。 這是 Azure AD 傳送回應用程式的 SAML 權杖，做為**簡報對象**參數的值，而且應用程式應該驗證了。 此值也會顯示為 [應用程式提供任何 SAML 中繼資料中的**實體識別碼**。 檢查內容的詳細資料的應用程式的 SAML 文件是實體識別碼，或對象該值。 以下是範例對象 URL SAML 權杖傳回應用程式中的顯示方式︰

```
    <Subject>
    <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:unspecificed">chad.smith@example.com</NameID>
        <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
      </Subject>
      <Conditions NotBefore="2014-12-19T01:03:14.278Z" NotOnOrAfter="2014-12-19T02:03:14.278Z">
        <AudienceRestriction>
          <Audience>https://tenant.example.com</Audience>
        </AudienceRestriction>
      </Conditions>
```

* **回覆 URL**回覆 URL 是應用程式預期接收 SAML 權杖的位置。 這也稱為**判斷提示消費者服務 (ACS) URL**。 查看應用程式的 SAML 文件的 URL 或 ACS URL 其 SAML 權杖回覆功能的詳細資訊。
 這些輸入後，按一下 [**下一步**前進到下一個畫面]。 這個畫面提供所需的設定讓它接受從 Azure AD SAML 權杖應用程式方的相關資訊。 

![][5]

哪些值所需將應用程式而有所不同，因此請查看應用程式的 SAML 文件，如需詳細資訊。 **登入**並**教具借出**服務兩者解析為相同的端點，也就是 SAML 要求處理端點的 Azure AD 您執行個體的 URL。 發行者 URL 是顯示為 「 簽發者 」 SAML 權杖內發行至應用程式的值。 

在設定您的應用程式之後，按一下 [**下一步**] 按鈕，然後關閉對話方塊的**完成**]。 

##<a name="assigning-users-and-groups-to-your-saml-application"></a>指定 [SAML 應用程式的 [使用者和群組 

一旦您的應用程式已設定以 SAML 為基礎的身分識別提供者使用 Azure AD，則幾乎可以測試。 做為安全性控制項，Azure AD 就不會發出權杖，讓他們能夠登入應用程式，除非已獲授予他人使用 Azure AD 存取。 使用者可能會被授與存取直接，或透過其所屬的群組。 

若要指定使用者或群組至您的應用程式中，按一下 [**指派的使用者**] 按鈕。 選取您想要指派，請] 群組或使用者，然後選取 [**指派**] 按鈕。 

![][6]

將使用者指派可讓 Azure AD 發出 token 使用者，以及造成此使用者的存取面板中出現的應用程式的磚。 如果使用者使用的 Office 365 應用程式方磚也會出現在 [Office 365 應用程式啟動器。 

您可以上傳使用在 [**設定**] 索引標籤上的 [**上傳標誌**] 按鈕，應用程式的應用程式的磚標誌。 

###<a name="customizing-the-claims-issued-in-the-saml-token"></a>自訂發出 SAML 權杖宣告 

當使用者至應用程式，Azure AD 會發出 SAML 權杖給包含資訊 （或 [宣告]） 的應用程式相關的唯一識別這些使用者。 根據預設，此包含使用者的使用者名稱、 電子郵件地址、 名字和姓氏。 

您可以檢視或編輯傳送 SAML 權杖，在 [**屬性**] 索引標籤下的應用程式中的宣告。 

![][7]

有兩個可能的原因，您可能需要編輯發出 SAML 權杖宣告︰ 寫入 •The 應用程式需要不同的宣告 Uri 集或已部署 • 您應用程式的方式，需要 NameIdentifier 宣告值宣告是儲存在 Azure Active Directory 中的使用者名稱 （AKA 使用者主體名稱） 以外。 

如何新增及編輯宣告，這些案例的詳細資訊，請參閱此[上宣告自訂文件](active-directory-saml-claims-customization.md)。 

###<a name="testing-the-saml-application"></a>測試 SAML 應用程式 

Azure AD 和應用程式中有設定 SAML Url 及憑證之後, 使用者或群組已指派給 Azure 中的應用程式並已檢閱及編輯如有必要，宣告然後使用者已準備好要登入應用程式。 

若要測試，只要登-到 Azure AD 存取面板 https://myapps.microsoft.com 使用應用程式，您指派的使用者帳戶，然後按一下 [] 以開始單一登入程序的應用程式的磚。 或者，您可以瀏覽到應用程式和登入的登入 URL 直接從該處。 

偵錯秘訣，請參閱此[文章︰ 如何偵錯 SAML 式單一登入應用程式](active-directory-saml-debugging.md) 

##<a name="password-single-sign-on"></a>密碼單一登入

選取此選項，以設定[密碼以單一登入](active-directory-appssoaccess-whatis.md)web 應用程式含有 HTML 登入頁面。 密碼為基礎的 SSO，也稱為密碼 vaulting，可讓您管理使用者存取權和不支援的識別同盟的 web 應用程式的密碼。 也很適合案例多個使用者需要以共用單一帳戶，例如，您組織的社交媒體應用程式帳戶的位置。 

選取 [**下一步**之後, 會提示您輸入的登入頁面應用程式的 web URL。 請注意，這必須是包含的使用者名稱和密碼的輸入的欄位的頁面。 在輸入後，Azure AD 啟動程序來剖析輸入使用者名稱和密碼輸入的登入頁面。 如果不成功程序，然後將其引導您完成安裝瀏覽器副檔名 （需要 Internet Explorer、 Chrome、 或 Firefox） 可讓您以手動方式擷取欄位的替代程序。

一旦擷取的登入頁面，可能會指派給使用者和群組，可以設定認證原則，就像一般的[密碼 SSO 應用程式](active-directory-appssoaccess-whatis.md)。

注意︰ 您可以上傳使用在 [**設定**] 索引標籤上的 [**上傳標誌**] 按鈕，應用程式的應用程式的磚標誌。 

##<a name="existing-single-sign-on"></a>現有單一登入

選取此選項，將連結新增至您組織的 Azure AD 存取畫面或 Office 365 入口網站應用程式。 您可以將連結新增至目前使用的 Azure Active Directory Federation Services （或其他同盟服務） 的自訂 web 應用程式使用此，而不是 Azure AD 進行驗證。 或者，您可以新增 [深層連結至特定的 SharePoint 頁面或您只是要出現在您的使用者存取面板上其他網頁。 

選取 [**下一步**之後, 會提示您輸入要連結的應用程式的 URL。 完成之後，應用程式，使應用程式，以顯示[Office 365 應用程式啟動器](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/)或這些使用者[Azure AD 存取面板](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)中可能會指派使用者和群組。

注意︰ 您可以上傳使用在 [**設定**] 索引標籤上的 [**上傳標誌**] 按鈕，應用程式的應用程式的磚標誌。

## <a name="related-articles"></a>相關的文章

- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
- [自訂宣告發出 SAML 權杖預先整合式應用程式中的方式](active-directory-saml-claims-customization.md)
- [以 SAML 為基礎單一登入的疑難排解](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png
