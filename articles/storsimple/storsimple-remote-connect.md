<properties 
   pageTitle="從遠端連線到您的 StorSimple 裝置 |Microsoft Azure"
   description="說明如何設定遠端管理您的裝置，以及如何連線到 Windows PowerShell StorSimple 透過 HTTP 或 HTTPS。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/21/2016"
   ms.author="alkohli" />

# <a name="connect-remotely-to-your-storsimple-device"></a>從遠端連線到您的 StorSimple 裝置

## <a name="overview"></a>概觀

您可以使用 Windows PowerShell 遠端連線到您 StorSimple 裝置。 當您連線以下列方式時，將不會看到的功能表。 （您看到功能表只有當您在裝置上使用循序主控台連線）。使用 Windows PowerShell 遠端您連線至特定的 runspace。 您也可以指定的顯示語言。 

如需使用 Windows PowerShell 遠端管理您的裝置的詳細資訊，請移至[使用 Windows PowerShell 來管理您的 StorSimple 裝置 StorSimple 的](storsimple-windows-powershell-administration.md)。

本教學課程說明如何設定遠端管理您的裝置，然後連線到 Windows PowerShell 用 StorSimple 的方式。 您可以透過 Windows PowerShell 遠端使用 HTTP 或 HTTPS。 不過，當您決定如何連線到 Windows PowerShell StorSimple，請考慮下列動作︰ 

- 直接連接到裝置循序主控台安全，但無法透過網路參數連線至循序主控台。 請務必謹慎安全性風險透過網路參數連線至裝置循序主控台時。 

- 透過 HTTP 工作階段連線，可能會提供更高的安全性比透過網路連線透過循序主控台。 雖然這不是最安全的方法，則可在受信任的網路上。 

- 透過 HTTPS 與工作階段自我簽署憑證連線且最安全的建議的選項。

您可以從遠端連線到 Windows PowerShell 介面。 不過，預設不會啟用遠端存取您的 StorSimple 裝置，透過 Windows PowerShell 介面。 您必須先啟用遠端管理裝置上的，然後在用戶端，會用來存取您的裝置。

Host （主機） 系統執行 Windows Server 2012 R2 上執行本文所述的步驟。

## <a name="connect-through-http"></a>透過 HTTP 連線

連線到 Windows PowerShell StorSimple 透過 HTTP 工作階段提供更高的安全性比透過循序主控台 StorSimple 裝置的連線。 雖然這不是最安全的方法，則可在受信任的網路上。

