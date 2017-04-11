<properties
    pageTitle="Azure Active Directory 身分識別保護 playbook |Microsoft Azure"
    description="瞭解如何 Azure AD 身分識別保護可讓您利用識別或裝置與身分識別或先前可疑或洩漏已知的裝置的安全性攻擊的能力。"
    services="active-directory"
    keywords="azure active directory 身分識別保護]，[管理應用程式、 安全性、 風險、 風險層級、 弱點、 安全性原則的雲端應用程式探索"
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
    ms.date="08/22/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection-playbook"></a>Azure Active Directory 身分識別保護 playbook 

此 playbook 可協助您︰

- 填入模擬風險事件和弱點身分識別保護環境中的資料
- 風險條件存取原則設定並測試這些原則的影響


## <a name="simulating-risk-events"></a>模擬風險事件

本節提供您的步驟，模擬下列風險事件類型︰

- 簽署增益集從匿名 IP 位址 （簡單）
- 從熟悉的位置 （中等） 登增益集。
- 典型的位置 （不易） 理解旅遊

其他風險事件無法模擬以安全的方式。


### <a name="sign-ins-from-anonymous-ip-addresses"></a>從匿名 IP 位址登增益集。

將這個風險事件類型識別有從已被識別為匿名 proxy IP 位址的 IP 位址順利登入的使用者。 想要隱藏其裝置的 IP 位址與可能會用於惡意的人員會使用這些 proxy。

**模擬登入，從匿名 IP，請執行下列步驟**︰

