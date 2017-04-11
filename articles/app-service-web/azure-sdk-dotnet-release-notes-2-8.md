
<properties 
   pageTitle="Azure SDK.NET 2.8 版本資訊" 
   description="Azure SDK.NET 2.8 版本資訊" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/17/2016"
   ms.author="juliako"/>
 
# <a name="azure-sdk-for-net-28-281-and-282"></a>Azure SDK.NET 2.8、 2.8.1 和 2.8.2

##<a name="overview"></a>概觀
 
本文包含版本資訊 （包含已知的問題和重大變更） Azure SDK 的.NET 2.8，2.8.1 和 2.8.2 發行。 

完成清單中的新功能及在這個版本中所做的更新，請參閱[Azure SDK 2.8 Visual Studio 2013 和 Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)公告。 

##  <a name="azure-sdk-for-net-28"></a>Azure SDK.NET 2.8

### <a name="download-azure-sdk-for-net-28"></a>下載適用於.NET 2.8 Azure SDK

[Azure SDK.NET 2.8 Visual Studio 2015](http://go.microsoft.com/fwlink/?LinkId=699285) 

[Azure SDK.NET 2.8 Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=699287)
 
### <a name="net-452-support"></a>.NET 4.5.2 支援 

####<a name="known-issues"></a>已知的問題

Azure.NET SDK 2.8 可讓您建立.NET 4.5.2 雲端服務套件。 不過.NET 4.5.2 framework 將不會安裝在預設的來賓 OS 圖像，直到年 1 月 2016 來賓 OS 放開。 前 4.5.2，.NET framework 才會在另一個來賓 OS 發行版本 – 11 月 2015年 02。 請參閱追蹤時將發行圖像[Azure 來賓作業系統版本和 SDK 相容性矩陣](../cloud-services/cloud-services-guestos-update-matrix.md)的頁面。  發行年 11 月 2015年 02 圖像是當您可以選擇使用該映像更新雲端服務設定檔案 (.cscfg) 檔案。 在服務設定中設定檔 ServiceConfiguration 項目的 osVersion 屬性字串 「 「 台北 」-來賓-OS-4.26_201511-02 」。 如果您選擇要使用此圖像，然後不再就會自動更新，以來賓 OS 選擇加入集。 若要取得自動更新 osVersion 必須設定為 「 * 」 和.NET 4.5.2 時，才會透過年 1 月 2016年中的自動更新。

###<a name="azure-data-factory"></a>Azure 資料工廠

####<a name="known-issues"></a>已知的問題 

在**資料工廠範本**建立專案包含範例資料，如果電腦上安裝的 azure power 命令介面版本之後 0.9.8，可能會失敗 azure power 命令介面指令碼。

若要順利建立此類型的專案，您必須安裝[azure power 命令介面版本 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi)。


### <a name="azure-resource-manager-tools"></a>Azure 資源管理員工具 

####<a name="breaking-changes"></a>重大變更

若要使用新的 Azure PowerShell cmdlet 1.0 版這個版本中已更新 Azure 資源群組專案所提供的 PowerShell 指令碼。  這個新的指令碼無法搭配從 Visual Studio 時使用 SDK 2.8 之前的版本。  

從 SDK 的舊版本中建立專案的指令碼不在 Visual Studio 中執行，使用 2.8 SDK 時。  所有指令碼會繼續外部 Visual Studio 使用適當的 Azure PowerShell 指令程式的版本。  

為 2.8 SDK 需要 1.0 版 Azure PowerShell cmdlet。  所有其他版本的 SDK 需要 0.9.8 新版 Azure PowerShell cmdlet。  如需詳細資訊，請參閱[此](http://go.microsoft.com/fwlink/?LinkID=623011)部落格。

###<a name="web-tools-extensions"></a>Web 工具] 的副檔名

####<a name="known-issues"></a>已知的問題

在下列版本中，將會解決下列已知的問題。

- 應用程式服務相關雲端和伺服器管理員無法運作的非生產環境 （例如 Azure china （中國） 或 Azure 堆疊客戶） 的手勢。 這些受影響的區域中的客戶，從 Azure 入口網站下載發佈設定檔會啟用發佈功能。 未來版本會 Azure china （中國） 和堆疊客戶修復手勢，例如 「 附加偵錯工具 」 和 「 檢視串流的記錄檔]。 
- 建立應用程式的深入見解的執行個體的部署時位於非東亞美國地區的應用程式服務期間，客戶可能會看到錯誤。 在下列情況下，在入口網站中建立應用程式服務並下載發佈設定檔會啟用發佈的案例。 

###<a name="azure-hdinsight-tools"></a>Azure HDInsight 工具

####<a name="new-updates"></a>新的更新

- 您可以在幾乎任何虛透過 HiveServer2 叢集執行登錄區查詢，並查看工作記錄即時。
- 使用新登錄區工作執行檢視，您可以深入探究您更深入的工作，尋找更多詳細資料，並識別潛在問題。

資訊，請參閱[Azure SDK 2.8 Visual Studio 2013 和 Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)。 

## <a name="azure-sdk-for-net-281"></a>Azure SDK.NET 2.8.1

### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>Visual Studio 2013 和 Visual Studio 2015 的已知的問題
 
1. 觸發的 WebJob 發佈至位置會顯示及錯誤並不會設定排程，但它會推 WebJob 至 Azure。 需要排程工作的客戶可以設定 WebJob 的排程，然後使用 Azure 入口網站。 
2. Python 客戶可能會遇到偵錯工具問題。 服務小組這個時推出修正，但如果受影響的客戶，請讓 Microsoft 知道的論壇或公告部落格上，或釋備忘稿註解] 區段。 
3. 在特定的區域 （例如南美洲印度） 的客戶，會遇到佈建錯誤的應用程式服務。 這是與入口網站，一致，會發生此問題的客戶可以使用 Azure 入口網站要求存取發佈至這些地理區域。 當使用者要求使用這些區域存取 Azure 的入口網站佈建應合作。 

##<a name="azure-sdk-for-net-282"></a>Azure SDK.NET 2.8.2

追蹤您在安裝 2.8.2 工具]、 [客戶可能會遇到下列問題。         

- 如果您使用的 Windows 10，沒有安裝 Internet Explorer，您可能會收到 「 無法找到 Internet Explorer 」 錯誤。
若要解決此問題，請安裝 Internet Explorer，使用 [新增/移除 Windows 元件] 對話方塊。

如果您查看此問題，請使用 [傳送的笑臉] 功能回報。

如需詳細資訊，請參閱[此](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/)文章。
##<a name="other-updates"></a>其他的更新

其他的更新，請參閱[Azure SDK 2.8 公告張貼的文章](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)。

##<a name="also-see"></a>另請參閱

[Azure SDK 2.8 公告文章](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[支援與淘汰網站資訊 Azure SDK 的.NET 和 Api](https://msdn.microsoft.com/library/azure/dn479282.aspx)

