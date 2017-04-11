<properties 
   pageTitle="Azure SDK.NET 2.6 版本資訊" 
   description="Azure SDK.NET 2.6 版本資訊" 
   services="app-service/web" 
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

 
# <a name="azure-sdk-for-net-26-release-notes"></a>Azure SDK.NET 2.6 版本資訊

這份文件包含 Azure SDK.NET 2.6 發行版本資訊。 

Azure SDK 2.6 與您可以開發雲端服務 (PaaS) 為目標應用程式.NET 4.5.2 或.NET 4.6 所提供的手動安裝目標.NET Framework 雲端服務角色。 請參閱[安裝.NET 雲端服務角色](http://go.microsoft.com/fwlink/?LinkID=309796)。


##<a name="service-bus-updates"></a>服務匯流排更新

- 事件集線器︰ 

    - 將其他 publisher 端點公開事件集線器傳送事件時，現在可讓目標的存取控制。
    - 其他穩定性與新增事件集線器功能改進。
    - 新增透過 WebSocket 的 Amqp 通訊協定支援的通訊功能和事件集線器。

##<a name="hdinsight-tools-for-visual-studio-updates"></a>Visual Studio 更新 HDInsight 工具

- **IntelliSense 增強**︰ 遠端中繼資料的建議

    當您在編輯登錄區指令碼，現在 Visual Studio HDInsight 工具支援取得遠端中繼資料。 例如，您可以在其中輸入**選取*從**，則會顯示所有資料表名稱。 此外，指定表格後就會顯示的資料行名稱。

- **HDInsight 模擬器支援**

    Visual Studio HDInsight 工具現在支援連接到 HDInsight 模擬器，因此您無法本機開發登錄區指令碼，且不會造成任何成本，然後執行針對 HDInsight 叢集這些指令碼。 

    如需詳細資訊，請參閱[此手冊](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409)。

- **支援一般 Hadoop 叢集 Visual Studio HDInsight 工具**（預覽版本）

    現在 Visual Studio HDInsight 工具會支援一般 Hadoop 叢集，，您可以使用 Visual Studio HDInsight 工具來執行下列動作︰

    - 連線至您的叢集 
    - 撰寫增強的 IntelliSense/自動完成支援，登錄區查詢 
    - 使用直覺式的 UI 叢集中檢視所有的工作。 

    如需詳細資訊，請參閱[此手冊](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409)。

##<a name="in-role-cache-updates"></a>在 [角色快取更新

- 若要使用**Microsoft Azure 儲存體 SDK**版本 4.3 更新**角色中快取**。 到目前為止，使用 Azure 儲存體 SDK 版本 1.7**角色中快取**。

    客戶使用 Azure SDK 2.5 或以下應該 Azure SDK 2.6 更新，並且移到 Azure 儲存體 SDK 的新版本。 

    這次 Azure 儲存體版 2011年-08 18 排程移除 2016 年 8 月 1 日。 從 Azure SDK 2.5 或以下的角色中快取的任何移轉 2.6 必須是完成此時間。 更多的 Azure 儲存體版 2011年-08 18 退休的詳細資訊，請參閱[Microsoft Azure 儲存服務版本移除更新︰ 2016 的副檔名](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx)。

>[AZURE.IMPORTANT]我們正在宣佈使用受管理的快取服務 Azure 和 Azure 中的角色快取 2016 年 11 月 30 日淘汰網站。 我們建議您在此退休做準備移轉到 Azure Redis 快取。 日期和移轉指南的詳細資訊，請參閱[的 Azure 快取服務是適合我？](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)

##<a name="azure-app-service-tools"></a>Azure 應用程式服務工具

更新下列項目 Azure SDK 2.6 版本。

- 包含 Azure API 應用程式部署目標為增強型 azure 發佈。
- API 應用程式佈建功能，讓使用者使用 API 應用程式建立和佈建功能。
- 伺服器總管變更，以反映新的應用程式服務節點網頁、 行動電話，與 API 依資源群組] 應用程式。
- 新增 Azure API 應用程式的用戶端新增至大部分 C# 專案可讓 Swagger 啟用 API 應用程式執行中的使用者 Azure 訂閱的自動產生的手勢。
- API 應用程式工具及應用程式服務節點伺服器總管] 中的，可在 Visual Studio 2013 只。 