1.  下載[Tor 瀏覽器](https://www.torproject.org/projects/torbrowser.html.en)。
2.  使用 Tor 瀏覽器，瀏覽至[https://myapps.microsoft.com](https://myapps.microsoft.com)。   
3.  輸入您要顯示**登增益集匿名 IP 位址從**報表中的帳戶認證。

登入會顯示在身分識別保護儀表板上 5 分鐘內。 


###<a name="sign-ins-from-unfamiliar-locations"></a>從熟悉的位置登增益集。

不熟悉位置風險是根據過去的登入位置的即時登入評估機制 (IP、 緯度 / 經度和 ASN) 來決定新 / 不熟悉的位置。 系統會將儲存先前 IPs，緯度 / 經度和 ASNs 的使用者並將這些熟悉的位置。 登入位置會被視為不熟悉，如果登入位置不符合任何現有熟悉的位置。

Azure Active Directory 身分識別保護︰  

 - 具有初始學習一段期間它不會標示為不熟悉位置的任何新位置的 14 天。
 - 會忽略登增益集從熟悉的裝置與現有熟悉位置靠近地理位置的位置。

若要模擬不熟悉的位置，您必須從位置和帳戶沒有登入從之前的裝置登入。 


**若要模擬登入，從熟悉的位置，請執行下列步驟**︰

1.  選擇 [具有至少 14 天登入歷程記錄的帳戶]。 

2.  執行︰
    
    。 時使用 VPN，瀏覽至[https://myapps.microsoft.com](https://myapps.microsoft.com) ，然後輸入您想要模擬的風險事件的帳戶認證。

    b。 要求登入使用的帳戶認證 （不建議使用） 關聯在不同的位置。

登入會顯示在身分識別保護儀表板上 5 分鐘內。
 
### <a name="impossible-travel-to-atypical-location"></a>無法執行旅遊典型的位置
模擬理解旅遊條件是因為演算法會使用電腦學習快速清除 false-正數，例如理解旅遊從熟悉的裝置或從 Vpn 所使用的目錄中的其他使用者的登增益集。 此外，演算法需要 3 至 14 天的登入記錄使用者作業產生風險事件。

**若要模擬理解旅遊典型的位置，請執行下列步驟**︰

1.  使用標準瀏覽器，瀏覽至[https://myapps.microsoft.com](https://myapps.microsoft.com)。  

2.  輸入您想要產生的理解旅遊風險事件的帳戶認證。

3.  變更您的使用者代理程式。 您可以變更 Internet Explorer 中的使用者代理程式開發人員工具]，或變更您的使用者代理程式，在 Firefox 或使用使用者代理程式切換器附加元件的 Chrome。

4.  變更您的 IP 位址。 您可以藉由使用 VPN，Tor 附加元件，或在不同的資料中心的 Azure 中的新電腦上旋轉來變更您的 IP 位址。

5.  登入[https://myapps.microsoft.com](https://myapps.microsoft.com)為之前使用相同的認證與上一個登入後數分鐘內。

登入會顯示在身分識別保護儀表板 2-4 小時內。<br>
複雜的電腦，學習相關的模型，因為有的機會不取得挑選設定。<br> 您可能會想要複製多個 Azure AD 帳戶的步驟。


## <a name="simulating-vulnerabilities"></a>模擬弱點 
弱點是在錯誤的動作項目就可以利用 Azure AD 環境中的弱點。 目前的弱點的 3 種呈現中運用 Azure AD 的其他功能的 Azure AD 身分識別保護。 這些弱點會自動顯示身分識別保護儀表板上後設定這些功能。

-   Azure AD[多重因素驗證嗎？](../multi-factor-authentication/multi-factor-authentication.md)
-   Azure AD[雲端應用程式探索](active-directory-cloudappdiscovery-whatis.md)。
-   Azure AD[有權限的身分識別管理](active-directory-privileged-identity-management-configure.md)。 



##<a name="user-compromise-risk"></a>使用者洩漏的風險

**若要測試使用者洩漏的風險，請執行下列步驟**︰

1.  登入以全域管理員認證的[https://portal.azure.com](https://portal.azure.com)租用戶。

2.  瀏覽至**身分識別保護**。 

3.  在主**Azure AD 身分識別保護**刀中，按一下 [**設定**]。 

4.  在**入口網站設定**防禦，以在**安全性規則**] 底下按一下 [**使用者侵入的風險**。 

5.  在**登入風險**刀，關閉**啟用規則**，然後再按一下 [**儲存**設定。

6.  指定的使用者帳戶，模擬不熟悉位置] 或 [匿名 IP 風險事件。 這會將該使用者的使用者風險層級提升為**中**。

7.  請稍候幾分鐘，，然後確認您的使用者的使用者層級****。

8.  移至**入口網站設定**刀。

9.  在**使用者危害的風險**防禦，以在**啟用規則**，請選取 [**上**]。 

10. 選取下列選項之一︰

    。 若要封鎖，請選取**媒體**下**區塊登入**。

    b。 若要變更安全密碼，選取 [**媒體**下**需要多重因素驗證**。

13. 按一下 [**儲存**]。

14. 您現在可以測試風險型條件存取登入使用者使用的風險提高權限層級。 如果使用者風險中，根據您的原則設定，您登入 [封鎖或您必須變更您的密碼。 
<br><br>
![Playbook](./media/active-directory-identityprotection-playbook/201.png "Playbook")
<br>

 
##<a name="sign-in-risk"></a>登入風險

 
**若要測試登入風險，請執行下列步驟︰**

1.  登入以全域管理員認證的[https://portal.azure.com](https://portal.azure.com)租用戶。

2.  瀏覽至**身分識別保護**。

3.  在主**Azure AD 身分識別保護**刀中，按一下 [**設定**]。 

4.  在**入口網站設定**刀的 [**安全性規則**] 下，按一下 [**登入 [風險**]。

5.  在**登入風險**刀中，選取 [**在**底下**啟用規則**。 

7.  選取下列選項之一︰

    。 若要封鎖，請選取 [**媒體**下**區塊登入**

    b。 若要強制變更安全密碼，請選取下**需要多重因素驗證**的**媒體**。

8.  若要封鎖，請選取 [封鎖登入下的 [媒體]。

9.  若要強制執行多因素驗證，請選取下**需要多重因素驗證**的**媒體**。

10. 按一下 [**儲存**]。

11. 您現在可以藉由模擬不熟悉的位置] 或 [匿名 IP 風險事件，因為它們是兩個**中型**風險事件測試風險型條件存取。

<br>
![Playbook](./media/active-directory-identityprotection-playbook/200.png "Playbook")
<br>


## <a name="see-also"></a>另請參閱

 - [Azure Active Directory 身分識別保護](active-directory-identityprotection.md)
