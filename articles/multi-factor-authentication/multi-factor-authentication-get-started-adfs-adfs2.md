<properties
    pageTitle="Azure MFA 伺服器使用 AD FS 2.0 |Microsoft Azure"
    description="這是說明如何開始使用 Azure MFA 與 AD FS 2.0 Azure 多重因素驗證頁面。"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>

# <a name="secure-cloud-and-on-premises-resources-using-azure-multi-factor-authentication-server-with-ad-fs-20"></a>保全與 AD FS 2.0 使用 Azure 多重因素驗證伺服器的雲端和內部部署資源

本文適用於組織與 Azure Active Directory 同盟及想保護內部部署的資源，或在雲端。 保護您的資源，藉由使用 Azure 多重因素驗證伺服器，並將它設為使用 AD FS，以便進行雙步驟驗證觸發高值結束點。

本文涵蓋 Azure 多重因素驗證伺服器使用 AD FS 2.0。  取得[使用與 Windows Server 2012 R2 AD FS Azure 多重因素驗證伺服器維護雲端和內部部署資源](multi-factor-authentication-get-started-adfs-w2k12.md)中的詳細資訊。


## <a name="secure-ad-fs-20-with-a-proxy"></a>安全 AD FS 2.0 使用 proxy
若要保護 AD FS 2.0 使用 proxy，ADFS proxy 伺服器上安裝 Azure 多重因素驗證伺服器，並設定伺服器。

### <a name="configure-iis-authentication"></a>設定 IIS 驗證

1. Azure 多重因素驗證伺服器上，按一下左側功能表中的 [ **IIS 驗證**] 圖示。
2. 按一下**表單為基礎**的索引標籤。
3. 按一下 [**新增...** ] 按鈕。
<center>![設定](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. 自動偵測使用的使用者名稱、 密碼和網域變數輸入登入 URL （例如 https://sso.contoso.com/adfs/ls) Auto-Configure Form-Based 網站] 對話方塊中，按一下 [確定]。
5. 如果所有的使用者已，或將匯入伺服器，並受到雙步驟驗證，請核取 [**需要 Azure 多重因素驗證使用者符合**] 方塊。 如果大量的使用者不尚未已匯入伺服器及/或將會免除雙步驟驗證，核取方塊。 如需有關此功能的詳細資訊，請參閱的說明檔。
6. 如果頁面變數無法自動偵測到，按一下 [**指定手動...** 在 [Auto-Configure Form-Based 網站] 對話方塊中的按鈕。
7. Add Form-Based 網站] 對話方塊中，在 ADFS 登入] 頁面 （例如 https://sso.contoso.com/adfs/ls) 的 [送出的 URL] 欄位中輸入的 URL，然後輸入應用程式名稱 （選擇性）。 應用程式的名稱會出現在 Azure 多重因素驗證報表，可能會顯示在簡訊或行動應用程式的驗證訊息。 送出的 url，請參閱的說明檔，如需詳細資訊。
8. 設定 [要求格式] 以 「 張貼的文章，或取得 」。
9. 輸入使用者名稱變數 (ctl00$ ContentPlaceHolder1$ UsernameTextBox) 和密碼變數 (ctl00$ ContentPlaceHolder1$ PasswordTextBox)。 如果您表單為基礎的登入頁面會顯示 [網域] 文字方塊，請輸入的網域變數。 您可能需要瀏覽至登入頁面的網頁瀏覽器中，以滑鼠右鍵按一下頁面上選取**[檢視原始檔**]，若要尋找 [輸入] 方塊內的登入頁面的名稱。
10. 如果所有的使用者已，或將匯入伺服器，並受到雙步驟驗證，請核取 [**需要 Azure 多重因素驗證使用者符合**] 方塊。 如果大量的使用者不尚未已匯入伺服器及/或將會免除雙步驟驗證，核取方塊。
<center>![設定](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. 按一下**進階...** 若要檢閱進階的設定] 按鈕。 您可以設定包括可選取自訂拒絕頁面檔案，以快取成功驗證網站使用 cookie，並選取驗證的主要的認證。
12. 由於 ADFS proxy 伺服器不可能會加入網域，您可以使用 LDAP 連線到您的網域控制站的使用者匯入及預先驗證。 在 [Advanced Form-Based 網站] 對話方塊中，按一下 [**主要驗證**] 索引標籤並選取**LDAP 繫結**預先驗證驗證類型。
13. 完成時，按一下 [**確定**] 按鈕，若要返回 [Add Form-Based 網站] 對話方塊。 在進階設定，請參閱的說明檔，如需詳細資訊。
14. 按一下 [**確定**] 按鈕，關閉對話方塊]。
15. 當已偵測到的 URL 及頁面的變數或輸入時，網站資料的顯示表單為基礎的面板中。
16. 按一下 [**原生模組**] 索引標籤，然後選取 [伺服器、 ADFS proxy 執行底下 （例如 「 預設的網站 」） 的網站或 ADFS proxy 應用程式 （如 「 1 」 ！ 」 adfs] 底下） 若要啟用您要的層級的外掛程式 IIS。
17. 按一下畫面頂端的 [**啟用 IIS 驗證**] 方塊。
18. IIS 驗證現在已啟用。

### <a name="configure-directory-integration"></a>設定目錄整合

啟用 IIS 驗證，但執行測試驗證若要將 Active Directory (AD) 透過 LDAP 中，您必須設定網域控制站的 LDAP 連線。

1. 按一下 [**目錄整合**圖示。
2. 在 [設定] 索引標籤中，選取 [**使用特定的 LDAP 設定**] 選項按鈕。
<center>![設定](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
3. 按一下 [**編輯...** ] 按鈕。
4. 在 [編輯 LDAP 設定] 對話方塊中填入欄位連接至 AD 網域控制站所需的資訊。 下表中包含欄位的說明。 這項資訊也會包含在 Azure 多重因素驗證伺服器說明檔案中。
5. 按一下 [**測試**] 按鈕以測試 LDAP 連線。
<center>![設定](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
6. 如果 LDAP 連接測試成功，請按一下**[確定**] 按鈕。

### <a name="configure-company-settings"></a>設定公司設定

1. 接下來，按一下 [**公司設定**] 圖示，然後選取 [**使用者名稱解析**] 索引標籤。
2. 選取 [**使用 LDAP 相符的使用者名稱的唯一識別碼屬性**] 選項按鈕。
3. 如果使用者輸入使用者名稱為 「 網域 \ 使用者名稱 」 格式，伺服器需要能夠建立 LDAP 查詢時中去除關閉使用者名稱的網域。 可以完成的透過登錄設定。
4. 開啟登錄編輯程式，然後移至 HKEY_LOCAL_MACHINE/軟體/Wow6432Node/正面網路/PhoneFactor 64 位元伺服器上。 如果在 32 位元伺服器上，需要 「 Wow6432Node 「 不在路徑。 建立名為 「 UsernameCxz_stripPrefixDomain 」 DWORD 登錄機碼，並將值設為 1。 Azure 多重因素驗證現在保護 ADFS proxy。

確認使用者擁有已匯入從 Active Directory 到伺服器。 如果您想要 whitelist 內部 IP 位址以便從這些位置登入網站時，不需要進行雙步驟驗證，請參閱[信任 IPs 一節](#trusted-ips)。

<center>![設定](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## <a name="ad-fs-20-direct-without-a-proxy"></a>AD FS 2.0 直接不 proxy

您可以保護的 AD FS 時不會使用 AD FS proxy。 安裝 Azure 多重因素驗證伺服器上 AD FS server 與設定伺服器每下列步驟︰

1. Azure 多重因素驗證伺服器上，按一下左側功能表中的 [ **IIS 驗證**] 圖示。
2. 按一下 [ **HTTP** ] 索引標籤。
3. 按一下 [**新增...** ] 按鈕。
4. 在 [新增基底 URL 對話] 方塊中，輸入 ADFS 網站 URL （例如 https://sso.domain.com/adfs/ls/auth/integrated) 執行 HTTP 驗證的位置，將 [基底 URL] 欄位。 請輸入應用程式名稱 （選用）]。 應用程式的名稱會出現在 Azure 多重因素驗證報表，可能會顯示在簡訊或行動應用程式的驗證訊息。
5. 如果有需要，請調整閒置逾時，最大值工作階段的時間。
6. 如果所有的使用者已，或將匯入伺服器，並受到雙步驟驗證，請核取 [**需要 Azure 多重因素驗證使用者符合**] 方塊。 如果大量的使用者不尚未已匯入伺服器及/或將會免除雙步驟驗證，核取方塊。 如需有關此功能的詳細資訊，請參閱的說明檔。
7. 如有需要，請核取 [cookie 的快取] 方塊中。
<center>![設定](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. 按一下**[確定**] 按鈕。
9. 按一下 [**原生模組**] 索引標籤，然後選取 [啟用您要的層級的外掛程式 IIS 的 [伺服器、 網站 （例如 「 預設的網站 」） 或 ADFS 應用程式 （例如 「 1 ！ 」 在 「 adfs 」）。
10. 按一下畫面頂端的 [**啟用 IIS 驗證**] 方塊。 Azure 多重因素驗證現在保護 ADFS。

確認使用者擁有已匯入從 Active Directory 到伺服器。 如果您想要 whitelist 內部 IP 位址以便從這些位置登入網站時，不需要進行雙步驟驗證，請參閱信任 IPs 節。


## <a name="trusted-ips"></a>信任的 IPs
信任的 Ip 允許使用者略過來自特定 IP 位址或子網站要求 Azure 多重因素驗證。 例如，您可能要免除使用者從雙步驟驗證當使用者登入 office。 此，您可以指定 office 子網路為受信任 IPs 項目。

### <a name="to-configure-trusted-ips"></a>若要設定受信任的 Ip


1. IIS 驗證] 區段中，按一下 [**信任 IPs**索引標籤。
1. 按一下 [**新增...** ] 按鈕。
1. 新增受信任 IPs] 對話方塊中出現時，選取其中一個**單一 IP**、 **IP 範圍**或**子網路**選項按鈕。
1. 輸入的 IP 位址、 IP 位址範圍或應該 whitelisted 的子網路。 如果輸入子網路，請選取適當的遮罩，然後按一下**[確定**] 按鈕。 現在已新增受信任的 IP。


<center>![設定](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>
