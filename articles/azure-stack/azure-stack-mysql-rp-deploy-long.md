<properties
    pageTitle="部署 MySQL 資源提供者上 Azure 堆疊 |Microsoft Azure"
    description="Azure 堆疊部署 MySQL 資源提供者的詳細的步驟。"
    services="azure-stack"
    documentationCenter=""
    authors="Dumagar"
    manager="byronr"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dumagar"/>


# <a name="deploy-the-mysql-resource-provider-on-azure-stack-to-use-with-webapps"></a>部署 MySQL 資源提供者，若要使用 WebApps Azure 堆疊上

> [AZURE.NOTE] Azure 堆疊 TP1 部署僅適用於下列資訊。

使用本文所要遵循概念 (POC) Azure 堆疊證明 MySQL 資源提供者設定的詳細的步驟，好讓您可以開始[使用 MySQL 資料庫](azure-stack-mysql-rp-deploy-short.md)Azure 堆疊，包括使用 MySQL 作為後端 WordPress 網站上建立[Azure Web](azure-stack-webapps-deploy.md)應用程式。

## <a name="set-up-steps-before-you-deploy"></a>部署前設定步驟

部署資源提供者之前，您需要︰

- 有預設的 Windows Server 圖像的.NET 3.5
- 關閉 Internet Explorer (IE) 增強安全性
- 安裝最新版的 PowerShell 的 Azure

### <a name="create-an-image-of-windows-server-including-net-35"></a>建立包括.NET 3.5 的 Windows Server 的圖像

如果您將下載 Azure 堆疊位 2016/2/23，因為預設基底 Windows Server 2012 R2 圖像包含.NET 3.5 framework 在此下載及更新版本，您可以略過此步驟。

如果您下載 2/23/2016 之前，您需要建立 Windows Server 2012 R2 資料中心 VHD.NET 3.5 圖像，並設定為在平台圖像存放庫中的預設圖像。

### <a name="turn-off-ie-enhanced-security-and-enable-cookies"></a>關閉 IE 增強的安全性和啟用 cookie

