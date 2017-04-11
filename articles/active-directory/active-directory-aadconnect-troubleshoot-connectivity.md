<properties
    pageTitle="Azure AD Connect︰ 疑難排解連線問題 |Microsoft Azure"
    description="說明如何疑難排解連線問題 Azure AD Connect。"
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="billmath"/>

# <a name="troubleshoot-connectivity-issues-with-azure-ad-connect"></a>疑難排解連線問題 Azure AD Connect
本文說明 Azure AD Connect 與 Azure AD 連線的運作方式，以及如何疑難排解連線問題。 這些問題是最可能會出現在 proxy 伺服器的環境中。

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>在安裝精靈中的連線發生問題的疑難排解
Azure AD Connect （使用 ADAL 的文件庫） 的現代化驗證使用進行驗證。 在安裝精靈和同步處理引擎適當要求 machine.config，因為這些是.NET 應用程式正確設定。

本文中，我們會示範如何 Fabrikam 連線到其 proxy 透過 Azure AD。 Proxy 伺服器名稱為 fabrikamproxy，且並使用連接埠 8080。

首先，我們需要確認[**machine.config**](active-directory-aadconnect-prerequisites.md#connectivity)已正確設定。  
![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

>[AZURE.NOTE]
在某些非 Microsoft 部落格記錄的變更應該可對 miiserver.exe.config 改為。 不過，此檔案是在每個升級偶數如果其運作方式初始在安裝期間，系統會停止處理第一個升級覆寫。 因此建議改為更新 machine.config。

Proxy 伺服器也必須開啟所需的 Url。 [正式] 清單會說明在[Office 365 Url 與 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)。

其中下, 表會是能夠在連線至 Azure AD 的絕對最低。 此清單不包含任何選用的功能，例如密碼回寫或 Azure AD 連線狀況。 它會以協助疑難排解初始設定以下說明。

URL | 連接埠 | 描述
---- | ---- | ----
mscrl.microsoft.com | HTTP/80 | 用來下載 CRL 清單。
\*。 verisign.com | HTTP/80 | 用來下載 CRL 清單。
\*。 entrust.com | HTTP/80 | 用來為 MFA 下載 CRL 清單。
\*。 windows.net | HTTPS/443 | 用來登入 Azure AD。
secure.aadcdn.microsoftonline p.com | HTTPS/443 | 用於 MFA。
\*。 microsoftonline.com | HTTPS/443 | 用來設定您的 Azure AD 目錄及匯入/匯出資料。

## <a name="errors-in-the-wizard"></a>在精靈中的錯誤
在安裝精靈使用兩個不同的安全性內容。 在**連線至 Azure AD** ] 頁面上，使用目前登入的使用者。 在 [**設定**] 頁面上將其變更為[帳戶執行同步處理引擎服務](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts)。 我們的 proxy 設定是全域到電腦，因此問題如果有問題，請將最可能會顯示已在精靈中的 [**連線至 Azure AD** ] 頁面。

這些是最常見的錯誤，您會看到安裝精靈] 中。

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>在安裝精靈設定不正確
當精靈本身無法連線 proxy，則會顯示這個錯誤。
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

