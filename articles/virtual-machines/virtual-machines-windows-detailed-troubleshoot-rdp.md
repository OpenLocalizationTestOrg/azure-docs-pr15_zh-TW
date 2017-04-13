<properties
    pageTitle="詳細遠端桌面疑難排解 |Microsoft Azure"
    description="檢視詳細的疑難排解步驟遠端桌面錯誤，您無法在其中至 Azure 中的 Windows 虛擬機器"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"
    keywords="無法連線到遠端桌面，疑難排解遠端桌面、 遠端桌面無法連線，遠端桌面錯誤、 遠端桌面疑難排解、 遠端桌面的問題
"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>取得詳細的疑難排解步驟 Azure 中的 Windows Vm 遠端桌面連線問題

本文提供詳細診斷並修復 windows Azure 虛擬機器複雜遠端桌面錯誤的疑難排解步驟。

> [AZURE.IMPORTANT] 若要避免常見的遠端桌面錯誤，請務必閱讀，再繼續執行[遠端桌面版的基本的疑難排解文件](virtual-machines-windows-troubleshoot-rdp-connection.md)。

您可能會遇到不類似任何一項[基本的遠端桌面疑難排解指南](virtual-machines-windows-troubleshoot-rdp-connection.md)中包含特定錯誤訊息的遠端桌面錯誤訊息。 請遵循下列步驟，判斷遠端桌面 (RDP) 用戶端為何無法連線至 Azure VM 上 RDP 服務。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

如果您需要更多協助，這份文件中的任一點時，您也可以連絡 Azure 專家[MSDN Azure 和堆疊溢位論壇](https://azure.microsoft.com/support/forums/)上。 或者，您也可以檔案 Azure 支援事件。 移至[Azure 支援網站](https://azure.microsoft.com/support/options/)，然後按一下 [**取得支援**。 如需使用 Azure 支援的資訊，請閱讀[Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。


## <a name="components-of-a-remote-desktop-connection"></a>遠端桌面連線的元件

連線至 RDP 涉及下列元件︰

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_0.png)

在進行之前，它可能協助心理檢閱以來 VM 的最後一個成功遠端桌面連線的變更。 例如︰

