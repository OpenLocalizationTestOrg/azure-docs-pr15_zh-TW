<properties
    pageTitle="無法 Azure vm RDP |Microsoft Azure"
    description="當您無法連線至 Windows 中使用遠端桌面 Azure 虛擬機器疑難排解問題"
    keywords="遠端桌面錯誤、 遠端桌面連線錯誤，無法連線至 VM 遠端桌面疑難排解"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="10/26/2016"
    ms.author="iainfou"/>

# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>疑難排解遠端桌面連線至 Azure 虛擬機器

Windows Azure 虛擬機器 (VM) 遠端桌面通訊協定 (RDP) 連線失敗的各種原因，讓您無法存取您 VM。 問題可能是遠端桌面服務 VM、 網路連線，或遠端桌面用戶端 host （主機） 電腦上。 本文會引導您完成一些最常見的方法來解決 RDP 連線問題。 

如果您需要更多協助，這份文件中的任一點時，您也可以連絡[MSDN Azure](https://azure.microsoft.com/support/forums/)和堆疊溢位論壇 Azure 專家。 或者，您可以檔案 Azure 支援事件。 移至[Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [**取得支援**。

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>快速的疑難排解步驟
每個疑難排解步驟之後，請嘗試重新連線到 VM:

1. 重設遠端桌面設定。
2. 檢查網路安全性群組規則 / 雲端服務結束點。
3. 檢閱 VM 主控台記錄。
4. 核取 VM 資源健康狀況。
5. 重設 VM 密碼。
6. 重新啟動您 VM。
7. 重新部署您 VM。

如果您需要更多詳細的步驟和說明，請繼續閱讀。

> [AZURE.TIP] 如果您 VM [**連線**] 按鈕會呈現灰色，在入口網站，您一定要連線到 Azure 透過[快速傳送](../expressroute/expressroute-introduction.md)或[網站-VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)連線，您需要建立及指定您 VM 的公用 IP 位址，您才能使用 RDP。 您可以閱讀更多關於[Azure 中的公用 IP 位址](../virtual-network/virtual-network-ip-addresses-overview-arm.md)。


## <a name="ways-to-troubleshoot-rdp-issues"></a>疑難排解 RDP 問題的方法
您可以疑難排解 Vm 建立使用資源管理員部署模型，請使用下列方法之一︰

- [Azure 入口網站](#using-the-azure-portal)的絕佳若要快速重設 RDP 設定] 或 [使用者認證，您不需要安裝的 Azure 工具。
- [PowerShell 的 azure](#using-azure-powershell) -如果您習慣使用 PowerShell 提示中，快速重設使用 PowerShell 的 Azure cmdlet RDP 設定] 或 [使用者認證。

您也可以尋找疑難排解 Vm 建立使用[傳統部署模型](#troubleshoot-vms-created-using-the-classic-deployment-model)的步驟。


<a id="fix-common-remote-desktop-errors"></a>
## <a name="troubleshoot-using-the-azure-portal"></a>使用 [Azure 入口網站的疑難排解
每個疑難排解步驟之後，再次嘗試連線到您 VM。 如果您仍然無法連線，請嘗試下一個步驟。

1. **重設您的 RDP 連線**。 遠端連線會停用或 Windows 防火牆規則會封鎖 RDP，例如時，此疑難排解步驟重設 RDP 設定。

    選取您 VM Azure 入口網站中。 捲動到清單底部附近的 [**支援 + 疑難排解**] 區段的 [設定] 窗格。 按一下 [**重設密碼**] 按鈕。 重設**只組態**設定**模式**，然後按一下 [**更新**] 按鈕︰

    ![重設 RDP 設定 Azure 入口網站中](./media/virtual-machines-windows-troubleshoot-rdp-connection/reset-rdp.png)

2. **驗證網路安全性群組規則**。 此疑難排解步驟，驗證您的網路安全性群組，以允許 RDP 流量的規則。 TCP 連接埠 3389 已 RDP 的預設連接埠。 若要允許 RDP 傳輸規則可能不會自動建立當您建立您 VM。

    選取您 VM Azure 入口網站中。 按一下 [設定] 窗格**的網路介面**。

    ![檢視 VM Azure 入口網站中的網路介面](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-network-interfaces.png)

    從 （沒有通常只有一個） 清單中選取您的網路介面︰

    ![選取 [Azure 入口網站中的 [網路介面](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-interface.png)

    選取要檢視您的網路介面相關聯的網路安全性群組的**網路安全性群組**︰

    ![選取網路安全性群組 Azure 入口網站中](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-nsg.png)

    驗證輸入的規則的存在，可讓 TCP 連接埠 3389 RDP 流量。 下列範例顯示有效的安全性規則，允許 RDP 流量。 您可以看到`Service`和`Action`正確設定︰

    ![驗證 RDP NSG Azure 入口網站中的規則](./media/virtual-machines-windows-troubleshoot-rdp-connection/verify-nsg-rules.png)

    如果您沒有規則，可讓 RDP 流量，[建立網路安全性群組規則](virtual-machines-windows-nsg-quickstart-portal.md)。 允許 TCP 連接埠 3389。

3. **檢閱 VM 開機診斷**。 此疑難排解步驟檢閱 VM 主控台記錄 VM 如果報告問題。 並非所有 Vm 都有開機診斷啟用，可能是選擇性的此疑難排解步驟。
    
    特定的疑難排解步驟本文中的範圍之內，但可能會影響 RDP 連線更多問題。 如需有關檢閱主控台記錄和 VM 螢幕擷取畫面的詳細資訊，請參閱[Vm 開機診斷](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)。

4. **檢查 VM 資源健康狀況**。 此疑難排解步驟確認沒有可能會影響連線至 VM Azure 平台的已知的問題。

    選取您 VM Azure 入口網站中。 捲動到清單底部附近的 [**支援 + 疑難排解**] 區段的 [設定] 窗格。 按一下 [**資源健康狀況**] 按鈕。 狀況良好 VM 報告為**可用**︰

    ![檢查 VM Azure 入口網站中的資源健全狀況](./media/virtual-machines-windows-troubleshoot-rdp-connection/check-resource-health.png)

5. **重設使用者的認證**。 當您不確定或忘記認證，此疑難排解步驟重設本機系統管理員帳戶的密碼。

    選取您 VM Azure 入口網站中。 捲動到清單底部附近的 [**支援 + 疑難排解**] 區段的 [設定] 窗格。 按一下 [**重設密碼**] 按鈕。 確認**模式**設定為 [**重設密碼**，然後輸入您的使用者名稱和新密碼。 最後，按一下 [**更新**] 按鈕︰

    ![重設 Azure 入口網站中的使用者認證](./media/virtual-machines-windows-troubleshoot-rdp-connection/reset-password.png)

6. **重新啟動您 VM**。 此疑難排解步驟可修正 VM 本身有任何基礎問題。

    選取您 VM Azure 入口網站中，按一下 [**概觀**] 索引標籤。 按一下 [**重新啟動**] 按鈕︰

    ![重新啟動 VM Azure 入口網站](./media/virtual-machines-windows-troubleshoot-rdp-connection/restart-vm.png)

7. **重新部署您 VM**。 此疑難排解步驟重新您 VM 內 Azure 修正任何基本平台或網路問題的另一個主機。

    選取您 VM Azure 入口網站中。 捲動到清單底部附近的 [**支援 + 疑難排解**] 區段的 [設定] 窗格。 按一下 [**部署**] 按鈕，然後再按一下 [**重新部署**︰

    ![重新部署 VM Azure 入口網站中](./media/virtual-machines-windows-troubleshoot-rdp-connection/redeploy-vm.png)

    這項作業完成後，請暫時磁碟資料遺失，而更新動態 VM 與相關聯的 IP 位址。

如果您仍然遇到 RDP 問題，您可以[開啟支援要求](https://azure.microsoft.com/support/options/)或讀取[詳細的 RDP 疑難排解的概念與步驟](virtual-machines-windows-detailed-troubleshoot-rdp.md)。


## <a name="troubleshoot-using-azure-powershell"></a>使用 PowerShell 的 Azure 進行疑難排解
如果您未，[安裝和設定最新 PowerShell 的 Azure](../powershell-install-configure.md)。

下列範例使用變數例如`myResourceGroup`， `myVM`，及`myVMAccessExtension`。 您自己的值取代這些變數名稱和位置。

> [AZURE.NOTE] 您重設使用者認證與 RDP 設定，藉由[設定 AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) PowerShell 指令程式。 在下列範例中，`myVMAccessExtension`是您指定的程序的一部分的名稱。 如果您先前使用過 VMAccessAgent，您可以使用取得副檔名為現有的名稱`Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"`檢查 VM 的屬性。 若要檢視的名稱，查看在輸出的 「 延伸模組 」 區段。

每個疑難排解步驟之後，再次嘗試連線到您 VM。 如果您仍然無法連線，請嘗試下一個步驟。

1. **重設您的 RDP 連線**。 遠端連線會停用或 Windows 防火牆規則會封鎖 RDP，例如時，此疑難排解步驟重設 RDP 設定。

    以下範例將重設為在名為 VM RDP 連線`myVM`中`WestUS`位置名為資源群組`myResourceGroup`:

    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```

2. **驗證網路安全性群組規則**。 此疑難排解步驟，驗證您的網路安全性群組，以允許 RDP 流量的規則。 TCP 連接埠 3389 已 RDP 的預設連接埠。 若要允許 RDP 傳輸規則可能不會自動建立當您建立您 VM。

    首先，您的網路安全性群組指派所有設定資料`$rules`變數。 下列範例會取得資訊名稱為網路安全性群組`myNetworkSecurityGroup`[資源] 群組中名為`myResourceGroup`:

    ```powershell
    $rules = Get-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```

    現在，檢視已為此網路的安全性] 群組中的規則。 驗證規則存在於，若要允許輸入連線的 TCP 連接埠 3389，如下所示︰

    ```powershell
    $rules.SecurityRules
    ```

    下列範例顯示有效的安全性規則，允許 RDP 流量。 您可以看到`Protocol`， `DestinationPortRange`， `Access`，及`Direction`正確設定︰

    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```

    如果您沒有規則，可讓 RDP 流量，[建立網路安全性群組規則](virtual-machines-windows-nsg-quickstart-powershell.md)。 允許 TCP 連接埠 3389。

3. **重設使用者的認證**。 此疑難排解步驟重設您指定當您不確定或忘記，認證本機系統管理員帳戶的密碼。

    首先，指定使用者名稱和新密碼指派的認證以`$cred`變數，如下所示︰

    ```powershell
    $cred=Get-Credential
    ```

    現在，更新您的 VM 上的憑證。 下列範例會更新在名為 VM 認證`myVM`中`WestUS`位置名為資源群組`myResourceGroup`:

    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```

4. **重新啟動您 VM**。 此疑難排解步驟可修正 VM 本身有任何基礎問題。

    下列範例會重新命名 VM `myVM` [資源] 群組中名為`myResourceGroup`:

    ```powershell
    Restart-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

5. **重新部署您 VM**。 此疑難排解步驟重新您 VM 內 Azure 修正任何基本平台或網路問題的另一個主機。

    下列範例會重新命名的 VM`myVM`中`WestUS`位置名為資源群組`myResourceGroup`:

    ```powershell
    Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

如果您仍然遇到 RDP 問題，您可以[開啟支援要求](https://azure.microsoft.com/support/options/)或讀取[詳細的 RDP 疑難排解的概念與步驟](virtual-machines-windows-detailed-troubleshoot-rdp.md)。


## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>疑難排解 Vm 建立使用傳統部署模型

每個疑難排解步驟之後，請嘗試重新連線到 VM。

1. **重設您的 RDP 連線**。 遠端連線會停用或 Windows 防火牆規則會封鎖 RDP，例如時，此疑難排解步驟重設 RDP 設定。

    選取您 VM Azure 入口網站中。 按一下 [ **...更多**] 按鈕，然後按一下 [**重設遠端存取**︰

    ![重設 RDP 設定 Azure 入口網站中](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-reset-rdp.png)

2.  **驗證雲端服務結束點**。 此疑難排解步驟，驗證您的雲端服務，以允許 RDP 流量的結束點。 TCP 連接埠 3389 已 RDP 的預設連接埠。 若要允許 RDP 傳輸規則可能不會自動建立當您建立您 VM。

    選取您 VM Azure 入口網站中。 按一下 [**結束點**] 按鈕，可檢視目前設定為您 VM 結束點]。 確認有端點，讓 TCP 連接埠 3389 RDP 流量。
    
    下列範例顯示允許 RDP 流量的有效結束點︰

    ![驗證 Azure 入口網站中的雲端服務端點](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)

    如果您沒有端點，可讓 RDP 流量，[建立雲端服務結束點](virtual-machines-windows-classic-setup-endpoints.md)。 允許私人連接埠 3389 TCP。

3. **檢閱 VM 開機診斷**。 此疑難排解步驟檢閱 VM 主控台記錄 VM 如果報告問題。 並非所有 Vm 都有開機診斷啟用，可能是選擇性的此疑難排解步驟。
    
    特定的疑難排解步驟本文中的範圍之內，但可能會影響 RDP 連線更多問題。 如需有關檢閱主控台記錄和 VM 螢幕擷取畫面的詳細資訊，請參閱[Vm 開機診斷](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)。

4. **檢查 VM 資源健康狀況**。 此疑難排解步驟確認沒有可能會影響連線至 VM Azure 平台的已知的問題。

    選取您 VM Azure 入口網站中。 捲動到清單底部附近的 [**支援 + 疑難排解**] 區段的 [設定] 窗格。 按一下 [**資源健康狀況**] 按鈕。 狀況良好 VM 報告為**可用**︰

    ![檢查 VM Azure 入口網站中的資源健全狀況](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-check-resource-health.png)

5. **重設使用者的認證**。 此疑難排解步驟重設您指定當您不確定或忘記認證本機系統管理員帳戶的密碼。

    選取您 VM Azure 入口網站中。 捲動到清單底部附近的 [**支援 + 疑難排解**] 區段的 [設定] 窗格。 按一下 [**重設密碼**] 按鈕。 輸入您的使用者名稱和新密碼。 最後，按一下 [**儲存**] 按鈕︰

    ![重設 Azure 入口網站中的使用者認證](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-reset-password.png)

6. **重新啟動您 VM**。 此疑難排解步驟可修正 VM 本身有任何基礎問題。

    選取您 VM Azure 入口網站中，按一下 [**概觀**] 索引標籤。 按一下 [**重新啟動**] 按鈕︰

    ![重新啟動 VM Azure 入口網站](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-restart-vm.png)
    
如果您仍然遇到 RDP 問題，您可以[開啟支援要求](https://azure.microsoft.com/support/options/)或讀取[詳細的 RDP 疑難排解的概念與步驟](virtual-machines-windows-detailed-troubleshoot-rdp.md)。


## <a name="troubleshoot-specific-rdp-errors"></a>疑難排解特定 RDP 錯誤
嘗試連線到您透過 RDP VM 時，您可能會遇到特定的錯誤訊息。 以下是最常見的錯誤訊息︰

- [遠端工作階段中斷，因為沒有遠端桌面授權伺服器提供授權](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdplicense)。
- [遠端桌面找不到"名稱"的電腦](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpname)。
- [驗證錯誤。無法連線到本機安全性授權](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpauth)。
- [Windows 安全性錯誤︰ 無法解決您的認證](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#wincred)。
- [[這部電腦無法連線到遠端電腦](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpconnect)]。


## <a name="additional-resources"></a>其他資源
如果沒有任何這些錯誤發生，您仍然無法連線到遠端桌面透過 VM，閱讀詳細[疑難排解指南遠端桌面版](virtual-machines-windows-detailed-troubleshoot-rdp.md)的。

- [Azure IaaS (Windows) 診斷程式套件](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)
- 如需疑難排解存取 VM 上執行應用程式中的步驟，請參閱[疑難排解存取 Azure VM 上執行應用程式](virtual-machines-linux-troubleshoot-app-connection.md)。
- 如果您有使用安全命令介面 (SSH) 連線到 Linux VM Azure 中的問題，請參閱[疑難排解 SSH 連線 Linux VM Azure 中](virtual-machines-linux-troubleshoot-ssh-connection.md)。
