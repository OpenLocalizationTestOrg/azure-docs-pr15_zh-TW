<properties
    pageTitle="單一登入與應用程式 Proxy |Microsoft Azure"
    description="說明如何提供單一登入使用 Azure AD 應用程式 Proxy。"
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
    ms.date="08/10/2016"
    ms.author="kgremban"/>


# <a name="single-sign-on-with-application-proxy"></a>單一登入與應用程式 Proxy

單一登入是 Azure AD 應用程式 Proxy 的關鍵元素。 它提供最佳的使用者體驗，執行下列步驟︰

1. 使用者登入雲端  
2. 所有的安全性驗證發生在雲端 （預先驗證）  
3. 當邀請傳送至內部部署的應用程式時，應用程式 Proxy 連接器模擬使用者。 後端應用程式認為這是一般使用者即將從網域的裝置。

![從使用者透過應用程式 Proxy 至公司網路的存取圖表](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png)

Azure AD 應用程式 Proxy 可協助您為使用者提供單一登入 (SSO) 的體驗。 若要發佈您的應用程式使用 SSO 使用下列指示︰


## <a name="sso-for-on-prem-iwa-apps-using-kcd-with-application-proxy"></a>使用應用程式 Proxy KCD 的內部部署 IWA 應用程式 SSO
您可以啟用單一登入應用程式使用整合式 Windows 驗證 (IWA) 模擬的使用者，並傳送及接收代表自己的權杖 Active Directory 中提供的應用程式 Proxy 連接器權限。


### <a name="network-diagram"></a>網狀圖

當使用者嘗試存取的內部部署應用程式使用 IWA 時，此圖說明流程。

