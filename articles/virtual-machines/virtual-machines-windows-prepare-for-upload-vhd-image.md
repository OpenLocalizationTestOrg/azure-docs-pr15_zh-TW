<properties
    pageTitle="準備 Windows VHD 上傳至 Azure |Microsoft Azure"
    description="準備 Windows VHD 上傳至 Azure 之前的建議的作法"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="genlin"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/18/2016"
    ms.author="glimoli;genli"/>

# <a name="prepare-a-windows-vhd-to-upload-to-azure"></a>準備 Windows VHD 上傳至 Azure
若要上傳至 Azure Windows VM 來自內部部署，您必須正確準備虛擬硬碟 (VHD)。 有數種建議的步驟，您可以先將 VHD 上傳至 Azure 完成。 本文將示範如何準備 Windows VHD 上傳至 Microsoft Azure，同時也會說明[何時及如何使用 Sysprep](#step23)。

## <a name="prepare-the-virtual-disk"></a>準備虛擬磁碟

>[AZURE.NOTE] 
> Azure 只支援[產生 1 虛擬機器](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx)中的 VHD 檔案格式。 Azure 中不支援較新的 VHDX 格式。 
>
> VHD 必須是固定的大小，不動態。 如有需要詳細說明下方的指示，從 VHDX 或動態磁碟轉換。 最大可容許的 VHD 是 1023 GB。


請確認 Windows VHD 正常運作本機伺服器上。 VM 本身之前，先轉換或上傳至 Azure 中的任何錯誤的解決方法。

如果您需要將虛擬磁碟轉換為 Azure 所需的格式，請使用下列各節所述的兩種方法之一。 執行任何虛擬磁碟轉換程序或 Sysprep 之前，先備份 VM。

### <a name="convert-using-hyper-v-manager"></a>轉換使用 HYPER-V 管理員
- 開啟 HYPER-V 管理員，然後選取您在左側的本機電腦。 在其上方的功能表，按一下 [**巨集指令** > **編輯磁碟**。
    - 在 [**尋找虛擬硬碟**] 畫面中，瀏覽]，然後選取虛擬磁碟。
    - 選取下一個畫面上的 [**轉換**
        - 如果您需要從 VHDX 轉換，請選取 [ **VHD** ，然後按一下 [**下一步**
        - 如果您需要從動態磁碟轉換，請選取 [**固定的大小]** ，然後按一下 [**下一步**

    - 瀏覽並選取**新的 VHD 檔案的路徑**。
    - 按一下 [**完成**] 以關閉。

### <a name="convert-using-powershell"></a>使用 PowerShell
您可以轉換虛擬磁碟使用[轉換 VHD PowerShell 指令程式](http://technet.microsoft.com/library/hh848454.aspx)。 在下列範例中，我們會從 VHDX 轉換成 VHD，並從動態轉換成固定的類型︰

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

### <a name="convert-from-vmware-vmdk-disk-format"></a>從 VMware VMDK 磁碟格式轉換
如果您有 Windows VM 圖像[VMDK 檔案格式](https://en.wikipedia.org/wiki/VMDK)時，將它轉換 VHD 使用[Microsoft 虛擬機器轉換程式](https://www.microsoft.com/download/details.aspx?id=42497)。 閱讀部落格[如何轉換至 HYPER-V VHD VMware VMDK](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) ，如需詳細資訊。

## <a name="prepare-windows-configuration-for-upload"></a>準備上傳的 Windows 的設定

> [AZURE.NOTE] 執行下列命令所有具有[系統管理權限](https://technet.microsoft.com/library/cc947813.aspx)。

1. 移除任何持續的靜態路由，路由表格︰

    - 若要檢視路由表格，請執行`route print`。
    - 核取 [**持續路由**節]。 如果有一個持續路由，請使用[路由刪除](https://technet.microsoft.com/library/cc739598.apx)將其移除。

2. 移除 WinHTTP proxy:

    ```
    netsh winhttp reset proxy
    ```

3. 設定磁碟[Onlineall](https://technet.microsoft.com/library/gg252636.aspx)舊原則︰

    ```
    diskpart san policy=onlineall
    ```

4. 使用 Windows 版的國際標準時間 (UTC) 的時間，並將 Windows 時間 (w32time) 服務的啟動類型設定為 [**自動**︰

    ```
    REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
    sc config w32time start= auto
    ```


## <a name="configure-windows-services"></a>設定 Windows 服務
5. 請確定每個下列 Windows 服務會設定為**Windows 的預設值**。 設定記下下列清單中的啟動設定。 您可以執行下列命令來重設啟動設定︰

    ```
    sc config bfe start= auto

    sc config dcomlaunch start= auto

    sc config dhcp start= auto

    sc config dnscache start= auto

    sc config IKEEXT start= auto

    sc config iphlpsvc start= auto

    sc config PolicyAgent start= demand

    sc config LSM start= auto

    sc config netlogon start= demand

    sc config netman start= demand

    sc config NcaSvc start= demand

    sc config netprofm start= demand

    sc config NlaSvc start= auto

    sc config nsi start= auto

    sc config RpcSs start= auto

    sc config RpcEptMapper start= auto

    sc config termService start= demand

    sc config MpsSvc start= auto

    sc config WinHttpAutoProxySvc start= demand

    sc config LanmanWorkstation start= auto

    sc config RemoteRegistry start= auto
    ```


## <a name="configure-remote-desktop-configuration"></a>設定遠端桌面設定
6. 如果有任何繫結至遠端桌面通訊協定 (RDP) 接聽的自我簽署的憑證，請將其移除︰

    ```
    REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
    ```

    如需關於設定 RDP 接聽項的憑證的詳細資訊，請參閱[接聽程式在 Windows Server 的憑證設定](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/)

7. 設定 RDP 服務的[存留](https://technet.microsoft.com/library/cc957549.aspx)值︰

    ```
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
    ```

8. 設定 RDP 服務的驗證模式︰

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
    ```

9. 啟用 RDP 服務將下列子機碼新增至登錄機碼︰

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
    ```


## <a name="configure-windows-firewall-rules"></a>設定 Windows 防火牆規則
10. 允許 WinRM 到三個防火牆設定檔 （網域、 私人和公用），並啟用遠端 PowerShell 服務︰

    ```
    Enable-PSRemoting -force
    ```

11. 請確認下列來賓作業系統防火牆規則中的位置︰

    - 連入

    ```
    netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (LLMNR-UDP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Datagram-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Name-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (Pub-WSD-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (SSDP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD EventsSecure-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
    ```

    - 輸入與輸出

    ```
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes

    netsh advfirewall firewall set rule group="Core Networking" new enable=yes
    ```

    - 輸出

    ```
    netsh advfirewall firewall set rule dir=out name="Network Discovery (LLMNR-UDP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Datagram-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Name-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (Pub-WSD-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (SSDP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnPHost-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD Events-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD EventsSecure-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD-Out)" new enable=yes
    ```


## <a name="additional-windows-configuration-steps"></a>其他 Windows 設定步驟
12. 執行`winmgmt /verifyrepository`確認 Windows 管理儀器 (WMI) 存放庫一致。 如果將存放庫已損毀，請參閱[此部落格文章](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not)。

13. 請確定開機設定資料 (BCD) 設定符合下列動作︰

    ```
    bcdedit /set {bootmgr} integrityservices enable

    bcdedit /set {default} device partition=C:

    bcdedit /set {default} integrityservices enable

    bcdedit /set {default} recoveryenabled Off

    bcdedit /set {default} osdevice partition=C:

    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
    ```

14. 移除任何額外傳輸驅動程式介面篩選器，例如分析 TCP 封包的軟體。
15. 若要確保磁碟良好且一致，請執行`CHKDSK /f`] 命令。
16. 解除安裝協力廠商的軟體和相關實體元件或任何其他虛擬化技術驅動程式。
17. 請確認協力廠商應用程式不使用連接埠 3389。 此連接埠用於 Azure 中的 RDP 服務。 您可以使用`netstat -anob`命令，以檢查所使用的應用程式的連接埠。
18. 如果您要上傳 Windows VHD 網域控制站，請遵循[這些額外的步驟](https://support.microsoft.com/kb/2904015)來準備的磁碟。
19. 重新啟動 Windows 仍健全 VM 連接使用 RDP 連線。
20. 重設目前使用本機系統管理員的密碼並確認您可以透過 RDP 連線登入 Windows 使用此帳戶。  此存取權限會控制 」 允許透過登入遠端桌面服務 」 原則物件。 這個物件位於 [電腦設定 windows 設定安全性設定本機原則權限指派。 」


## <a name="install-windows-updates"></a>安裝 Windows 更新
22. 在 Windows 版安裝最新的更新。 如果不可行，請確定已安裝下列更新︰

    - [KB3137061](https://support.microsoft.com/kb/3137061)Microsoft Azure Vm 不復原網路中斷並出現資料損毀的問題

    - [KB3115224](https://support.microsoft.com/kb/3115224)針對正在執行 Windows Server 2012 R2 或 Windows Server 2012 主機的 Vm 的可靠性改良項目

    - [KB3140410](https://support.microsoft.com/kb/3140410)MS16 031︰ 安全性更新 Microsoft Windows 的地址提高權限︰ 2016 年 3 月 8 日

    - [KB3063075](https://support.microsoft.com/kb/3063075)當您執行 Windows Server 2012 R2 虛擬機器 Microsoft Azure 中記錄許多識別碼 129 事件

    - [KB3137061](https://support.microsoft.com/kb/3137061)Microsoft Azure Vm 不復原網路中斷並出現資料損毀的問題

    - [KB3114025](https://support.microsoft.com/kb/3114025)當您從 Windows 8.1 或 Server 2012 R2 存取 Azure 檔案儲存空間的效能變慢

    - [KB3033930](https://support.microsoft.com/kb/3033930)Hotfix 增加里約緩衝時間，每個程序在 Windows Azure 服務 64k 限制

    - [KB3004545](https://support.microsoft.com/kb/3004545)您無法存取裝載透過 VPN 連線在 Windows Azure 代管服務的虛擬機器

    - [KB3082343](https://support.microsoft.com/kb/3082343)Azure 網站-VPN 通道使用 Windows Server 2012 R2 RRAS 時，會遺失交叉內部部署 VPN 連線

    - [KB3140410](https://support.microsoft.com/kb/3140410)MS16 031︰ 安全性更新的 Microsoft Windows 位址提高權限︰ 2016 年 3 月 8 日

    - [KB3146723](https://support.microsoft.com/kb/3146723)MS16 048︰ 說明 CSRSS 安全性更新︰ 2016 年 4 月 12 日
    - [KB2904100](https://support.microsoft.com/kb/2904100)系統會凍結磁碟 I/O Windows 中時<a id="step23"></a>
23. 如果您想要建立部署多部電腦從該圖像時，您需要執行一般化圖像`sysprep`VHD 上傳至 Azure 之前。 您不需要執行`sysprep`使用特殊的 VHD。 如需有關如何建立一般化映像的詳細資訊，請參閱下列文章︰

    - [從現有的 Azure VM 使用資源管理員部署模型建立 VM 圖像](virtual-machines-windows-create-vm-generalized.md)
    - [建立使用傳統部署數據機現有 Azure VM VM 圖像](virtual-machines-windows-classic-capture-image.md)
    - [Sysprep 支援的伺服器角色](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)



## <a name="suggested-extra-configurations"></a>建議的額外設定

下列設定不會影響 VHD 上傳。 不過，我們強烈建議您有這些設定。

- 安裝[Azure 虛擬機器代理程式](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 安裝代理程式之後，您可以啟用 VM 副檔名。 VM 副檔名實作大部分您想要使用您 Vm 設定 RDP，例如重設密碼的重要功能和許多其他人。

- 傾印記錄可以在 Windows 當機問題的疑難排解很有幫助。 啟用傾印記錄集合︰

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f

    sc config wer start= auto
    ```

- Azure 中建立 VM 後，設定磁碟機 d 定義系統大小分頁

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
    ```


## <a name="next-steps"></a>後續步驟

- [將 Windows VM 圖像上傳到 Azure 資源管理員部署](virtual-machines-windows-upload-image.md)
