<properties
    pageTitle="Azure 堆疊應用程式服務技術預覽您開始之前的 1 |Microsoft Azure"
    description="部署 Azure 堆疊上的 Web 應用程式之前，先完成的步驟"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>
    
# <a name="before-you-get-started-with-azure-stack-web-apps"></a>之前 Azure 堆疊 Web Apps 快速入門

> [AZURE.NOTE] Azure 堆疊 TP1 部署僅適用於下列資訊。

您必須安裝 Azure 堆疊 Web 應用程式的幾個項目︰

- 完成的部署的[Azure 堆疊技術預覽 1](azure-stack-run-powershell-script.md)
- 在小型 Azure 堆疊 Web 應用程式部署 Azure 堆疊系統中足夠的空間。  所需的空間大約是 20 GB 的磁碟空間。
- [執行 SQL Server 的伺服器](#SQL-Server)。

>[AZURE.NOTE] 所有用戶端 VM 進行下列步驟。

## <a name="before-you-deploy-azure-stack-web-apps"></a>部署 Azure 堆疊 Web 應用程式之前

若要部署資源提供者，您必須以系統管理員身分執行 PowerShell 整合式指令碼 Environment(ISE)。 因此，您需要您用來登入 Azure Active Directory 的 Internet Explorer 設定檔中允許 cookie 及 JavaScript。

## <a name="turn-off-internet-explorer-enhanced-security"></a>關閉增強型的 Internet Explorer 安全性

1.  登入**AzureStack/系統管理員**，Azure 堆疊的概念 (POC) 電腦，然後開啟 [**伺服器管理員**。
2.  關閉**Internet Explorer 增強的安全性設定**管理員和使用者兩者均適用。
3.  身為管理員，ClientVM.AzureStack.local 虛擬機器登入，然後開啟 [**伺服器管理員**。
4.  關閉**Internet Explorer 增強的安全性設定**管理員和使用者兩者均適用。

## <a name="enable-cookies"></a>啟用 cookie

1.  選取 [**開始**] > [**所有應用程式**中，> **Windows 附屬應用程式**。 以滑鼠右鍵按一下**Internet Explorer** > **更多** > **系統管理員身分執行**。
2.  如果系統提示您選取 [**使用建議的安全性**，然後選取 [ **[確定]**。
3.  在 Internet Explorer 中，選取 [**工具**（齒輪圖示） >**網際網路選項** > **隱私權** > **進階]**。
4.  選取 [**進階**]。 請確定這兩個**接受**核取方塊已選取，然後再次選取 [**確定**] 和**[確定]** 。
5.  關閉 Internet Explorer 中，並重新啟動 PowerShell 以系統管理員身分 ise [以系統。

## <a name="install-the-latest-version-of-azure-powershell"></a>安裝最新版的 PowerShell 的 Azure

1.  **AzureStack/系統管理員**身分登入 Azure 堆疊 POC 電腦。
2.  使用遠端桌面連線到 ClientVM.AzureStack.local 虛擬機器以系統管理員身分登入。
3.  開啟**[控制台]** ，然後選取 [**解除安裝程式**。 
4.  **Microsoft Azure PowerShell 年 11 月 2015年**上按一下滑鼠右鍵，然後選取 [**解除安裝**。
5.  解除安裝完成後，重新啟動 ClientVM.AzureStack.local 虛擬機器
6.  下載並安裝最新的[PowerShell 的 Azure](http://aka.ms/azstackpsh)。


## <a name="sql-server"></a>SQL Server

根據預設，Azure 堆疊 Web 應用程式安裝程式設定為使用 Azure 堆疊 SQL 資源提供者以及已安裝 SQL Server。 當您安裝 SQL Server 資源提供者 （SQL 資源點數） 確保您記下資料庫系統管理員的使用者名稱和密碼。 您必須同時安裝 Azure 堆疊 Web 應用程式時。
注意︰ 您也必須部署與使用其他伺服器執行 SQL Server 的選項。 您可以選擇使用當您完成 Azure 堆疊 Web 應用程式安裝程式中的選項的 SQL Server 執行個體。
