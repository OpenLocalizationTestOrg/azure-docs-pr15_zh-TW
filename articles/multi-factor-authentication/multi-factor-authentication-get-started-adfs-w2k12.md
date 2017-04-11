<properties
    pageTitle="MFA 伺服器與 Windows Server 2012 R2 AD FS |Microsoft Azure"
    description="本文將說明如何開始使用 Azure 多重因素驗證與 AD FS Windows Server 2012 R2 中。"
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


# <a name="secure-your-cloud-and-on-premises-resources-using-azure-multi-factor-authentication-server-with-ad-fs-in-windows-server-2012-r2"></a>保護您的雲端和內部部署資源與 AD FS，在 Windows Server 2012 R2 使用 Azure 多重因素驗證伺服器

如果您使用 Active Directory Federation Services (AD FS)，並想要安全雲端或內部部署資源，您可以設定 Azure 多重因素驗證 Server 與 AD FS 搭配使用。 此設定會觸發雙步驟驗證的最高值的結束點。

在本文中，我們討論與 AD FS，在 Windows Server 2012 R2 使用 Azure 多重因素驗證伺服器。 如需詳細資訊，請瞭解如何[使用 Azure 多重因素驗證 Server 與 AD FS 2.0 安全雲端和內部部署資源](multi-factor-authentication-get-started-adfs-adfs2.md)。

## <a name="secure-windows-server-2012-r2-ad-fs-with-azure-multi-factor-authentication-server"></a>安全 Windows Server 2012 R2 AD FS 與 Azure 多重因素驗證伺服器

當您安裝 Azure 多重因素驗證伺服器時，您有下列選項︰