- 已變更 VM 或包含 （也稱為虛擬 IP 位址[VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)） VM 雲端服務的公用 IP 位址。 RDP 錯誤可能是因為您的 DNS 用戶端快取還是具有註冊 DNS 名稱*舊的 IP 位址*。 清除您的 DNS 用戶端快取，然後再次嘗試連線 VM。 或嘗試直接與新 VIP 連線。
- 您使用協力廠商應用程式來管理遠端桌面連線，而不是使用 Azure 入口網站所產生的連線。 驗證應用程式設定包含正確的 TCP 連接埠遠端桌面流量。 您可以檢查傳統虛擬機器中[Azure 入口網站](https://portal.azure.com)中，此連接埠按一下 [VM 的設定] > 結束點。


## <a name="preliminary-steps"></a>基本步驟

進行詳細的疑難排解，之前

- 檢查虛擬機器 Azure 傳統入口網站或任何明顯問題的 Azure 入口網站中的狀態。
- 遵循[基本的疑難排解指南中的一般 RDP 錯誤修正快速步驟](virtual-machines-windows-troubleshoot-rdp-connection.md#quick-troubleshooting-steps)。


請嘗試這些步驟後，透過遠端桌面 vm 重新連線。


## <a name="detailed-troubleshooting-steps"></a>詳細的疑難排解步驟

遠端桌面用戶端可能無法連線到遠端桌面服務，由於在下列來源問題，因此 Azure VM 上︰

- [遠端桌面用戶端電腦](#source-1-remote-desktop-client-computer)
- [組織內部網路邊緣裝置](#source-2-organization-intranet-edge-device)
- [雲端服務端點，以及存取控制清單 (ACL)](#source-3-cloud-service-endpoint-and-acl)
- [網路安全性群組](#source-4-network-security-groups)
- [Windows Azure VM](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>來源 1︰ 遠端桌面用戶端電腦

請確認您的電腦可以讓遠端桌面連線至另一個內部部署，Windows 電腦。

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_1.png)

如果您無法，檢查您的電腦上的下列設定︰

- 設定的本機防火牆封鎖了遠端桌面流量。
- 本機安裝讓遠端桌面連線的用戶端 proxy 軟體。
- 本機安裝網路監視讓遠端桌面連線的軟體。
- 其他類型的安全性軟體監視流量或允許/不允許特定類型的流量使遠端桌面連線。

在下列情況下，請暫時停用軟體，並嘗試連線到內部部署電腦，以透過遠端桌面。 如果這種方式，您可以找出實際的原因中，使用您的網路系統管理員以修正軟體設定為允許遠端桌面連線。

## <a name="source-2-organization-intranet-edge-device"></a>來源 2︰ 組織內部網路邊緣裝置

確認電腦連線至網際網路進行 Azure 虛擬機器遠端桌面連線。

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_2.png)

如果您沒有直接連線到網際網路的電腦，建立並使用新的 Azure 虛擬機器中的資源群組] 或 [雲端服務測試。 如需詳細資訊，請參閱[建立虛擬機器執行 Windows Azure 中](virtual-machines-windows-hero-tutorial.md)。 測試之後，您可以刪除虛擬機器和資源群組或雲端服務。

如果直接連接至網際網路的電腦，您可以建立遠端桌面連線，請檢查您的組織內部網路邊緣的裝置︰

- 內部防火牆封鎖 HTTPS 連線至網際網路。
- Proxy 伺服器，防止遠端桌面連線。
- 侵入偵測或網路監視邊緣網路使遠端桌面連線的裝置上執行軟體。

使用您的網路系統管理員，若要修正您的組織內部網路邊緣裝置設定，以允許 HTTPS 型遠端桌面連線至網際網路。

## <a name="source-3-cloud-service-endpoint-and-acl"></a>來源 3︰ 雲端服務端點和 ACL

建立使用傳統部署模型 Vm，請用的另一個相同的雲端服務或虛擬網路中的 Azure VM 進行遠端桌面連線至您的 Azure VM。

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_3.png)

> [AZURE.NOTE] 資源管理員中建立的虛擬機器，直接跳到[來源 4︰ 網路安全性群組](#source-4-network-security-groups)。

如果您沒有在同一個雲端服務或虛擬網路中的另一個虛擬機器，建立一個。 請依照[建立虛擬機器執行 Windows Azure 中](virtual-machines-windows-hero-tutorial.md)的步驟進行。 測試完畢之後，請刪除測試虛擬機器。

如果您可以透過遠端桌面連線到虛擬機器中的相同的雲端服務或虛擬網路，請核取這些設定︰

- 在目標 VM 遠端桌面流量的端點組態︰ 端點的私人 TCP 連接埠必須符合要在其接聽 VM 的遠端桌面服務的 TCP 連接埠 （預設為 3389）。
- 遠端桌面流量端點目標 VM ACL: Acl 可讓您指定允許或拒絕連入流量從網際網路根據其來源 IP 位址。 設定錯誤的 Acl 可以防止端點連入遠端桌面流量。 核取您 Acl，確定該連入流量從您的 proxy 的公用 IP 位址或允許其他 edge server。 如需詳細資訊，請參閱[什麼是網路存取控制清單 (ACL)？](../virtual-network/virtual-networks-acl.md)

若要查看端點是否問題的來源，移除目前的結束點，然後建立新的範本，在 [外部連接埠號碼的範圍 49152 – 65535 選擇隨機連接埠。 如需詳細資訊，請參閱[如何設定虛擬機器結束點](virtual-machines-windows-classic-setup-endpoints.md)。

## <a name="source-4-network-security-groups"></a>來源 4︰ 網路安全性群組

網路安全性群組允許微調控制允許傳入和傳出流量。 您可以建立規則橫跨子網路和雲端 Azure 虛擬網路中的服務。 檢查您的網路安全性群組規則，以確保允許的遠端桌面流量從網際網路︰

- 在 Azure 入口網站中，選取您 VM
- 按一下 [**所有設定** | **網路介面**，然後選取您的網路介面。
- 按一下 [**所有設定** | **網路安全性群組**，然後選取您的網路安全性群組。
- 按一下 [**所有設定** | **輸入的安全性規則**，並確保您已設定規則，允許 RDP TCP 連接埠 3389 上的。
    - 如果您沒有規則，請按一下 [**新增**]，以建立規則]。 輸入**TCP**通訊協定，然後**3389**目的地連接埠範圍。
    - 請確定動作設定為**允許**，然後按一下 [確定] 儲存新輸入的規則]。


如需詳細資訊，請參閱[什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md)

## <a name="source-5-windows-based-azure-vm"></a>來源 5: Windows Azure VM

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_5.png)

使用[Azure IaaS (Windows) 診斷套件](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)，因為 Azure 虛擬機器本身是否為錯誤。 如果此診斷封裝無法解決**Azure vm （需要重新開機） RDP 連線**問題，請遵循[本文](virtual-machines-windows-reset-rdp.md)中的指示進行。 本文會重設遠端桌面服務虛擬機器上︰

- 啟用 「 遠端桌面 」 Windows 防火牆預設的規則 （TCP 連接埠 3389）。
- 啟用遠端桌面連線 HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections 登錄值設定為 0。

再次嘗試連線從您的電腦。 如果您仍然無法透過遠端桌面連線，檢查下列可能發生的問題︰

- 在目標 VM 未執行遠端桌面服務。
- TCP 連接埠 3389 上未接聽的遠端桌面服務。
- 在 Windows 防火牆或另一個本機防火牆有輸出規則使遠端桌面流量。
- 入侵偵測或監控 Azure 虛擬機器上執行軟體的網路不讓遠端桌面連線。

建立使用傳統的部署模型 Vm，您可以使用 Azure 虛擬機器的遠端 PowerShell 的 Azure 工作階段。 首先，您必須安裝虛擬機器裝載雲端服務的憑證。 移至 [[設定安全遠端 PowerShell 存取至 Azure 虛擬機器](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe)和**InstallWinRMCertAzureVM.ps1**指令碼檔案下載到您的本機電腦。

如果您未，接下來，請安裝 PowerShell 的 Azure。 瞭解[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。

接下來，開啟 PowerShell 的 Azure 命令提示字元，並變更目前的資料夾**InstallWinRMCertAzureVM.ps1**指令碼檔的位置。 若要執行 Azure PowerShell 指令碼，您必須設定正確執行原則。 執行 [**取得 ExecutionPolicy** ] 命令，決定您目前的原則等級。 設定適當的層級的相關資訊，請參閱[設定 ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx)。

接下來，填入您的 Azure 訂閱名稱、 雲端服務名稱和您的虛擬機器名稱 (移除 < 及 > 字元)，然後執行下列命令。

    $subscr="<Name of your Azure subscription>"
    $serviceName="<Name of the cloud service that contains the target virtual machine>"
    $vmName="<Name of the target virtual machine>"
    .\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

您可以從顯示的 [**取得 AzureSubscription** ] 命令的_SubscriptionName_屬性取得正確的訂閱名稱。 您可以取得雲端服務名稱虛擬機器_ServiceName_中的資料行的 [**取得 AzureVM**命令顯示。

確認您是否有新的憑證。 開啟憑證嵌入式管理單元目前的使用者，然後在 [**受信任的根憑證授權單位 \ 憑證**] 資料夾中尋找。 您應該會看到您發出到] 欄中的雲端服務的 DNS 名稱的憑證 (範例︰ cloudservice4testing.cloudapp.net)。

接下來，請使用這些命令來啟動遠端 PowerShell 的 Azure 工作階段。

    $uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
    $creds = Get-Credential
    Enter-PSSession -ConnectionUri $uri -Credential $creds

輸入有效的系統管理員認證，您應該會看到類似下列 PowerShell 的 Azure 提示︰

    [cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

此提示中的第一部分是您的雲端服務名稱包含目標 VM，可能是不同的 「 cloudservice4testing.cloudapp.net 」。 您現在可以發出 PowerShell 的 Azure 提及調查問題此雲端服務的命令，並更正設定。

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>若要手動修正接聽 TCP 連接埠遠端桌面服務

如果您無法執行的遠端 PowerShell 的 Azure 工作階段提示**Azure vm （需要重新開機） RDP 連線**問題， [Azure IaaS (Windows) 診斷套件](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)，請執行此命令。

    Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

PortNumber 屬性會顯示目前的連接埠號碼。 視需要變更遠端桌面連接埠為預設值 (3389) 的數字後，使用此命令。

    Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

驗證的連接埠已變更為 3389 使用此命令。

    Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

使用這個命令來結束遠端 PowerShell 的 Azure 工作階段。

    Exit-PSSession

確認遠端桌面端點 Azure vm 也使用 TCP 連接埠 3398 為其內部連接埠。 重新啟動 Azure VM，然後再試一次的遠端桌面連線。


## <a name="additional-resources"></a>其他資源

[Azure IaaS (Windows) 診斷程式套件](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[如何重設密碼或遠端桌面服務的 Windows 虛擬機器](virtual-machines-windows-reset-rdp.md)

[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)

[疑難排解安全命令介面 (SSH) 連線到 Linux 型 Azure 虛擬機器](virtual-machines-linux-troubleshoot-ssh-connection.md)

[疑難排解執行 Azure 虛擬機器上的應用程式存取](virtual-machines-linux-troubleshoot-app-connection.md)