若要部署資源提供者，您 PowerShell 整合式指令碼環境 （ise [以系統） 系統管理員身分執行，因此您必須登入 Azure Active Directory 系統管理員和使用者同時登增益集您使用 Internet Explorer 設定檔中允許 cookie 及 JavaScript。

**若要關閉 IE 增強安全性︰**

1. 登入/管理員，AzureStack Azure 堆疊的概念 (PoC) 電腦，然後開啟 [伺服器管理員。

2. 關閉**IE 增強的安全性設定**管理員和使用者兩者均適用。

3. 身為管理員， **ClientVM.AzureStack.local**虛擬機器登入，然後開啟 [伺服器管理員。

4. 關閉**IE 增強的安全性設定**管理員和使用者兩者均適用。

**若要啟用 cookie:**

1. 在 Windows [開始] 畫面中，按一下 [**所有應用程式** **Windows 附屬應用程式**，以滑鼠右鍵按一下**Internet Explorer**、 指向 [**更多**]，然後按一下然後選取 [**以系統管理員身分執行**。

2. 如果出現提示，請核取 [**使用建議的安全性**，然後再按一下**[確定]**。

3. 在 Internet Explorer 中，按一下 [**工具 （齒輪） 圖示** &gt; **網際網路選項** &gt; **隱私權**] 索引標籤。

4. 按一下 [**進階]**確定已選取兩個**接受**按鈕、 **[確定**]，然後按一下然後再按一下**[確定]** 。

5. 關閉 Internet Explorer，並重新啟動 PowerShell 以系統管理員身分 ise [以系統。

### <a name="install-an-azure-stack-compatible-release-of-azure-powershell"></a>安裝 PowerShell 的 Azure Azure 堆疊相容發行

1. 從您的用戶端 VM] 解除安裝任何現有 PowerShell 的 Azure。

2. 以 AzureStack/系統管理員身分登入 Azure 堆疊 POC 電腦。

3. 使用遠端桌面，請登入**ClientVM.AzureStack.local**虛擬機器以系統管理員。

4. 開啟 [控制台]，按一下 [**解除安裝程式**&gt;按一下**PowerShell 的 Azure** &gt;按一下 [**解除安裝**。

5. [下載最新 PowerShell 的 Azure 支援的 Azure 堆疊](http://aka.ms/azstackpsh)並將其安裝。

    安裝 PowerShell 之後，您可以執行此驗證 PowerShell 指令碼，請確定您可以連線到您 Azure 堆疊的執行個體 （應該出現登入網頁）。

## <a name="bootstrap-the-resource-provider-deployment-powershell"></a>啟動安裝程式資源提供者部署 PowerShell

1. 將 Azure 堆疊 POC 遠端桌面連線到 clientVm.AzureStack.Local 並登入 azurestack\\azurestackuser。

2. [下載 MySQL 資源點數二進位](http://aka.ms/masmysqlrp)檔案，然後將它解壓縮至 d\\MySQLRP。

3. 執行 d\\MySQLRP\\Bootstrap.cmd 檔案以系統管理員 (azurestack\administrator)。

    這會在 PowerShell ise [以系統開啟 Bootstrap.ps1 檔案。

4. 當 PowerShell ise [以系統 windows 完成載入時，請按一下 [播放] 按鈕，或按 F5。

    將會載入兩個主要的搜尋索引標籤，每個包含所有的指令碼及部署您 MySQL 資源提供者所需的檔案。

## <a name="prepare-prerequisites"></a>準備的先決條件

按一下 [**準備先決條件**] 索引標籤︰

- 建立所需的憑證
- MySQL 二進位檔案下載到 Azure 疊
- 上傳到 Azure 堆疊上儲存的帳戶的成品
- 發佈庫項目

### <a name="create-the-required-certificates"></a>建立所需的憑證
這個**新增 SslCert.ps1**指令碼加入\_。D: AzureStack.local.pfx SSL 憑證\\MySQLRP\\先決條件\\BlobStorage\\容器資料夾。 憑證保護資源提供者與本機的執行個體的 Azure 資源管理員之間的通訊。

1. **準備先決條件**主要的搜尋] 索引標籤中，按一下 [**新增 SslCert.ps1** ] 索引標籤，並執行。

2. 在出現提示時，輸入 PFX 密碼來保護私人索引鍵和**記下密碼**。 您必須將其更新版本。

### <a name="download-mysql-binaries-to-your-azure-stack"></a>MySQL 二進位檔案下載到 Azure 疊

1. 選取 [**下載 MySqlServer.ps1** ] 索引標籤上，執行。
2. 出現提示時，按一下 [****在 [確認] 對話方塊中，接受使用者授權合約。

    此命令新增至 D:\MySql\Prerequisites\BlobStorage\Container 資料夾的兩個 zip 檔案。

### <a name="upload-all-artifacts-to-a-storage-account-on-azure-stack"></a>將所有成品上都傳到 Azure 堆疊上儲存的帳戶

1. 按一下 [**上傳-Microsoft.MySql-RP.ps1** ] 索引標籤上，執行。

2. 在 [Windows PowerShell 認證邀請] 對話方塊中，輸入 Azure 堆疊服務系統管理員認證。

3. 出現提示時的 Azure Active Directory 租用戶識別碼，輸入您的 Azure Active Directory 租用戶的完整網域名稱︰ 例如，microsoftazurestack.onmicrosoft.com。

    在快顯視窗會要求認證。

    > [AZURE.TIP] 如果沒有出現快顯視窗，您可能還沒有關閉 IE 增強的安全性，以啟用 JavaScript 上這台電腦和使用者，或您還沒有接受 cookie ie。 請參閱[設定部署前的步驟](#set-up-steps-before-you-deploy)。

4. 輸入您的 Azure 堆疊服務管理員認證，然後再按一下 [**登入**]。

### <a name="publish-gallery-items-for-later-resource-creation"></a>發佈庫更新版本的資源建立的項目

選取 [**發佈 GalleryPackages.ps1** ] 索引標籤上，執行。 這個指令碼加入 Azure 堆疊 POC 入口網站的 marketplace，您可以用來部署服務商場項目的資料庫資源的兩個服務商場項目。

## <a name="deploy-the-mysql-resource-provider-vm"></a>部署 MySQL 資源提供者 VM

現在您已準備好 Azure 堆疊 PoC 必要的憑證與服務商場的項目，您可以部署 SQL Server 資源提供者。 按一下 [**部署 MySQL 提供者**] 索引標籤︰

   - 提供的部署程序參照 JSON 檔案中的值
   - 部署資源提供者
   - 更新的本機 DNS
   - 註冊的 SQL Server 資源提供者介面卡

### <a name="provide-values-in-the-json-file"></a>提供 JSON 檔案中的值

按一下 [ **Microsoft.MySqlprovider.Parameters.JSON**]。 這個檔案的 Azure 資源管理員範本必須正確部署至 Azure 堆疊的參數。

1. 填寫 JSON 檔案中的**空白**參數︰

    - 請確定您提供**adminusername**和**adminpassword** MySQL 資源提供者 vm。

    - 請確定您所做的筆記中[準備 prequisites](#prepare-prerequisites)步驟**SetupPfxPassword**參數提供的密碼。

    - 請確定您提供**basicAuthUserName**和**basicAuthPassword**參數。 **記下這些值。** 您需要進行更新版本才能註冊資源提供者。

2. 按一下 [**儲存**]。

### <a name="deploy-the-resource-provider"></a>部署資源提供者

1. 按一下 [**部署-Microsoft.Mysql-provider.PS1** ] 索引標籤，並執行指令碼。
2. 在出現提示時，則 Azure Active Directory 中輸入您的租用戶的名稱。
3. 在快顯視窗中，提交您 Azure 堆疊 service 的管理員認證。

完整的部署可能需要一些高度利用 Azure 堆疊 POCs 上的 15 又 45 分鐘。

### <a name="update-the-local-dns"></a>更新的本機 DNS

1. 按一下 [ **Register-Microsoft.MySQL-fqdn.ps1** ] 索引標籤，並執行指令碼。
2. 出現提示時的 Azure Active Directory 租用戶識別碼，輸入您的 Azure Active Directory 租用戶的完整網域名稱︰ 例如， **microsoftazurestack.onmicrosoft.com**。

### <a name="register-the-sql-rp-resource-provider"></a>註冊 SQL 資源點數資源提供者

1. 按一下 [ **Register-Microsoft.My-provider.ps1** ] 索引標籤，並執行指令碼。

2. 提示輸入認證，請使用您為**basicAuthUserName**和**basicAuthPassword**參數記下。

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>驗證使用 Azure 堆疊入口網站的部署

1. 登出 ClientVM 並再次登入為**AzureStack\User**。

2. 在桌面上，按一下 [ **Azure 堆疊 POC 入口網站**並登入服務管理員入口網站

3. 請確認部署成功。 按一下 [**瀏覽** &gt; **資源群組**] 下，按一下您所使用的資源群組 （預設為**MySQLRP**），然後確定刀 （上半） 的基礎部分讀取**已成功部署**。


4. 請確認註冊成功。 按一下 [**瀏覽** &gt; **資源提供者**，然後**MySQL**本機的外觀。

## <a name="create-your-first-mysql-database-to-test-your-deployment"></a>建立您的第一個 MySQL 資料庫來測試您的部署

1. 服務管理員身分登入 Azure 堆疊 POC 入口網站

2. 按一下 [**+**按鈕&gt;**自訂** &gt; **MySQL 伺服器與資料庫**。

3. 填寫表單和資料庫的詳細資訊。

    **記下 」 「 您輸入伺服器名稱。** 您的資料庫的連線字串包含 「 伺服器名稱 」 的使用者名稱的一部分︰ 例如， ** "user@ <ServerName>」**。 您必須輸入此格式的使用者名稱，當您連線至資料庫︰ 例如，當您部署 MySQL 網站使用 Azure 網站資源提供者


## <a name="next-steps"></a>後續步驟

請嘗試其他[PaaS 服務](azure-stack-tools-paas-services.md)，例如[SQL Server 資源提供者](azure-stack-sql-rp-deploy-short.md)與[Web 應用程式資源提供者](azure-stack-webapps-deploy.md)。
