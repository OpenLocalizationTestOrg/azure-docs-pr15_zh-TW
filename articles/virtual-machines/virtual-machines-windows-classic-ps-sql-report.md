<properties 
    pageTitle="使用 PowerShell 來使用原生模式報表伺服器建立 VM |Microsoft Azure"
    description="本主題說明，並會引導您完成的部署和 SQL Server Reporting Services 原生模式報表伺服器 Azure 虛擬機器中的設定。 "
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="guyinacube"
    manager="erikre"
    editor="monicar" 
    tags="azure-service-management"/>
<tags 
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/04/2016"
    ms.author="asaxton" />

# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>使用 PowerShell 來建立 Azure VM 與原生模式報表伺服器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

本主題說明，並會引導您完成的部署和 SQL Server Reporting Services 原生模式報表伺服器 Azure 虛擬機器中的設定。 這份文件中的步驟會使用手動步驟的組合，來建立虛擬機器和 VM 上設定 Reporting Services 的 Windows PowerShell 指令碼。 設定指令碼包含 HTTP 或 HTTPs 開啟防火牆連接埠。

>[AZURE.NOTE] 如果您不需要**HTTPS**在報表伺服器上，**略過步驟 2**。
>
>建立之後 VM 步驟 1 中，移至 [] 區段中使用指令碼來設定報表伺服器和 HTTP。 執行指令碼之後，報表伺服器是可供使用。

## <a name="prerequisites-and-assumptions"></a>先決條件和假設

