<properties
   pageTitle="建立 StorSimple 支援套件 |Microsoft Azure"
   description="瞭解如何建立、 解密，及編輯您 StorSimple 裝置支援套件。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2016"
   ms.author="alkohli" />


# <a name="create-and-manage-a-storsimple-support-package"></a>建立及管理 StorSimple 支援封裝

## <a name="overview"></a>概觀

StorSimple 支援套件是方便使用機制收集所有相關記錄，以協助 Microsoft 支援服務疑難排解任何 StorSimple 裝置問題。 收集的記錄加密和壓縮。

本教學課程中會包含來建立和管理支援套件的逐步指示。

## <a name="create-and-upload-a-support-package-in-the-azure-classic-portal"></a>建立並上傳 Azure 傳統入口網站中的支援套件

您可以建立並上傳至 Microsoft 支援網站透過 Azure 傳統入口網站中的服務**進行的維修作業**] 頁面的支援套件。

> [AZURE.NOTE] 上傳需要支援金鑰。 您的支援工程師應提供此您電子郵件中。

建立並上傳至支援網站的支援加密和壓縮套件 （.cab 檔案）。 支援工程師然後可以擷取此套件支援網站，取得疑難排解問題。

若要建立支援套件傳統入口網站中，執行下列步驟。

#### <a name="to-create-a-support-package-in-the-azure-classic-portal"></a>Azure 傳統入口網站中建立支援套件

1. 選取 [**裝置** > **進行的維修作業**。

2. 在**支援套件**] 區段中，選取 [**建立和上傳支援套件**]。

3. 在 [**建立] 和 [上傳支援套件**] 對話方塊中，執行下列動作︰

    ![建立支援套件](./media/storsimple-create-manage-support-package/IC740923.png)

    - 在**支援密碼**] 文字方塊中，輸入密碼。 Microsoft 技術支援工程師應該傳送給您此金鑰，透過電子郵件。

    - 選取核取方塊，以提供同意自動上傳至 Microsoft 支援網站的支援套件。

    - 按一下 [檢查] 圖示 ![檢查圖示](./media/storsimple-create-manage-support-package/IC740895.png).


## <a name="manually-create-a-support-package"></a>手動建立支援套件

在某些情況下，必須手動建立的支援封裝透過 Windows PowerShell StorSimple。 例如︰

- 如果您要移除您之前使用 Microsoft 支援共用的記錄檔中的機密資訊。

- 如果您無法連線問題因為套件上傳。

您可以使用 Microsoft 支援共用您手動產生的支援套件，將電子郵件。 執行下列步驟，以在 Windows PowerShell 中建立 StorSimple 支援封裝。

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>若要在 Windows PowerShell 中建立 StorSimple 支援封裝

1. 若要用來連線到您的 StorSimple 裝置的遠端電腦上以系統管理員身分開始的 Windows PowerShell 工作階段，輸入下列命令︰

    `Start PowerShell`

2. Windows PowerShell 工作階段，連線到您的裝置 SSAdmin 主控台︰

    - 在命令提示字元中，輸入︰

        `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`

    1. 在開啟的對話方塊中，輸入您的裝置管理員密碼。 預設密碼為︰

        `Password1`

        ![PowerShell [認證] 對話方塊](./media/storsimple-create-manage-support-package/IC740962.png)

    2. 選取**[確定]**。
    1. 在命令提示字元中，輸入︰

        `Enter-PSSession $MS`

3. 在開啟的工作階段，請輸入適當的命令。

    - 有密碼保護的網路共用，請輸入︰

        `Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`

        您會提示您輸入密碼，路徑網路共用的資料夾，以及加密複雜密碼 （因為支援封裝加密）。 支援套件然後會建立在指定的資料夾中。

    - 不是密碼保護的共用，您不需要`-Credential`參數。 輸入下列資訊︰

        `Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`

        指定的網路共用資料夾中的兩個控制站建立支援套件。 這是可以傳送到 Microsoft 支援服務疑難排解加密、 壓縮檔案。 如需詳細資訊，請參閱[連絡 Microsoft 支援人員](storsimple-contact-microsoft-support.md)。


### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>匯出 HcsSupportPackage cmdlet 參數
您可以使用下列參數與匯出 HcsSupportPackage 指令程式。

| 參數            | 必要/選用 | 描述                                                                                                                                                             |
|----------------------|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `-Path`                 | 所需          | 使用提供的支援套件位於網路共用資料夾的位置。                                                                 |
| `-EncryptionPassphrase` | 所需          | 使用提供協助加密的支援封裝複雜密碼。                                                                                                        |
| `-Credential`           | 選擇性          | 使用提供的網路共用資料夾的存取認證。                                                                                        |
| `-Force`                | 選擇性          | 使用略過加密複雜密碼的確認步驟。                                                                                                                |
| `-PackageTag`           | 選擇性          | 使用指定下*路徑*支援套件所在的目錄。 預設值是 [裝置名稱] 的 [目前的日期和 time:yyyy-MM-dd-HH-mm-ss]。       |
| `-Scope`                | 選擇性          | 指定為兩個控制站**叢集**（預設） 若要建立支援套件。 如果您想要建立僅適用於目前控制器套件，指定**控制站**。 |


## <a name="edit-a-support-package"></a>編輯支援套件

您有產生支援套件之後，您可能需要編輯封裝移除機密資訊。 這可包括大量、 裝置的 IP 位址和記錄檔從備份的名稱。

> [AZURE.IMPORTANT] 您僅可以編輯的已透過 Windows powershell 執行產生的 StorSimple 支援套件。 您無法編輯 StorSimple 管理員服務 Azure 傳統入口網站中建立的封裝。

以編輯支援套件之前上傳 Microsoft 支援網站上，請先解密支援套件、 編輯檔案，然後重新加密。 執行下列步驟。

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>若要編輯 StorSimple 在 Windows PowerShell 中的支援封裝

1. 產生支援套件中所述，[建立 StorSimple 的 Windows PowerShell 中支援套件](#to-create-a-support-package-in-windows-powershell-for-storsimple)。

2. 在本機上您的用戶端，[下載指令碼](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65)。

3. 匯入 Windows PowerShell 模組。 指定您要在其中下載指令碼的本機資料夾的路徑。 若要匯入模組，請輸入︰

    `Import-module <Path to the folder that contains the Windows PowerShell script>`

4. 所有檔案都是*.aes*檔案壓縮並加密。 若要解壓縮並解密的檔案，請輸入︰

    `Open-HcsSupportPackage <Path to the folder that contains support package files>`

    請注意，實際的檔案副檔名現在會顯示的所有檔案。

    ![編輯支援封裝](./media/storsimple-create-manage-support-package/IC750706.png)

5. 當系統提示您的加密複雜密碼時，輸入您所建立的支援套件時的複雜密碼。

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:EncryptionPassphrase: ****

6. 瀏覽至含有記錄檔的資料夾。 記錄檔現在會解壓縮並解密，因為這些會有原始副檔名。 修改這些檔案，以移除任何客戶特定資訊，例如大量名稱及裝置 IP 位址，然後儲存檔案。

7. 關閉與 gzip 壓縮圖片，並以 AES 256 加密這些檔案。 這是速度和轉接支援套件透過網路中的安全性。 若要壓縮並加密檔案中，輸入下列資訊︰

    `Close-HcsSupportPackage <Path to the folder that contains support package files>`

    ![編輯支援套件](./media/storsimple-create-manage-support-package/IC750707.png)

8. 出現提示時，請修改過的支援封裝提供加密複雜密碼。

        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****

9. 記新的複雜密碼，以便您可以使用 Microsoft 支援服務要求時共用。


### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>範例︰ 編輯中支援套件受密碼保護共用的檔案

下列範例會示範如何解密、 編輯及重新加密的支援封裝。

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>後續步驟

- 瞭解如何[使用支援封裝和裝置記錄檔給您的裝置部署中的疑難排解](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting)。

- 瞭解如何[使用 StorSimple 管理員服務，來管理您的 StorSimple 裝置](storsimple-manager-service-administration.md)。
