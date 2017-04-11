<properties 
    pageTitle="Azure 多重因素驗證伺服器的部署使用者入口網站"
    description="這是 Azure 多重因素驗證] 頁面的說明如何開始使用 Azure MFA 與使用者入口網站。"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="kgremban"/>

# <a name="deploying-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Azure 多重因素驗證伺服器的部署使用者入口網站

[使用者] 入口網站可讓系統管理員安裝和設定 Azure 多重因素驗證使用者入口網站。 [使用者] 入口網站是 IIS 網站可讓使用者註冊 Azure 多重因素驗證與維護自己的帳戶。 使用者可能會變更他們的電話號碼、 變更其 PIN]，或在其下一步註冊時略 Azure 多重因素驗證，在。

使用者將使用者入口網站的標準的使用者名稱和密碼登入並完成 Azure 多重因素驗證通話或完成其驗證安全性問題。 如果允許使用者註冊，使用者將會設定他們的電話號碼和 PIN 第一次登入使用者入口網站。

使用者入口網站系統管理員可能會設定，並授與權限新增使用者及更新現有的使用者。

<center>![設定](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## <a name="deploying-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>部署 Azure 多重因素驗證伺服器相同的伺服器上的 [使用者] 入口網站

下列的必要條件所需的 Azure 多重因素驗證伺服器相同的伺服器上安裝的使用者入口網站︰

- IIS 必須安裝包括 asp.net 和 IIS 6 中繼基底相容性 （IIS 7 或更高）
- 登入使用者必須具備管理員權限的電腦和網域，如果有的話。  這是因為帳戶需要權限可建立 Active Directory 安全性群組。

### <a name="to-deploy-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Azure 多重因素驗證伺服器的部署使用者入口網站

1. Azure 多重因素驗證伺服器中︰ 按一下左側功能表中的使用者入口網站圖示，按一下 [安裝使用者入口網站] 按鈕。
1. 按一下 [下一步]。
1. 按一下 [下一步]。
1. 如果電腦加入網域，並確保使用者入口網站和 Azure 多重因素驗證服務之間的通訊 Active Directory 設定不完整，就會顯示 Active Directory 步驟。 按一下 [下一步] 按鈕，自動完成這項設定。
1. 按一下 [下一步]。
1. 按一下 [下一步]。
1. 按一下 [關閉]。
1. 從任何電腦開啟網頁瀏覽器，並瀏覽至安裝使用者入口網站的 URL (例如 https://www.publicwebsite.com/MultiFactorAuth)。 請確定沒有憑證警告或錯誤會顯示。

<center>![設定](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## <a name="deploying-the-azure-multi-factor-authentication-server-user-portal-on-a-separate-server"></a>部署 Azure 多重因素驗證伺服器使用者入口網站上不同的伺服器

若要使用 Azure 多重因素驗證應用程式，需要下列條件，讓應用程式可以順利與使用者入口網站的通訊︰

請參閱硬體與軟體需求的硬體與軟體需求︰

- 您必須使用 6.0 或較高的 Azure 多重因素驗證伺服器。
- 執行 Microsoft® 網際網路資訊服務 (IIS) 網際網路的網頁伺服器上必須安裝使用者入口網站 6.x，IIS 7.x 或更高。
- 使用 IIS 時 6.x，確定已安裝、 登錄且設定為 [允許 ASP.NET v2.0.50727。
- 使用 IIS 時，所需的角色服務 7.x 或更高包含 ASP.NET 和 IIS 6 中繼資料庫相容性。
- 使用 SSL 憑證安全使用者入口網站。
- Azure 多重因素驗證的 Web 服務 SDK 必須安裝 IIS 中 6.x，IIS 7.x 或更新版本伺服器上已安裝 Azure 多重因素驗證伺服器上。
- Azure 多重因素驗證的 Web 服務 SDK 必須受到保護的 SSL 憑證。
- 您必須能夠透過 SSL 連線至 Azure 多重因素驗證的 Web 服務 SDK 使用者入口網站。
- 使用者入口網站必須能夠驗證 Azure 多重因素驗證 Web 服務 sdk 的變更使用的是稱為 「 PhoneFactor 管理員 」 的安全性群組的成員的服務帳戶認證。 此服務帳戶與群組存在於 Active Directory 中，如果網域的伺服器上執行的 Azure 多重因素驗證伺服器。 此服務帳戶與群組存在於本機 Azure 多重因素驗證伺服器如果沒有結合網域。

Azure 多重因素驗證伺服器非在伺服器上安裝使用者入口網站需要下列三個步驟︰

1. 安裝 web 服務 SDK
2. 安裝使用者入口網站
3. 在 [Azure 多重因素驗證伺服器設定使用者入口網站設定


### <a name="install-the-web-service-sdk"></a>安裝 web 服務 SDK

如果 Azure 多重因素驗證的 Web 服務 SDK 不 Azure 多重因素驗證伺服器上已安裝，請移至該伺服器，並開啟 Azure 多重因素驗證伺服器。 按一下 [Web 服務 SDK] 圖示，按一下 [安裝 Web 服務 SDK... ] 按鈕，然後依照指示操作。 Web 服務 SDK 必須受到保護的 SSL 憑證。 自我簽署的憑證接受達到這個目的，但它有其匯入使用者入口網站的網頁伺服器上的本機電腦帳戶的 「 信任的根憑證授權單位 」 存放區中，使其起始 SSL 連線信任的憑證。

<center>![設定](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### <a name="install-the-user-portal"></a>安裝使用者入口網站

安裝之前使用者入口網站上不同的伺服器，請注意下列動作︰

- 還是有幫助以開啟網際網路的網頁伺服器上的網頁瀏覽器，並瀏覽至 Web 服務 SDK web.config 將輸入的 URL。 如果在瀏覽器可以順利移至 web 服務，它應該提示您輸入認證。 輸入使用者名稱和中輸入了 web.config 一樣會出現在檔案中的密碼。 請確定沒有憑證警告或錯誤會顯示。
- 如果在反向 proxy 或防火牆，坐使用者入口網站的網頁伺服器，並執行 SSL 卸載，您可以編輯使用者入口網站 web.config 檔案，並新增下列機<appSettings>區段，讓其他使用者入口網站可使用 http，而不是 https。 <add key="SSL_REQUIRED" value="false"/>

#### <a name="to-install-the-user-portal"></a>若要安裝使用者入口網站

1. Azure 多重因素驗證伺服器伺服器上開啟 Windows 檔案總管]，然後瀏覽至 Azure 多重因素驗證伺服器安裝所在的資料夾 （例如 C:\Program Files\Multi 雙因素驗證伺服器）。 選擇 32 位元或 64 位元版本的使用者入口網站將會安裝在 MultiFactorAuthenticationUserPortalSetup 安裝檔案視您的伺服器。 將安裝檔案複製到網際網路的伺服器。
2. 在網際網路的網頁伺服器上，您必須以系統管理員權限，執行 [安裝] 檔案。 若要執行此動作的最簡單方法是開啟命令提示字元以系統管理員身分，然後瀏覽至安裝檔案複製其中的位置。
3. 執行 MultiFactorAuthenticationUserPortalSetup64 安裝檔案，視需要變更的網站和虛擬目錄名稱。
4. 完成之後安裝的使用者入口網站，瀏覽至 C:\inetpub\wwwroot\MultiFactorAuth （或適當的目錄，根據虛擬目錄名稱） 和編輯 web.config 檔案。
5. 找出 USE_WEB_SERVICE_SDK 鍵並從 false 的值變更為 true。 找出 [WEB_SERVICE_SDK_AUTHENTICATION_USERNAME] 與 [WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD 鍵，並將值設定為 [使用者名稱和成員 PhoneFactor 管理員安全性的服務帳戶的密碼群組 （請參閱上述需求一節）。 請務必輸入使用者名稱和密碼之間的線條，結尾的引號 (值 = 」 「 / >)。 建議使用完整的使用者名稱 （例如︰ 網域 \ 使用者名稱或 machine\username）
6. 找出 pfup_pfwssdk_PfWsSdk 設定，並變更的值的 「 http://localhost:4898/PfWsSdk.asmx 」 的 Web 服務 SDK Azure 多重因素驗證 (例如 https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx) 伺服器上執行的 URL。 由於 SSL 用於此連線時，您必須先參考 Web 服務 SDK 的伺服器名稱] 和 [不使用 IP 位址，因為 SSL 憑證將已發行的伺服器名稱，然後用的 URL，必須符合憑證的名稱。 如果伺服器名稱不會從網際網路的伺服器解決 IP 位址，請主機檔案，將 Azure 多重因素驗證伺服器的名稱對應到其 IP 位址的伺服器上新增項目。 進行變更之後，請儲存 web.config 檔案。
7. 如果已安裝在 （例如預設網站） 的使用者入口網站的網站尚未繫公開簽章的憑證，在伺服器上安裝的憑證如果不是已安裝，請開啟 IIS 管理員，並將憑證繫結至網站。
8. 從任何電腦開啟網頁瀏覽器，並瀏覽至安裝使用者入口網站的 URL (例如 https://www.publicwebsite.com/MultiFactorAuth)。 請確定沒有憑證警告或錯誤會顯示。



## <a name="configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>設定使用者入口網站中 Azure 多重因素驗證伺服器
現在已安裝入口網站，您需要將 Azure 多重因素驗證伺服器設定為使用入口網站。

Azure 多重因素驗證伺服器提供數種選項使用者入口網站。  下表提供這些選項和 explaination 用途的清單。

使用者入口網站設定|描述|
:------------- | :------------- |
使用者入口網站的 URL| 可讓您輸入的裝載的入口網站的 URL。
主要驗證| 可讓您指定的登入入口網站時使用的驗證類型。  Windows、 半徑或 LDAP 驗證。
允許使用者登入|可讓使用者在登入頁面上輸入使用者名稱和密碼，使用者入口網站。  如果未選取此項目，則會被灰色方塊。
允許使用者註冊|可讓使用者取得其提示如需詳細資訊，例如電話號碼的安裝程式] 畫面加入多重因素驗證。  提示如備份電話可讓使用者指定的第二個電話號碼。  第三方提示誓言權杖可讓使用者指定的第 3 的廠商誓言權杖。
允許使用者啟動 One-Time 略過| 這個選項可讓使用者進行一次性略過。  如果需要設定其使用者設定會影響到下一次使用者登入。  略過提示秒為使用者提供的方塊，讓他們可以變更 300 秒的預設值。  否則，一次性略過是僅適用於 300 秒。
允許使用者選取方法| 可讓使用者指定其主要的連絡方式。  這可能是電話、 文字訊息、 行動應用程式或誓言權杖。
允許使用者選取語言|  允許使用者變更語言所用的電話、 文字訊息、 行動應用程式或誓言權杖。
允許使用者啟動行動應用程式| 可讓使用者產生的伺服器使用行動應用程式啟動程序完成啟動程式碼。  您也可以設定其可以在啟動此裝置的數目。  介於 1 到 10。
使用後援安全性問題|可讓您使用的安全性問題，以避免多重因素驗證失敗。  您可以指定必須成功答案的安全性問題的數目。
允許使用者建立第三方誓言權杖之間的關聯| 可讓使用者指定的第三方誓言權杖。
使用後援誓言權杖|在事件的多重因素驗證不成功，可讓誓言權杖使用。  您也可以指定工作階段逾時以分鐘為單位。
啟用記錄功能|可讓使用者入口網站上的記錄。  記錄檔位於︰ C:\Program Files\Multi 雙因素驗證 Server\Logs。

大部分的這些設定會使用者啟用後，將使用者入口網站使用者符號。

![使用者入口網站設定](./media/multi-factor-authentication-get-started-portal/portalsettings.png)



### <a name="to-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>在 [Azure 多重因素驗證伺服器設定使用者入口網站設定




1. 在 Azure 多重因素驗證伺服器，按一下 [使用者入口網站] 圖示。 在 [設定] 索引標籤中，輸入使用者入口網站的 URL] 文字方塊中的 [使用者] 入口網站的 URL。 如果已啟用電子郵件功能匯入 Azure 多重因素驗證伺服器時，會傳送給使用者的電子郵件將會插入這個 URL。
2. 選擇您想要使用使用者入口網站中的設定。 例如，如果使用者可控制其驗證方法，確定允許使用者選取方法已核取，以及它們可以選擇的方法。
3. 按一下右上角的協助，以瞭解的任何設定顯示在 [說明] 連結。

<center>![設定](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## <a name="administrators-tab"></a>系統管理員] 索引標籤
在此索引標籤只可讓您將會具有系統管理權限的使用者。  新增管理員，您可以微調接收的權限。  如此一來，您可以確定只所需的權限授與系統管理員。  只要按一下 [新增] 按鈕，然後選取 [使用者和其權限，然後按一下 [新增]。

![使用者入口網站管理員](./media/multi-factor-authentication-get-started-portal/admin.png)


## <a name="security-questions"></a>安全性問題
在此索引標籤可讓您指定的使用者會需要提供的答案，如果已選取 [使用安全性問題後援選項安全性問題。  Azure 多重因素 Authenticaton 伺服器隨附於預設的問題，您可以使用。  您也可以變更順序，或新增您自己的問題。  新增您自己的問題，您可以指定您想要同時出現在這些問題的語言。

![使用者入口網站的安全性問題](./media/multi-factor-authentication-get-started-portal/secquestion.png)


## <a name="passed-sessions"></a>傳遞的工作階段

## <a name="saml"></a>SAML
可讓您設定的使用者入口網站，以接受使用 SAML 身分識別提供者所提供的宣告。  您可以指定工作階段逾時，指定驗證憑證和 URL 重新導向登出。

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## <a name="trusted-ips"></a>信任的 IPs
在此索引標籤可讓您指定單一 IP 位址或 IP 位址範圍，因此如果使用者登入，從這些 IP 位址，然後多重因素驗證略過可新增。

![使用者入口網站信任 IPs](./media/multi-factor-authentication-get-started-portal/trusted.png)

## <a name="self-service-user-enrollment"></a>自助使用者註冊
如果您希望您的使用者登入並註冊您必須選取 [允許使用者為中登入，並允許使用者註冊選項。 請記住您選取的設定會影響使用者的登入體驗。

例如，當使用者登入使用者入口網站，並按一下 [登入] 按鈕，他們會然後移至 Azure 多重因素驗證使用者設定頁面。  根據您設定的方式 Azure 多重因素驗證，使用者可以選取他們的驗證方法。  

如果選取的語音通話驗證方法或已預先設定，可使用的方法，頁面會提示使用者輸入其主要的電話號碼和副檔名的話。  他們也可能允許輸入備份的電話號碼。  

![使用者入口網站信任 IPs](./media/multi-factor-authentication-get-started-portal/backupphone.png)

如果使用者需要使用 PIN 驗證時，畫面也將提示他們輸入 pin 碼。  輸入他們的電話號碼和 PIN （如果適用的話） 後, 使用者按一下 [撥號給我立即驗證] 按鈕。  Azure 多重因素驗證會執行電話驗證使用者的主要的電話號碼。  使用者必須接聽電話並輸入 pin 碼 （如果適用的話），然後按 # 移到下一個自我註冊程序的步驟。   

如果使用者選擇簡訊文字驗證方法，或已預先設定，可使用的方法，頁面會提示使用者輸入其行動電話號碼。  如果使用者需要使用 PIN 驗證時，畫面也將提示他們輸入 pin 碼。  使用者輸入他們的電話號碼和 PIN （如果適用的話） 之後, 按一下文字我立即驗證] 按鈕。  Azure 多重因素驗證會執行 SMS 驗證到使用者的行動電話。  使用者必須先收到簡訊其中包含一次密碼 (OTP) 及回覆郵件的 OTP 加上 PIN，如果有的話） 若要移至下一個自我註冊程序的步驟。

![使用者入口網站簡訊](./media/multi-factor-authentication-get-started-portal/text.png)   

如果使用者選擇行動應用程式的驗證方法，或已預先設定，可使用的方法，頁面會提示使用者在他們的裝置上安裝 Azure 多重因素驗證應用程式，並產生啟動的程式碼。  安裝後 Azure 多重因素驗證應用程式，使用者，請按一下 [產生啟動程式碼] 按鈕。    

>[AZURE.NOTE]若要使用 Azure 多重因素驗證應用程式，使用者必須啟用其裝置的推播通知。

啟動的程式碼，以及條碼圖片的 URL，然後會顯示的頁面。  如果使用者需要使用 PIN 驗證時，畫面也將提示他們輸入 pin 碼。  使用者啟動的程式碼和 URL 輸入 Azure 多重因素驗證應用程式，或使用條碼掃描器掃描條碼圖片並按一下 [啟動] 按鈕。    

啟動完成之後，使用者按一下 [立即驗證] 按鈕。  Azure 多重因素驗證會執行驗證使用者的行動應用程式。  使用者必須輸入 pin 碼 （如果適用的話），然後按 [移到下一步自我註冊程序的其行動應用程式中的 [驗證] 按鈕。  


如果系統管理員已設定 Azure 多重因素驗證伺服器收集安全性問題及答案，使用者會然後會移至 [安全性問題] 頁面。  使用者必須選取四個安全性問題，並提供其所選的問題的答案。    

![使用者入口網站的安全性問題](./media/multi-factor-authentication-get-started-portal/secq.png)  

使用者自我註冊現已完成，使用者已有使用者入口網站登入。  使用者可以重新登入使用者入口網站在未來若要變更電話號碼、 Pin、 驗證方法和安全性問題如果允許他們的管理員，隨時。