- AD FS 相同的伺服器上的本機安裝 Azure 多重因素驗證伺服器
- 本機安裝 Azure 多重因素驗證介面卡，在 AD FS 伺服器上，然後再安裝不同的電腦上的 [多重因素驗證伺服器

在開始之前，請注意下列資訊︰

- 您不是必要 AD FS 伺服器上安裝 Azure 多重因素驗證伺服器。 不過，您必須安裝在 Windows Server 2012 R2 執行 AD FS AD FS 多重因素驗證介面卡。 如果是受支援的版本，且您 AD FS 介面卡另行安裝 AD FS 同盟伺服器上，您可以在不同的電腦上安裝伺服器。 請參閱下列程序，瞭解如何另行安裝介面卡。

- 當 MFA 伺服器 AD FS 介面卡的設計時，它所預期 AD FS，無法傳遞介面卡的信賴的名稱。 然後，依賴的合作對象名稱無法作為應用程式名稱。 不過，這後的結果不是以這種情況。 如果您的組織使用的文字訊息或行動應用程式驗證方法，在公司設定中定義的字串包含版面配置區，<$*application_name*$>。 當您使用 AD FS 介面卡時，不會自動取代此版面配置區。 我們建議您移除版面配置區的適當的字串時安全 AD FS。

- 您用來登入的帳戶必須建立安全性群組，在您的 Active Directory 服務的使用者權限。

- 多重因素驗證 AD FS 介面卡安裝精靈建立安全性群組稱為 PhoneFactor 管理員 Active Directory 執行個體。 接著，它會加入 AD FS 服務帳戶的同盟服務給此群組。 我們建議您先確認您網域控制站確實建立 PhoneFactor 管理員群組] 和 [AD FS 服務帳戶是此群組的成員。 如有必要，手動將 AD FS 服務帳戶新增至您網域控制站 PhoneFactor 管理員群組。

- 安裝 Web 服務 SDK 使用者入口網站的相關資訊，請參閱關於[Azure 多重因素驗證伺服器部署使用者入口網站。](multi-factor-authentication-get-started-portal.md)


### <a name="install-azure-multi-factor-authentication-server-locally-on-the-ad-fs-server"></a>Azure 多重因素驗證伺服器本機的伺服器上安裝 AD FS

1. 下載並安裝 Azure 多重因素驗證伺服器 AD FS 伺服器上。 安裝資訊，請參閱[Azure 多重因素驗證 Server 快速入門](multi-factor-authentication-get-started-server.md)。
2. 在 Azure 多重因素驗證伺服器管理主控台中，按一下 [ **AD FS** ] 圖示，，然後選取 [**允許使用者註冊**] 與 [**允許使用者選取方法**] 選項。
3. 選取您想要指定組織的任何其他選項。
4. 按一下 [**安裝 AD FS 介面卡**]。
<center>![雲端](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. 如果 [Active Directory] 視窗隨即出現，代表兩個項目。 您的電腦加入網域，並保護 AD FS 介面卡和多重因素驗證服務之間的通訊的 Active Directory 設定不完整。 按一下 [**下一步**自動完成這項設定，或選取**略過自動 Active Directory 設定，並設定手動**核取方塊，然後再按 [**下一步**。
6. 如果顯示 [本機群組] 視窗，這表示兩個項目。 您的電腦未加入網域，並保護 AD FS 介面卡和多重因素驗證服務之間的通訊的本機群組設定不完整。 按一下 [**下一步**自動完成這項設定，或選取**略過自動本機群組設定，並設定手動**核取方塊，然後再按 [**下一步**。
7. 在安裝精靈中，按一下 [**下一步**]。 Azure 多重因素驗證伺服器建立 PhoneFactor 管理員群組，並將 AD FS 服務帳戶新增至 PhoneFactor 管理員群組。
<center>![雲端](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. 在 [**啟動安裝程式**] 頁面上，按一下 [**下一步**]。
9. 在多重因素驗證 AD FS 介面卡安裝程式中，按一下 [**下一步**]。
10. 安裝完成時，請按一下 [**關閉**]。
11. 當已安裝介面卡時，您必須搭配 AD FS 來進行註冊。 開啟 Windows PowerShell，並執行下列命令︰<br>
    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
   <center>![雲端](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. 若要使用您的新註冊的卡，編輯全域驗證原則 AD FS 中。 在 AD FS 管理主控台中，移至 [**驗證原則**] 節點。 在**多重因素驗證**] 區段中，按一下 [**通用設定**] 區段] 旁的 [**編輯**] 連結。 在 [**編輯全域驗證原則**] 視窗中，選取**多重因素驗證**做為其他驗證方法，，然後按一下**[確定]**。 介面卡已註冊為 WindowsAzureMultiFactorAuthentication。 重新啟動 AD FS 服務註冊，才會生效。

<center>![雲端](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

此時，多重因素驗證伺服器設為要搭配 AD FS 的其他驗證提供者。

## <a name="install-a-standalone-instance-of-the-ad-fs-adapter-by-using-the-web-service-sdk"></a>使用 Web 服務 SDK 安裝 AD FS 介面卡的獨立執行個體
1. 安裝 Web 服務 SDK 伺服器上執行多因素驗證伺服器。
2. 下列檔案複製 \Program Files\Multi-因素驗證伺服器目錄至您計劃安裝 AD FS 介面卡伺服器︰
  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register MultiFactorAuthenticationAdfsAdapter.ps1
  - 移除註冊 MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config
3. 執行 MultiFactorAuthenticationAdfsAdapterSetup64.msi 安裝檔案。
4. 在多重因素驗證 AD FS 介面卡安裝程式中，按一下 [**下一步**開始安裝。
5. 安裝完成時，請按一下 [**關閉**]。

## <a name="edit-the-multifactorauthenticationadfsadapterconfig-file"></a>編輯 MultiFactorAuthenticationAdfsAdapter.config 檔案

請遵循這些步驟來編輯 MultiFactorAuthenticationAdfsAdapter.config 檔案︰

1. **UseWebServiceSdk**節點設**為 true**。  
2. 設定多重因素驗證 Web 服務 SDK 的 url **WebServiceSdkUrl**的值。 例如︰ *https://contoso.com/&lt;certificatename&gt;/MultiFactorAuthWebServicesSdk/PfWsSdk.asmx*，其中 certificatename 是您的憑證的名稱。  
3. 編輯 Register MultiFactorAuthenticationAdfsAdapter.ps1 指令碼加入*-ConfigurationFilePath&lt;路徑&gt;*結尾`Register-AdfsAuthenticationProvider`] 命令，其中*&lt;路徑&gt;*MultiFactorAuthenticationAdfsAdapter.config 檔案的完整路徑。

### <a name="configure-the-web-service-sdk-with-a-username-and-password"></a>設定 Web 服務 SDK 的使用者名稱和密碼

有兩個選項，以設定 Web 服務 SDK。 第一個與使用者名稱和密碼，第二個用戶端憑證。 請遵循下列步驟，第一個選項，或跳第二個。  

1. 設定帳戶 PhoneFactor 管理員安全性群組的成員**WebServiceSdkUsername**的值。 使用&lt;網域&gt;& # 頁，共 92;&lt;使用者名稱&gt;格式。  
2. **WebServiceSdkPassword**的設定值，以適當的帳戶密碼。

### <a name="configure-the-web-service-sdk-with-a-client-certificate"></a>設定 Web 服務 SDK 的用戶端憑證

如果您不想要使用的使用者名稱和密碼，請遵循下列步驟以設定 Web 服務 SDK 的用戶端憑證。

1. 從伺服器執行 Web 服務 SDK 的憑證授權單位取得用戶端憑證。 瞭解如何[取得用戶端憑證](https://technet.microsoft.com/library/cc770328.aspx)。  
2. 用戶端憑證匯入執行 Web 服務 SDK 的伺服器上的本機電腦個人憑證存放區。 請確定憑證授權單位的公用憑證受信任的根憑證憑證存放區。  
3. 匯出的公開及私密金鑰機碼的用戶端憑證.pfx 檔。  
4. 匯出 Base64 格式公開金鑰.cer 檔案。  
5. 在 [伺服器管理員] 中，確認已安裝的網頁伺服器 (IIS) \Web Server\Security\IIS 用戶端憑證對應驗證功能。 如果沒有安裝，選取 [**新增角色與功能**加入此功能]。  
6. 在 IIS 管理員中，按兩下包含 Web 服務 SDK 虛擬目錄的網站中**設定編輯器**]。 請務必在網站層級，而非虛擬目錄層級執行此動作。  
7. 移至 [ **system.webServer/security/authentication/iisClientCertificateMappingAuthentication** ] 區段。  
8. 啟用為**true**的設定。  
9. OneToOneCertificateMappingsEnabled 設**為 true**。  
10. 按一下 [oneToOneMappings 旁的 [ **...** ] 按鈕，然後按一下 [**新增**] 連結。  
11. 開啟您之前匯出 Base64.cer 檔案。 移除*---開始憑證---* *---結束憑證---*，與任何分行符號。 複製產生的字串。  
12. 在上述步驟中複製的字串設定憑證。  
13. 啟用為**true**的設定。  
14. 設定使用者名稱的帳戶是 PhoneFactor 管理員安全性群組的成員的帳戶。 使用&lt;網域&gt;& # 頁，共 92;&lt;使用者名稱&gt;格式。  
15. 適當的帳戶的密碼，設定密碼，然後關閉 [設定編輯器。  
16. 按一下 [**套用**] 連結。  
17. 在 Web 服務 SDK 虛擬目錄中，按兩下 [**驗證**]。  
18. 確認 ASP.NET 模擬和基本驗證設定為 [**啟用**] 和 [其他所有項目會設定為 [**停用**。  
19. 在 Web 服務 SDK 虛擬目錄中，按兩下 [ **SSL 設定**]。  
20. [**接受**]，以設定用戶端憑證，然後按一下 [**套用]**。  
21. 複製您之前匯出至的伺服器執行 AD FS 介面卡的.pfx 檔。  
22. .Pfx 檔匯入到本機電腦上的個人憑證存放區。  
23. 以滑鼠右鍵按一下並選取 [**管理私密金鑰**]，然後授與您用來登入 AD FS 服務帳戶的 「 讀取 」 權限。  
24. 開啟用戶端憑證，然後複製 [**詳細資料**] 索引標籤上的指紋。  
25. 在 MultiFactorAuthenticationAdfsAdapter.config 檔案中，設定**WebServiceSdkCertificateThumbprint**在上一個步驟中複製的字串。  


最後，若要註冊介面卡，請執行 [\Program Files\Multi-因素驗證 Server\Register MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell 指令碼。 介面卡已註冊為 WindowsAzureMultiFactorAuthentication。 重新啟動 AD FS 服務註冊，才會生效。

## <a name="related-topics"></a>相關的主題

疑難排解說明，請參閱[Azure 多重因素驗證常見問題集](multi-factor-authentication-faq.md)
