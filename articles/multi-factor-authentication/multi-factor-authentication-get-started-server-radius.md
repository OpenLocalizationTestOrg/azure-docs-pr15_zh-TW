<properties 
    pageTitle="RADIUS 驗證和 Azure 多重因素驗證伺服器"
    description="這是 Azure 多重因素驗證] 頁面，能夠協助您部署 RADIUS 驗證和 Azure 多重因素驗證伺服器。"
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



# <a name="radius-authentication-and-azure-multi-factor-authentication-server"></a>RADIUS 驗證和 Azure 多重因素驗證伺服器

[RADIUS 驗證] 區段，可讓您以啟用及設定 RADIUS Azure 多重因素驗證伺服器驗證。 RADIUS 是標準的通訊協定接受驗證要求並處理這些要求。 Azure 多重因素驗證伺服器做為半徑伺服器，並插入您 RADIUS 用戶端 （例如 VPN 應用裝置） 和您驗證目標之間，這可能是 Active Directory (AD)，LDAP 目錄或另一個 RADIUS 伺服器，才能新增 Azure 多重因素驗證。 Azure 多重因素驗證函數，您必須設定 Azure 多重因素驗證伺服器，讓它可以通訊與用戶端伺服器和驗證目標。 Azure 多重因素驗證伺服器接受要求 RADIUS 用戶端、 驗證憑證驗證目標、 新增 Azure 多重因素驗證，及傳送 RADIUS 用戶端的回應。 才會成功的整個驗證主要的驗證和 Azure 多重因素驗證成功。

>[AZURE.NOTE]
>MFA 伺服器僅支援 PAP （密碼驗證通訊協定） 和 MSCHAPv2 （Microsoft 的挑戰交換驗證通訊協定） 做為半徑伺服器時，針對通訊協定。  MFA 伺服器做為 RADIUS proxy 到另一個支援的通訊協定，例如 Microsoft NPS RADIUS 伺服器時，可以用其他通訊協定，例如 EAP （延伸驗證通訊協定）。
></br>
>在此組態中使用其他通訊協定，單向 SMS 和誓言權杖會無法使用因為 MFA 伺服器無法啟動使用該通訊協定成功 RADIUS 挑戰回應。


![Radius 驗證](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## <a name="radius-authentication-configuration"></a>針對驗證設定

若要設定 RADIUS 驗證，請在 Windows server 上安裝 Azure 多重因素驗證伺服器。 如果您有的 Active Directory 環境時，伺服器應該加入內部網路的網域。 您可以使用下列程序來設定 Azure 多重因素驗證伺服器︰

1. Azure 多重因素驗證伺服器按一下左側功能表中的 [RADIUS 驗證] 圖示。
2. 核取 [啟用 RADIUS 驗證核取方塊。
3. 在 [客戶] 索引標籤上變更驗證的連接埠與會計專用的連接埠如果 Azure 多重因素驗證 RADIUS 服務應該繫結至非標準的連接埠以接聽 RADIUS 要求將會設定用戶端。
4. 按一下 [新增... ] 按鈕。
5. 在 [新增 RADIUS 用戶端] 對話方塊中，輸入將驗證應用裝置/伺服器的 IP 位址 Azure 多重因素驗證伺服器、 應用程式名稱 （可省略） 和共用密碼。 共用的私人需要 Azure 多重因素驗證伺服器和應用裝置/伺服器上都相同。 應用程式的名稱會出現在 Azure 多重因素驗證報表，可能會顯示在簡訊或行動應用程式的驗證訊息。
6. 如果所有的使用者已，或將匯入伺服器，並受到多重因素驗證，請核取 [需要多重因素驗證使用者符合的項目] 方塊。 如果大量的使用者不尚未已匯入伺服器及/或將會免除多重因素驗證，核取方塊。 此功能，請參閱的說明檔，如需詳細資訊。
7. 如果使用者將使用 Azure 多重因素驗證行動應用程式驗證，而且您想要使用誓言密碼在哪裡後援驗證-頻電話的通話、 簡訊或推入通知，請核取 [啟用後援誓言 token 方塊。
8. 按一下 [確定] 按鈕。
9. 您可以重複步驟 4 到 8，以新增其他 RADIUS 用戶端。
10. 按一下 [目標] 索引標籤。
11. 如果在 Active Directory 環境中的網域的伺服器上安裝 Azure 多重因素驗證伺服器，請選取 [Windows 網域]。
12. 如果針對 LDAP 目錄驗證使用者，請選取 [LDAP 繫結]。 使用時的 LDAP 連結，您必須按一下 [目錄整合] 圖示，並編輯 [設定] 索引標籤上的 LDAP 設定，讓伺服器可以繫結至您的目錄。 設定 LDAP 指示可以 LDAP Proxy 設定指南中找到。
13. 如果其他 RADIUS 伺服器驗證使用者，請選取 [RADIUS 伺服器。
14. 設定伺服器的伺服器會 proxy RADIUS 要求，按一下 [新增... ] 按鈕。
15. 在 [新增 RADIUS 伺服器] 對話方塊中輸入 [RADIUS 伺服器並共用密碼的 IP 位址。 共用的私人需要 Azure 多重因素驗證伺服器和 RADIUS 伺服器上都相同。 變更驗證連接埠與會計專用連接埠如果 RADIUS 伺服器會使用不同的連接埠。
16. 按一下 [確定] 按鈕。
17. 使其會處理傳送給它從 Azure 多重因素驗證伺服器的存取要求，您必須在 [RADIUS 伺服器 RADIUS 用戶端新增 Azure 多重因素驗證伺服器。 您必須使用相同的共用的密碼中 Azure 多重因素驗證伺服器設定。
18. 您可能會重複這個步驟，以新增其他 RADIUS 伺服器與設定的伺服器應該撥打電話給移] 及 [下移] 按鈕的順序。 這樣就完成 Azure 多重因素驗證伺服器設定。 伺服器正在設定的連接埠上接聽 RADIUS 存取要求設定的用戶端。   


## <a name="radius-client-configuration"></a>RADIUS 用戶端設定

若要設定 RADIUS 用戶端，使用規定︰

- 設定應用裝置/伺服器驗證 RADIUS 透過 Azure 多重因素驗證伺服器 IP 位址，做為 RADIUS 伺服器。
- 使用相同的共用的密碼上述設定。
- 設定這些 30 到 60 秒的 RADIUS 逾時，以便驗證使用者的認證執行多因素驗證、 接收其回應，然後回應 RADIUS 存取要求的時間。
