<properties 
   pageTitle="建立自我簽署的憑證的點為網站虛擬網路跨內部部署連線使用 makecert |Microsoft Azure"
   description="本文包含使用 makecert 在 Windows 10 上建立自我簽署的憑證的步驟。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="cherylmc" />

# <a name="working-with-self-signed-certificates-for-point-to-site-connections"></a>使用自我簽署憑證以點為網站的連線

本文可協助您建立自我簽署的憑證使用**makecert**，然後從該產生用戶端憑證。 Windows 10 版 makecert 撰寫步驟。 若要建立與 P2S 連線相容的憑證已驗證 Makecert。 

P2S 連線的憑證慣用的方法是使用您的企業憑證解決方案，並確定使用的常見的名稱值格式發行用戶端憑證'name@yourdomain.com',而不是 「 NetBIOS 網域 name\username 」 格式。

如果您沒有安裝企業方案，自我簽署的憑證才能讓 P2S 用戶端連線至虛擬網路。 我們會注意 makecert 已被取代，，但仍有效方法來建立自我簽署的憑證相容於 P2S 連線。 我們正在另一種解決方案建立自我簽署的憑證，但這次 makecert 慣用的方法。

## <a name="create-a-self-signed-certificate"></a>建立自我簽署的憑證

Makecert 是一種方式建立自我簽署的憑證。 下列步驟會引導您完成建立自我簽署的憑證使用 makecert。 這些步驟無法部署模型特定。 他們的年齡資源管理員] 及 [傳統的有效號碼。

### <a name="to-create-a-self-signed-certificate"></a>若要建立自我簽署的憑證

1. 在電腦上執行的 Windows 10，下載並安裝[Windows 軟體開發套件 (SDK) 適用於 Windows 10](https://dev.windows.com/en-us/downloads/windows-10-sdk)。

2. 安裝完成後，您可以找到 [此路徑] 底下的 makecert.exe 公用程式︰ C:\Program Files (x86) \Windows Kits\10\bin\<弧形 >。 
        
    範例︰`C:\Program Files (x86)\Windows Kits\10\bin\x64`

3. 接下來，建立並安裝在電腦上的個人憑證存放區中的憑證。 下列範例會建立您上傳至 Azure 設定 P2S 時的對應*.cer*檔案。 以管理員身分執行以下命令。 您想要使用的憑證名稱取代*ARMP2SRootCert*和*ARMP2SRootCert.cer* 。<br><br>憑證會位於您的憑證-目前的憑證。

        makecert -sky exchange -r -n "CN=ARMP2SRootCert" -pe -a sha1 -len 2048 -ss My "ARMP2SRootCert.cer"


###  <a name="rootpublickey"></a>若要取得公開金鑰

點-網站連線 VPN 閘道設定的一部分，公開金鑰根憑證上傳到 Azure。

1. 若要取得.cer 檔案從憑證，請開啟**certmgr.msc**。 以滑鼠右鍵按一下自我簽署的根憑證，按一下 [**全部任務**]，再按一下 [**匯出**。 這會開啟 [**憑證匯出精靈**]。

2. 在精靈中，按一下 [**下一步****否，不會匯出私密金鑰**]，然後選取然後按 [**下一步**。

3. 在 [**匯出檔案格式**] 頁面上選取**64 基本編碼 X.509 (。CER)。** 然後，請按一下 [**下一步**]。 

4. 在**[要匯出的檔案**，**瀏覽**到您要匯出的憑證的位置。 [**檔案名稱**] 中，名稱憑證檔案。 然後按一下 [**下一步**。

5. 按一下 [**完成**]，匯出的憑證。

 
### <a name="export-the-self-signed-certificate-optional"></a>匯出自我簽署的憑證 （選用）

您可能會想要匯出自我簽署的憑證和安全地將其儲存。 如果需要您可以稍後再將其安裝於另一部電腦和產生多個用戶端憑證，或將另一個.cer 檔案匯出。 與用戶端憑證安裝的電腦也是以適當的 VPN 用戶端設定可以連線至 P2S 透過虛擬網路設定。 因此，您會想要確認的用戶端憑證會產生與安裝僅在需要時，自我簽署的憑證安全地儲存。

若要為.pfx 匯出自我簽署的憑證，選取根憑證並使用相同的步驟所述[匯出用戶端憑證](#clientkey)匯出。

## <a name="create-and-install-client-certificates"></a>建立並安裝用戶端憑證

您沒有安裝自我簽署的憑證直接在用戶端電腦上。 您需要從自我簽署憑證產生的用戶端憑證。 您然後匯出，並安裝的用戶端電腦的用戶端憑證。 下列步驟都無法部署模型特定。 他們的年齡資源管理員] 及 [傳統的有效號碼。

### <a name="part-1---generate-a-client-certificate-from-a-self-signed-certificate"></a>第 1 部分-從自我簽署憑證產生的用戶端憑證

下列步驟會引導您完成其中一個方法自我簽署憑證從產生的用戶端憑證。 您可能會產生多個用戶端憑證從相同的憑證。 可以匯出的每個用戶端憑證，然後用戶端電腦上安裝。 

1. 在同一部電腦，您用來建立自我簽署的憑證，請以系統管理員身分開啟命令提示字元。

2. 在此範例中，「 ARMP2SRootCert 」 是指您產生自我簽署憑證。 
    - 變更您要產生從用戶端憑證自我簽署根名稱*「 ARMP2SRootCert 」* 。 
    - 變更*ClientCertificateName*您想要產生用戶端憑證的名稱。 


    修改，並執行產生的用戶端憑證範例。 如果您執行下列範例，但無法加以修改，則結果為命名 ClientCertificateName 所產生的根憑證 ARMP2SRootCert 您個人憑證存放區中的用戶端憑證。

        makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "ARMP2SRootCert" -is my -a sha1

4. 所有的憑證會儲存在您 「 憑證-目前的憑證 」 存放在您的電腦上。 您可以產生多個用戶端憑證視基於此程序。

### <a name="clientkey"></a>第 2 部分-匯出用戶端憑證

1. 若要匯出的用戶端憑證，請開啟**certmgr.msc**。 以滑鼠右鍵按一下您想要匯出，按一下 [**所有任務**，再按一下 [**匯出**用戶端憑證。 這會開啟 [**憑證匯出精靈**]。

2. 在精靈中，請按 [**下一步**，然後選取 [**是]，匯出私密金鑰**，，然後按 [**下一步**。

3. 在 [**匯出檔案格式**] 頁面中，您可以將選取的預設值。 然後按一下 [**下一步**。 
 
4. 在 [**安全性**] 頁面中，您必須保護私密金鑰。 如果您選取要使用密碼，請務必記錄，或請記住，您可以為這個憑證的密碼。 然後按一下 [**下一步**。

5. 在**[要匯出的檔案**，**瀏覽**到您要匯出的憑證的位置。 [**檔案名稱**] 中，名稱憑證檔案。 然後按一下 [**下一步**。

6. 按一下 [**完成**]，匯出的憑證。  

### <a name="part-3---install-a-client-certificate"></a>第 3 部分-安裝用戶端憑證

每個您想要使用點為網站連線至虛擬網路連線的用戶端必須安裝用戶端憑證。 這是憑證除了所需的 VPN 設定套件。 下列步驟會引導您完成手動安裝用戶端憑證。

1. 找出並將*.pfx*檔複製到用戶端電腦。 用戶端電腦上，按兩下要安裝的*.pfx*檔。 保留**目前的使用者**，為**儲存位置**，然後按一下 [**下一步**]。

2. 在**檔案**匯入] 頁面，不進行任何變更。 按一下 [**下一步**]。

3. **私密金鑰**在頁面上，輸入憑證的密碼，如果您使用其中一個，或驗證安全性原則會安裝憑證的正確，然後按一下 [**下一步**。

4. **憑證存放區**在頁面上，保留預設的位置，然後再按 [**下一步**。

5. 按一下 [**完成**]。 在 [**安全性警告**的憑證安裝，請按一下**[是]**。 現在已成功匯入憑證。

## <a name="next-steps"></a>後續步驟

繼續使用您點為網站的設定。 

- **資源管理員**部署模型步驟，請參閱[使用 PowerShell VNet 點為網站連線設定](vpn-gateway-howto-point-to-site-rm-ps.md)。 
- **傳統**部署模型步驟，請參閱[使用 [傳統] 入口網站 VNet 設定網站-點 VPN 連線](vpn-gateway-point-to-site-create.md)。