<properties 
    pageTitle="如何偵錯 SAML 式單一登入 Azure Active Directory 中的應用程式 |Microsoft Azure" 
    description="瞭解如何以 SAML 為基礎單一登入 Azure Active Directory 中的應用程式偵錯 " 
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

#<a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>如何以 SAML 為基礎單一登入 Azure Active Directory 中的應用程式偵錯

當偵錯以 SAML 為基礎的應用程式整合，它，通常會使用這類[Fiddler](http://www.telerik.com/fiddler)的工具，以查看 SAML 要求、 SAML 的回應，發行至應用程式的實際 SAML token 還是有幫助。 藉由檢查 SAML 權杖，您可以確保預期會即將的所有必要的屬性、 SAML 主旨和發行者 URI 中的使用者名稱。

![][1]

從 Azure AD 包含 SAML 權杖回應通常是 HTTP 302 重新導向從 https://login.windows.net，並會傳送至應用程式設定的**回覆 URL**之後，就會發生的項目。 
 
您可以選取這一行，然後選取檢視 SAML 權杖**檢查 > WebForms**右側的面板中的索引標籤。 從該位置，請以滑鼠右鍵按一下**SAMLResponse**值，然後選取 [**傳送給 TextWizard**。 從 [解碼權杖，並查看其內容的 [**轉換**] 功能表，然後選取**從 Base64** 。
 
**附註**︰ 若要查看此 HTTP 要求的內容，Fiddler 可能會提示您設定的 HTTPS 流量，您將需要執行解密。

## <a name="related-articles"></a>相關的文章

- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
- [設定單一登入 Azure Active Directory 應用程式組件庫中的應用程式](active-directory-saas-custom-apps.md)
- [自訂宣告發出 SAML 權杖預先整合式應用程式中的方式](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ./media/active-directory-saml-debugging/fiddler.png
