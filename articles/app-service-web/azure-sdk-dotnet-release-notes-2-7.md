
<properties 
   pageTitle="Azure SDK.NET 2.7 和.NET 2.7.1 版本資訊" 
   description="Azure SDK.NET 2.7 和.NET 2.7.1 版本資訊" 
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

# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Azure SDK.NET 2.7 和.NET 2.7.1 版本資訊

##<a name="overview"></a>概觀

這份文件包含 Azure SDK.NET 2.7 發行版本資訊。 

文件也包含版本資訊 Azure SDK 的.NET 2.7.1 放開。

Visual Studio 2015 和 Visual Studio 2013 只支援 azure SDK 2.7。 [Azure SDK 2.6](https://azure.microsoft.com/downloads/)是最後一個支援 SDK for Visual Studio 2012。

如需詳細瞭解這個版本的詳細資訊，請參閱[Azure SDK 2.7 公告張貼](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)和[Azure SDK 2.7.1 公告張貼](http://go.microsoft.com/fwlink/?LinkId=623850)。

##<a name="azure-sdk-for-net-27"></a>Azure SDK.NET 2.7

###<a name="sign-in-improvements-for-visual-studio-2015"></a>登入 Visual Studio 2015 的改良功能

Visual Studio 2015 azure SDK 2.7 在 Visual Studio 2015 中支援的新的身分識別管理功能。  這包含支援存取 Azure 透過角色以存取控制項，雲端解決方案提供者，DreamSpark 與其他帳戶和訂閱的類型的帳戶。

Azure SDK 2.7 隨附的登入改良功能僅可在 Visual Studio 2015。 Visual Studio 2013 支援會包含在 Azure SDK 2.7.1。


###<a name="mobile-sdk"></a>行動 SDK

更新以反映最新的[NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)及設定程序的**行動應用程式**範本。

###<a name="service-bus"></a>服務匯流排 

一般錯誤修正及改良功能。 更新和功能的詳細資料，請參閱的最新的[服務匯流排 NuGet](http://www.nuget.org/packages/WindowsAzure.ServiceBus/)版本資訊。

###<a name="hdinsight-tools"></a>HDInsight 工具 

在這個版本中所做的動作的更新。 這些更新是在 [預覽]。 如需詳細資訊，請參閱[此部落格](http://go.microsoft.com/fwlink/?LinkId=619108)。

- 登錄區 Tez 工作的登錄區圖表
- 完整的登錄區 DML IntelliSense 支援
- 豬範本支援
- Azure 服務大量範本

####<a name="breaking-changes"></a>重大變更

- 使用此版本的工具時，必須升級舊**大量**專案。 如需詳細資訊，請參閱[此部落格](http://go.microsoft.com/fwlink/?LinkId=619108)。
- 不再支援 visual Studio Web Express。 如需詳細資訊，請參閱[此部落格](http://go.microsoft.com/fwlink/?LinkId=619108)。

###<a name="azure-app-service-tools"></a>Azure 應用程式服務工具

在這個版本中的下列更新已 Web 工具延伸。 如需詳細資訊，請參閱[此](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)部落格。 

- 新增 DreamSpark 帳戶支援
- 完整 Azure 工具變更為支援新 Azure 資源管理的 Api
- 新增至[雲端的檔案總管](#cloud_explorer)Azure 應用程式服務的支援

####<a name="known-issues"></a>已知的問題

Web 應用程式部署位置節點不會出現在 [伺服器總管位置節點下，Web 應用程式部署位置子節點無法載入在雲端的檔案總管。 此問題已解決並準備新版的 sdk。 


###<a name="cloud_explorer"></a>Visual Studio 2015 雲端總管

Azure SDK 2.7 包含雲端的檔案總管，這可讓您檢視 Azure 資源、 檢查其內容]，及索引鍵的開發人員內執行動作的 Visual Studio Visual Studio 2015。 

雲端總管支援下列動作︰

- 資源群組和 [資源類型] 檢視的 Azure 資源 
- 搜尋資源名稱 （資源類型] 檢視中提供）
- 支援的訂閱和有角色基礎存取控制 (RBAC) 套用的資源 
- 整合式的巨集指令面板會顯示 [開發人員導向動作的特定選取的資源。 例如︰ 建立虛擬機器使用資源管理員堆疊，檢視診斷資料的虛擬機器等的附加遠端偵錯工具。
- 整合式的 [屬性] 面板會顯示 [開發人員為焦點內容經常所需的裝置/測試 
- 列舉資源時使用的帳戶 （使用工具列上的設定] 命令） 的快速切換 
- 若要使用列舉 （使用工具列的設定] 命令） 的資源時訂閱的篩選 
- [深層連結 Azure 入口網站的資源與資源群組管理 
 
 
###<a name="azure-resource-manager-tools"></a>Azure 資源管理員工具 

Azure 資源管理員工具處理角色基礎存取控制 (RBAC) 和新的訂閱類型已更新。  包含在這些變更是使用新的儲存空間帳戶，除了傳統的儲存空間，儲存成品部署期間的能力。  

如果您從 SDK 的舊版 Azure 資源群組專案使用 SDK 2.7，需要新的部署指令碼部署使用新的儲存空間帳戶，而不傳統的儲存空間。  系統會提示之前，先將變更至您的專案新增新的指令碼。  舊的指令碼會被重新命名，您必須以手動進行新的指令碼任何修改。
 
 
###<a name="storage-explorer-tools"></a>儲存檔案總管的工具 

- 支援的檢視新增二進位大型物件。 在[此部落格文章](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx)中的其他資訊。 
- 檢視伺服器總管時的進階版儲存帳戶支援。 伺服器總管只會顯示進階版儲存的帳戶頁面 blob 它們是只支援進階版儲存的帳戶類型。

###<a name="azure-data-factory-tools-for-visual-studio"></a>Visual Studio azure 資料工廠工具 

Visual studio 介紹**Azure 資料工廠工具**。 以下是啟用的功能。 請參閱[此部落格](http://go.microsoft.com/fwlink/?LinkId=617530)的詳細資訊。

- **依據範本撰寫**︰ 選取 [使用大小寫基礎範本、 資料移動範本或部署端對端資料整合方案，並開始使用與資料工廠快速處理資料範本。 
- **撰寫及部署資料工廠實體方案總管整合**︰ 建立及部署管線與 Visual Studio 專案相關的實體。 
- **整合圖檢視視覺化的互動，同時撰寫**︰ 以視覺化方式作者管線和 「 輔助工具，從 [圖表檢視中的資料集。 
- **伺服器檔案總管，瀏覽及互動已部署實體的整合**︰ 運用伺服器總管，瀏覽已部署資料工廠和對應的實體。 將您的專案，匯入已部署的資料工廠或任何實體 （管線，連結的服務，資料集）。 
- **JSON 編輯結構描述驗證與豐富型的 intellisense**︰ 有效率地設定及編輯的豐富的 intellisense 和結構描述驗證資料工廠實體 JSON 文件 
- **多環境發佈**︰ 發佈撰寫開發、 測試或 Prod 環境的管線藉由建立個別的設定檔，每個環境。
- **豬、 群組和.Net 基礎資料處理支援**︰ 支援的豬和登錄區資料工廠專案中的指令碼。 支援管理.Net 參考 C# 專案活動。

##<a name="azure-sdk-for-net-271"></a>Azure SDK.NET 2.7.1

下一節包含 Azure SDK 的.NET 2.7.1 放開所提出的更新。
###<a name="hdinsight-tools"></a>HDInsight 工具 

如需詳細 HDInsight 工具更新的詳細說明，請參閱[此部落格](http://go.microsoft.com/fwlink/?LinkId=623831)。

- 群組工作運算子檢視 （新功能）

    若要協助您瞭解登錄區查詢，登錄區運算子檢視功能加入。 若要查看所有的運算子內頂點，按兩下該工作圖表的端點。 若要檢視特定運算子的更多詳細資料，請將游標停留在該運算子。
- 登錄區錯誤標記 （新功能）

    若要讓您檢視的文法錯誤立即，登錄區錯誤標記功能加入。 此外，已增強型錯誤訊息與您現在可以立即看到詳細的文法錯誤 （這個版本中，直到您已送出到叢集登錄區指令碼，並等待一些時間才收到錯誤訊息的詳細資料）。  
- 大量拓撲圖形 （新功能）

    視覺化是非常重要的當您想要查看您的拓撲是否如預期般運作。 在這個版本中，我們會新增大量圖形的視覺效果。 您可以以視覺化方式呈現重要的指標的拓撲 （例如色彩指出天氣特定螺栓是否 「 忙碌 」）。 您也可以按兩下螺栓/Spout 若要檢視更多詳細資料。

- HDInsight 叢集 Azure 入口網站 （修正） 中所建立的支援

    您現在可以使用 Visual Studio 檢視並提交給所有您 HDInsight 叢集，無論叢集建立所在的工作。

- 更多 IntelliSense 支援與中繼資料更快速登錄區載入 （改進）

    我們已改良 IntelliSense 藉由新增更多使用者好記的建議。 例如，資料表別名可以現在也會建議 IntelliSense 中，您可以更輕鬆地撰寫您的查詢。 此外，我們有改良載入，只需要幾秒鐘的清單中所有的資料庫、 表格和資料行您登錄區 metastore 登錄區中繼資料。

如需詳細 HDInsight 工具更新的詳細說明，請參閱[此部落格](http://go.microsoft.com/fwlink/?LinkId=623831)。

###<a name="improvements-in-visual-studio-2013"></a>在 Visual Studio 2013 中的改良功能

- Azure SDK 2.7.1 可讓您存取 Azure 帳戶和透過角色以存取控制項，雲端解決方案提供者，Dreamspark 訂閱的 Visual Studio 2013。
- Azure 2.7.1 sdk，您可以使用新的雲端總管工具視窗現在也會有在 Visual Studio 2013。

###<a name="known-issues"></a>已知的問題

安裝 Azure SDK 2.6 或 2.7.1 Visual Studio 社群 2013 在非英文作業系統會顯示可能不相符的 Visual Studio 英文與非英文資源的警告。 可能安全地據此警告。 如果電腦未包含先前安裝的 Visual Studio 社群 2013年，您在非英文作業系統安裝 SDK 時，其才會發生。 語言套件適用於 Visual Studio 中，RTM 資源後，但適用於更新 4，則會顯示警告。 關閉警告，可讓語言套件，以繼續進行並完成套用語言套件內容的 4 更新版本。

LightSwitch 專案都無法在這個版本的 compatibile。 此問題會與下一個 sdk 解析。

##<a name="also-see"></a>另請參閱
[Azure SDK 2.7.1 公告文章](http://go.microsoft.com/fwlink/?LinkId=623850)

[Azure SDK 2.7 公告文章](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[支援與淘汰網站資訊 Azure SDK 的.NET 和 Api](https://msdn.microsoft.com/library/azure/dn479282.aspx/)
