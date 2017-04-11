<properties
    pageTitle="Azure AD 網域服務中設定 Secure LDAP (LDAPS) |Microsoft Azure"
    description="Azure AD 網域服務受管理的網域設定安全 LDAP (LDAPS)"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Azure AD 網域服務受管理的網域設定安全 LDAP (LDAPS)
本文將示範如何啟用安全輕量型目錄存取通訊協定 (LDAPS) 您 Azure AD 網域服務受管理的網域。 安全的 LDAP 亦稱為 「 輕量型目錄存取通訊協定 (LDAP) 透過安全通訊端層 (SSL) / 傳輸層安全性 (TLS)'。

## <a name="before-you-begin"></a>開始之前
若要執行本文中所列的工作，您需要︰

1. 有效**Azure 訂閱**。

2. **Azure AD 目錄**中的 [同步處理內部部署目錄或雲端專用的目錄。

3. **Azure AD 網域服務**必須啟用 Azure AD 目錄。 如果您尚未這麼做，請遵循[快速入門指南](./active-directory-ds-getting-started.md)所述的所有工作。

4. **若要用來啟用安全的 LDAP 的憑證**。
    - **建議**-從您的企業 CA 或公用憑證授權單位取得憑證。 此設定選項是更加安全。
    - 或者，您也可以選擇建立[自我簽署的憑證](#task-1---obtain-a-certificate-for-secure-ldap)本文稍後所示。

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>安全的 LDAP 憑證需求
啟用安全的 LDAP 之前，請取得下列方針，每有效的憑證。 如果您嘗試啟用安全的 LDAP 的受管理的網域不正確/不正確的憑證，就會發生失敗。

1. **信任的發行者**-憑證必須由信任的電腦需要連線至網域使用安全的 LDAP 授權單位發出。 貴組織的企業憑證授權單位或信任這些電腦公用憑證授權單位，可能是此授權。

2. **存留時間**-憑證必須至少下一步 3-6 月有效。 憑證到期時，會中斷您受管理的網域的安全 LDAP 存取。

3. **主體名稱**憑證的主體名稱必須是您受管理的網域萬用字元。 例如，如果您的網域名稱為 「 contoso100.com 」，憑證的主體名稱必須是 「 *。 contoso100.com 」。 設定此萬用字元名稱 DNS 名稱 （主旨的替代名稱）。

3. **使用金鑰方式**-憑證必須設定，如下列範例使用-數位簽章及加密金鑰。

4. **憑證目的**-必須用於 SSL 伺服器驗證憑證。

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>任務 1-取得安全 LDAP 的憑證
第一個工作牽涉到取得用於管理網域的安全 LDAP 存取的憑證。 您有兩個選項︰

- 從憑證授權單位取得憑證。 貴組織的企業 CA 或公用憑證授權單位，可能是授權。

- 建立自我簽署的憑證。


### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>選項 （建議選項） – 從憑證授權單位取得安全 LDAP 憑證
如果您的組織有部署企業公用基礎結構 (PKI)，您需要從組織的企業憑證授權單位 (CA) 取得憑證。 如果貴組織取得其憑證公用憑證授權單位，您需要從該公開憑證授權單位取得安全的 LDAP 憑證。

當要求憑證，請確定您所追蹤[的安全的 LDAP 憑證需求](#requirements-for-the-secure-ldap-certificate)所述的需求。

> [AZURE.NOTE] 需要連線至受管理的網域使用安全的 LDAP 的用戶端電腦必須信任 LDAPS 憑證的發行者。


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>選項 B – 為安全的 LDAP 建立自我簽署的憑證
您可以選擇建立自我簽署的憑證以安全的 LDAP，如果︰

- 在您的組織中的憑證不是由企業憑證授權單位發出或
- 您不打算使用公用憑證授權單位的憑證。

**建立自我簽署的憑證使用 PowerShell**

在您的 Windows 電腦上開啟新 PowerShell 視窗以**系統管理員身分**，然後輸入下列命令，以建立新的自我簽署的憑證。

    $lifetime=Get-Date

    New-SelfSignedCertificate -Subject *.contoso100.com -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment -Type SSLServerAuthentication -DnsName *.contoso100.com

在上述範例中，取代 'contoso100.com' DNS 網域名稱與您 Azure AD 網域服務受管理的網域。

![選取 Azure AD 目錄](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

新建立自我簽署的憑證會放在本機電腦的憑證存放區。


## <a name="task-2---export-the-secure-ldap-certificate-to-a-pfx-file"></a>任務 2-匯出安全 LDAP 憑證。PFX 檔案
啟動此工作之前，請確定您已從您的企業憑證授權單位或公用憑證授權單位取得安全的 LDAP 憑證，或建立自我簽署的憑證。

執行下列步驟，匯出 LDAPS 憑證。PFX 檔案。

1. 按下**[開始**] 按鈕，然後輸入**R**。在 [**執行**] 對話方塊中，輸入**mmc** ，按一下**[確定]**。

    ![啟動 MMC 主控台](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)

2. **使用者帳戶控制**提示時，按一下**[是]**以啟動 [以系統管理員身分 MMC （Microsoft 管理主控台）。

3. 從 [**檔案**] 功能表中，按一下 [**新增/移除貼齊-打開方式**。

    ![新增嵌入式管理單元至 MMC 主控台](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)

4. 在 [**新增或移除增益集] 貼齊**] 對話方塊中，選取**憑證**嵌入式管理單元，然後按一下 [**新增 >** ] 按鈕。

    ![新增憑證嵌入式管理單元至 MMC 主控台](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)

5. **憑證嵌入式管理單元**在精靈中，選取 [**電腦帳戶**，然後按一下 [**下一步**。

    ![新增憑證嵌入式管理單元電腦帳戶](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)

6. 在 [**選擇電腦**] 頁面中，選取 [**本機電腦上: （執行這個主控台的電腦）**並按一下 [**完成]**。

    ![新增憑證嵌入式管理單元-選取的電腦](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)

7. 在 [**新增或移除增益集] 貼齊**] 對話方塊中，按一下**[確定**] 以新增憑證嵌入式管理單元至 MMC。

    ![新增憑證嵌入式管理單元至 MMC-完成](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)

8. 在 [MMC] 視窗中，按一下以展開 [**主控台根目錄**。 您應該會看到憑證嵌入式管理單元載入。 按一下以展開的**憑證 （本機電腦）** ]。 按一下以展開 [**個人**] 節點，後面接著**憑證**節點。

    ![開啟個人憑證 」 存放區](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)

9. 您應該會看到我們建立自我簽署的憑證。 您可以檢查以確保指紋符合報告 PowerShell windows 上，當您建立憑證的憑證的內容。

10. 選取自我簽署的憑證和**以滑鼠右鍵按一下**。 從快顯功能表，選取**所有工作**，然後都選取 [**匯出]**。

    ![匯出憑證](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)

11. 在**憑證匯出精靈]**中，按一下 [**下一步**]。

    ![匯出憑證] 精靈](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)

12. **匯出私密金鑰**在頁面上，選取**[是]，匯出私密金鑰**]，然後按一下 [**下一步**。

    ![匯出憑證私密金鑰](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [AZURE.WARNING] 您必須匯出憑證和私密金鑰。 如果您提供不包含憑證私密金鑰 PFX 時，啟用安全的 LDAP 您受管理的網域就會失敗。

13. 在 [**匯出檔案格式**] 頁面上選取 [**個人資訊交換 PKCS #12 (。PFX)**為匯出的憑證的檔案格式。

    ![匯出憑證檔案格式](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [AZURE.NOTE] 僅限。支援 PFX 檔案格式。 不會匯出的憑證。CER 檔案格式。

14. 在 [**安全性**] 頁面上選取一個密碼來保護中的 [**密碼**] 選項，並輸入。PFX 檔案。 請記住密碼，因為它會需要的下一個工作。 按一下 [**下一步**繼續進行。

    ![匯出憑證的密碼 ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [AZURE.NOTE] 記下密碼。 您需要時啟用安全的 LDAP 此受管理的網域，在[任務 3-啟用安全的 LDAP 受管理的網域](#task-3---enable-secure-ldap-for-the-managed-domain)

15. 在 [**匯出至檔案**] 頁面上指定的檔案名稱及您想要匯出的憑證的位置。

    ![匯出憑證路徑](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)

16. 在下列頁面上，按一下 [**完成**憑證匯出到 PFX 檔案。 匯出憑證時，您應該會看到 [確認] 對話方塊。

    ![完成匯出憑證](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="task-3---enable-secure-ldap-for-the-managed-domain"></a>任務 3-啟用安全的 LDAP 受管理的網域
若要啟用安全的 LDAP，請執行下列設定步驟︰

1. 瀏覽至**[Azure 傳統入口網站](https://manage.windowsazure.com)**。

2. 選取在左窗格的 [ **Active Directory**節點。

3. 選取 Azure AD 目錄 （也稱為 「 租用戶 」），您必須啟用 Azure AD 網域服務。

    ![選取 Azure AD 目錄](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. 按一下 [**設定**] 索引標籤。

    ![設定目錄] 索引標籤](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. 向下一節**網域服務**捲動。 您應該會看到下列的螢幕擷取畫面所示，標題**安全 LDAP (LDAPS)**的其中一個選項︰

    ![網域服務組態] 區段](./media/active-directory-domain-services-admin-guide/secure-ldap-start.png)

6. 按一下 [**設定憑證...** ] 按鈕以顯示 [**安全的 LDAP 設定憑證**] 對話方塊。

    ![為安全的 LDAP 設定憑證](./media/active-directory-domain-services-admin-guide/secure-ldap-configure-cert-page.png)

7. 按一下下方指定 PFX 檔案，其中包含您想要使用受管理的網域安全 LDAP 存取憑證**與憑證 PFX 檔案**的資料夾圖示。 也輸入憑證匯出到 PFX 檔案時所指定的密碼。 然後按一下 [完成] 按鈕下方。

    ![指定安全的 LDAP PFX 檔案和密碼](./media/active-directory-domain-services-admin-guide/secure-ldap-specify-pfx.png)

8. [**設定**] 索引標籤的 [**網域服務**] 區段應該取得呈現灰色，且位於**擱置...**狀態幾分鐘。 在這段期間，LDAPS 憑證驗證的正確性，並安全的 LDAP 設定為受管理的網域。

    ![安全 LDAP-擱置狀態](./media/active-directory-domain-services-admin-guide/secure-ldap-pending-state.png)

    > [AZURE.NOTE] 若要啟用安全的 LDAP 您受管理的網域需要關於 10 到 15 分鐘。 如果所提供的安全 LDAP 憑證不相符的必要的準則，為您的目錄未啟用安全的 LDAP，而且您看到失敗。 例如的網域名稱不正確，憑證已過期，或等即將到期。

9. 成功的受管理的網域，請啟用安全的 LDAP**擱置...**訊息應該會消失。 您應該會看到顯示憑證的指紋。

    ![啟用安全的 LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled.png)

<br>


## <a name="task-4---enable-secure-ldap-access-over-the-internet"></a>任務 4-在網際網路上啟用安全 LDAP 存取
**選擇性任務**-如果您不打算存取透過網際網路使用 LDAPS 受管理的網域，請略過此設定工作。

在開始此工作之前，請確定您已完成[任務 3](#task-3---enable-secure-ldap-for-the-managed-domain)中所述的步驟。

1. 您應該會看到**啟用安全 LDAP 存取透過網際網路**的 [**設定**] 頁面的 [**網域服務**] 區段中的選項。 這個選項設定為 [**否預設由於預設為停用的受管理的網域，透過安全的 LDAP 存取網際網路。**

    ![啟用安全的 LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled2.png)

2. 切換**啟用安全的 LDAP 存取網際網路上**為 [**是]**。 按一下 [底部] 面板上的 [**儲存**] 按鈕。
    ![啟用安全的 LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access.png)

3. [**設定**] 索引標籤的 [**網域服務**] 區段應該取得呈現灰色，且位於**擱置...**狀態幾分鐘。 一些時間之後, 會啟用您受管理的網域，透過安全的 LDAP 存取網際網路。

    ![安全 LDAP-擱置狀態](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access-pending-state.png)

    > [AZURE.NOTE] 若要在安全的 LDAP 您受管理的網域啟用存取網際網路需要 10 分鐘。

4. 順利啟用安全的 LDAP 存取至您受管理的網域，在網際網路上時，**擱置...**訊息應該會消失。 您應該會看到可以用來存取您的目錄透過 LDAPS**外部 IP 位址的 LDAPS 存取**] 欄位中的外部 IP 位址。

    ![啟用安全的 LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-internet-access-enabled.png)

<br>

## <a name="task-5---configure-dns-to-access-the-managed-domain-from-the-internet"></a>工作 5-設定 DNS 以從網際網路存取受管理的網域
**選用的任務**-如果您不打算存取透過網際網路使用 LDAPS 受管理的網域，請略過此設定工作。

在開始此工作之前，請確定您已完成[任務 4](#task-4---enable-secure-ldap-access-over-the-internet)中所述的步驟。

一旦您已在網際網路上啟用安全的 LDAP 存取，您受管理的網域，您需要更新 DNS，用戶端電腦才能找到這個受管理的網域。 任務 4 結尾外部 IP 位址的 LDAPS 在**ACCESS**中的 [**設定**] 索引標籤上會顯示外部 IP 位址。

設定外部 DNS 提供者，使受管理的網域 (例如，「 contoso100.com 」) 的 DNS 名稱會指向此外部的 IP 位址。 在此範例中，我們需要建立下列 DNS 項目︰

    contoso100.com  -> 52.165.38.113

這樣就可以-您已準備好要連線到網際網路上使用安全的 LDAP 受管理的網域。

> [AZURE.WARNING] 請記得在用戶端電腦必須信任 LDAPS 憑證的發行者，都能成功連線至受管理的網域使用 LDAPS。 如果您使用的企業憑證授權單位或公開信任的憑證授權單位，您不需要執行任何動作，因為用戶端電腦信任這些憑證的發行者。 如果您使用自我簽署的憑證，您需要安裝的自我簽署憑證公用的組件至用戶端電腦上的受信任的憑證存放區。

<br>

## <a name="related-content"></a>相關的內容

- [管理 Azure AD 網域服務管理的網域](active-directory-ds-admin-guide-administer-domain.md)
