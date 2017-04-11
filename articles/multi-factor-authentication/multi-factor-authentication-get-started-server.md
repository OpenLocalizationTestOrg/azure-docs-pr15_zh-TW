<properties 
    pageTitle="Azure 多重因素驗證 Server 快速入門"
    description="這是說明如何開始使用 Azure MFA Server Azure 多重因素驗證頁面。"
    services="multi-factor-authentication"
    keywords="驗證伺服器，azure 多重因素驗證應用程式啟動頁面上，驗證伺服器下載"
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

# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Azure 多重因素驗證 Server 快速入門




<center>![雲端](./media/multi-factor-authentication-get-started-server/server2.png)</center>

現在，我們決定是否要使用內部部署多重因素驗證，就讓我們開始進行。 本頁面說明新的伺服器，並取得其安裝於內部部署的 Active Directory 安裝。 如果您已安裝的 PhoneFactor 伺服器並尋找升級，請參閱[升級至 Azure 多重因素伺服器](multi-factor-authentication-get-started-server-upgrade.md)，或如果您正在尋找安裝的詳細資訊請參閱 web 服務[部署 Azure 多重因素驗證伺服器行動應用程式 Web 服務](multi-factor-authentication-get-started-server-webservice.md)。


## <a name="download-the-azure-multi-factor-authentication-server"></a>下載 Azure 多重因素驗證伺服器



有兩種不同方式，您可以下載 Azure 多重因素驗證伺服器。 兩者都是透過 Azure 入口網站中完成。 第一個是直接管理多重因素驗證提供者。 第二個是透過 [服務設定。 第二個選項需要多重因素驗證提供者或 Azure MFA、 Azure AD 進階版或企業版行動性套件的授權。


### <a name="to-download-the-azure-multi-factor-authentication-server-from-the-azure-portal"></a>若要從 Azure 入口網站下載 Azure 多重因素驗證伺服器
--------------------------------------------------------------------------------

1. Azure 入口網站以系統管理員身分登入。
2. 在左側，選取 [Active Directory]。
3. 在 Active Directory 頁面上，按一下 [頂端的 [**多重因素驗證提供者**
4. 按一下 [底部的 [**管理**
5. 這會開啟新的頁面。  按一下 [**下載。**
![下載](./media/multi-factor-authentication-sdk/download.png)
6. 按一下**產生啟動認證**，上方**下載。**
![下載](./media/multi-factor-authentication-get-started-server/download4.png)
7. 儲存下載。



### <a name="to-download-the-azure-multi-factor-authentication-server-via-the-service-settings"></a>若要下載的服務設定透過 Azure 多重因素驗證伺服器


1. Azure 入口網站以系統管理員身分登入。
2. 在左側，選取 [Active Directory]。
3. 按兩下 Azure AD 執行個體。
4. 按一下頂端的 [**設定**
![下載](./media/multi-factor-authentication-sdk/download2.png)
5. 多重因素驗證下選取 [**管理服務設定**
6. 在 [服務設定] 頁面上在畫面底部按一下 [**移至入口網站**。
![下載](./media/multi-factor-authentication-get-started-server/servicesettings.png)
7. 這會開啟新的頁面。 按一下 [**下載。**
8. 按一下**產生啟動認證**，上方**下載。**
9. 儲存下載。




## <a name="install-and-configure-the-azure-multi-factor-authentication-server"></a>安裝並設定 Azure 多重因素驗證伺服器
既然您已下載的伺服器，您可以安裝並設定讓它。  請確定您安裝在伺服器符合下列需求︰



Azure 多重因素驗證伺服器需求|描述|
:------------- | :------------- |
硬體|<li>200 MB 的硬碟空間</li><li>x32 或 x64 處理器</li><li>1 GB 或更大的 RAM</li>
軟體|<li>Windows Server 2008 或大於如果主機伺服器作業系統</li><li>Windows 7 或更大的用戶端 OS 主機時</li><li>Microsoft.NET 4.0 架構</li><li>IIS 7.0 或更大，如果使用者入口網站或 web 服務 SDK</li>

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Azure 多重因素驗證伺服器防火牆需求
--------------------------------------------------------------------------------
每個 MFA 伺服器必須能夠進行通訊的連接埠 443 輸出至下列︰

- https://pfd.phonefactor.net
- https://pfd2.phonefactor.net
- https://css.phonefactor.net

如果輸出防火牆連接埠 443 限制，下列的 IP 位址範圍需要開啟︰

IP 子網路|網路遮罩|IP 範圍
:------------- | :------------- | :------------- |
134.170.116.0/25|255.255.255.128|134.170.116.1 – 134.170.116.126
134.170.165.0/25|255.255.255.128|134.170.165.1 – 134.170.165.126
70.37.154.128/25|255.255.255.128|70.37.154.129 – 70.37.154.254

如果您不使用 Azure 多重因素驗證事件確認功能，而且使用者不會從裝置公司網路上的多重因素驗證行動應用程式與驗證的 IP 範圍可以降低下列︰


IP 子網路|網路遮罩|IP 範圍
:------------- | :------------- | :------------- |
134.170.116.72/29|255.255.255.248|134.170.116.72 – 134.170.116.79
134.170.165.72/29|255.255.255.248|134.170.165.72 – 134.170.165.79
70.37.154.200/29|255.255.255.248|70.37.154.201 – 70.37.154.206


### <a name="to-install-and-configure-the-azure-multi-factor-authentication-server"></a>安裝和設定 Azure 多重因素驗證伺服器
--------------------------------------------------------------------------------


1. 可執行檔上按兩下。 這樣就會開始安裝。
2. 在 [選擇安裝資料夾] 畫面中，確認資料夾正確無誤，然後 [下一步]。
3. 一旦完成安裝，請按一下 [完成]。  這會啟動設定精靈。
4. 在設定精靈歡迎畫面勾選 [**略過使用 [驗證設定精靈**中，按一下 [**下一步**。  這會關閉精靈，並啟動伺服器。
    ![雲端](./media/multi-factor-authentication-get-started-server/skip2.png)
5. 傳回在我們下載從伺服器的頁面上，按一下 [**產生啟動認證**] 按鈕。  此資訊複製到 Azure MFA 伺服器所提供的方塊中，按一下 [**啟動**]。


上述步驟顯示快速安裝與設定精靈。  您可以重新執行 [驗證] 精靈，從伺服器上的 [工具] 功能表中選取它。



##<a name="import-users-from-active-directory"></a>從 Active Directory 匯入的使用者

現在，當伺服器已安裝並設定您可以將 Azure MFA 伺服器的方式來快速匯入使用者。

### <a name="to-import-users-from-active-directory"></a>若要從 Active Directory 匯入的使用者
--------------------------------------------------------------------------------


1. 在 [Azure MFA 伺服器的左側，選取 [**使用者**]。
2. 在底部，選取 [**從 Active Directory 匯入**]。
3. 現在您可以個別使用者的搜尋，或搜尋 Ou AD 目錄中的使用者。  在此案例中，我們將會指定 OU 的使用者。
4. 醒目提示所有右側的使用者，然後按一下 [**匯入**]。  您應該會收到快顯向您通知您已成功。  關閉 [匯入] 視窗。

![雲端](./media/multi-factor-authentication-get-started-server/import2.png)

## <a name="send-users-an-email"></a>傳送電子郵件的使用者
現在，您有 Azure 多重因素驗證伺服器，匯入您的使用者，建議您傳送您的使用者，告訴他們必須在多重因素驗證中已註冊電子郵件。

多重因素驗證伺服器的 Azure 有幾種方式可以使用多重因素驗證設定您的使用者。  比方說，如果您知道使用者的電話號碼，或能夠將其公司目錄 Azure 多重因素驗證伺服器匯入電話號碼，電子郵件將可讓使用者知道他們已在使用 Azure 多重因素驗證，提供一些使用 Azure 多重因素驗證通知的電話號碼，會收到其驗證的使用者已設定。  

電子郵件的內容會而定的已設定 （例如電話，SMS、 行動應用程式） 的使用者驗證方法。  例如，如果使用者需要使用 PIN 驗證時，電子郵件會分辨什麼初始 PIN 已設定為。  使用者通常必須在其第一個驗證期間變更其 PIN。

如果使用者的電話號碼尚未設定或匯入至 Azure 多重因素驗證伺服器，或使用行動應用程式進行驗證預先設定的使用者，您可以傳送給他們，讓他們知道有使用 Azure 多重因素驗證設定，並引導即可完成其帳戶註冊，透過 Azure 多重因素驗證使用者入口網站的電子郵件。  超連結會包含在使用者按一下存取使用者入口網站。 當使用者按一下超連結時，其網頁瀏覽器會開啟，並進行拍攝其公司 Azure 多重因素驗證使用者入口網站。   


### <a name="configuring-email-and-email-templates"></a>設定電子郵件及電子郵件範本

在左側的 [電子郵件] 圖示上的 [您可以設定的設定傳送這些電子郵件。  這可讓您可以輸入您的郵件伺服器的 SMTP 資訊，您可以傳送資料組寬的電子郵件新增核取 [傳送至郵件的使用者] 核取方塊。

![電子郵件設定](./media/multi-factor-authentication-get-started-server/email1.png)

在電子郵件內容的索引標籤，您會看到所有可選擇各種電子郵件範本。  取決於您如何設定您要使用多重因素驗證的使用者，您可以選擇範本，讓該最適合您。

![電子郵件範本](./media/multi-factor-authentication-get-started-server/email2.png)

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>Azure 多重因素驗證伺服器處理使用者資料的方式

當您使用多重因素驗證 (MFA) 伺服器內部時，使用者的資料會儲存於內部部署伺服器中。 沒有常設使用者資料儲存在雲端中。 當使用者執行雙因素驗證時，則 MFA 伺服器會傳送資料至 Azure MFA 雲端服務來執行驗證。 這些驗證要求會傳送至雲端服務，下列欄位會傳送要求和記錄，使其客戶的驗證及使用方式報表中使用。 讓他們可以啟用或停用多重因素驗證伺服器中的某些欄位是選擇性的。 從 MFA 伺服器通訊 MFA 雲端服務使用 SSL/TLS 連接埠 443 輸出上。 這些欄位是︰

- 唯一識別碼-使用者名稱或內部 MFA 伺服器識別碼
- 第一個和最後一個名稱-選擇性
- 電子郵件地址-選擇性
- 電話號碼-時執行的語音通話或 SMS 驗證
- 裝置權杖-時執行行動應用程式驗證
- 驗證模式
- 驗證結果
- MFA 伺服器名稱
- MFA 伺服器 IP
- 用戶端 IP – 如果有的話



除了上述欄位的驗證結果 （成功/拒絕） 及任何拒絕的原因是也驗證資料儲存，可透過驗證/使用情況報告。


## <a name="advanced-azure-multi-factor-authentication-server-configurations"></a>進階 Azure 多重因素驗證伺服器設定
在 [進階設定的其他資訊及設定資訊使用下表。

方法|描述
:------------- | :------------- |
[使用者入口網站](multi-factor-authentication-get-started-portal.md)|  安裝和設定包括部署和使用者的自助使用者入口網站的相關資訊。
[Active Directory Federation 服務](multi-factor-authentication-get-started-adfs.md)|設定與 AD FS Azure 多重因素驗證的相關資訊。
[RADIUS 驗證](multi-factor-authentication-get-started-server-radius.md)|  安裝和設定 RADIUS Azure MFA 伺服器的相關資訊。
[IIS 驗證](multi-factor-authentication-get-started-server-iis.md)|安裝及設定 Azure MFA 伺服器 iis 的相關資訊。
[Windows 驗證](multi-factor-authentication-get-started-server-windows.md)|  安裝及設定 Azure MFA Server 使用 Windows 驗證的相關資訊。
[LDAP 驗證](multi-factor-authentication-get-started-server-ldap.md)|安裝和設定 LDAP 驗證 Azure MFA 伺服器的相關資訊。
[遠端桌面閘道並使用 RADIUS Azure 多重因素驗證伺服器](multi-factor-authentication-get-started-server-rdg.md)|  安裝及設定 Azure MFA 伺服器使用 RADIUS 遠端桌面閘道器的相關資訊。
[與 Windows Server 的 Active Directory 同步處理](multi-factor-authentication-get-started-server-dirint.md)|安裝和設定 Active Directory 和 Azure MFA 伺服器之間的同步處理的相關資訊。
[部署 Azure 多重因素驗證伺服器行動應用程式的 Web 服務](multi-factor-authentication-get-started-server-webservice.md)|安裝和設定 Azure MFA 伺服器 web 服務的相關資訊。
