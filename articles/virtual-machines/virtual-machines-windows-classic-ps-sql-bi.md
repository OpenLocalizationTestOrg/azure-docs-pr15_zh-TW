<properties
    pageTitle="SQL Server 的商務智慧功能 |Microsoft Azure"
    description="本主題使用傳統的部署模型，以建立的資源，並將可用的商務智慧 (BI) 功能說明的 SQL Server 執行上 Azure 虛擬機器 (Vm)。"
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

# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>Azure 虛擬機器中的 SQL Server 商務智慧功能

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Microsoft Azure 虛擬機器庫包含包含安裝 SQL Server 的圖像。 圖像庫中支援的 SQL Server 版本是您可以安裝內部部署電腦和虛擬機器相同的安裝檔案。 此主題概述虛擬機器已佈建後所需的設定步驟的圖像上安裝 SQL Server 商務智慧 (BI) 功能。 本文也說明支援的部署拓撲的 BI 功能及最佳作法。

## <a name="license-considerations"></a>授權考量

有授權 SQL Server Microsoft Azure 虛擬機器中的兩種方法︰

1. 授權的軟體保證行動性優點。 如需詳細資訊，請參閱[透過 Azure 上軟體保證授權行動性](https://azure.microsoft.com/pricing/license-mobility/)。

1. 支付每小時速率的安裝 SQL server Azure 虛擬機器。 請參閱[虛擬機器價格](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql)的 「 SQL Server 」 一節。

如需授權和目前的詳細資訊，請參閱[虛擬機器授權常見問題集](https://azure.microsoft.com/pricing/licensing-faq/%20/)。

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>SQL Server 中的圖像可用 Azure 虛擬機器圖庫

Microsoft Azure 虛擬機器庫包含包含 Microsoft SQL Server 的數個圖像。 虛擬機器圖像上安裝的軟體有所不同版本的作業系統和版本的 SQL server。 經常變更 Azure 虛擬機器圖庫中提供的圖像的清單。

![SQL azure VM 圖庫中的圖像](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) 下列 PowerShell 指令碼會傳回包含 「 SQL-伺服器 」 中 ImageName Azure 圖像的清單︰

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "settings" menu in Azure classic portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

如需有關版本和 SQL Server 支援的功能的詳細資訊，請參閱下列各項︰

- [SQL Server 版本](https://www.microsoft.com/server-cloud/products/sql-server-editions/#fbid=Zae0-E6r5oh)

- [版本的 SQL Server 2016 支援的功能](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>安裝 SQL Server 虛擬機器庫圖像上的 BI 功能

下表摘要列出的 SQL Server 安裝常見的 Microsoft Azure 虛擬機器庫圖像上的商務智慧功能 」

- SQL Server 2016 RC3

- SQL Server 2014 SP1 企業版

- SQL Server 2014 SP1 標準

- SQL Server 2012 SP2 企業版

- SQL Server 2012 SP2 標準

|SQL Server BI 功能|安裝在圖庫圖像|備忘稿|
|---|---|---|
|**Reporting Services 原生模式**|[是]|安裝，但必須進行設定，包括報表管理員 URL。 請參閱[設定 Reporting Services](#configure-reporting-services)] 區段。|
|**Reporting Services SharePoint 模式**|無|Microsoft Azure 虛擬機器庫圖像不包含 SharePoint 或 SharePoint 安裝檔案。 <sup>1</sup>|
|**Analysis Services 的多維度和資料採礦 (OLAP)**|[是]|安裝並設定為預設的 Analysis Services 執行個體|
|**Analysis Services 表格式**|無|支援的 SQL Server 2012，2014年和 2016年圖像但不是會安裝預設。 安裝另一個 Analysis Services 執行個體。 [] 區段中，請參閱本主題中，安裝其他的 SQL Server 服務和功能。|
|**Analysis Services Powerpivot for SharePoint**|無|Microsoft Azure 虛擬機器庫圖像不包含 SharePoint 或 SharePoint 安裝檔案。 <sup>1</sup>|

<sup>1</sup>的 SharePoint 和 Azure 虛擬機器上的其他資訊，請參閱[SharePoint 2013 適用的 Microsoft Azure 架構](https://technet.microsoft.com/library/dn635309.aspx)與[SharePoint 部署上 Microsoft Azure 虛擬機器](https://www.microsoft.com/download/details.aspx?id=34598)。

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) 執行下列 PowerShell 命令，以取得已安裝的服務服務名稱中包含 「 SQL 」 的清單。

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>一般的建議及最佳作法

- 使用 SQL Server 企業版時，建議使用最小虛擬機器為**A3** 。 Analysis Services 和 Reporting Services 的 SQL Server BI 部署建議**A4**虛擬機器大小。

    目前的 VM 大小的詳細資訊，請參閱[Azure 虛擬機器大小](virtual-machines-linux-sizes.md)。

- 儲存資料、 記錄與**c**磁碟機上的備份檔案的磁碟管理的最佳做法是︰ 與**D**:。 例如，建立資料磁碟**E**︰ 和**F**:。

    - 快取原則預設的磁碟機**C**磁碟機︰ 不是使用資料的最佳。

    - **D**︰ 是主要用於頁面檔案暫時磁碟機。 **D**︰ 磁碟機並不會保留，且不會儲存在 blob 儲存體。 管理工作，例如變更虛擬機器大小重設**D**︰ 磁碟機。 建議您是在**不**使用**D**︰ 資料庫檔案，包括 tempdb 的磁碟機。

    如需有關建立和附加磁碟的詳細資訊，請參閱[如何將附加至虛擬機器資料磁碟](virtual-machines-windows-classic-attach-disk.md)。

- 停止或解除安裝您不打算使用的服務。 範例如果虛擬機器僅用於 Reporting Services，停止或解除安裝 Analysis Services 和 SQL Server 整合服務。 下列圖像是啟動的服務，預設的範例。

    ![SQL Server 服務](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)

    >[AZURE.NOTE] SQL Server 資料庫引擎所需要的是支援的 BI 案例。 在單一伺服器 VM 拓撲，資料庫引擎，才能在同一個 VM 上執行。

    如需詳細資訊，請參閱下列內容︰[解除安裝 Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx)及[解除安裝執行個體 Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx)。

- 檢查**Windows Update** '重要更新]。 經常重新整理 Microsoft Azure 虛擬機器圖像。不過重要更新可能之後，才可從**Windows Update** VM 圖像上次重新整理。

## <a name="example-deployment-topologies"></a>範例部署拓撲

以下是使用 Microsoft Azure 虛擬機器中的範例部署。 在這些圖表拓撲是只是一些您可以使用 SQL Server BI 功能與 Microsoft Azure 虛擬機器可能拓撲。

### <a name="single-virtual-machine"></a>單一虛擬機器

Analysis Services、 Reporting Services、 SQL Server 資料庫引擎，與資料來源單一的虛擬機器上。

![1 的虛擬機器 bi iass 案例](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>兩個虛擬機器

- Analysis Services Reporting Services，與 SQL Server 資料庫引擎單一的虛擬機器上。 此部署包含報表伺服器資料庫。

- 在第二個 VM 上的資料來源。 第二個 VM 包含 SQL Server 資料庫引擎做為資料來源。

![bi iaas 案例 2 的虛擬機器](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>混合的 Azure – Azure SQL 資料庫的資料

- Analysis Services Reporting Services，與 SQL Server 資料庫引擎單一的虛擬機器上。 此部署包含報表伺服器資料庫。

- 資料來源是 SQL Azure 資料庫。

![bi iaas 案例 vm 和 AzureSQL 作為資料來源](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>混合式 – 資料內部部署

- 在此範例部署 Analysis Services，Reporting Services 和 SQL Server 資料庫引擎單一虛擬機器上執行。 虛擬機器裝載報表伺服器資料庫。 加入虛擬機器透過 Azure 虛擬網路，內部部署的網域或一些其他 VPN 通道解決方案。

- 資料來源的內部部署。

![bi iaas 案例 vm 和內部部署資料來源](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Reporting Services 原生模式的設定

SQL Server 的虛擬機器庫圖像會包含 Reporting Services 原生模式安裝，但是報表伺服器未設定。 本節中的步驟設定 Reporting Services 報表伺服器。 如需設定 Reporting Services 原生模式的詳細資訊，請參閱[安裝 Reporting Services 原生模式報表伺服器 (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx)。

>[AZURE.NOTE] 使用 Windows PowerShell 指令碼來設定報表伺服器的類似內容，請參閱[使用 PowerShell 來建立 Azure VM 與原生模式報表伺服器](virtual-machines-windows-classic-ps-sql-report.md)。

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>連線至虛擬機器並開始 Reporting Services 組態管理員

有兩個連接至 Azure 虛擬機器的常見工作流程︰

- 中，按一下 [連線的虛擬機器名稱，然後按一下 [**連線**。 遠端桌面連線隨即開啟，並會自動填入的電腦名稱。

    ![連線至 azure 虛擬機器](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)

- 連線到 Windows 遠端桌面連線的虛擬機器。 在遠端桌面的使用者介面︰

    1. 輸入**雲端服務名稱**和電腦名稱。

    1. 輸入冒號 （:），以及設定為 [TCP 遠端桌面端點公用的連接埠號碼。

        Myservice.cloudapp.net:63133

        如需詳細資訊，請參閱[什麼是雲端服務？](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/)。

**啟動 [Reporting Services 組態管理員]。**

1. 在**Windows Server 2012**:

1. 從**[開始**] 畫面中，輸入**Reporting Services** ，以查看應用程式的清單。

1. 以滑鼠右鍵按一下**Reporting Services 組態管理員**，然後按一下 [**以系統管理員身分執行**]。

1. 在**Windows Server 2008 R2**:

1. 按一下 [**開始**]，然後按一下 [**所有程式]**。

1. 按一下 [ **Microsoft SQL Server 2016**。

1. 按一下 [**組態工具**。

1. 以滑鼠右鍵按一下**Reporting Services 組態管理員**，然後按一下 [**以系統管理員身分執行**]。

或

1. 按一下 [**開始**]。

1. 在 [**搜尋程式及檔案**] 對話方塊中輸入**reporting services**。 如果 VM 正在執行 Windows Server 2012 **reporting services**在輸入 Windows Server 2012 [開始] 畫面。

1. 以滑鼠右鍵按一下**Reporting Services 組態管理員**，然後按一下 [**以系統管理員身分執行**]。

    ![搜尋 ssrs 組態管理員](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>設定 Reporting Services

**服務帳戶和 web 服務的 URL:**

1. 驗證**伺服器名稱**是本機的伺服器名稱，然後按一下 [**連線**]。

1. 請注意空白**報表伺服器資料庫名稱**。 設定完成時，會建立資料庫。

1. 請確認**報表伺服器狀態**為 [**已啟動**。 如果您想要確認 Windows 伺服器管理員] 中的服務，服務就會是**SQL Server Reporting Services**的 Windows 服務。

1. 按一下 [**服務帳戶**，並視需要變更帳戶。 如果虛擬機器非網域聯結的環境中使用，內建的**ReportServer**帳戶已足夠的。 如需有關服務帳戶的詳細資訊，請參閱[服務帳戶](https://msdn.microsoft.com/library/ms189964.aspx)。

1. 按一下左窗格中的 [ **Web 服務 URL** ]。

1. 按一下 [**套用**設定的預設值。

1. 請注意**報表伺服器 Web 服務的 Url**。 請注意，預設 TCP 連接埠 80 和 URL 的一部分。 在接下來的步驟，您可以建立 Microsoft Azure 虛擬機器結束點的連接埠。

1. 在 [**結果**] 窗格中，確認已順利完成的動作。

**資料庫︰**

1. 按一下左窗格中的 [**資料庫**]。

1. 按一下 [**變更資料庫**。

1. 確認已選取 [**建立新的報表伺服器資料庫**，然後按 [下一步。

1. 驗證**伺服器名稱**，然後按一下 [**測試連接**]。

1. 如果結果是**成功的測試連線**，請按一下**[確定]** ，然後按一下 [**下一步**。

1. 請注意資料庫名稱**ReportServer** ，而是**原生****報表伺服器模式**，然後按一下 [**下一步**。

1. 在 [**認證**] 頁面上，按一下 [**下一步**]。

1. 在 [**摘要**] 頁面上，按一下 [**下一步**]。

1. 在 [**進度] 與 [完成**] 頁面上按一下 [**下一步**]。

**2012 和 2014年網頁入口網站的 URL 或報表管理員 URL:**

1. 2014 與 2012，請在左窗格中的，按一下 [ **Web 入口網站的 URL**或**報表管理員 URL** ]。

1. 按一下 [**套用**]。

1. 在 [**結果**] 窗格中，確認已順利完成的動作。

1. 按一下 [**結束**]。

在報表伺服器權限的資訊，請參閱[原生模式報表伺服器上的 [授與權限](https://msdn.microsoft.com/library/ms156014.aspx)。

### <a name="browse-to-the-local-report-manager"></a>瀏覽至本機報表管理員

若要確認設定，請瀏覽至報表管理員 VM 上。

1. VM 啟動 Internet Explorer 具有管理員權限。

1. 瀏覽至 http://localhost/reports VM 上。

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>若要連線到遠端網站入口網站或報表管理員，2014年與 2012

如果您要連線至入口網站或報表管理員用 2014年與 2012，虛擬機器從遠端電腦上的建立新的虛擬機器 TCP 端點。 根據預設，報表伺服器接聽連接埠**80**HTTP 要求。 如果您設定的報表伺服器 Url，若要使用不同的連接埠，您必須在下列指示中指定的連接埠號碼。

1. 建立虛擬機器 TCP 連接埠 80 的端點。 如需詳細資訊，請參閱此文件中的 [[虛擬機器結束點和防火牆連接埠](#virtual-machine-endpoints-and-firewall-ports)] 區段。

1. 開啟連接埠 80 虛擬機器的防火牆。

1. 瀏覽至入口網站或報表管理員，使用 Azure 虛擬機器**DNS 名稱**為 URL 中的伺服器名稱。 例如︰

    **報表伺服器**︰ http://uebi.cloudapp.net/reportserver **入口網站**︰ http://uebi.cloudapp.net/reports

    [設定報表伺服器存取權的防火牆](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>建立及發佈報表 Azure 虛擬機器

下表摘要列出一些發佈從內部部署電腦到報表伺服器裝載於 Microsoft Azure 虛擬機器上現有的報表可用的選項︰

- **報表產生器**︰ 虛擬機器包含按一下-一次版本的 Microsoft SQL Server 報表產生器的 SQL 2014 與 2012年。 若要啟動報表產生器第一次使用 SQL 2016 虛擬機器上︰

    1. 開始您的瀏覽器與系統管理員權限。

    1. 瀏覽 web 入口網站，虛擬機器，然後選取右上角的 [**下載**] 圖示。
    
    1. 選取**報表產生器**。

    如需詳細資訊，請參閱[啟動報表產生器](https://msdn.microsoft.com/library/ms159221.aspx)。

- **SQL Server Data Tools**: VM: SQL Server Data Tools 虛擬機器上已安裝，可以用來建立 [虛擬機器上的 [**報表伺服器專案**和報表。 SQL Server Data Tools 可以將報表發佈至報表伺服器虛擬機器上。

- **SQL Server Data Tools︰ 遠端**︰ 您的本機電腦，請在包含 Reporting Services 報表的 SQL Server Data Tools 中建立 Reporting Services 專案。 設定專案以連線至 web 服務 URL。

    ![ssdt SSRS 專案的專案屬性](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)

- 建立。VHD 硬碟包含報表，然後上傳並附加磁碟機。

    1. 建立。VHD 包含您的報表的本機電腦上的硬碟。

    1. 建立並安裝管理憑證。

    1. 檔案上傳 VHD 至 Azure 使用[建立和上傳至 Azure Windows Server VHD](virtual-machines-windows-classic-createupload-vhd.md)新增 AzureVHD 指令程式。

    1. 虛擬機器中附加磁碟。

## <a name="install-other-sql-server-services-and-features"></a>安裝其他的 SQL Server 服務和功能

若要安裝其他的 SQL Server 服務，例如 Analysis Services 表格式模式，會執行 SQL server 安裝精靈。 設定檔案位於虛擬機器中的本機磁碟。

1. 按一下 [**開始]** ，然後按一下 [**所有程式]**。

1. 按一下 [ **Microsoft SQL Server 2016**]、 [ **Microsoft SQL Server 2014**或 [ **Microsoft SQL Server 2012** ，然後按一下 [**組態工具]**。

1. 按一下 [ **SQL Server 安裝中心**]。

執行 C:\SQLServer_13.0_full\setup.exe、 C:\SQLServer_12.0_full\setup.exe 或 C:\SQLServer_11.0_full\setup.exe

>[AZURE.NOTE] 第一次執行 SQL Server 設定更多設定檔案可能下載，並要求的虛擬機器重新啟動電腦並重新啟動 SQL Server 安裝程式。
>
>如果您需要重複自訂選取從 Microsoft Azure 虛擬機器中的圖像，請考慮建立您自己的 SQL Server 圖像。 Analysis Services SysPrep 功能已啟用與 SQL Server 2012 SP1 CU2。 如需詳細資訊，請參閱[使用 SysPrep 安裝 SQL Server 的考量](https://msdn.microsoft.com/library/ee210754.aspx)及[Sysprep 支援的伺服器角色。](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

### <a name="to-install-analysis-services-tabular-mode"></a>若要安裝 Analysis Services 表格式模式

在此步驟區段**摘要**的 Analysis Services 表格式模式安裝。 如需詳細資訊，請參閱下列各項︰

- [安裝 Analysis Services 表格式模式](https://msdn.microsoft.com/library/hh231722.aspx)

- [表格式模型 （Adventure Works 教學課程）](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**若要安裝 Analysis Services 表格式模式︰**

1. 在 SQL Server 安裝精靈中，按一下 [**安裝**的左窗格中，然後按一下**獨立安裝新的 SQL server 或將功能新增至現有的安裝**。

    - 如果您看到**瀏覽資料夾**，請瀏覽至 c:\SQLServer_13.0_full、 c:\SQLServer_12.0_full 或 c:\SQLServer_11.0_full，，然後按一下 [**確定]**。

1. 在 [產品更新] 頁面上，按一下 [**下一步**]。

1. 在 [**安裝類型**] 頁面上選取 [**執行新的 SQL Server 安裝**，然後按一下 [**下一步**。

1. 在**設定角色**] 頁面上，按一下 [**安裝 SQL Server 功能**。

1. 在**功能選擇**] 頁面上，按一下 [ **Analysis Services**]。

1. 在 [**執行個體設定**] 頁面中，輸入描述性的名稱，例如**表格式****名為執行個體**和**執行個體 Id** ] 文字方塊。

1. 在 [**分析服務設定**] 頁面上選取 [**表格式模式**]。 將目前的使用者新增至 [系統管理權限清單。

1. 完成並關閉 [SQL Server 安裝精靈]。

## <a name="analysis-services-configuration"></a>Analysis Services 設定

### <a name="remote-access-to-analysis-services-server"></a>遠端存取 Analysis Services 伺服器

Analysis Services 伺服器只支援 windows 驗證。 若要從用戶端應用程式，例如 SQL Server Management Studio 或 SQL Server Data Tools 遠端存取 Analysis Services，虛擬機器需要加入的本機的網域，請使用 Azure 虛擬網路。 如需詳細資訊，請參閱[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)。

TCP 連接埠**2383年**接聽的 Analysis Services 的**預設的執行個體**。 在虛擬機器防火牆開啟連接埠。 連接埠**2383年**也接聽叢集命名的 Analysis Services 執行個體。

**具名執行個體**的 Analysis Services 的 SQL Server 瀏覽器服務，才能管理連接埠的存取權。 SQL Server 瀏覽器預設設定不連接埠**2382年**。

在虛擬機器防火牆中，開啟連接埠**2382年**並建立名為執行個體連接埠靜態 Analysis Services。

1. 若要確認已在 VM 及程序使用連接埠的使用中的連接埠，請執行下列命令具有管理員權限︰

        netstat /ao

1. 若要建立靜態的 Analysis Services 使用 SQL Server Management Studio 命名執行個體連接埠更新 「 連接埠' 表格式另存新檔中的值執行個體一般的屬性。 如需詳細資訊，請參閱[設定允許 Analysis Services 存取 Windows 防火牆](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed)中的 「 預設值，使用固定的連接埠或具名執行個體 」。

1. 重新啟動 Analysis Services 服務的表格式執行個體。

如需詳細資訊，請參閱此文件中的 [**虛擬機器結束點和防火牆連接埠**] 區段。

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>虛擬機器結束點和防火牆連接埠

本節摘要列出建立的 Microsoft Azure 虛擬機器結束點和虛擬機器防火牆中開啟的連接埠。 下表摘要列出建立端點的**TCP**連接埠和虛擬機器防火牆中開啟的連接埠。

- 如果您使用的單一 VM 下列兩個項目均為 true，您不需要建立 VM 端點，您不需要的防火牆 VM 上開啟連接埠。

    - 您遠端連線到 SQL Server 功能 VM 上。 建立遠端桌面連線的 vm 和存取 VM 本機的 SQL Server 功能不會視為遠端連線至 SQL Server 功能。

    - 您不加入 VM 內部部署網域透過 Azure 虛擬網路或另一種 VPN 通道解決方案。

- 如果網域並未加入虛擬機器，但您想要從遠端連線 VM 的 SQL Server 功能︰

    - 開啟連接埠 VM 上的防火牆。

    - 建立所述的連接埠 （*） 的虛擬機器結束點。

- 如果加入虛擬機器使用 VPN 通道，例如 Azure 虛擬網路的網域，然後結束點就不需要。 不過在 VM 防火牆中開啟連接埠。

  	|連接埠|類型|描述|
|---|---|---|
|**80**|TCP|報表伺服器遠端存取 （*）。|
|**1433**|TCP|SQL Server Management Studio （*）。|
|**1434**|UDP|SQL Server 瀏覽器。 這需要時 VM 中的加入網域。|
|**2382**|TCP|SQL Server 瀏覽器。|
|**2383**|TCP|SQL Server Analysis Services 預設的執行個體，直具名的執行個體。|
|**使用者定義**|TCP|建立靜態的 Analysis Services 執行個體連接埠名為您選擇的連接埠號碼，然後解除封鎖的防火牆連接埠號碼。|

如需有關如何建立端點的詳細資訊，請參閱下列各項︰

- 建立結束點︰[如何設定虛擬機器結束點](virtual-machines-windows-classic-setup-endpoints.md)。

- SQL Server︰ 請參閱[佈建 Azure SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)」 完成設定步驟連線至虛擬機器使用 SQL Server Management Studio 」 區段。

下圖說明 VM 上允許遠端存取功能及元件 VM 防火牆中開啟的連接埠。

![若要開啟 Azure Vm 中的 bi 應用程式的連接埠](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>資源

- 檢閱支援原則的 Microsoft Azure 虛擬機器環境中使用的伺服器軟體。 下列主題摘要列出支援 BitLocker 移轉叢集，與網路負載平衡等功能。 [Microsoft 伺服器軟體支援的 Azure 虛擬機器](http://support.microsoft.com/kb/2721672)。

- [SQL Server，內部 Azure 虛擬機器概觀](virtual-machines-windows-sql-server-iaas-overview.md)

- [虛擬機器](https://azure.microsoft.com/documentation/services/virtual-machines/)

- [佈建 Azure SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)

- [如何將資料磁碟附加至虛擬機器](virtual-machines-windows-classic-attach-disk.md)

- [SQL server Azure VM 上移轉資料庫](virtual-machines-windows-migrate-sql.md)

- [決定伺服器模式的 Analysis Services 執行個體](https://msdn.microsoft.com/library/gg471594.aspx)

- [多維度模型 （Adventure Works 教學課程）](https://technet.microsoft.com/library/ms170208.aspx)

- [Azure 文件中心](https://azure.microsoft.com/documentation/)

- [混合環境中使用 Power BI](https://msdn.microsoft.com/library/dn798994.aspx)

>[AZURE.NOTE] [送出意見反應及透過 Microsoft SQL Server 連線的連絡人資訊](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>社群內容

- [使用 PowerShell 的 azure SQL 資料庫管理](http://blogs.msdn.com/b/windowsazure/archive/2013/02/07/windows-azure-sql-database-management-with-powershell.aspx)
