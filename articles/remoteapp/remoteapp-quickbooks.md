<properties 
    pageTitle="部署中 Azure RemoteApp QuickBooks |Microsoft Azure" 
    description="瞭解如何使用 Azure RemoteApp 共用 QuickBooks。" 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-do-you-deploy-quickbooks-in-azure-remoteapp"></a>您部署中 Azure RemoteApp QuickBooks 的方式？

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

以 Azure RemoteApp 中的應用程式共用 QuickBooks，請使用下列資訊。


在混合式部署或雲端集合中，您可以使用 Azure RemoteApp 共用 QuickBooks 2015 企業版。 公司檔案必須位於 VM 執行 QuickBooks 有別於 Azure RemoteApp 伺服器的資料庫伺服器。 永遠不儲存公司檔案的 Azure RemoteApp 圖像-如果您執行這個動作預期資料遺失。 只有 QuickBooks 企業支援 QuickBooks 檔案 QuickBooks 資料庫伺服器可透過標準 Windows 網路存取與外部共用。   

> [AZURE.IMPORTANT] 公司檔案的 QuickBooks 資料庫伺服器必須位於相同的 VNET 與 Azure RemoteApp 集合中的個別 VM。  

## <a name="steps-to-deploy-quickbooks"></a>若要部署 QuickBooks 的步驟

1. 建立 Azure VM 安裝 QuickBooks，QuickBooks 資料庫伺服器，並將 Azure VM 公司檔案。  請務必妥善設定防火牆規則。
2. [自訂圖像](remoteapp-imageoptions.md)上安裝 QuickBooks 和建立[Azure RemoteApp 集合](remoteapp-collections.md)，[雲端] 或 [內 VM 裝載 QuickBooks 資料庫伺服器的公司檔案的所在位置完全相同 VNET 混合式部署。 
3.  [發佈](remoteapp-publish.md)使用者 QuickBooks 應用程式
4.  啟動 Azure RemoteApp 裝載 QuickBooks 用戶端、 使用標準 Windows 網路主機服務 QuickBooks 資料庫伺服器的 vm 瀏覽和開啟公司檔案。 

## <a name="documentation-references"></a>文件參照

- QuickBooks[支援的設定](http://enterprisesuite.intuit.com/products/enterprise-solutions/technical/#top)
- QuickBooks[的部署選項](http://enterprisesuite.intuit.com/everythingenterprise/launchpad/new-user/)

您也可以查看我 Ignite 簡報[的基本概念的 Microsoft Azure RemoteApp 管理](https://channel9.msdn.com/Events/Ignite/2015/BRK3868)-快轉至 1:02:45，前往 QuickBooks 組件。

## <a name="deployment-architecture"></a>部署架構

![QuickBooks + Azure RemoteApp 部署](./media/remoteapp-quickbooks/ra-quickbooks.png)