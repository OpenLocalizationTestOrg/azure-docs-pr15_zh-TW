<properties
    pageTitle="疑難排解應用程式 Proxy |Microsoft Azure"
    description="說明如何疑難排解 Azure AD 應用程式 Proxy 中的錯誤。"
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
    ms.date="08/19/2016"
    ms.author="kgremban"/>



# <a name="troubleshoot-application-proxy"></a>疑難排解應用程式 Proxy

如果在存取已發佈的應用程式或發佈應用程式，就會發生錯誤，請檢查下列選項，請參閱 Microsoft Azure AD 應用程式 Proxy 如果正確運作︰

- 開啟 Windows 服務主控台，並確認已啟用**Microsoft AAD 應用程式 Proxy 連接器**服務和執行。 您也可以查看 [應用程式 Proxy 服務屬性] 頁面中，如下圖所示︰  
  ![Microsoft AAD 應用程式 Proxy 連接器內容] 視窗的螢幕擷取畫面](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)

- 開啟事件檢視器]，然後尋找**應用程式及服務記錄檔**中的應用程式 Proxy 連接器事件 > **Microsoft** > **AadApplicationProxy** > **連接器** > **系統**。
- 如有需要更詳細的記錄，可開啟分析偵錯記錄，並開啟應用程式 Proxy 連接器階段登入]。

## <a name="the-page-is-not-rendered-correctly"></a>不正確呈現的頁面

如果您無法聽到特定的錯誤訊息，您可能仍有問題應用程式呈現，或不正確運作。 如果您已發佈的文章路徑，但應用程式必須要有該路徑外的內容，會發生這項目。

例如，如果發佈路徑 https://yourapp/app，但是應用程式中 https://yourapp/media 呼叫圖像，他們無法呈現。 請確定您發佈的最高的層級路徑，您必須包含所有的相關內容應用程式。 在此範例中，就會 http://yourapp/。

如果您變更您的路徑，包括參考的內容，但仍需要使用者開啟路徑中更深入的連結，請參閱的部落格文章[設定 Azure AD 存取面板和 Office 365 應用程式啟動器中的應用程式 Proxy 應用程式的 [右] 連結](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/)。

## <a name="general-errors"></a>一般錯誤

錯誤 | 描述 | 解決方法
--- | --- | ---
無法存取此公司的應用程式。 您未獲授權存取此應用程式。 授權失敗。 請務必將存取權的使用者指派給此應用程式。 | 您可能不指定此應用程式的使用者。 | 移至 [**應用程式**] 索引標籤，然後**使用者和群組**] 下，將這個使用者或使用者群組指派給此應用程式。
無法存取此公司的應用程式。 您未獲授權存取此應用程式。 授權失敗。 確認使用者擁有授權的 Azure Active Directory 進階版或基本。 | 嘗試存取如果未明確指定進階版或基本授權的訂閱者的系統管理員，您發佈的應用程式時，您的使用者可能會收到此錯誤。 | 前往 [訂閱者的 Active Directory**授權**] 索引標籤，並確定這個使用者或使用者群組，已指派的進階版或基本的授權。


## <a name="connector-troubleshooting"></a>連接器疑難排解
如果連接器精靈安裝期間，無法註冊，有兩種方式檢視失敗的原因。 請查看在 [**應用程式與服務 Logs\Microsoft\AadApplicationProxy\Connector\Admin**] 事件記錄檔，或執行下列 Windows PowerShell 命令。

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

| 錯誤 | 描述 | 解決方法 |
| --- | --- | --- |
| 連接器註冊失敗︰ 確認您啟用 Azure 管理入口網站中的應用程式 Proxy 和您輸入您的 Active Directory 使用者名稱和密碼正確。 錯誤: 「 一或多個發生錯誤。 」 | 您不需執行登入 Azure AD 關閉 [註冊] 視窗。 | 再次執行 [連接器] 精靈，並登錄連接器。 |
| 連接器註冊失敗︰ 確認您啟用 Azure 管理入口網站中的應用程式 Proxy 和您輸入您的 Active Directory 使用者名稱和密碼正確。 錯誤: 「 AADSTS50001︰ 資源`https://proxy.cloudwebappproxy.net /registerapp`已停用。 」 | 應用程式 Proxy 會停用。  | 請確定您 Azure 傳統入口網站中啟用應用程式 Proxy 之前，先註冊連接器。 如需有關如何啟用應用程式 Proxy 的詳細資訊，請參閱[啟用應用程式 Proxy 服務](active-directory-application-proxy-enable.md)。 |
| 連接器註冊失敗︰ 確認您啟用 Azure 管理入口網站中的應用程式 Proxy 和您輸入您的 Active Directory 使用者名稱和密碼正確。 錯誤: 「 一或多個發生錯誤。 」 | 如果 [註冊] 視窗中開啟，並立即關閉而不允許您登入，您可能會收到此錯誤。 在系統網路錯誤時，就會發生此錯誤。 | 請確定也可從瀏覽器連線到公用網站的連接埠是開啟指定[的應用程式 Proxy 的先決條件](active-directory-application-proxy-enable.md)。 |
| 連接器註冊失敗︰ 請確定電腦已連線至網際網路。 錯誤: 「 不發生在接聽任何端點`https://connector.msappproxy.net :9090/register/RegisterConnector`的接受訊息。 這通常被因為不正確的地址或 SOAP 動作。 如果有的話，如需詳細資訊，請參閱狀況，。 」 | 如果您使用您的 Azure AD 使用者名稱和密碼登入，但是再收到此錯誤，可能會遭到封鎖所有的連接埠 8081 上方。 | 請確定開啟必要的連接埠。 如需詳細資訊，請參閱[應用程式 Proxy 的先決條件](active-directory-application-proxy-enable.md)。 |
| 清除錯誤以呈現 [註冊] 視窗。 無法繼續 – 只關閉視窗。 | 您輸入錯誤的使用者名稱或密碼。 | 再試一次。 |
| 連接器註冊失敗︰ 確認您啟用 Azure 管理入口網站中的應用程式 Proxy 和您輸入您的 Active Directory 使用者名稱和密碼正確。 錯誤: 「 AADSTS50059︰ 沒有任何租用戶識別的資訊集中要求，或任何提供的認證以隱含和搜尋功能，依據服務原則 URI 失敗。 | 您嘗試使用 Microsoft 帳戶並不屬於您嘗試存取目錄的組織識別碼的網域登入。 | 確保管理員是租用戶網域相同的網域名稱，例如，contoso.com Azure AD 網域時，管理員應該admin@contoso.com。 |
| 無法擷取目前執行原則執行 PowerShell 指令碼。 | 如果連接器安裝失敗，請檢查確認 PowerShell 執行原則不會停用。 | 開啟 [群組原則編輯器。 移至 [**電腦設定** > **系統管理範本** > **Windows 元件** > **Windows PowerShell**按兩下**開啟執行指令碼**。 這可以設定**未設定**或**啟用**。 如果設定為 [**已啟用**，請確定的選項] 底下，執行原則設定為 [**允許本機及遠端簽署指令碼**或允許**所有指令碼**。 |
| 連接器無法下載設定。 | 連接器的用戶端憑證驗證所用過期。 這也可能是如果您有安裝在 proxy 後方的連接器。 在此情況下，不能存取網際網路及其不能提供給遠端使用者的應用程式。 | 更新以手動方式使用信任`Register-AppProxyConnector`在 Windows PowerShell 指令程式。 如果您的連接器是在 proxy 後方，則必須授與存取網際網路連接器帳戶 」 網路服務 」 和 「 本機系統 」。 這可以完成授與他們 access Proxy 或略過 proxy 設定。 |
| 連接器註冊失敗︰ 請確定您是全域管理員的您註冊連接器的 Active Directory。 錯誤: 「 註冊拒絕要求。 」 | 您嘗試登入的別名，則無法在此網域的管理員。 您的連接器永遠安裝目錄擁有使用者的網域。 | 請確定您的管理員您嘗試登入，為具有 Azure AD 租用戶的全域管理員權限。|


## <a name="kerberos-errors"></a>Kerberos 錯誤

| 錯誤 | 描述 | 解決方法 |
| --- | --- | --- |
| 無法擷取目前執行原則執行 PowerShell 指令碼。 | 如果連接器安裝失敗，請檢查確認 PowerShell 執行原則不會停用。 | 開啟 [群組原則編輯器。 移至 [**電腦設定** > **系統管理範本** > **Windows 元件** > **Windows PowerShell**按兩下**開啟執行指令碼**。 這可以設定**未設定**或**啟用**。 如果設定為 [**已啟用**，請確定的選項] 底下，執行原則設定為 [**允許本機及遠端簽署指令碼**或允許**所有指令碼**。 |
| 12008-azure AD 超過最大次數允許 Kerberos 驗證後端伺服器。 | 此事件可能表示 Azure AD 之間的設定不正確和後端應用程式伺服器或在兩部電腦上的時間和日期設定問題。 | 後端伺服器拒絕 Azure AD 所建立的 Kerberos 票證。 請確認該 Azure AD 和後端應用程式伺服器設定正確。 請確定 Azure AD 的時間和日期設定和同步處理應用程式後端伺服器。 |
| 13016-azure AD 無法擷取代表使用者 Kerberos 票證，因為沒有 UPN 邊緣權杖或存取 cookie。 | 有 STS 組態問題。 | 在 STS 修正 UPN 宣告設定。 |
| 13019-azure AD 無法擷取代表使用者 Kerberos 票證一般 API 錯誤如下。 | 此事件可能表示 Azure AD 之間的設定不正確和網域控制站伺服器或在兩部電腦上的時間和日期設定問題。 | 網域控制站拒絕 Azure AD 所建立的 Kerberos 票證。 請確認該 Azure AD 後端應用程式伺服器，且設定正確，尤其是 SPN 設定。 請確定 Azure AD 加入至相同的網域，以確保網域控制站建立與 Azure AD 信任的網域控制站的網域。 請確定 Azure AD 的時間和日期設定和網域控制站會同步處理。 |
| 13020-azure AD 無法擷取代表使用者 Kerberos 票證，因為後端伺服器 SPN 未定義。 | 此事件可能表示 Azure AD 之間的設定不正確和網域控制站伺服器或在兩部電腦上的時間和日期設定問題。 | 網域控制站拒絕 Azure AD 所建立的 Kerberos 票證。 請確認該 Azure AD 後端應用程式伺服器，且設定正確，尤其是 SPN 設定。 請確定 Azure AD 加入至相同的網域，以確保網域控制站建立 Azure AD 與信任的網域控制站的網域。 請確定 Azure AD 的時間和日期設定和網域控制站會同步處理。 |
| 13022-azure AD 無法使用者進行驗證，因為後端伺服器回應 Kerberos 驗證嘗試使用 HTTP 401 錯誤。 | 此事件可能表示 Azure AD 之間的設定不正確和後端應用程式伺服器或在兩部電腦上的時間和日期設定問題。 | 後端伺服器拒絕 Azure AD 所建立的 Kerberos 票證。 請確認該 Azure AD 和後端應用程式伺服器設定正確。 請確定 Azure AD 的時間和日期設定和同步處理應用程式後端伺服器。 |
| 網站無法顯示的頁面。 | 嘗試存取您發佈的應用程式時 IWA 應用程式的應用程式時，您的使用者可能會收到此錯誤。 這個應用程式定義的 SPN 可能不正確。 | IWA 應用程式︰ 請確認 SPN 此應用程式的設定正確無誤。 |
| 網站無法顯示的頁面。 | 嘗試存取您發佈的應用程式時 OWA 應用程式的應用程式時，您的使用者可能會收到此錯誤。 這可能是由下列其中一項︰ <br> -這個應用程式已定義之的 SPN 不正確。 <br> 的嘗試存取應用程式使用者使用 Microsoft 帳戶，而不是正確的公司帳戶登入，或使用者是來賓使用者。 <br> -嘗試存取應用程式使用者未正確定義在內部部署側此應用程式。 | 減輕適當的步驟︰ <br> -請確認 SPN 此應用程式的設定正確無誤。 <br> -確定使用者登入使用其公司帳戶符合該網域的已發佈的應用程式中。 Microsoft 帳戶使用者和來賓無法存取 IWA 應用程式]。 <br> -請確定此使用者定義後端應用程式在內部部署電腦上有適當的權限。 |
| 無法存取此公司的應用程式。 您未獲授權存取此應用程式。 授權失敗。 請務必將存取權的使用者指派給此應用程式。 | 嘗試存取如果，而非其公司帳戶使用 Microsoft 帳戶登入您發佈的應用程式時，您的使用者可能會收到此錯誤。 來賓使用者也可能會收到此錯誤。 | Microsoft 帳戶使用者和來賓無法存取 IWA 應用程式]。 請確定使用者登入使用其公司帳戶符合發佈的應用程式的網域。 |
| 無法立即存取此公司的應用程式。 請稍後再次嘗試...連接器逾時。 | 嘗試存取如果內部部署端此應用程式未正確定義您發佈的應用程式時，您的使用者可能會收到此錯誤。 | 請確定您的使用者會有適當的權限，此的後端應用程式，在內部部署電腦上的定義。 |


## <a name="see-also"></a>另請參閱

- [啟用 Azure Active Directory 的應用程式的 Proxy](active-directory-application-proxy-enable.md)
- [發佈應用程式的應用程式 Proxy](active-directory-application-proxy-publish.md)
- [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
- [啟用條件的存取](active-directory-application-proxy-conditional-access.md)

最新消息和更新，請參閱[應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
