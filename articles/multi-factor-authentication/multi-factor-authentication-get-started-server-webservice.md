<properties 
    pageTitle="快速入門 MFA 伺服器行動應用程式的 Web 服務"
    description="Azure 多重因素驗證應用程式提供的其他-頻驗證選項。  其允許 MFA 伺服器使用推入通知給使用者。"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="getting-started-the-mfa-server-mobile-app-web-service"></a>快速入門 MFA 伺服器行動應用程式的 Web 服務

Azure 多重因素驗證應用程式提供的其他-頻驗證選項。 使用者將自動化的電話或 SMS 登入時，而不是 Azure 多重因素驗證將推入通知 Azure 多重因素驗證應用程式使用者的智慧型手機或平板電腦上。 使用者只要點選 「 驗證 」 （或輸入 PIN 和點選 「 驗證 」） 登入應用程式中。

若要使用 Azure 多重因素驗證應用程式，需要下列條件，讓應用程式可以順利與行動應用程式 Web 服務進行通訊︰

- 請參閱 [硬體與軟體需求的硬體與軟體需求
- 您必須使用 6.0 或較高的 Azure 多重因素驗證伺服器
- 執行 Microsoft® 網際網路資訊服務 (IIS) IIS 網際網路的網頁伺服器上必須安裝行動應用程式 Web 服務 7.x 或更高。  如需有關 IIS，請參閱[IIS.NET](http://www.iis.net/)。
- 確定已安裝、 登錄且設定為 [允許 ASP.NET v4.0.30319
- 必要的角色服務包含 ASP.NET 和 IIS 6 中繼資料庫相容性
- 行動應用程式 Web 服務必須透過公用 URL
- 使用 SSL 憑證，就必須保護行動應用程式 Web 服務。
- Azure 多重因素驗證的 Web 服務 SDK 必須安裝 IIS 中 7.x 或更新版本伺服器上的 Azure 多重因素驗證伺服器
- Azure 多重因素驗證的 Web 服務 SDK 必須受到保護的 SSL 憑證。
- 行動應用程式 Web 服務必須為能透過 SSL 連線 Azure 多重因素驗證 Web 服務 sdk 的變更
- 行動應用程式 Web 服務必須能夠驗證 Azure 多重因素驗證 Web 服務 sdk 的變更使用的是稱為 「 PhoneFactor 管理員 」 的安全性群組的成員的服務帳戶認證。 此服務帳戶與群組存在於 Active Directory 中，如果網域的伺服器上執行的 Azure 多重因素驗證伺服器。 此服務帳戶與群組存在於本機 Azure 多重因素驗證伺服器如果沒有結合網域。


Azure 多重因素驗證伺服器非在伺服器上安裝使用者入口網站需要下列三個步驟︰

1. 安裝 web 服務 SDK
2. 安裝行動應用程式 web 服務
3. 在 [Azure 多重因素驗證伺服器設定行動應用程式
4. 使用者啟動 Azure 多重因素驗證應用程式

## <a name="install-the-web-service-sdk"></a>安裝 web 服務 SDK

如果 Azure 多重因素驗證的 Web 服務 SDK 不 Azure 多重因素驗證伺服器上已安裝，請移至該伺服器，並開啟 Azure 多重因素驗證伺服器。 按一下 [Web 服務 SDK] 圖示，按一下 [安裝 Web 服務 SDK... ] 按鈕，然後依照指示操作。 Web 服務 SDK 必須受到保護的 SSL 憑證。 自我簽署的憑證接受達到這個目的，但它有其匯入使用者入口網站的網頁伺服器上的本機電腦帳戶的 「 信任的根憑證授權單位 」 存放區中，使其起始 SSL 連線信任的憑證。

<center>![設定](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## <a name="install-the-mobile-app-web-service"></a>安裝行動應用程式 web 服務
在安裝之前的行動應用程式的 web 服務，請注意下列任一動作︰

- 如果網際網路的伺服器上已安裝 Azure 多重因素驗證使用者入口網站，從使用者入口網站的 web.config 檔案可以複製的使用者名稱、 密碼及 URL Web 服務 sdk 的變更。
- 還是有幫助以開啟網際網路的網頁伺服器上的網頁瀏覽器，並瀏覽至 Web 服務 SDK web.config 將輸入的 URL。 如果在瀏覽器可以順利移至 web 服務，它應該提示您輸入認證。 輸入使用者名稱和中輸入了 web.config 一樣會出現在檔案中的密碼。 請確定沒有憑證警告或錯誤會顯示。
- 如果在反向 proxy 或防火牆，坐行動應用程式 Web 服務網頁伺服器，並執行 SSL 卸載，您可以編輯行動應用程式 Web 服務 web.config 檔案，並新增下列機<appSettings>區段，讓其他行動應用程式的 Web 服務可以使用 http，而不是 https。 不過 SSL 是仍須行動應用程式的防火牆/反向 proxy。 <add key="SSL_REQUIRED" value="false"/>

### <a name="to-install-the-mobile-app-web-service"></a>若要安裝的行動應用程式的 web 服務

<ol>
<li>開啟 Azure 多重因素驗證伺服器上的 [Windows 檔案總管]，然後瀏覽至 Azure 多重因素驗證伺服器安裝所在的資料夾 （例如 C:\Program Files\Azure 多重因素驗證）。 選擇 32 位元或 64 位元版本的行動應用程式 Web 服務將會安裝在 Azure 多重因素 AuthenticationPhoneAppWebServiceSetup 安裝檔案視您的伺服器。 將安裝檔案複製到網際網路的伺服器。</li>

<li>在網際網路的網頁伺服器上，您必須以系統管理員權限，執行 [安裝] 檔案。 若要執行此動作的最簡單方法是開啟命令提示字元以系統管理員身分，然後瀏覽至安裝檔案複製其中的位置。</li>  

<li>執行多因素 AuthenticationMobileAppWebServiceSetup 安裝檔案，視需要變更網站並變更虛擬目錄為簡短的名稱，例如"PA"。 由於使用者必須輸入行動應用程式的 Web 服務 URL 的行動裝置在啟動過程中，則建議使用簡短的虛擬目錄的名稱。</li>

<li>完成之後的 Azure 多重因素 AuthenticationMobileAppWebServiceSetup 安裝，瀏覽至 C:\inetpub\wwwroot\PA （或適當的目錄，根據虛擬目錄名稱），然後編輯 web.config 檔案。</li>  

<li>找出 [WEB_SERVICE_SDK_AUTHENTICATION_USERNAME] 與 [WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD 鍵，並將值設定為 [使用者名稱和成員 PhoneFactor 管理員安全性的服務帳戶的密碼群組 （請參閱上述需求一節）。 這可能是如果已經先前安裝的為 Azure 多重因素驗證使用者入口網站的身分識別用的同一個帳戶。 請務必輸入使用者名稱和密碼之間的線條，結尾的引號 (值 = 」 「 / >)。 建議使用完整的使用者名稱 （例如︰ 網域 \ 使用者名稱或 machine\username）。</li>  

<li>找出 pfMobile 應用程式網頁 Service_pfwssdk_PfWsSdk 設定，並變更的值的 「 http://localhost:4898/PfWsSdk.asmx 」 的 Web 服務 SDK Azure 多重因素驗證 (例如 https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx) 伺服器上執行的 URL。 由於 SSL 用於此連線時，您必須先參考 Web 服務 SDK 的伺服器名稱] 和 [不使用 IP 位址，因為 SSL 憑證將已發行的伺服器名稱，然後用的 URL，必須符合憑證的名稱。 如果伺服器名稱不會從網際網路的伺服器解決 IP 位址，請主機檔案，將 Azure 多重因素驗證伺服器的名稱對應到其 IP 位址的伺服器上新增項目。 進行變更之後，請儲存 web.config 檔案。</li>  

<li>如果行動應用程式 Web 服務已安裝在 （例如預設網站） 的網站尚未繫公開簽章的憑證，在伺服器上安裝的憑證如果不是已安裝，請開啟 IIS 管理員，並將憑證繫結至網站。</li>  

<li>從任何電腦開啟網頁瀏覽器，並瀏覽至安裝行動應用程式 Web 服務的 URL (例如 https://www.publicwebsite.com/PA)。 請確定沒有憑證警告或錯誤會顯示。</li>

### <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>在 [Azure 多重因素驗證伺服器設定行動應用程式
現在已安裝的行動應用程式的 web 服務，您需要將 Azure 多重因素驗證伺服器設定為使用入口網站。

#### <a name="to-configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>若要在 Azure 多重因素驗證伺服器設定行動應用程式設定

1. 在 Azure 多重因素驗證伺服器，按一下 [使用者入口網站] 圖示。 如果使用者可以控制他們的驗證方法，在 [設定] 索引標籤的 [允許使用者選取方法，核取 Mobile 應用程式。 沒有啟用此功能，使用者都必須連絡您服務台以完成啟動的行動應用程式。
2. 核取 [允許使用者為啟動 Mobile 應用程式] 方塊。
3. 核取 [允許使用者註冊] 方塊。
4. 按一下 [行動應用程式圖示。
5. 輸入安裝 Azure 多重因素 AuthenticationMobileAppWebServiceSetup 時所建立的虛擬目錄搭配使用的 URL。 可能會提供的空間中輸入 [帳戶名稱。 此公司名稱會顯示在行動應用程式。 如果空白，會顯示在 Azure 管理入口網站中建立多重因素驗證提供者的名稱。



<center>![設定](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>
