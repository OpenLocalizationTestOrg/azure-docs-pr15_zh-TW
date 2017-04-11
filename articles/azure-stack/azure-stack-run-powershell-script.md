<properties
    pageTitle="部署 Azure 堆疊 POC |Microsoft Azure"
    description="瞭解如何準備 Azure 堆疊 POC 並進行部署 Azure 堆疊 POC 的 PowerShell 指令碼。"
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="erikje"/>

# <a name="deploy-azure-stack-poc"></a>部署 Azure 堆疊 POC
若要部署 Azure 堆疊 POC，首先必須[準備部署電腦](#prepare-the-deployment-machine)，然後[執行 PowerShell 部署指令碼](#run-the-powershell-deployment-script)。

## <a name="download-and-extract-microsoft-azure-stack-poc-tp2"></a>下載並解壓縮 Microsoft Azure 堆疊 POC TP2

在開始之前，請確定您至少 85 GB 的空間。

1. Azure 堆疊 POC TP2 下載包含包含下列 12 檔案，加總 ~ 20 GB 的 zip 檔案︰
    - 1 MicrosoftAzureStackPOC.EXE
2. 檢閱授權合約畫面和 [自動] 精靈的資訊，然後按一下 [**下一步**。
3. 檢視隱私權聲明畫面和 [自動] 精靈的資訊，然後按一下 [**下一步**。
4. 選取 [檔案目的地擷取，請按一下 [**下一步**]。
    - 預設值是︰ <drive letter>:\<目前資料夾 > \Microsoft Azure 堆疊 POC
5. 檢閱的目的地位置] 畫面及自動在精靈的資訊，，然後按一下 [**擷取**擷取 CloudBuilder.vhdx (~44.5 GB) 和 ThirdPartyLicenses.rtf 檔案。

> [AZURE.NOTE] 將檔案解壓縮後，您可以刪除 zip 檔案復原在電腦上的空間。 或者，您可以移動到另一個位置，因此，如果您需要重新部署您 zip 檔案不需要再次下載 zip 檔案。

## <a name="prepare-the-deployment-machine"></a>準備部署電腦

1. 請確定您可以實際連接到部署電腦，或實體主控台存取 （例如 KVM)。 當您重新開機部署電腦在步驟 9，您將需要存取。

2. 請確定部署電腦符合[最低需求](azure-stack-deploy.md)。 您可以使用[Azure 堆疊技術預覽 2 部署檢查](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b)以確認您的需求。

3. 為本機系統管理員，您 POC 的電腦登入。

4. 將 CloudBuilder.vhdx 檔案複製到 C:\CloudBuilder.vhdx。

    > [AZURE.NOTE] 如果您選擇不使用建議的指令碼來準備您 POC 主機電腦 （步驟 5-步驟 7），不要輸入 [啟動] 頁面在任何授權鍵。 試用版的 Windows Server 2016 圖像，並輸入授權金鑰會造成過期警告的訊息。

5. POC 在電腦上，執行下列 PowerShell 指令碼，若要下載 Azure 堆疊 TP2 支援檔案︰

    ```powershell
    # Variables
    $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/'
    $LocalPath = 'c:\AzureStack_TP2_SupportFiles'

    # Create folder
    New-Item $LocalPath -type directory

    # Download files
    ( 'BootMenuNoKVM.ps1', 'PrepareBootFromVHD.ps1', 'Unattend.xml', 'unattend_NoKVM.xml') | foreach { Invoke-WebRequest ($uri + $_) -OutFile ($LocalPath + '\' + $_) } 
    ```

    這個指令碼將 Azure 堆疊 TP2 支援檔案下載到 $LocalPath 參數所指定的資料夾。
    
6. 開啟提高權限的 PowerShell 主控台並變更目錄複製檔案的位置。
    - 11 MicrosoftAzureStackPOC-N.BIN （N 是 1-11）
7. 以滑鼠右鍵按一下 MicrosoftAzureStackPOC.EXE > 系統管理員身分執行。

8. 執行 PrepareBootFromVHD.ps1 指令碼。 這個指令碼並自動檔案時才可使用此建立以及所提供的其他支援指令碼。
    有五個參數，此 PowerShell 指令碼︰
    - CloudBuilderDiskPath （必填） – CloudBuilder.vhdx 主機上的路徑。
    - （選擇性） – DriverPath 可讓您在虛擬 HD 主機新增其他驅動程式。
    - （選擇性） – ApplyUnattend 指定此切換參數來自動化作業系統的設定。 如果指定，使用者必須提供在開機 （需要提供公開金鑰檔案 unattend_NoKVM.xml） 設定 OS AdminPassword。
    如果您不使用此參數，進一步自訂不使用一般 unattend.xml 檔案。 您必須 KVM 完成自訂之後，重新啟動。
    - AdminPassword （選擇性） – 只用設定 ApplyUnattend 參數時，需要的六個字元。
    - （選擇性） VHDLanguage – 指定 VHD 語言，預設為 「 EN-US]。
    指令碼記錄，並包含範例使用方式，雖然最常見的使用方式是︰
    
        `.\PrepareBootFromVHD.ps1 -CloudBuilderDiskPath C:\CloudBuilder.vhdx -ApplyUnattend`
    
        如果您執行此確切的命令，您必須輸入出現提示時 AdminPassword。

9. 指令碼完成後，您必須確認重新開機。 如果有其他使用者身分登入，此命令將會失敗。 如果命令失敗，請執行下列命令︰`Restart-Computer -force` 

10. Host （主機） 重新開機的 CloudBuilder.vhdx，部署持續位置的作業系統。

> [AZURE.IMPORTANT] Azure 堆疊需要存取網際網路，直接或透過透明 proxy。 TP2 POC 部署支援一個 NIC 的網路。 如果您有多個 Nic，而且請務必確保僅有一個已啟用 （所有其他人已停用） 之前執行部署指令碼下, 一節。

## <a name="run-the-powershell-deployment-script"></a>執行 PowerShell 部署指令碼

1. 為本機系統管理員，您 POC 的電腦登入。 使用上述步驟中指定的認證。

2. 開啟提高權限的 PowerShell 主控台。

3. 在 PowerShell 中執行此命令時︰`cd C:\CloudDeployment\Configuration`

4. 執行 [部署] 命令︰`.\InstallAzureStackPOC.ps1`

5. 在**輸入密碼**的提示時，輸入密碼，然後進行確認。 這是您所有的虛擬機器密碼。 請務必記錄它。

6. 輸入您的 Azure Active Directory 帳戶認證。 此使用者必須是全域管理員目錄租用戶中。

7. 部署程序可能需要幾個小時，在系統會自動重新啟動一次。

    > [AZURE.IMPORTANT] 如果您想要監視的部署進度，以 azurestack\AzureStackAdmin 登入。 如果您登入以本機系統管理員的電腦加入網域後，您就不會看到部署進度。 執行重新執行部署，請改為以登入 azurestack\AzureStackAdmin 確認它正在執行。

    部署順利完成，PowerShell 主控台會顯示︰**完成︰ 巨集指令 '部署'**。

    如果部署失敗，您可以嘗試[重新執行](azure-stack-rerun-deploy.md)。 或者，您可以[重新部署](azure-stack-redeploy.md)，從頭。

### <a name="deployment-script-examples"></a>部署指令碼範例

如果您的 AAD 身分識別只有一個 AAD 目錄相關聯︰

    cd C:\CloudDeployment\Configuration
    $adminpass = ConvertTo-SecureString "<LOCAL ADMIN PASSWORD>" -AsPlainText -Force
    $aadpass = ConvertTo-SecureString "<AAD GLOBAL ADMIN ACCOUNT PASSWORD>" -AsPlainText -Force
    $aadcred = New-Object System.Management.Automation.PSCredential ("<AAD GLOBAL ADMIN ACCOUNT>", $aadpass)
    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred

如果您的 AAD 身分識別與大於超過一個 AAD 目錄相關聯︰

    cd C:\CloudDeployment\Configuration
    $adminpass = ConvertTo-SecureString "<LOCAL ADMIN PASSWORD>" -AsPlainText -Force
    $aadpass = ConvertTo-SecureString "<AAD GLOBAL ADMIN ACCOUNT PASSWORD>" -AsPlainText -Force
    $aadcred = New-Object System.Management.Automation.PSCredential ("<AAD GLOBAL ADMIN ACCOUNT> example: user@AADDirName.onmicrosoft.com>", $aadpass)
    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred -AADDirectoryTenantName "<SPECIFIC AAD DIRECTORY example: AADDirName.onmicrosoft.com>"

如果您的環境中沒有 DHCP 功能，您必須包含下列其他參數至其中一個選項上方 （範例使用方式提供）︰

    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred
    -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1


### <a name="installazurestackpocps1-optional-parameters"></a>InstallAzureStackPOC.ps1 選擇性參數

| 參數 | 必要/選用 | 描述 |
| --------- | ----------------- | ----------- |
| AADAdminCredential | 選擇性 | 設定的 Azure Active Directory 使用者名稱和密碼。 組織識別碼或 Microsoft 帳戶，可以是這些 Azure 認證。 若要使用的 Microsoft 帳戶認證，忽略這個參數中指令程式。 部署時，省略此參數提示 Azure 驗證快顯功能表。 這樣會建立部署期間使用的驗證和重新整理權杖。 |
| AADDirectoryTenantName | 所需 | 設定租用戶的目錄。 使用此參數指定特定目錄 AAD 帳戶有權限管理多個目錄的位置。 完整名稱的格式 AAD 目錄租用戶<directoryName>。 onmicrosoft.com。 |
| AdminPassword | 所需 | 設定建立 POC 部署的一部分的所有虛擬機器本機系統管理員帳戶及所有其他的使用者帳戶。 此密碼必須符合主機上目前使用本機系統管理員的密碼。 |
| AzureEnvironment | 選擇性 | 選取您要註冊此 Azure 堆疊部署 Azure 環境。 選項包括 [*公用 Azure* *Azure-china （中國)* *Azure-美國政府*。 |
| EnvironmentDNS | 選擇性 | DNS 伺服器建立為 Azure 堆疊部署的一部分。 若要讓電腦內方案以解決戳記外的名稱，請提供您現有的基礎結構 DNS 伺服器。 在 [戳記 DNS 伺服器轉寄未知的名稱解析要求此伺服器。 |
| NatIPv4Address | 所需的 DHCP NAT 支援 | 設定 MAS BGPNAT01 的靜態 IP 位址。 如果 DHCP 無法指派存取網際網路有效的 IP 位址，僅使用此參數。 |
| NatIPv4DefaultGateway | 所需的 DHCP NAT 支援 | 設定預設閘道器的靜態 IP 位址用於 MAS BGPNAT01。 如果 DHCP 無法指派存取網際網路有效的 IP 位址，僅使用此參數。  |
| NatIPv4Subnet | 所需的 DHCP NAT 支援 | 用於 DHCP NAT 支援上 IP 子網路前置字元。 如果 DHCP 無法指派存取網際網路有效的 IP 位址，僅使用此參數。  |
| PublicVLan | 選擇性 | 設定虛擬區域網路 id。 只有當主機和 MAS BGPNAT01 必須設定虛擬區域網路 ID，才能存取實體網路 （和網際網路） 時，才能使用此參數。 例如，`.\InstallAzureStackPOC.ps1 –Verbose –PublicVLan 305` |
| 重新執行 | 選擇性 | 若要重新執行部署使用這個旗標。  使用所有的上一個輸入。 不支援重新輸入先前所提供的資料，因為是產生多個唯一值，並將其用於以供部署。 |
| TimeServer | 選擇性 | 若要指定特定時間伺服器，請使用此參數。 |

## <a name="next-steps"></a>後續步驟

[連線至 Azure 堆疊](azure-stack-connect-azure-stack.md)
