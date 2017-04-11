<properties
    pageTitle="使用自訂網域中 Azure AD 應用程式 Proxy |Microsoft Azure"
    description="涵蓋如何搭配 Azure AD 應用程式 Proxy 中的自訂網域。"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>使用自訂網域中 Azure AD 應用程式 Proxy

使用預設網域，可讓您設定的相同 URL 為內部和外部的 URL，讓使用者只能有一個 URL 存取，請在應用程式，無論從的存取，請記住，存取應用程式。 這也可讓您在 Access 應用程式] 面板中建立單一的捷徑。 如果您是使用 Azure AD 應用程式 Proxy 所提供的預設網域，則沒有額外的設定，必須先啟用您的網域。 讓您使用自訂網域時，有幾個項目，您需要執行，請確定應用程式 Proxy 辨識您的網域，並確認其憑證。

## <a name="selecting-your-custom-domain"></a>選取您的自訂網域

1. 在[發佈應用程式中的使用應用程式 Proxy](active-directory-application-proxy-publish.md)發佈您的應用程式，根據的指示。
2. 應用程式清單中出現的應用程式之後，請選取它，然後按一下 [**設定**]。
3. 在 [**外部的 URL**，輸入您的自訂網域。
4. 如果您的外部 URL https，將會提示您上傳憑證，因此該 Azure 可以驗證的應用程式的 URL。 您也可以上傳的萬用字元憑證符合外部應用程式的 URL。 此網域必須是您的[Azure 驗證網域](https://msdn.microsoft.com/library/azure/jj151788.aspx)的清單中進行。 Azure 必須擁有憑證的應用程式或符合外部應用程式的 URL 的萬用字元憑證的網域 url。
5. 請確定新增至應用程式可讓您使用者的應用程式清單中有相同的 URL，內部和外部存取和應用程式的單一快速鍵路由內部 URL 的 DNS 記錄。

## <a name="frequently-asked-questions-about-working-with-custom-domains"></a>瞭解如何使用自訂網域的常見問題集

問︰ 可以選取已上傳的憑證不上載一次？  
A︰ 先前上傳的憑證自動繫結至應用程式，還有一個應用程式的主機名稱相符的憑證。  

問︰ 如何新增憑證，何種格式應該匯出的憑證上傳中？  
答︰ 憑證應從應用程式的 [設定] 頁面上傳。 認證應 PFX 檔案。  

問︰ 可以使用 ECC 憑證？  
A︰ 沒有任何明確的限制，在 [簽章的方法。  

問︰ 可以使用舊的憑證？  
A: [是]。  

問︰ 可以使用萬用字元的憑證？  
A: [是]。  

問︰ 可以在每個應用程式上使用不同的憑證？  
答︰ 是的除非兩個應用程式共用相同的外部主機。  

問︰ 如果我註冊新的網域，我可以使用該網域嗎？  
A: [是] 的網域清單是會找出 fed 從承租人驗證的網域清單。  

問︰ 什麼情況憑證到期日？  
A︰ 您會收到警告，在 [應用程式設定] 頁面中的 [憑證] 區段中。 當使用者嘗試存取應用程式時，會出現安全性警告。  

問︰ 若要取代指定的應用程式的認證，我該如何什麼？  
答︰ 上傳新的憑證，從 [應用程式設定] 頁面。  

問︰ 可以我刪除憑證，然後將它取代嗎？  
答︰ 當您上傳新的憑證，如果舊的憑證不在另一個應用程式時，它會自動刪除。  

問︰ 什麼情況憑證已撤銷？  
答︰ 撤銷檢查不會執行的憑證。 當使用者嘗試存取應用程式，取決於瀏覽器中，可能會出現安全性警告。  

問︰ 可以使用自我簽署的憑證嗎？  
A: [是]，允許自我簽署的憑證。 請注意，是否您使用的私人的憑證授權單位，CDP （憑證撤銷點發佈點） 憑證應該公用。  

問︰ 有位置]，請參閱我的租用戶的憑證嗎？  
答︰ 這不支援目前的版本。  


## <a name="see-also"></a>另請參閱

- [發佈應用程式的應用程式 Proxy](active-directory-application-proxy-publish.md)
- [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
- [啟用條件的存取](active-directory-application-proxy-conditional-access.md)
- [將您的自訂網域名稱新增至 Azure AD](active-directory-add-domain.md)

最新消息和更新，請參閱[應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
