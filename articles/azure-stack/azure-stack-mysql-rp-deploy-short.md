<properties
    pageTitle="MySQL 資料庫作為 PaaS Azure 堆疊上 |Microsoft Azure"
    description="瞭解部署 MySQL 資源提供者，並提供為 Azure 堆疊上服務的 MySQL 的快速步驟。"
    services="azure-stack"
    documentationCenter=""
    authors="Dumagar"
    manager="bradleyb"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dumagar"/>

# <a name="use-mysql-databases-as-paas-on-azure-stack"></a>MySQL 資料庫作為 PaaS Azure 堆疊上

> [AZURE.NOTE] Azure 堆疊 TP1 部署僅適用於下列資訊。

您可以部署 Azure 堆疊上的 MySQL 資源提供者。 部署資源提供者之後，您可以建立 MySQL 伺服器和透過 Azure 資源管理員部署範本的資料庫，並提供 MySQL 資料庫的服務。 MySQL 資料庫]，也就是常見的網站上，支援許多網站平台。 部署資源提供者之後，您可以建立 WordPress 網站從 Azure Web 應用程式平台作為服務 (PaaS) 的附加元件的 Azure 堆疊。

## <a name="quick-steps-to-deploy-the-resource-provider"></a>快速步驟來部署資源提供者
如果您已經熟悉 Azure 堆疊，請使用這些步驟。 如果您想要更多詳細資料，請遵循每個區段中的連結，或直接前往[部署 MySQL 資料庫資源提供者上的介面卡 Azure 堆疊 POC](azure-stack-mysql-rp-deploy-long.md)。

1.  請確定該您[完成所有設定步驟](azure-stack-mysql-rp-deploy-long.md#set-up-steps-before-you-deploy)才能部署資源提供者︰

    - .NET 3.5 framework 已設定基底的 Windows Server 圖像。 （如果您下載 Azure 堆疊位年 2 月 23，2016 年，您可以略過此步驟。）
    - [已安裝與 Azure 堆疊相容 PowerShell 的 Azure 版本](http://aka.ms/azStackPsh)。
    - 在 Internet Explorer ClientVM，[加強的安全性已關閉的 Internet Explorer 和 cookie 已啟用](azure-stack-mysql-rp-deploy-long.md#Turn-off-IE-enhanced-security-and-enable-cookies)的安全性設定。

2. [下載 MySQL 資源提供者的二進位檔案](http://aka.ms/masmysqlrp)，然後將它解壓縮的概念 (PoC) 證明您 Azure 堆疊的 ClientVM 上。

3. [執行 bootstrap.cmd 和指令碼](azure-stack-mysql-rp-deploy-long.md#Bootstrap-the-resource-provider-deployment-PowerShell-and-Prepare-for-deployment)。

    一組指令碼分為兩個主要的搜尋索引標籤，隨即會開啟中 PowerShell 整合式指令碼環境 （ise [以系統）。 序列中執行所有載入指令碼從左到右中每個索引標籤。

    1. 在 [**準備**] 索引標籤從左到右，若要執行的指令碼︰

        - 建立萬用字元憑證的安全性資源提供者和 Azure 資源管理員之間的通訊。
        - 接受 MySQL 使用者授權合約，然後下載 MySQL 二進位檔案。
        - Azure 堆疊儲存帳戶上, 傳憑證和其他所有的成品。
        - 發佈庫封包，好讓您可以部署透過庫 MySQL 資源。

        > [AZURE.IMPORTANT] 如果有任何指令碼懸置沒有什麼理由提交您的 Azure Active Directory 租用戶後，您的安全性設定可能會封鎖 DLL 所需執行的部署。 若要解決此問題，尋找 Microsoft.AzureStack.Deployment.Telemetry.Dll 資料夾中的資源提供者，以滑鼠右鍵按一下、 [**屬性**]，然後檢查 [**一般**] 索引標籤中的 [**解除封鎖**。

    2. 在 [**部署**] 索引標籤從左到右，若要執行的指令碼︰

        - [部署虛擬機器 (VM)](azure-stack-mysql-rp-deploy-long.md#Deploy-the-MySQLResource-Provider-VM)將裝載您資源提供者，MySQL 伺服器和會產生的資料庫。 這個指令碼參照 JSON 參數檔案，您需要更新與一些值，才能執行指令碼。
        - [註冊本機 DNS 記錄](azure-stack-mysql-rp-deploy-long.md#Update-the-local-DNS)的會對應到您的資源提供者 VM。
        - [註冊您的資源提供者](azure-stack-mysql-rp-deploy-long.md#Register-the-MySQL-RP-Resource-Provider)使用本機 Azure 資源管理員。

        > [AZURE.IMPORTANT] 所有指令碼假設基本作業系統圖像符合先決條件 (.NET 3.5 安裝，JavaScript 及 ClientVM，與最新版的安裝 PowerShell 的 Azure 上啟用 cookie)。 如果您收到錯誤，當您執行指令碼時，請再次檢查您符合的先決條件。

5. 若要[測試新 MySQL 資源提供者](/azure-stack-MySql-rp-deploy-long.md#create-your-first-mysql-database-to=test-your-deployment)，部署 MySQL 資料庫從 Azure 堆疊入口網站。 按一下 [**建立** &gt; **自訂** &gt; **MySQL 伺服器和資料庫**。

此應收到您 MySQL 資源提供者，並執行關於 25 分鐘。
