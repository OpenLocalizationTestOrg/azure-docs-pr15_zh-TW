<properties 
    pageTitle="LOB 測試環境中的應用程式 |Microsoft Azure" 
    description="瞭解如何建立混合式雲端環境的商務應用程式的 web 式行 IT 專業人員或開發測試。" 
    services="virtual-machines-windows" 
    documentationCenter="" 
    authors="JoeDavies-MSFT" 
    manager="timlt" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/30/2016" 
    ms.author="josephd"/>

# <a name="set-up-a-web-based-lob-application-in-a-hybrid-cloud-for-testing"></a>在混合式雲端測試設定的 web 式 LOB 應用程式

本主題會引導您完成建立模擬的混合式雲端環境進行測試裝載於 Microsoft Azure 中的商務 (LOB) 應用程式的 web 式的線條。 以下是結果的設定。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

此設定所組成︰

- 模擬內部部署網路裝載於 Azure (測試實驗室 VNet)。
- 跨內部部署虛擬網路裝載於 Azure (TestVNET)。
- VNet-VNet VPN 連線。
- 網頁 LOB 伺服器、 SQL server，與 TestVNET 虛擬網路中的第二個網域。

此設定會提供基礎] 和 [一般起始點，您可以︰

- 開發和測試 LOB 應用程式與 SQL Server 2014 資料庫後端 Azure 中裝載在網際網路資訊服務 (IIS)。
- 執行此模擬的混合式雲端 IT 工作負載的測試。

有三個主要階段，以設定此混合雲端測試環境︰

1.  設定模擬的混合式雲端環境。
2.  將 SQL server 的電腦 (SQL1) 設定。
3.  設定 LOB 伺服器 (LOB1)。

此工作負載需要 Azure 的訂閱。 如果您有 MSDN 或 Visual Studio 訂閱，請參閱[針對 Visual Studio 版訂閱的每月 Azure 信用卡](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。

例如生產裝載於 Azure LOB 應用程式中，請參閱[Microsoft 軟體架構圖表](http://msdn.microsoft.com/dn630664)和藍圖**商務應用程式**結構藍圖。

## <a name="phase-1-set-up-the-simulated-hybrid-cloud-environment"></a>階段 1︰ 設定模擬的混合式雲端環境

建立的[模擬混合式雲端測試環境](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md)。 由於此測試環境中不需要 APP1 伺服器網卡子網路上的目前狀態，您可以暫時將其關閉。

這是您目前的設定。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph1.png)
 
## <a name="phase-2-configure-the-sql-server-computer-sql1"></a>階段 2︰ 設定的 SQL server 的電腦 (SQL1)

如有需要請從 Azure 入口網站中，開始 DC2 電腦。

接下來，建立 SQL1 使用這些命令，在您的本機電腦上 PowerShell 的 Azure 命令提示字元虛擬機器。 之前執行下列命令，填入 [變數值並移除 < 及 > 字元。

    $rgName="<your resource group name>"
    $locName="<the Azure location of your resource group>"
    $saName="<your storage account name>"
    
    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName SQL1 -VMSize Standard_A4
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-SQLDataDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name "Data" -DiskSizeInGB 100 -VhdUri $vhdURI  -CreateOption empty
    
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for the SQL Server computer." 
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName SQL1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Standard -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

連線至 SQL1 使用 Azure 入口網站使用本機系統管理員帳戶的 SQL1。

接下來，設定為允許基本連線測試] 與 [SQL Server 流量的 Windows 防火牆規則。 系統管理員層級 Windows PowerShell 命令提示字元 SQL1 上，執行下列命令。

    New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

偵測 （ping） 命令應該會導致 IP 位址 192.168.0.4 從四個成功的回覆。

接下來，新增額外的資料磁碟上 SQL1 為磁碟機字母 f: 新增大量。

1.  在左窗格的 [伺服器管理員，按一下 [**檔案及儲存服務**]，然後按一下**磁碟**。
2.  在 [內容] 窗格中，在 [**磁碟**] 群組中按一下**磁碟 2** （設定為 [**未知****的磁碟分割**)。
3.  按一下 [**工作**]，然後按一下 [**新的區**。
4.  在與前段距離您開始新的大量精靈頁面，按一下 [**下一步**]。
5.  在 [選取 [伺服器和磁碟] 頁面上，按一下**磁碟 2**]，然後按 [**下一步**。 出現提示時，按一下**[確定]**。
6.  在 [指定 [音量] 頁面的大小，請按一下 [**下一步**]。
7.  在 [指派至磁碟機字母或資料夾頁面中，按一下 [**下一步**]。
8.  在 [選取檔案系統設定] 頁面中，按一下 [**下一步**]。
9.  在 [確認] 選項頁面中，按一下 [**建立**]。
10. 完成時，請按一下 [**關閉**]。

在 Windows PowerShell 命令提示字元中執行下列命令，在 SQL1 上︰

    md f:\Data
    md f:\Log
    md f:\Backup

接下來，加入 SQL1 CORP Windows Server Active Directory 網域的 Windows PowerShell 在提示 SQL1 這些命令。

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

使用 CORP\User1 帳戶出現提示時提供**新增電腦**命令的網域帳戶認證。

重新啟動後，使用 [連線至 SQL1*本機系統管理員帳戶的 SQL1*Azure 入口網站。

接下來，設定以使用 f︰ 磁碟機，新的資料庫，以及使用者帳戶權限的 SQL Server 2014。

1.  從 [開始] 畫面中，輸入**SQL Server 管理**]，然後按一下**SQL Server 2014 Management Studio 中**。
2.  在**連線到伺服器**，請按一下 [**連線**。
3.  在 [物件總管樹狀目錄] 窗格中， **SQL1**，以滑鼠右鍵按一下，然後按一下**屬性**。
4.  在 [**伺服器內容**] 視窗中，按一下 [**資料庫設定**]。
5.  找出**資料庫的預設位置**，並設定這些值︰ 
    - 輸入**資料**，路徑**f:\Data**。
    - **記錄**中，輸入路徑**f:\Log**。
    - 為**備份**，鍵入路徑**f:\Backup**。
    - 注意︰ 只有新的資料庫中使用下列位置。
6.  按一下**[確定]** ，關閉視窗]。
7.  在 [**物件總管**樹狀目錄] 窗格中，開啟 [**安全性**]。
8.  以滑鼠右鍵按一下 [**登入**，然後按一下 [**新的登入**。
9.  在 [**登入名稱**，輸入**CORP\User1**。
10. 在**伺服器角色**] 頁面上，按一下 [**系統管理員**，然後再按一下**[確定]**。
11. 關閉 Microsoft SQL Server Management Studio 中。

這是您目前的設定。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph2.png)
 
## <a name="phase-3-configure-the-lob-server-lob1"></a>階段 3︰ 設定 LOB 伺服器 (LOB1)

首先，建立 LOB1 的虛擬機器的本機電腦上的 PowerShell 的 Azure 命令提示字元這些命令。

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<your storage account name>"
    
    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName LOB1 -VMSize Standard_A2
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for LOB1."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName LOB1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/LOB1-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name LOB1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

若要連線到 LOB1 LOB1 的本機系統管理員帳戶的認證，接下來，使用 Azure 入口網站。

接下來，設定 Windows 防火牆規則，允許流量基本連線測試。 系統管理員層級 Windows PowerShell 命令提示字元 LOB1 上，執行下列命令。

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

偵測 （ping） 命令應該會導致 IP 位址 192.168.0.4 從四個成功的回覆。

接下來，加入 LOB1 CORP Active Directory 網域的 Windows PowerShell 提示時，這些命令。

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

使用 CORP\User1 帳戶出現提示時提供**新增電腦**命令的網域帳戶認證。

重新啟動後，使用 Azure 入口網站連線至 LOB1 CORP\User1 帳戶與密碼。

接下來，設定 LOB1 iis 並測試從 CLIENT1 存取。

1.  從 [伺服器管理員] 中，按一下 [**新增角色和功能**]。
2.  在 [**之前**] 頁面上，按一下 [**下一步**]。
3.  在 [**選取 [安裝類型**] 頁面上，按一下 [**下一步**]。
4.  在 [**選取目的伺服器**] 頁面上，按一下 [**下一步**]。
5.  在**伺服器角色**] 頁面上，按一下 [**網頁伺服器 (IIS)**清單中的**角色**。
6.  出現提示時，請按一下 [**新增功能**]，然後按一下 [**下一步**。
7.  在 [**選取功能**] 頁面上，按一下 [**下一步**]。
8.  在 [**網頁伺服器 (IIS)** ] 頁面中，按一下 [**下一步**]。
9.  在 [**選取角色服務**] 頁面上，選取或清除核取方塊，以進行測試 LOB 應用程式中，您需要的服務，然後再按 [**下一步**。
10. 在 [**確認安裝選項**] 頁面上，按一下 [**安裝**]。
11. 等到完成元件的安裝，然後按一下 [**關閉**。
12. 從 Azure 入口網站，以連線到 CLIENT1 電腦 CORP\User1 帳戶認證，，然後再啟動 Internet Explorer。
13. 在網址列中輸入**http://lob1/** ，然後按 enter 鍵。 您應該會看到預設 IIS 8 網頁。

這是您目前的設定。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)
 
此環境已準備好要部署網頁上的應用程式 LOB1 及測試 CLIENT1 網卡子網路上的功能。

## <a name="next-step"></a>下一步

- 新增新的虛擬機器使用[Azure 入口網站](virtual-machines-windows-hero-tutorial.md)。