##<a name="azure-resource-manager-tools-updates"></a>Azure 資源管理員工具更新

Azure 資源的 [管理員] 工具已更新包含虛擬機器、 網路和儲存的範本。 編輯經驗 JSON 已更新以包含新大綱檢視範本] 和 [可以編輯使用 JSON 程式碼片段的範本。 從 Visual Studio 中部署範本使用 PowerShell 指令碼，指令碼做的任何變更將會使用 Visual Studio 專案，提供。

##<a name="diagnostics-improvements-for-cloud-services"></a>針對 Cloud Services 的診斷改良項目

Azure SDK 2.6 再度支援收集 Azure 計算模擬器中的 [診斷記錄，並傳送到開發儲存空間。 任何診斷記錄 （包括應用程式追蹤記錄，追蹤 Windows (ETW) 記錄、 效能計數器、 基礎結構記錄檔和 windows 事件記錄檔的事件） 產生的應用程式時執行在模擬器中可以轉移到開發儲存空間，以驗證您的診斷記錄正在處理您的本機電腦。 

診斷儲存帳戶現在可以指定在容易不同環境中使用不同的診斷儲存帳戶的服務設定 (.cscfg) 檔案。 有一些主要差異的連接字串 Azure SDK 2.4 和 Azure SDK 2.6 的正常運作。 如需有關如何使用的診斷儲存連線字串，它將會如何影響您的專案，請參閱[設定診斷 Azure 雲端服務](http://go.microsoft.com/fwlink/?LinkID=532784)。

##<a name="breaking-changes"></a>重大變更

###<a name="azure-resource-manager-tools"></a>Azure 資源管理員工具 

- 用於 Azure SDK 2.5**雲端部署專案**專案類型已重新命名**Azure 資源**群組。
- **雲端部署專案**類型的專案中 Azure SDK 2.5 建立可用於 2.6 四捨五入，但部署從 Visual Studio 範本將會失敗。 不過，使用 PowerShell 指令碼部署仍能運作和之前一樣。  有關如何使用**雲端部署專案**中 2.6 閱讀此[文章](http://go.microsoft.com/fwlink/?LinkID=534086)。
 
##<a name="known-issues"></a>已知的問題

- 收集診斷記錄在模擬器中的需要 64 位元作業系統。 在 32 位元作業系統上執行時，不會收集診斷記錄。 這不會影響其他任何模擬器功能。 

- 發行 4/29/2015 azure SDK 2.6 四捨五入都有兩個問題︰ 

    - 在電腦上安裝 Azure SDK 2.6 時無法在 Visual Studio 2015 中載入通用的應用程式。
    - 在 Visual Studio 2013 和 Visual Studio 2015 Visual Studio 變成沒有回應及顯示 「 設定診斷模擬器 」 訊息對話方塊時當機的位置，就會失敗偵錯雲端服務專案。
    
    5/18/2015年發行 Azure SDK 2.6 的更新。 更新的版本是 2.6.30508.1601;包含上述兩個問題的修正。 您可以找出 SDK 從 [控制台] 的建立]-> [程式和功能]-> [Microsoft Azure 工具 Microsoft 的 Visual Studio 2013 – 而 v 2.6。 [版本] 欄會顯示建立數字。

    如果您仍然會對上述問題，安裝最新版 Azure 2.6 SDK[與 2012年](http://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409)、[與 2013年](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)或[與 2015年](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409)。
 
##<a name="see-also"></a>另請參閱

[支援與淘汰網站資訊 Azure SDK 的.NET 和 Api](https://msdn.microsoft.com/library/azure/dn479282.aspx/)
