<properties
    pageTitle="使用宣告注意的應用程式中的應用程式 Proxy"
    description="說明如何使用 Azure AD 應用程式 Proxy 運用及執行。"
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



# <a name="working-with-claims-aware-apps-in-application-proxy"></a>使用宣告注意的應用程式中的應用程式 Proxy

宣告注意應用程式執行重新導向至安全性 Token 服務 (STS)，依序要求認證來 token 使用者的使用者導向至應用程式之前。 若要啟用應用程式 Proxy]，使用 [重新導向，您必須採取下列步驟。

## <a name="prerequisites"></a>必要條件
之前執行此程序，請務必 STS 的宣告注意應用程式會重新導向至您的內部部署網路以外，您可以使用。

## <a name="azure-classic-portal-configuration"></a>Azure 傳統的入口網站設定

1. 發佈您的應用程式，根據[發佈應用程式的應用程式 Proxy](active-directory-application-proxy-publish.md)所述的指示。
2. 在應用程式清單中，選取宣告注意應用程式，然後按一下 [**設定**]。
3. 如果您選擇**傳遞**做為您**預先驗證方法**，請務必為您的**外部 URL**配置選取**HTTPS** 。
4. 如果您選擇**Azure Active Directory**作為您**預先驗證方法**時，選取 [**無**做為您的**內部驗證方法**。


## <a name="adfs-configuration"></a>在 ADFS 設定

1. 開啟 ADFS 管理]。
2. 移至**依賴廠商信任**，以滑鼠右鍵按一下您要發佈的應用程式 Proxy]，然後選擇 [**內容**的應用程式。  
  ![依賴廠商信任應用程式名稱-screentshot 上以滑鼠右鍵按一下](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  
3. 在 [**結束點**] 索引標籤的 [**結束點類型**] 之下選取**WS 同盟**。
4. **信任的 URL** ] 下輸入您在應用程式 Proxy**外部 URL** ] 下輸入的 URL，然後按一下**[確定]**。  
  ![新增端點-設定受信任的 URL 值-螢幕擷取畫面](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="see-also"></a>另請參閱

- [發佈應用程式的應用程式 Proxy](active-directory-application-proxy-publish.md)
- [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
- [您正與應用程式 Proxy 的問題進行疑難排解](active-directory-application-proxy-troubleshoot.md)
- [啟用與 proxy 應用程式互動的原生的用戶端應用程式](active-directory-application-proxy-native-client.md)

最新消息和更新，請參閱[應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