若要設定 [遠端管理，您可以使用 [Azure 傳統入口網站或循序主控台。 選取下列程序︰

- [若要啟用遠端管理透過 HTTP 使用 Azure 傳統入口網站](#use-the-azure-classic-portal-to-enable-remote-management-over-http)

- [使用循序 HTTP 上啟用遠端管理主控台](#use-the-serial-console-to-enable-remote-management-over-http)

啟用遠端管理之後，使用下列程序來連線遠端準備用戶端。

- [準備用戶端遠端的連線](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-http"></a>若要啟用遠端管理透過 HTTP 使用 Azure 傳統入口網站 

若要啟用遠端管理透過 HTTP Azure 傳統入口網站中，執行下列步驟。

#### <a name="to-enable-remote-management-through-the-azure-classic-portal"></a>若要啟用透過 Azure 傳統入口網站的遠端管理

1. 存取**裝置** > **設定**您的裝置。

2. 捲動到 [**遠端管理**] 區段。

3. 設定**啟用遠端管理**為**[是]**。

4. 您現在可以選擇使用 HTTP 連線。 （預設值是透過 HTTPS）。請確定已選取 [HTTP。

    >[AZURE.NOTE] 透過 HTTP 連線是可接受只在信任網路上。

6. 按一下 [在頁面底部的 [**儲存**]。

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>使用循序 HTTP 上啟用遠端管理主控台

在裝置啟用遠端管理主控台循序執行下列步驟。

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>若要啟用遠端管理透過裝置循序主控台

1. 在循序主控台] 功能表中選取選項 1。 如需有關如何在裝置上使用循序主控台的詳細資訊，請移至[StorSimple 透過裝置循序主控台的 Windows powershell 連線](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console)。

2. 出現提示時，請輸入︰`Enable-HcsRemoteManagement –AllowHttp`

3. 關於使用 HTTP 連線到裝置的安全性漏洞，會通知您。 出現提示時，請輸入**Y**以確認。

4. 確認 HTTP 已啟用輸入︰`Get-HcsSystem`

5. 請確認 [ **RemoteManagementMode** ] 欄位會顯示**HttpsAndHttpEnabled**。下圖顯示在 PuTTY 的這些設定。

     ![序列 HTTPS 及 HTTP 啟用](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>準備用戶端遠端的連線

若要啟用遠端管理用戶端上執行下列步驟。

#### <a name="to-prepare-the-client-for-remote-connection"></a>準備遠端連線的用戶端

1. 以系統管理員身分啟動 Windows PowerShell 工作階段。

2. 輸入下列命令以將 StorSimple 裝置的 IP 位址新增到用戶端的信任的主機清單︰ 

     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`

     取代您的裝置; 的 IP 位址 <*device_ip*>例如︰ 

     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`

3. 輸入下列命令以在變數儲存裝置認證︰ 

     *$cred = 取得認證*

4. 在出現的對話方塊中︰

    1. 此格式輸入使用者名稱︰ *device_ip\SSAdmin*。
    2. 輸入時設定精靈設定裝置有設定的裝置管理員密碼。 預設密碼是*密碼 1*。

7. 在裝置上啟動 Windows PowerShell 工作階段，輸入此命令︰

     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`

     >[AZURE.NOTE] 若要建立用於 Windows PowerShell 工作階段與 StorSimple 虛擬裝置，附加`–Port`參數指定 StorSimple 虛擬應用裝置中遠端設定公用連接埠。

     此時，您應該使用 Windows PowerShell 到遠端工作階段的裝置。

    ![PowerShell 遠端 http](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>連線到 HTTPS

連線到 Windows PowerShell StorSimple 透過 HTTPS 工作階段是從遠端連線到 Microsoft Azure StorSimple 裝置最安全和建議方法。 下列程序說明如何設定循序主控台與用戶端電腦，使您可以連線到 Windows PowerShell 用 StorSimple 使用 HTTPS。

若要設定 [遠端管理，您可以使用 [Azure 傳統入口網站或循序主控台。 選取下列程序︰

- [若要在 HTTPS 上啟用遠端管理使用 Azure 傳統入口網站](#use-the-azure-classic-portal-to-enable-remote-management-over-https)

- [若要在 HTTPS 上啟用遠端管理使用循序主控台](#use-the-serial-console-to-enable-remote-management-over-https)

啟用遠端管理之後，請使用下列程序準備主機遠端管理並從遠端主機連線至裝置。

- [Host （主機） 準備遠端管理](#prepare-the-host-for-remote-management)

- [從遠端主機連線至裝置](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-https"></a>若要在 HTTPS 上啟用遠端管理使用 Azure 傳統入口網站

若要在 HTTPS 上啟用遠端管理 Azure 傳統入口網站中，執行下列步驟。

#### <a name="to-enable-remote-management-over-https-from-the-azure-classic-portal"></a>若要在 HTTPS 上啟用遠端管理，從 Azure 傳統入口網站

1. 存取**裝置** > **設定**您的裝置。

2. 捲動到 [**遠端管理**] 區段。

3. 設定**啟用遠端管理**為**[是]**。

4. 您現在可以選擇以 HTTPS 進行連線。 （預設值是透過 HTTPS）。請確定已選取 [HTTPS。 

5. 按一下 [**下載遠端管理憑證**。 指定要儲存此檔案的位置。 您必須在用戶端或主機電腦，您將用來連線到裝置上安裝這個憑證。

6. 按一下 [在頁面底部的 [**儲存**]。

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>若要在 HTTPS 上啟用遠端管理使用循序主控台

在裝置啟用遠端管理主控台循序執行下列步驟。

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>若要啟用遠端管理透過裝置循序主控台

1. 在循序主控台] 功能表中選取選項 1。 如需有關如何在裝置上使用循序主控台的詳細資訊，請移至[StorSimple 透過裝置循序主控台的 Windows powershell 連線](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console)。

2. 出現提示時，請輸入︰ 

     `Enable-HcsRemoteManagement`

    在您的裝置上，此應該啟用 HTTPS。

3. 驗證輸入的已啟用 HTTPS: 

     `Get-HcsSystem`

    請確定 [ **RemoteManagementMode** ] 欄位會顯示**HttpsEnabled**。下圖顯示在 PuTTY 的這些設定。

     ![啟用的循序 HTTPS](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)

4. 從的輸出`Get-HcsSystem`、 複製序列值的裝置，並將其儲存以供日後使用。

    >[AZURE.NOTE] 序列值對應到憑證 CN 名稱。

5. 輸入以取得遠端管理憑證︰ 
 
     `Get-HcsRemoteManagementCert`

    會顯示類似以下的憑證。

    ![取得遠端管理憑證](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)

5. 資訊複製憑證**---開始憑證---** **---**結束憑證---到文字編輯器中例如 「 記事本 」，並將其儲存為.cer 檔案。 （您會複製此檔案至您的遠端主機當您準備主機時）。

    >[AZURE.NOTE] 若要產生新的憑證，請使用`Set-HcsRemoteManagementCert`指令程式。

### <a name="prepare-the-host-for-remote-management"></a>Host （主機） 準備遠端管理

若要使用的 HTTPS 工作階段的遠端連接準備主機電腦，請執行下列程序︰

- [匯入至根存放區的用戶端或遠端主機.cer 檔案](#to-import-the-certificate-on-the-remote-host)。

- [新增裝置序列您遠端主機上 hosts 檔案](#to-add-device-serial-numbers-to-the-remote-host)。

下面這種程序描述。

#### <a name="to-import-the-certificate-on-the-remote-host"></a>若要匯入遠端主機上的憑證

1. 以滑鼠右鍵按一下.cer 檔案，然後選取 [**安裝憑證**。 這會啟動 [憑證匯入精靈。

    ![憑證匯入精靈 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)

2. **儲存位置**，請選取**本機電腦**]，然後按 [**下一步**。

3. 選取 [**將下列存放區中的所有憑證**，，然後再按一下 [**瀏覽**]。 瀏覽至您的遠端主機的根存放區，然後按一下 [**下一步**。

    ![憑證匯入精靈 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)

4. 按一下 [**完成**]。 出現訊息，告訴您匯入成功。

    ![憑證匯入精靈 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>若要新增至遠端主機的裝置的序列值

1. 身為管理員，啟動 [記事本]，然後開啟 [位於 \Windows\System32\Drivers\etc hosts 檔案。

2. 下列三個項目新增至 hosts 檔案︰**資料 0 IP 位址**]、 [**控制器 0 固定的 IP 位址**] 及 [**控制器 1 固定的 IP 位址**。

3. 輸入您先前儲存裝置序列值。 下圖所示，請將此對應的 IP 位址。 如需控制器 0 與控制器 1，附加**Controller0**和**Controller1**結尾的序列值 （CN 名稱）。

    ![將 CN 名稱新增至 hosts 檔案](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)

4. 儲存 hosts 檔案。

### <a name="connect-to-the-device-from-the-remote-host"></a>從遠端主機連線至裝置

使用 Windows PowerShell SSL] 和 [從遠端主機] 或 [用戶端，輸入您的裝置上的 SSAdmin 工作階段。 SSAdmin 工作階段對應至選項 1[循序主控台](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console)] 功能表中的 [您的裝置。

在您要建立遠端的 Windows PowerShell 連線的電腦上執行下列程序。

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>若要在裝置上輸入 SSAdmin 工作階段，使用 Windows PowerShell 和 SSL

1. 以系統管理員身分啟動 Windows PowerShell 工作階段。

2. 輸入裝置的 IP 位址新增至用戶端的信任的主機︰

     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`

    其中 <*device_ip*> 是裝置的您; 的 IP 位址例如︰ 

     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`

3. 輸入以建立新的憑證︰ 

     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`

    其中 <*目標裝置的 IP*> 是您的裝置; 0 資料的 IP 位址例如， **10.126.173.90**前一個影像的 hosts 檔案中所示。 此外，提供您的裝置系統管理員的密碼。

4. 建立工作階段輸入︰

     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`

    -電腦名稱中的參數 cmdlet，提供 <*目標裝置序列*>。 此序列對應到資料在您的遠端主機; hosts 檔案中的 0 的 IP 位址例如， **SHX0991003G44MT**下圖所示。

5. [類型]: 

     `Enter-PSSession $session`

6. 您將需要等候幾分鐘，，然後您會透過 SSL 連線到您的裝置，透過 HTTPS。 您會看到一個訊息，表示您已連線到您的裝置。

    ![使用 SSL] 和 HTTPS 的 PowerShell 遠端處理](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>後續步驟

- 深入瞭解如何[使用 Windows PowerShell 來管理您的 StorSimple 裝置](storsimple-windows-powershell-administration.md)。

- 瞭解更多關於[使用 StorSimple 管理員服務來管理您的 StorSimple 裝置](storsimple-manager-service-administration.md)。
