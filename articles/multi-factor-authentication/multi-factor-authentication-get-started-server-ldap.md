<properties 
    pageTitle="LDAP 驗證和 Azure 多重因素驗證伺服器"
    description="這是 Azure 多重因素驗證] 頁面，能夠協助您部署 LDAP 驗證和 Azure 多重因素驗證伺服器。"
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

# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP 驗證和 Azure 多重因素驗證伺服器


根據預設，Azure 多重因素驗證伺服器設定為匯入，或將使用者從 Active Directory 同步處理。 不過，可以將它設定為繫結至不同的 LDAP 目錄，例如 ADAM 目錄或特定的 Active Directory 網域控制站。 設定連線到透過 LDAP 目錄，Azure 多重因素驗證伺服器可以做為執行驗證的 LDAP proxy 設定。 您也可以使用的 LDAP 繫結為半徑目標，預先驗證的使用者使用 IIS 驗證]，或在 Azure 多重因素驗證使用者入口網站的主要驗證。

使用 Azure 多重因素驗證時為 LDAP proxy，Azure 多重因素驗證伺服器之間插入 LDAP 用戶端 （例如 VPN 應用裝置、 應用程式） LDAP 目錄伺服器才能新增多重因素驗證。 Azure 多重因素驗證函數，必須設定 Azure 多重因素驗證伺服器進行通訊的用戶端伺服器和 LDAP 目錄。 在此組態中，Azure 多重因素驗證伺服器接受 LDAP 要求用戶端伺服器及應用程式，並轉寄目標 LDAP 目錄伺服器驗證主要的認證。 如果所顯示的 LDAP 目錄的回應主要認證都有效，Azure 多重因素驗證會執行第二個因素驗證，並傳送 LDAP 用戶端的回應。 只有當 LDAP 伺服器驗證和多重因素驗證成功，會成功整個驗證。





## <a name="ldap-authentication-configuration"></a>LDAP 驗證設定


若要設定 LDAP 驗證，請在 Windows server 上安裝 Azure 多重因素驗證伺服器。 使用下列程序︰

1. Azure 多重因素驗證伺服器按一下左側功能表中的 [LDAP 驗證] 圖示。
2. 核取 [啟用 LDAP 驗證核取方塊。![LDAP 驗證](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)
3. 在 [客戶] 索引標籤上變更 TCP 連接埠和 SSL 連接埠如果 Azure 多重因素驗證 LDAP 服務應該繫結至非標準的連接埠以接聽 LDAP 要求將會設定用戶端。
4. 如果您打算使用 LDAPS 從用戶端 Azure 多重因素驗證伺服器，必須在伺服器上執行的伺服器上安裝的 SSL 憑證。 按一下瀏覽... [SSL 憑證] 方塊下的一步] 按鈕，然後選取 [已安裝的憑證用於安全連線。
5. 按一下 [新增... ] 按鈕。
6. 在 [新增 LDAP 用戶端] 對話方塊中，輸入的應用裝置、 伺服器或應用程式將驗證的 IP 位址到伺服器，以及應用程式名稱 （選擇性）。 應用程式的名稱會出現在 Azure 多重因素驗證報表，可能會顯示在簡訊或行動應用程式的驗證訊息。
7. 如果所有的使用者已，或將匯入伺服器，並受到多重排序因素驗證，請核取 [需要 Azure 多重因素驗證使用者符合的項目] 方塊。 如果大量的使用者不尚未已匯入伺服器及/或將會免除多重排序因素驗證，核取方塊。 此功能，請參閱的說明檔，如需詳細資訊。
8. 您可能會重複步驟 5 到 7 加入額外的 LDAP 用戶端。
9. 當 Azure 多重因素驗證設定接收 LDAP 驗證時，它必須 proxy LDAP 目錄這些驗證。 因此，目標的] 索引標籤僅顯示單一的灰色而無法使用 LDAP 目標的選項。 若要設定 LDAP 目錄的連線，按一下 [目錄整合圖示。
10. 在 [設定] 索引標籤中，選取 [使用特定的 LDAP 設定] 選項按鈕。
11. 按一下 [編輯... ] 按鈕。
12. 在 [編輯 LDAP 設定] 對話方塊中填入欄位所需的資訊連線至 LDAP 目錄。 下表中包含欄位的說明。 注意︰ 這項資訊也包含 Azure 多重因素驗證伺服器說明檔案中。![目錄整合](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)
13. 按一下 [測試] 按鈕以測試 LDAP 連線。
14. 如果 LDAP 連接測試成功，請按一下 [確定] 按鈕。
15. 按一下 [篩選] 索引標籤。 若要從 Active Directory 載入容器、 安全性群組和使用者預先設定伺服器。 如果繫結至不同的 LDAP 目錄，您可能要編輯顯示篩選器。 按一下 [說明] 連結，如需詳細資訊，在 [篩選]。
16. 按一下 [屬性] 索引標籤。 若要將 Active directory 屬性對應預先設定的伺服器。
17. 如果繫結至不同的 LDAP 目錄，或是變更預先設定的屬性對應，請按一下 [編輯... ] 按鈕。
18. 在 [編輯屬性] 對話方塊中，修改您的目錄的 LDAP 屬性對應。 可以在輸入屬性名稱，或按一下即可選取... 每個欄位旁邊的按鈕。
19. 按一下 [說明] 連結，如需屬性的詳細資訊。
20. 按一下 [確定] 按鈕。
21. 按一下 [公司設定] 圖示，然後選取 [使用者名稱解析] 索引標籤。
22] 如果連線到 Active Directory 網域的伺服器，應該能夠離開使用 Windows 安全性識別碼 (Sid) 相符的已選取的使用者名稱選項] 按鈕。 否則，請選取使用 LDAP 唯一識別碼屬性相符的使用者名稱選項] 按鈕。 選取後，Azure 多重因素驗證伺服器會嘗試解決每個使用者名稱 LDAP 目錄中的唯一識別碼。 將執行的 LDAP 搜尋在 [使用者名稱的目錄整合所定義的屬性]-> [屬性] 索引標籤。 當使用者，使用者名稱會解析為 LDAP 目錄中的唯一識別碼，唯一識別碼用於比對 Azure 多重因素驗證資料檔案中的使用者。 如此一來，不區分大小寫的比較，以及為長和短的使用者名稱格式這完成 Azure 多重因素驗證伺服器設定。 伺服器正在接聽上設定的連接埠 LDAP 存取要求設定的用戶端，並設定為 [proxy 驗證的 LDAP 目錄這些要求。


## <a name="ldap-client-configuration"></a>LDAP 用戶端設定

若要設定 LDAP 用戶端，使用規定︰

- 設定您的應用裝置、 伺服器或驗證透過 Azure 多重因素驗證伺服器的 LDAP 當作 LDAP 目錄的應用程式。 您應該使用相同的設定，您通常會使用直接連接到您的 LDAP 目錄，除了的伺服器名稱或是 Azure 多重因素驗證伺服器 IP 位址。
- 設定這些 30 到 60 秒的 LDAP 逾時，以便驗證使用者的認證與 LDAP 目錄執行第二個因素驗證、 接收其回應，然後回應 LDAP 存取要求的時間。
- 如果使用 LDAPS 的應用裝置或伺服器進行 LDAP 查詢必須信任 Azure 多重因素驗證伺服器上安裝的 SSL 憑證。