![Microsoft AAD 驗證流程圖](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

1. 使用者可以輸入透過應用程式 Proxy 存取的內部部署的應用程式的 URL。
2. 應用程式 Proxy 重新導向至要預先驗證 Azure AD 驗證服務要求。 此時，Azure AD 適用於任何適用的驗證和授權原則，例如多重要素驗證。 如果使用者已驗證，則 Azure AD 建立權杖，並將其傳送給使用者。
3. 使用者將權杖傳遞到應用程式 Proxy。
4. 應用程式 Proxy 驗證的權杖擷取使用者主要名稱 (UPN)，並透過 dually 經過驗證的安全通道連接器傳送邀請 UPN，與服務主要名稱 (SPN)。
5. 連接線會執行與內部部署的 Kerberos 限制委派 (KCD) 交涉 AD，模擬以取得應用程式的 Kerberos 權杖使用者。
6. Active Directory 會傳送至連接器應用程式的 Kerberos token。
7. 連接器會原始邀請傳送至應用程式伺服器時，使用 AD 接收到的 Kerberos token。
8. 應用程式傳送至連接器會回到應用程式 Proxy 服務的回應，最後的使用者。

### <a name="prerequisites"></a>必要條件

您開始使用 SSO 版應用程式 Proxy 之前，請確定您的環境時使用下列設定值和設定︰

- 您的應用程式，例如 SharePoint online 使用整合式 Windows 驗證設定。 如需詳細資訊請參閱[啟用支援 Kerberos 驗證](https://technet.microsoft.com/library/dd759186.aspx)，或 sharepoint 請參閱[規劃 SharePoint 2013 中的 Kerberos 驗證](https://technet.microsoft.com/library/ee806870.aspx)。

- 所有應用程式的服務主體名稱。

- 執行連接器伺服器及執行應用程式的伺服器是加入網域，相同的網域或信任的網域的一部分。 如需有關網域聯結的詳細資訊，請參閱[加入網域的電腦](https://technet.microsoft.com/library/dd807102.aspx)。

- 執行連接器伺服器擁有的存取權的使用者讀取 TokenGroupsGlobalAndUniversal。 這是可能會影響安全性強化環境的預設設定。 取得更多協助[KB2009157](https://support.microsoft.com/en-us/kb/2009157)中。

### <a name="active-directory-configuration"></a>作用中的目錄設定

Active Directory 設定，根據您的應用程式 Proxy 連接器和發行的伺服器是否位於相同的網域是否而異。

#### <a name="connector-and-published-server-in-the-same-domain"></a>連接器和發行的伺服器，在相同的網域

1. 在 Active Directory 中移至 [**工具** > **使用者和電腦**。
2. 選取 [執行連接器伺服器]。
3. 以滑鼠右鍵按一下，然後選取 [**內容** > **委派**。
4. 選取 [**信任的委派的指定服務這台電腦**，然後新增**到此帳戶可以提供委派的憑證的服務**] 下的 [SPN 身分識別的應用程式伺服器] 的值。
5. 這可讓應用程式 Proxy 連接器，以在 AD 模擬使用者針對清單中所定義的應用程式。

![連接器 SVR 屬性視窗的螢幕擷取畫面](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### <a name="connector-and-published-server-in-different-domains"></a>連接器並在不同的網域已發行的伺服器

1. 使用 KCD 各網域的必要條件的清單，請參閱[各網域的 [Kerberos 限制委派](https://technet.microsoft.com/library/hh831477.aspx)。
2. 在 Windows 2012 R2 使用`principalsallowedtodelegateto`啟用應用程式 Proxy 委派的連接器伺服器，其中已發行的伺服器是連接器伺服器上的屬性`sharepointserviceaccount`和委派伺服器`connectormachineaccount`。

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE] `sharepointserviceaccount`可以是預存程序電腦帳戶或服務帳戶執行的預存程序應用程式集區。


### <a name="azure-classic-portal-configuration"></a>Azure 傳統的入口網站設定

1. 發佈您的應用程式，根據[發佈應用程式的應用程式 Proxy](active-directory-application-proxy-publish.md)所述的指示。 請務必選取**Azure Active Directory**做為**預先驗證方法**。
2. 應用程式清單中出現您的應用程式之後，請選取它，然後按一下 [**設定**]。
3. 在 [**屬性**] 設定**內部驗證方法****整合 Windows**驗證。  
  ![進階應用程式設定](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  
4. 輸入應用程式伺服器**內部應用程式 SPN** 。 在此範例中，我們已發佈的應用程式的 SPN 是 http/lob.contoso.com。  

>[AZURE.IMPORTANT] 如果您的內部部署 UPN 和 Azure Active Directory 中的 UPN 並不相同，您必須設定才能讓搭配使用的預先驗證的[委派的登入身分識別](#delegated-login-identity)。

|  |  |
| --- | --- |
| 內部驗證方法 | 如果您使用的預先驗證的 Azure AD，您可以設定的內部驗證方法，讓使用者能受益此應用程式的 [單一登入 (SSO)。 <br><br> 如果您的應用程式使用 IWA，且您可以設定 Kerberos 限制委派 (KCD) 若要啟用 SSO 此應用程式，請選取 [**整合式 Windows 驗證**(IWA)]。 使用 IWA 應用程式，必須使用 KCD 設定，否則應用程式 Proxy 無法發佈這些應用程式。 <br><br> 如果您的應用程式不會使用 IWA，請選取 [**無**]。 |
| 應用程式內部 SPN | 這是在內部部署 Azure AD 中設定的內部應用程式服務主要名稱 (SPN)。 SPN 應用程式 Proxy 連接器用於擷取 Kerberos 權杖使用 KCD 應用程式。 |


## <a name="sso-for-non-windows-apps"></a>非 Windows 應用程式 SSO
Azure AD 驗證使用者在雲端中的時，就會啟動 Azure AD 應用程式 Proxy Kerberos 委派流程。 一旦要求送達內部部署，Azure AD 應用程式 Proxy 連接器中發生的問題代表使用者 Kerberos 票證與本機 Active Directory 互動。 此程序被指為 Kerberos 限制委派 (KCD)。 在下一個階段中，會傳送要求給此 Kerberos 票證後端應用程式。 有定義如何傳送這類要求的通訊協定的數字。 大部分的非 Windows 伺服器預期會交涉/SPNego Azure AD 應用程式 Proxy 現在支援的。

![非 Windows SSO 圖表](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

### <a name="delegated-login-identity"></a>委派的登入身分識別
委派的登入身分識別，可協助您處理兩個不同的登入案例︰

- 通常取得在表單中的使用者名稱或 SAM 帳戶名稱，不電子郵件地址的使用者身分識別的非 Windows 應用程式(username@domain)。
- 替代的登入設定位置中 Azure AD UPN 和您的內部部署 Active Directory 中 UPN 不同。

使用應用程式 Proxy]，您可以選取要使用來取得 Kerberos 票證的身分識別。 此設定為每個應用程式。 部分這些選項適用於系統不接受電子郵件地址的格式，其他人專為替代的登入。

![委派的登入身分識別參數螢幕擷取畫面](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

如果使用委派的登入身分識別，則可能無法網域或貴組織中的唯一值。 您可以發佈按兩次使用兩個不同的連接器群組這些應用程式，以避免此問題。 由於每個應用程式會有不同的使用者對象，您可以加入其連接器，以不同的網域。


## <a name="working-with-sso-when-on-premises-and-cloud-identities-are-not-identical"></a>使用 SSO 時內部部署和雲端身分識別並不相同
除非加以設定，否則應用程式 Proxy 假設擁有完全相同的識別雲端和內部部署使用者。 您可以設定，每個應用程式，執行單一登入時應該使用哪一個身分識別。  

此功能可讓許多有不同內部部署和雲端身分識別，而不需輸入不同的使用者名稱和密碼的使用者至內部部署的應用程式有 SSO 雲端的組織。 包括組織的︰

- 在內部有多個網域(joe@us.contoso.com,joe@eu.contoso.com)和雲端中的單一網域(joe@contoso.com)。

- 在內部有非可路由的網域名稱(joe@contoso.usa)法律雲端中的其中一種。

- 不使用內部網域名稱 （王）

- 使用不同的別名內部部署和雲端中。 例如︰ joe-johns@contoso.com與。joej@contoso.com  

它也有助於不接受地址的電子郵件地址，這是常見的案例，非 Windows 後端伺服器的表單中的應用程式。


### <a name="setting-sso-for-different-cloud-and-on-prem-identities"></a>設定不同的雲端和內部部署識別 SSO

1. 設定 Azure AD Connect，在您的主要的身分識別將電子郵件地址 （郵件）。 藉由變更同步處理設定] 中的 [**使用者主要名稱**] 欄位，這種做法是自訂程序的一部分。 請注意，這些設定也會決定如何使用者登入 Office365，Windows10 裝置及其他應用程式的 Azure AD 作為其身分識別存放區。  
  ![識別使用者螢幕擷取畫面的使用者主要名稱] 下拉式清單](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. 在應用程式設定的設定，您想要修改的應用程式，選取要使用的**委派的登入身分識別**︰
  - 使用者主要名稱︰joe@contoso.com  
  - 替代的使用者主要名稱︰joed@contoso.local  
  - 使用者名稱的使用者主要名稱的組件︰ 王  
  - 替代的使用者主要名稱的使用者名稱部分︰ joed  
  - 內部部署 SAM 帳戶名稱︰ 相依內部部署網域控制站設定

  ![委派的登入身分識別下拉功能表螢幕擷取畫面](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)  

### <a name="troubleshooting-sso-for-different-identities"></a>疑難排解 SSO 針對不同的身分識別
如果 SSO 程序中有錯誤，會出現在連接器電腦的事件記錄檔[疑難排解](active-directory-application-proxy-troubleshoot.md)所述。
不過，在某些情況下，將會順利傳送要求給後端應用程式時此應用程式將回覆中各種其他 HTTP 回應。 疑難排解這種情況下應該先檢查應用程式 Proxy 工作階段的事件記錄檔連接器電腦上的事件編號 24029。 委派所用的使用者身分識別會出現在 「 使用者 」 欄位中，在 [事件詳細資料。 若要開啟記錄工作階段，請選取**顯示分析及偵錯記錄**在事件檢視器中檢視] 功能表。


## <a name="see-also"></a>另請參閱

- [發佈應用程式的應用程式 Proxy](active-directory-application-proxy-publish.md)
- [您正與應用程式 Proxy 的問題進行疑難排解](active-directory-application-proxy-troubleshoot.md)
- [使用 [宣告應用程式](active-directory-application-proxy-claims-aware-apps.md)
- [啟用條件的存取](active-directory-application-proxy-conditional-access.md)

最新消息和更新，請參閱[應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg
