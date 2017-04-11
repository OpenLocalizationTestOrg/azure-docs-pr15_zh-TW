<properties 
    pageTitle="IIS 驗證和 Azure 多重因素驗證伺服器"
    description="這是 Azure 多重因素驗證] 頁面，能夠協助您部署 IIS 驗證和 Azure 多重因素驗證伺服器。"
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
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="iis-authentication"></a>IIS 驗證

Azure 多重因素驗證伺服器 IIS 驗證區段可讓您啟用及設定 Microsoft IIS web 應用程式以整合 IIS 驗證。 Azure 多重因素驗證伺服器安裝外掛程式的可以篩選所套用至 IIS 網頁伺服器，才能新增 Azure 多重因素驗證的要求。 外掛程式 IIS 提供支援表單型驗證 」 和 「 整合 Windows HTTP 驗證。 信任 IPs 也可以設定免除內部的 IP 位址的雙因素驗證。


![IIS 驗證](./media/multi-factor-authentication-get-started-server-iis/iis.png)


## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>使用表單型 IIS 驗證與 Azure 多重因素驗證伺服器

若要保護使用表單型驗證的 IIS web 應用程式，IIS 網頁伺服器上安裝 Azure 多重因素驗證伺服器及設定伺服器每下列程序。

1. Azure 多重因素驗證伺服器按一下左側功能表中的 [IIS 驗證] 圖示。
2. 按一下表單為基礎的索引標籤。
3. 按一下 [新增... ] 按鈕。
4. 使用者名稱，密碼和網域變數自動 Auto-Configure Form-Based 網站] 對話方塊中輸入登入 URL (例如 https://localhost/contoso/auth/login.aspx) 並按一下 [確定]。
5. 如果所有的使用者已，或將匯入伺服器，並受到多重因素驗證，請核取 [需要多重因素驗證使用者符合的項目] 方塊。 如果大量的使用者不尚未已匯入伺服器及/或將會免除多重因素驗證，核取方塊。
6. 如果頁面變數無法自動偵測到，按一下 [指定手動... 在 [Auto-Configure Form-Based 網站] 對話方塊中的按鈕。
7. Add Form-Based 網站] 對話方塊中登入頁面的 [送出的 URL] 欄位中輸入的 URL，然後輸入應用程式名稱 （選擇性）。 應用程式的名稱會出現在 Azure 多重因素驗證報表，可能會顯示在簡訊或行動應用程式的驗證訊息。 送出的 url，請參閱的說明檔，如需詳細資訊。
8. 選取正確的要求格式。 這是設定為 「 文章或取得 」 大部分的 web 應用程式。
9. （如果出現在 [登入] 頁面上），請輸入使用者名稱變數、 密碼變數和網域變數。 您可能需要瀏覽至登入頁面的網頁瀏覽器中，以滑鼠右鍵按一下頁面上選取 [若要尋找的名稱，在頁面中的 [輸入] 方塊的 「 檢視原始檔]。
10. 如果所有的使用者已，或將匯入伺服器，並受到多重因素驗證，請核取 [需要 Azure 多重因素驗證使用者符合的項目] 方塊。 如果大量的使用者不尚未已匯入伺服器及/或將會免除多重因素驗證，核取方塊。 此功能，請參閱的說明檔，如需詳細資訊。
11.  按一下 [進階... 若要檢閱進階的設定，包括可選取自訂拒絕頁面檔案，可快取一段時間使用 cookie 成功驗證網站並選擇是否要驗證 Windows 網域、 LDAP 目錄或 RADIUS 伺服器主要憑證] 按鈕。 完成時按一下 [確定] 按鈕，若要返回 [Add Form-Based 網站] 對話方塊。 在進階設定，請參閱的說明檔，如需詳細資訊。
12. 按一下 [確定] 按鈕。
13. 一旦已偵測到的 URL 及頁面的變數或輸入，網站資料會顯示在 [表單為基礎的面板中。
14. 請參閱啟用 IIS 外掛程式 Azure 多重因素驗證伺服器] 區段中直接下列完成 IIS 驗證設定。

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>使用整合式的 Windows 驗證與 Azure 多重因素驗證伺服器

若要保護的 IIS web 應用程式使用整合式 Windows HTTP 驗證，IIS 網頁伺服器上安裝 Azure 多重因素驗證伺服器及設定伺服器每下列程序。

1. Azure 多重因素驗證伺服器按一下左側功能表中的 [IIS 驗證] 圖示。
2. 按一下 [HTTP] 索引標籤。 按一下表單為基礎的索引標籤。
3. 按一下 [新增... ] 按鈕。
4. 在 [新增基底 URL 對話] 方塊中，將 URL 輸入 HTTP 驗證的網站執行 (例如 http://localhost/owa) 的 [基底 URL] 欄位並輸入應用程式名稱 （選擇性）。 應用程式的名稱會出現在 Azure 多重因素驗證報表，可能會顯示在簡訊或行動應用程式的驗證訊息。
5. 調整閒置逾時，最大值工作階段時間如果不足的預設值。
6. 如果所有的使用者已，或將匯入伺服器，並受到多重因素驗證，請核取 [需要多重因素驗證使用者符合的項目] 方塊。 如果大量的使用者不尚未已匯入伺服器及/或將會免除多重因素驗證，核取方塊。 此功能，請參閱的說明檔，如需詳細資訊。
7. 如有需要，請核取 [cookie 的快取] 方塊中。
8. 按一下 [確定] 按鈕。
9. 請參閱[啟用 IIS 外掛程式 Azure 多重因素驗證伺服器](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server)直接下列完成 IIS 驗證設定。


## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Azure 多重因素驗證伺服器啟用 IIS 外掛程式

一旦您已設定表單為基礎或 HTTP 驗證 Url 和設定，您必須先在 IIS 中選取應該載入並啟用 Azure 多重因素驗證 IIS 外掛程式的位置。 使用下列程序︰

1. 如果 IIS 6 上執行，請按一下 [ISAPI] 索引標籤，然後選取 [web 應用程式執行下 （例如預設網站） 若要啟用外掛程式 Azure 多重因素驗證 ISAPI 篩選該網站的網站。
2. 如果執行 IIS 7 或更新版本，請按一下 [原生模組] 索引標籤，然後選取 [伺服器、 website(s) 或應用程式，以便在您要的等級的外掛程式 IIS。
3. 按一下畫面頂端的 [啟用 IIS 驗證] 方塊。 Azure 多重因素驗證現在保護所選取的 IIS 應用程式。 確定使用者都已匯入伺服器。 如果您想要的 whitelist 內部 IP 位址，登入該網站從這些位置時，不需要的雙因素驗證，請參閱下方的信任 IPs 一節。


## <a name="trusted-ips"></a>信任的 IPs

信任的 Ip 允許使用者略過來自特定 IP 位址或子網站要求 Azure 多重因素驗證。 例如，您可能要免除使用者從 Azure 多重因素驗證時登入 office。 此，您可以指定 office 子網路為受信任 IPs 項目。 若要設定受信任 IPs 會使用下列程序︰

1. IIS 驗證] 區段中，按一下 [信任 IPs 索引標籤。
2. 按一下 [新增... ] 按鈕。
3. 新增受信任 IPs] 對話方塊中出現時，選取 [單一 IP、 IP 範圍或子網路的選項按鈕]。
4. nter 為止 IP 位址範圍的 IP 位址或子網路應該 whitelisted。 如果輸入子網路，請選取適當的遮罩，然後按一下 [確定] 按鈕。 現在已加入 whitelist。