- 如果您看到，請確認已正確設定[machine.config](active-directory-aadconnect-prerequisites.md#connectivity) 。
- 如果，看起來正確，請遵循[驗證的 proxy 連線](#verify-proxy-connectivity)問題是否為 「 精靈 」 以外的簡報中的步驟進行。

### <a name="the-mfa-endpoint-cannot-be-reached"></a>無法透過 MFA 端點
如果無法結束點**https://secure.aadcdn.microsoftonline-p.com** ，全域管理員已啟用的 MFA，會出現這個錯誤。  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

- 如果您看到，請確認結束點 secure.aadcdn.microsoftonline p.com 的已新增至 proxy。

### <a name="the-password-cannot-be-verified"></a>無法驗證密碼
如果在安裝精靈成功連接到 Azure AD，但無法驗證本身的密碼，您會看到︰ ![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

- 暫時密碼是密碼，而且必須變更？ 這是實際正確的密碼？ 嘗試登入 https://login.microsoftonline.com （在比 Azure AD Connect 伺服器的另一部電腦），並確認帳戶是使用。

### <a name="verify-proxy-connectivity"></a>驗證的 proxy 連線
若要驗證 Azure AD Connect 伺服器是否有網際網路 Proxy 與實際連線我們將使用一些 PowerShell 以查看 proxy 是否允許 web 要求。 在 PowerShell 提示中，執行`Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`。 （技術上第一個呼叫是 https://login.microsoftonline.com 和運作，但是其他 URI 會比較容易回應）。

PowerShell 會使用設定中 machine.config 連絡 proxy。 Winhttp/netsh 的設定不應該會影響這些 cmdlet。

如果 proxy 設定正確，您應該取得成功的狀態︰ ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

如果您收到**無法連線到遠端伺服器**PowerShell 再嘗試直接打電話不使用 proxy 或未正確設定 DNS。 請確定已正確設定**machine.config**檔案。
![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

如果 proxy 設定不正確，我們將會看到的錯誤︰ ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png)
![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

錯誤 | 錯誤文字 | 註解
---- | ---- | ---- |
403 | 禁止 | Proxy 尚未開啟要求的 URL。 再次 proxy 設定，並確定已開啟[的 Url](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) 。
407 | 需要 proxy 驗證 | Proxy 伺服器所需的登入並無所提供。 如果您的 proxy 伺服器需要驗證，請確定此設定在 machine.config。 也請確定您執行精靈以及服務帳戶的使用者使用網域的帳戶。

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Azure AD Connect 和 Azure AD 之間的通訊模式
如果您已按照這些步驟上述並仍無法連線所有現在可能會開始位於網路記錄。 本節會記錄的一般和成功連線圖樣。 它也會列出常見的紅色 herrings 這可以略過，如果您正在讀網路記錄。

- 會有 https://dc.services.visualstudio.com 來電。 不需要已成功安裝項目的 proxy 此開啟，因此可忽略這些。
- 您會看到 dns 解析會列出的實際主機到 DNS 名稱空間 nsatc.net，不是在 microsoftonline.com 其他命名空間。 不過，實際的伺服器名稱不會有任何 web 服務要求，您不需要新增這些內容至 proxy。
- 結束點 adminwebservice 和 provisioningapi （請參閱下方記錄） 是探索結束點，用來找出實際的端點，以使用，並會根據您的地區得到。

### <a name="reference-proxy-logs"></a>參照 proxy 記錄
以下是傾印實際 proxy 記錄檔和 [安裝] 精靈頁面，從拍攝地點 （已移除重複的項目至相同的端點）。 這可以使用的參考自己 proxy 和網路的記錄。 可能不同環境中實際的結束點 (特別是在*斜體*)。

**Azure ad 連線**

時間 | URL
--- | ---
1/11/2016年 8:31 | connect://login.microsoftonline.com:443
1/11/2016年 8:31 | connect://adminwebservice.microsoftonline.com:443
1/11/2016年 8:32 | 連線: / /*bba800 錨點*。 microsoftonline.com:443
1/11/2016年 8:32 | connect://login.microsoftonline.com:443
1/11/2016年 8:33 | connect://provisioningapi.microsoftonline.com:443
1/11/2016年 8:33 | 連線: / /*bwsc02 轉送*。 microsoftonline.com:443

**設定**

時間 | URL
--- | ---
1/11/2016年 8:43 | connect://login.microsoftonline.com:443
1/11/2016年 8:43 | 連線: / /*bba800 錨點*。 microsoftonline.com:443
1/11/2016年 8:43 | connect://login.microsoftonline.com:443
1/11/2016年 8:44 | connect://adminwebservice.microsoftonline.com:443
1/11/2016年 8:44 | 連線: / /*bba900 錨點*。 microsoftonline.com:443
1/11/2016年 8:44 | connect://login.microsoftonline.com:443
1/11/2016年 8:44 | connect://adminwebservice.microsoftonline.com:443
1/11/2016年 8:44 | 連線: / /*bba800 錨點*。 microsoftonline.com:443
1/11/2016年 8:44 | connect://login.microsoftonline.com:443
1/11/2016年 8:46 | connect://provisioningapi.microsoftonline.com:443
1/11/2016年 8:46 | 連線: / /*bwsc02 轉送*。 microsoftonline.com:443

**進行初始同步處理**

時間 | URL
--- | ---
1/11/2016年 8:48 | connect://login.windows.net:443
1/11/2016年 8:49 | connect://adminwebservice.microsoftonline.com:443
1/11/2016年 8:49 | 連線: / /*bba900 錨點*。 microsoftonline.com:443
1/11/2016年 8:49 | 連線: / /*bba800 錨點*。 microsoftonline.com:443

## <a name="authentication-errors"></a>驗證錯誤
本節說明可以從 ADAL （使用 Azure AD Connect 驗證庫） 和 PowerShell 傳回的錯誤。 說明錯誤應能助您在瞭解接下來的步驟。

### <a name="invalid-grant"></a>無效的授權
無效的使用者名稱或密碼。 如需詳細資訊，請參閱[無法確認密碼](#the-password-cannot-be-verified)。

### <a name="unknown-user-type"></a>無法辨識的使用者類型
Azure AD 目錄找不到或解決。 或許您嘗試登入與未經驗證的網域中的使用者名稱？

### <a name="user-realm-discovery-failed"></a>使用者領域探索失敗
網路] 或 [proxy 設定問題。 無法網路，請參閱[在安裝精靈中的疑難排解連線問題](#troubleshoot-connectivity-issues-in-the-installation-wizard)。

### <a name="user-password-expired"></a>使用者密碼過期
您的認證已過期。 變更您的密碼。

### <a name="authorizationfailure"></a>AuthorizationFailure
未知的問題。

### <a name="authentication-cancelled"></a>取消驗證
多重因素驗證 (MFA) 挑戰已取消。

### <a name="connecttomsonline"></a>ConnectToMSOnline
驗證成功，但 PowerShell 的 Azure AD 驗證問題。

### <a name="azurerolemissing"></a>AzureRoleMissing
驗證成功。 您不是全域管理員。

### <a name="privilegedidentitymanagement"></a>PrivilegedIdentityManagement
驗證成功。 有權限的身分識別管理已啟用，您目前不是全域管理員。 如需詳細資訊，請參閱[有權限的身分識別管理](active-directory-privileged-identity-management-getting-started.md)。

### <a name="companyinfounavailable"></a>CompanyInfoUnavailable
驗證成功。 無法從 Azure AD 擷取的公司資訊。

### <a name="retrievedomains"></a>RetrieveDomains
驗證成功。 無法從 Azure AD 擷取網域資訊。

## <a name="troubleshooting-steps-for-previous-releases"></a>舊版的疑難排解步驟。
開始建立數字 （發行二月 2016年） 1.1.105.0 版本已停用 [登入小幫手]。 本節，並設定不會出現必要，但保留參考。

單一-登入小幫手搭配使用，必須設定 winhttp。 這可以使用[**netsh**](active-directory-aadconnect-prerequisites.md#connectivity)。  
![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>登入小幫手設定不正確
登入小幫手無法達到 proxy 或 proxy 不允許要求時，就會顯示這個錯誤。
![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

- 如果您看到這，看看在[netsh](active-directory-aadconnect-prerequisites.md#connectivity) proxy 設定，並確認是正確的位置。
![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
- 如果，看起來正確，請遵循[驗證的 proxy 連線](#verify-proxy-connectivity)問題是否為 「 精靈 」 以外的簡報中的步驟進行。

## <a name="next-steps"></a>後續步驟
進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