- **Azure 訂閱**︰ 驗證核心 Azure 訂閱中提供的數量。 如果您建立的**A3**建議的虛擬記憶體大小，您會需要**4**可用核心。 如果您使用的**A2**虛擬記憶體大小，您會需要**2**可用核心。
    
    - 若要驗證您的訂閱，在 Azure 傳統入口網站中的核心限制上方的功能表中，按一下左的窗格，然後按一下 [使用方式的設定。
    
    - 若要增加核心配額，請連絡[Azure 支援](https://azure.microsoft.com/support/options/)。 VM 大小的詳細資訊，請參閱[Azure 虛擬機器大小](virtual-machines-linux-sizes.md)。

- **Windows PowerShell 指令碼**︰ 主題假設您有 Windows PowerShell 的基本工作知識。 如需有關如何使用 Windows PowerShell 的詳細資訊，請參閱下列各項︰

    - [在 Windows Server 上啟動 Windows PowerShell](https://technet.microsoft.com/library/hh847814.aspx)
    
    - [開始使用 Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>步驟 1︰ 佈建 Azure 虛擬機器

1. 瀏覽至 Azure 傳統入口網站。

1. 按一下左窗格中的**虛擬機器**。

    ![microsoft azure 虛擬機器](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)

1. 按一下 [**新增**]。

    ![[新增] 按鈕](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)

1. 按一下 [**從圖庫**。

    ![從圖庫新增 vm](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)

1. 按一下 [ **SQL Server 2014 RTM 標準 – Windows Server 2012 R2** ，然後按一下 [箭號以繼續。

    ![下一步](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

    如果您需要 Reporting Services 資料導向訂閱功能，請選擇 [ **SQL Server 2014 RTM Enterprise-Windows Server 2012 R2**]。 如需有關 SQL Server 版本和支援功能的詳細資訊，請參閱[的 SQL Server 2012 版本支援的功能](https://msdn.microsoft.com/library/cc645993.aspx#Reporting)。

1. 在 [**虛擬機器設定**] 頁面中，編輯下列欄位︰
                                    
    - 如果有多個**版本發行日期**，請選取 [最新版本。
    
    - **虛擬機器名稱**︰ 電腦名稱下的 [設定] 頁面上也使用預設名稱為 [雲端服務的 DNS。 透過 Azure 服務，DNS 名稱必須是唯一的。 考慮設定 VM 說明 VM 使用的電腦名稱。 例如 ssrsnativecloud。
    
    - **層**︰ 標準
    
    - **大小︰ A3**是 SQL Server 工作負載的建議的虛擬記憶體大小。 如果 VM 只作為報表伺服器，A2 的虛擬記憶體大小為足夠除非報表伺服器體驗大型的工作量。 VM 價格資訊，請參閱[虛擬機器價格](https://azure.microsoft.com/pricing/details/virtual-machines/)。
    
    - **新的使用者名稱**︰ 建立您所提供的名稱為 VM 的系統管理員。
    
    - **新密碼**並**確認**。 為新的系統管理員帳戶使用這個密碼時，建議使用強式密碼。
    
    - 按一下 [**下一步**]。 ![下一步](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

1. 在下一個頁面上，編輯下列欄位︰

    - **雲端服務**︰ 選取 [**建立新的雲端服務**。
    
    - **雲端服務的 DNS 名稱**︰ 這是雲端服務與 VM 相關聯的公用 DNS 名稱。 預設名稱是您所輸入 VM 名稱中的名稱。 中的主題稍後的步驟，如果您建立受信任的 SSL 憑證，然後 DNS 名稱會用來 「**發給**」 的憑證的值。
    
    - **地區/相關性群組/虛擬網路**︰ 選擇使用者最接近的區域。
    
    - **儲存帳戶**︰ 使用自動產生的儲存空間的帳戶。
    
    - **可用性設定**︰ 無。
    
    - **結束點**讓**遠端桌面**] 和 [ **PowerShell**結束點，然後新增 [HTTP 或 HTTPS 端點，視您的環境而定。

        - **HTTP**︰ 預設公開及私密金鑰連接埠是**80**。 請注意，如果您是使用私人的連接埠 80、 以外修改**$HTTPport = 80**在 http 指令碼。

        - **HTTPS**︰ 預設公開及私密金鑰連接埠是**443**。 安全性的最佳作法是變更私人的連接埠並設定您的防火牆和報表伺服器使用私人的連接埠。 如需有關端點的詳細資訊，請參閱[如何設定設定與通訊虛擬機器](virtual-machines-windows-classic-setup-endpoints.md)。 請注意，如果您使用的連接埠 443、 以外變更參數**$HTTPsport = 443** HTTPS 指令碼。
    
    - 按一下 [下一步]。 ![下一步](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

1. 在精靈的最後一個頁面上，請**安裝 VM 代理程式**已選取的預設值。 本主題中的步驟，將不會使用 VM 代理程式，但如果您打算將此 VM，VM 代理程式和擴充功能可讓您以增強他公分。  如需有關 VM 代理程式的詳細資訊，請參閱[VM 代理程式 」 和 「 延伸模組-第 1 部分](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/)。 執行預設副檔名安裝 ad 是 「 BGINFO 「 延伸模組會顯示在 VM 桌面]、 [系統資訊，例如內部 IP 和可用磁碟空間。

1. 按一下 [完成]。 ![確定](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)

1. VM**狀態**佈建程序期間會顯示為**開始 （提供）** ，然後顯示為**執行**VM 時能夠並且可供使用。

## <a name="step-2-create-a-server-certificate"></a>步驟 2︰ 建立伺服器憑證

>[AZURE.NOTE] 如果您不需要 HTTPS 報表伺服器上，您可以**略過步驟 2** ，然後移至 [] 區段中**使用指令碼來設定報表伺服器和 HTTP**。 若要快速設定報表伺服器使用 HTTP 指令碼和報表伺服器就可以開始使用。

若要使用 HTTPS VM 上，您需要受信任的 SSL 憑證。 根據您的狀況，您可以使用下列兩種方法之一︰

- 有效的 SSL 憑證發出的憑證授權單位 (CA)，並且受 Microsoft。 CA 根憑證，才能透過 Microsoft 根憑證計發佈。 如需此程式的詳細資訊，請參閱[Windows 和 Windows Phone 8 SSL 根憑證計 (成員 Ca)](http://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) ，以及[Microsoft 根憑證計簡介](http://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx)。

- 自我簽署的憑證。 自我簽署的憑證不建議使用生產環境。

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>若要使用的憑證建立由受信任的憑證授權單位 (CA)

1. **要求從憑證授權單位網站的伺服器憑證**。 

    產生憑證要求檔案 (Certreq.txt) 所傳送的憑證授權單位，或產生的要求，線上憑證授權單位，您可以使用 Web 伺服器憑證精靈。 例如，Microsoft 憑證服務在 Windows Server 2012 中。 根據您的伺服器憑證所提供的識別保證層級，則幾天到幾個月的憑證授權單位核准您的要求，並將憑證檔案傳送給您。 

    如需要求伺服器憑證的詳細資訊，請參閱下列各項︰ 
    
    - 使用[Certreq](https://technet.microsoft.com/library/cc725793.aspx)， [Certreq](https://technet.microsoft.com/library/cc725793.aspx)。
    
    - 安全性工具來管理 Windows Server 2012。

    [安全性工具來管理 Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)

    >[AZURE.NOTE] **發行到**欄位的信任的 SSL 憑證應該是您所用的新 VM 的**雲端服務的 DNS 名稱**相同。

1. **安裝 Web 伺服器上的伺服器憑證**。 網頁伺服器在此情況下 VM 裝載報表伺服器，而當您設定 Reporting Services，將會建立在稍後步驟中的網站。 如需有關如何使用憑證 MMC 嵌入式管理單元網頁伺服器上安裝的伺服器憑證的詳細資訊，請參閱[安裝伺服器憑證](https://technet.microsoft.com/library/cc740068)。
    
    如果您想要使用本主題隨附的指令碼，若要設定報表伺服器憑證的值**指紋**不需要做為參數的指令碼。 如何取得指紋的憑證，請參閱下一節，如需詳細資訊。

1. 指派到報表伺服器的伺服器憑證。 工作分派已完成 [下一步] 區段中，當您設定報表伺服器。

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>若要使用的虛擬機器自我簽署憑證

時 VM 佈 VM 上建立自我簽署的憑證。 憑證具有相同名稱的 VM DNS 名稱。 若要避免憑證錯誤，就需要的是在本身的 VM 和網站的所有使用者受信任的憑證。

1. 若要信任的根 CA 本機 VM 上的憑證，將憑證新增至 「**信任的根憑證授權單位**。 以下是摘要所需的步驟。 如何信任的 CA 的詳細步驟，請參閱[安裝伺服器憑證](https://technet.microsoft.com/library/cc740068)。

    1. 從 Azure 傳統的入口網站中，選取 VM，然後按一下 [連線]。 根據您的瀏覽器設定，可能會提示您儲存的 vm 連線的.rdp 檔案。
    
        ![連線至 azure 虛擬機器](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) 使用使用者 VM 名稱、 使用者名稱和密碼設定當您建立 VM。 
    
        例如，在下圖中，VM 名稱**ssrsnativecloud** ，而使用者名稱是**testuser**。
        
        ![登入 inlcudes vm 名稱](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
    
    1. 執行 mmc.exe。 如需詳細資訊，請參閱[如何︰ 檢視憑證 MMC 嵌入式管理單元](https://msdn.microsoft.com/library/ms788967.aspx)。
    
    1. 在 [主控台應用程式的 [**檔案**] 功能表中，新增**憑證**嵌入式管理單元**電腦帳戶**出現提示時，，然後選取然後按 [**下一步**。
    
    1. 選取 [**本機電腦上**管理，然後按一下 [**完成**]。
    
    1. 按一下**[確定]** ，然後再展開**憑證-個人**節點並按一下 [**憑證**。 憑證為 VM DNS 名稱之後， **cloudapp.net**最後一句]。 以滑鼠右鍵按一下的憑證名稱，然後按一下 [**複製**]。
    
    1. 展開**受信任的根憑證授權單位**節點並**憑證**以滑鼠右鍵按一下，然後按一下 [**貼**。
    
    1. 驗證在**受信任的根憑證授權單位**] 下的憑證名稱上按兩下，請確認沒有發生錯誤，而且您會看到您的憑證。 如果您想要使用本主題隨附的 HTTPS 指令碼，若要設定報表伺服器憑證的值**指紋**不需要做為參數的指令碼。 **若要取得指紋值**]，完成動作。 此外，還有擷取指紋[來設定報表伺服器及 HTTPS 使用指令碼](#use-script-to-configure-the-report-server-and-HTTPS)] 區段中的 PowerShell 範例。
        
        1. 按兩下憑證的名稱，例如 ssrsnativecloud.cloudapp.net。
        
        1. 按一下 [**詳細資料**] 索引標籤。
        
        1. 按一下 [**指紋**]。 指紋的值會顯示在詳細資料欄位，例如 a6 08 3 c 自由度 f9 0b f7 e3 7 c 25 ed a4 ed 7e ac 91 9 c 2 c 體 2f。
        
        1. 複製指紋和儲存以供日後的值或 [現在編輯指令碼。
        
        1. (*)之前執行指令碼，移除成對值之間的空格。 例如之前所述的指紋現在會 a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f。
        
        1. 指派到報表伺服器的伺服器憑證。 工作分派已完成 [下一步] 區段中，當您設定報表伺服器。

如果您使用自我簽署的 SSL 憑證，憑證的名稱已經比對 VM 主機的名稱。 因此，電腦的 DNS 全域已註冊，而且可以從任何用戶端存取。

## <a name="step-3-configure-the-report-server"></a>步驟 3︰ 設定報表伺服器

本節會引導您設定 VM Reporting Services 原生模式報表伺服器。 您可以使用下列方法之一來設定報表伺服器︰

- 使用指令碼來設定報表伺服器

- 您可以使用組態管理員來設定報表伺服器。

如需詳細步驟，請參閱[連線至虛擬機器並開始 Reporting Services 組態管理員](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager)] 區段。

**驗證附註︰**Windows 驗證 」 建議的驗證方法，是預設 Reporting Services 驗證。 VM 所設定的使用者可以存取 Reporting Services，並指定給 Reporting Services 角色。

### <a name="use-script-to-configure-the-report-server-and-http"></a>若要設定的報表伺服器及 HTTP 使用指令碼

若要使用 Windows PowerShell 指令碼來設定報表伺服器，請完成下列步驟。 設定包含 HTTP]，[不 HTTPS:

1. 從 Azure 傳統的入口網站中，選取 VM，然後按一下 [連線]。 根據您的瀏覽器設定，可能會提示您儲存的 vm 連線的.rdp 檔案。

    ![連線至 azure 虛擬機器](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) 使用使用者 VM 名稱、 使用者名稱和密碼設定當您建立 VM。 

    例如，在下圖中，VM 名稱**ssrsnativecloud** ，而使用者名稱是**testuser**。
    
    ![登入 inlcudes vm 名稱](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)

1. 在 VM 開啟**Windows PowerShell ise [以系統**管理權限。 在 Windows server 2012 上的預設會安裝 PowerShell ise [以系統。 建議您將使用 ise [以系統，而非標準的 Windows PowerShell 視窗，讓您可以將指令碼貼到 ise [以系統、 修改指令碼，並執行指令碼。

1. 在 Windows PowerShell ise [以系統，按一下 [**檢視**] 功能表，然後按一下**窗格中顯示指令碼**。

1. 複製下列指令碼，並貼到 Windows PowerShell ise [以系統指令碼] 窗格。

        ## This script configures a Native mode report server without HTTPS
        $ErrorActionPreference = "Stop"
        
        $server = $env:COMPUTERNAME
        $HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
        
        ## Set PowerShell execution policy to be able to run scripts
        Set-ExecutionPolicy RemoteSigned -Force
        
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
        
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
        
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
        
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
        
        ## Report Server Configuration Steps
        
        ## Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
        
        ## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
            write-host "Calling ReserveURL port $HTTPport"
            $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
           
        ## Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
        
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS              ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
          
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
        
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
        
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
        
        ## Setting the Report Manager URL ##
        
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
        
        ## ReserveURL for ReportManager  - port $HTTPport
            write-host "Calling ReserveURL for ReportManager, port $HTTPport"
            $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportManager port $HTTPport"
        
        write-host -foregroundcolor green "Open Firewall port for $HTTPport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## Open Firewall port for $HTTPport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
            write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
        
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time

1. 如果您建立 VM 與 HTTP 連接埠 80 以外，修改參數 $HTTPport = 80。

1. 指令碼目前已設定 Reporting Services。 如果您想要執行指令碼的 Reporting Services，修改路徑 」 v11 」，取得 WmiObject 陳述式的命名空間版本部分。

1. 執行指令碼。

**驗證**︰ 若要確認基本報表伺服器功能正常運作，請參閱本主題稍後的 [[驗證設定](#verify-the-configuration)] 區段。

### <a name="use-script-to-configure-the-report-server-and-https"></a>使用指令碼來設定報表伺服器及 HTTPS

若要使用 Windows PowerShell 來設定報表伺服器，請完成下列步驟。 設定包含不 HTTP HTTPS。

1. 從 Azure 傳統的入口網站中，選取 VM，然後按一下 [連線]。 根據您的瀏覽器設定，可能會提示您儲存的 vm 連線的.rdp 檔案。

    ![連線至 azure 虛擬機器](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) 使用使用者 VM 名稱、 使用者名稱和密碼設定當您建立 VM。 

    例如，在下圖中，VM 名稱**ssrsnativecloud** ，而使用者名稱是**testuser**。

    ![登入 inlcudes vm 名稱](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)

1. 在 VM 開啟**Windows PowerShell ise [以系統**管理權限。 在 Windows server 2012 上的預設會安裝 PowerShell ise [以系統。 建議您將使用 ise [以系統，而非標準的 Windows PowerShell 視窗，讓您可以將指令碼貼到 ise [以系統、 修改指令碼，並執行指令碼。

1. 若要啟用執行指令碼，請執行下列 Windows PowerShell 命令︰

        Set-ExecutionPolicy RemoteSigned

    然後，您可以執行下列動作，請確認原則︰

        Get-ExecutionPolicy

1. 在**Windows PowerShell ise [以系統**，按一下 [**檢視**] 功能表，然後按一下**窗格中顯示指令碼**。

1. 複製下列指令碼，並將它貼到 Windows PowerShell ise [以系統指令碼] 窗格。

        ## This script configures the report server, including HTTPS
        $ErrorActionPreference = "Stop"
        $httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
        
        # You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
        #dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
        #
        # The certifacte hash is a REQUIRED parameter
        $certificatehash="" 
        # the certificate hash should not contain spaces
        
        if ($certificatehash.Length -lt 1) 
        {
            write-error "certificatehash is a required parameter"
        } 
        # Certificates should be all lower case
        $certificatehash=$certificatehash.ToLower()
        $server = $env:COMPUTERNAME
        # If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
        $DNSName="+"
        #$DNSName="$server.cloudapp.net"
        $DNSNameAndPort = $DNSName + ":$httpsport"
        
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
        
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
        
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
        
        write-host "The script will use $DNSNameAndPort as the DNS name and port" 
        
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
        
        ## Reporting Services Report Server Configuration Steps
        
        ## 1. Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
        
        ## ReserveURL for ReportServerWebService - port 80 (for local usage)
            write-host 'Calling ReserveURL port 80'
            $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportServer port 80" 
        
        ## ReserveURL for ReportServerWebService - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportServer port $httpsport" 
        
        ## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
            
        ## 2. Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
        
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS                    ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
          
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
        
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
        
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
        
        ## 3. Setting the Report Manager URL ##
        
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
        
        ## ReserveURL for ReportManager  - port 80
            write-host 'Calling ReserveURL for ReportManager, port 80'
            $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportManager port 80"
        
        ## ReserveURL for ReportManager - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportManager port $httpsport" 
        
        ## CreateSSLCertificateBinding for ReportManager port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
        
        write-host -foregroundcolor green "Open Firewall port for $httpsport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## Open Firewall port for $httpsport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
            write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
        
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time

1. 修改**$certificatehash**參數的指令碼︰

    - 這是**必要**的參數。 如果您沒有儲存憑證值從先前的步驟，使用以下兩種方法的其中一個憑證雜湊值複製憑證指紋。:

        在 VM 開啟 Windows PowerShell ise [以系統，然後執行下列命令︰

            dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer

        輸出看起來類似下列項目。 如果指令碼會傳回空白的行，VM 不擁有憑證，例如設定，請參閱[使用虛擬機器自我簽署憑證](#to-use-the-virtual-machines-self-signed-certificate)的章節。
    
    OR
    
    - VM 上執行 mmc.exe，然後新增**憑證**嵌入式管理單元 」。
    
    - 在 [**信任的根憑證授權單位**節點下按兩下您的憑證名稱。 如果您使用的 VM 自我簽署的憑證，憑證為 VM DNS 名稱之後， **cloudapp.net**最後一句]。
    
    - 按一下 [**詳細資料**] 索引標籤。
    
    - 按一下 [**指紋**]。 指紋的值會顯示在詳細資料欄位，例如 af 11 60 b6 4b 28 8 d 89 0a 82 12 ff 6b a9 c3 66 4f 31 90 48
    
    - **之前執行指令碼**，移除成對值之間的空格。 例如 af1160b64b288d890a8212ff6ba9c3664f319048

1. 修改**$httpsport**參數︰ 

    - 如果您使用連接埠 443 HTTPS 端點，然後您不需要更新此參數的指令碼。 否則，請使用您在 VM 設定 HTTPS 私人結束點時，您所選取的連接埠值。

1. 修改**$DNSName**參數︰ 

    - 指令碼設定的萬用字元憑證 $DNSName = [+]。 如果您不想来設定的萬用字元憑證繫結、 註解 $DNSName ="+ 「 和啟用的下列行、 完整 $DNSNAme 參考、 # # $DNSName="$server.cloudapp.net 」。

        如果您不想要使用的 Reporting Services 的虛擬機器的 DNS 名稱，請變更 $DNSName 的值。 如果您使用參數，憑證也必須使用這個名稱，並登錄全域 DNS 伺服器上的名稱。

1. 指令碼目前已設定 Reporting Services。 如果您想要執行指令碼的 Reporting Services，修改路徑 」 v11 」，取得 WmiObject 陳述式的命名空間版本部分。

1. 執行指令碼。

**驗證**︰ 若要確認基本報表伺服器功能正常運作，請參閱本主題稍後的 [[驗證設定](#verify-the-connection)] 區段。 若要驗證憑證繫結管理權限，以開啟命令提示字元，並執行下列命令︰

    netsh http show sslcert

結果會包括下列各項︰

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>若要設定報表伺服器使用組態管理員

如果您不想要執行的 PowerShell 指令碼來設定報表伺服器，請遵循此節] 來設定報表伺服器 Reporting Services 原生模式組態管理員中的步驟進行。

1. 從 Azure 傳統的入口網站中，選取 VM，然後按一下 [連線]。 使用的使用者名稱和密碼設定當您建立 VM。

    ![連線至 azure 虛擬機器](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)

1. 執行 Windows update 並安裝更新的 vm。 如果需要 VM 重新啟動，請重新啟動 VM，並從 Azure 傳統入口網站的 vm 重新連線。

1. 從 [VM 上的 [開始] 功能表中，輸入**Reporting Services**並開啟**Reporting Services 組態管理員**。

1. 您可以保留預設值的**伺服器名稱**] 和 [**報表伺服器執行個體**。 按一下 [**連線**]。

1. 在左窗格中，按一下 [ **Web 服務 URL**]。

1. 根據預設，瞭解設定 HTTP 連接埠 80 和 」 所有分派 」 的 IP。 若要新增 HTTPS:

    1. 在 [ **SSL 憑證**︰ 選取您想要使用，例如 [VM 名稱] 的憑證。 cloudapp.net。 如果沒有憑證未列出，請參閱節**步驟 2︰ 建立伺服器憑證**如何安裝和信任的憑證 VM 上的資訊。
    
    1. 在**SSL 連接埠**︰ 選擇 443。 如果您在使用不同的私人連接埠 VM 設定 HTTPS 私人端點，請使用以下的值。
    
    1. 按一下 [**套用**]，並等待完成作業。

1. 在左窗格中，按一下 [**資料庫**]。

    1. 按一下 [**變更 Databas**e。
    
    1. 按一下 [**建立新的報表伺服器資料庫**，然後按一下 [**下一步**。
    
    1. 保留預設的 [**伺服器名稱**︰ 為 VM 命名，然後以**目前的使用者**-**整合式安全性**保留預設的 [**驗證類型**。 按一下 [**下一步**]。
    
    1. 保留預設值為**ReportServer**的**資料庫名稱**，然後按一下 [**下一步**]。
    
    1. 保留預設的**服務憑證****驗證類型**，然後按一下 [**下一步**]。
    
    1. 在 [**摘要**] 頁面上，按一下 [**下一步**]。
    
    1. 設定完成後，按一下 [**完成**]。

1. 在左窗格中，按一下 [**報表管理員 URL**]。 保留預設的 [**報表**的**虛擬目錄**，然後按一下 [**套用**]。

1. 按一下 [**結束**] 以關閉 [Reporting Services 組態管理員]。

## <a name="step-4-open-windows-firewall-port"></a>步驟 4︰ 開啟 Windows 防火牆連接埠

>[AZURE.NOTE] 如果您是使用其中一個指令碼設定報表伺服器，您可以略過此節。 指令碼包含步驟開啟防火牆連接埠。 預設的連接埠 80 http 和 HTTPS 的連接埠 443。

遠端報表管理員或虛擬機器上的報表伺服器連線，VM 上需要 TCP 端點。 需要在 VM 的防火牆中開啟相同的連接埠。 佈 VM 時，所建立的結束點。

本節提供基本資訊如何開啟防火牆連接埠。 如需詳細資訊，請參閱[設定防火牆報表伺服器存取](https://technet.microsoft.com/library/bb934283.aspx)

>[AZURE.NOTE] 如果您使用指令碼來設定報表伺服器時，您可以略過此節。 指令碼包含步驟開啟防火牆連接埠。

如果您設定為 HTTPS 的私人的連接埠 443 以外，請視情況修改下列指令碼。 若要開啟 Windows 防火牆連接埠**443** ，完成下列步驟︰

1. 開啟 Windows PowerShell 視窗具有系統管理權限。

1. 如果您使用的連接埠 443 以外，當您設定 HTTPS 端點 VM 上時，更新下列命令中的連接埠並執行命令︰

        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443

1. 命令完成時，**確定**會顯示在命令提示字元。

若要確認連接埠開啟，請開啟 Windows PowerShell 視窗，然後執行下列命令︰

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>混合式部署設定

要檢查的基本報表伺服器功能現在運作，請開啟瀏覽器與系統管理員權限，然後瀏覽至下列報表伺服器 ad 報表管理員 URL:

- 在 VM 上瀏覽至報表伺服器 URL:

        http://localhost/reportserver

- 在 VM 上瀏覽至報表管理員 URL:

        http://localhost/Reports

- 從您的本機電腦，瀏覽至**遠端**報表管理員 VM 上。 更新在下列範例中為適當的 DNS 名稱。 出現提示時輸入密碼，請使用 VM 佈時所建立的系統管理員認證。 在 [網域] 中的使用者名稱是\[使用者名稱] 格式，其中的網域是 VM 電腦名稱，例如 ssrsnativecloud\testuser。 如果您不使用 HTTP**S**，移除**s** URL 中。 在 VM 上建立其他的使用者，請參閱下一節的資訊。

        https://ssrsnativecloud.cloudapp.net/Reports

- 從您的本機電腦，瀏覽至遠端報表伺服器 URL。 更新在下列範例中為適當的 DNS 名稱。 如果您不使用 HTTPS，移除 s URL 中。

        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>建立使用者並指派角色

設定與驗證報表伺服器後, 常見的管理任務是建立一或多位使用者並指派給 Reporting Services 角色的使用者。 如需詳細資訊，請參閱下列各項︰

- [建立本機的使用者帳戶](https://technet.microsoft.com/library/cc770642.aspx)

- [授與使用者存取報表伺服器 （報表管理員）](https://msdn.microsoft.com/library/ms156034.aspx)）

- [建立及管理角色指派](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>建立及發佈報表 Azure 虛擬機器

下表摘要列出一些發佈從內部部署電腦到報表伺服器裝載於 Microsoft Azure 虛擬機器上現有的報表可用的選項︰

- **RS.exe 指令碼**︰ 使用 RS.exe 指令碼來將報表中的項目與現有的報表伺服器複製到 Microsoft Azure 虛擬機器。 如需詳細資訊，請參閱[範例 Reporting Services rs.exe 指令碼來移轉內容之間報表伺服器](https://msdn.microsoft.com/library/dn531017.aspx)中的區段 」 原生模式 – Microsoft Azure 虛擬機器原生模式 」。

- **報表產生器**︰ 虛擬機器包含按一下-一次的 Microsoft SQL Server 報表產生器的版本。 若要啟動報表產生器第一次虛擬機器上︰

    1. 開始您的瀏覽器與系統管理員權限。
    
    1. 瀏覽至報表管理員虛擬機器上，按一下 [功能區中的 [**報表產生器**。

    如需詳細資訊，請參閱[安裝、 解除安裝，以及支援報表產生器](https://technet.microsoft.com/library/dd207038.aspx)。

- **SQL Server Data Tools: VM**︰ 如果您建立的 VM 與 SQL Server 2012，然後 SQL Server Data Tools 虛擬機器上已安裝可用於建立**報表伺服器專案**及虛擬機器上的報表。 SQL Server Data Tools 可以將報表發佈至報表伺服器虛擬機器上。
    
    如果您使用 SQL server 2014 建立 VM，您可以安裝 SQL Server 資料工具-BI for visual Studio。 如需詳細資訊，請參閱下列各項︰

    - [Microsoft SQL Server Data Tools for Visual Studio 2013 的商務智慧功能](https://www.microsoft.com/download/details.aspx?id=42313)

    - [Microsoft SQL Server Data Tools for Visual Studio 2012 的商務智慧功能](https://www.microsoft.com/download/details.aspx?id=36843)

    - [SQL Server Data Tools 和 SQL Server 商業智慧 (BI SSDT)](http://curah.microsoft.com/30004/sql-server-data-tools-ssdt-and-sql-server-business-intelligence)

- **SQL Server Data Tools︰ 遠端**︰ 您的本機電腦，請在包含 Reporting Services 報表的 SQL Server Data Tools 中建立 Reporting Services 專案。 設定專案以連線至 web 服務 URL。

    ![ssdt SSRS 專案的專案屬性](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)

- **使用指令碼**︰ 使用指令碼複製報表伺服器內容。 如需詳細資訊，請參閱[範例 Reporting Services rs.exe 指令碼來移轉內容之間報表伺服器](https://msdn.microsoft.com/library/dn531017.aspx)。

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>如果您不使用 VM，最小化成本

>[AZURE.NOTE] 若要最小化費用的 Azure 虛擬機器在不使用時，關閉 VM 從 Azure 傳統入口網站。 如果您使用 Windows power 選項內 VM 關閉 VM 時，您仍 vm 會相同的數量。 若要降低費用，您必須關閉 VM Azure 傳統入口網站中。 如果您不再需要 VM，請記得要刪除 VM 和相關聯的.vhd 檔案，以避免儲存費用。如需詳細資訊，請參閱[虛擬機器定價詳細資料](https://azure.microsoft.com/pricing/details/virtual-machines/)的常見問題集一節。

## <a name="more-information"></a>詳細資訊

### <a name="resources"></a>資源

- 單一伺服器部署的 SQL Server 的商務智慧與 SharePoint 2013 相關相似的內容，請參閱[使用 Windows PowerShell 來建立 Azure VM 與 SQL Server BI 和 SharePoint 2013](https://msdn.microsoft.com/library/azure/dn385843.aspx)。

- 與多伺服器部署的 SQL Server 的商務智慧與 SharePoint 2013 相關相似的內容，請參閱[部署 SQL Server 的商務智慧功能 Azure 虛擬機器中的商務連絡人](https://msdn.microsoft.com/library/dn321998.aspx)。

- 部署的 SQL Server 的商務智慧功能 Azure 虛擬機器中的商務連絡人與相關的一般資訊，請參閱[SQL Server 的商務智慧功能 Azure 虛擬機器中的商務連絡人](virtual-machines-windows-classic-ps-sql-bi.md)。

- 如需成本的 Azure 計算費用，請參閱[Azure 價格計算機](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines)的虛擬機器] 索引標籤。

### <a name="community-content"></a>社群內容

- 如需如何建立 Reporting Services 原生模式報表伺服器，而不使用指令碼的逐步指示，請參閱[裝載 SQL 報告 」 服務 Azure 虛擬機器上的商務連絡人](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html)。

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>SQL Server Azure Vm 中的其他資源的連結

[SQL Server，內部 Azure 虛擬機器概觀](virtual-machines-windows-sql-server-iaas-overview.md)
