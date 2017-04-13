<properties 
   pageTitle="測試雲端服務的效能 |Microsoft Azure"
   description="測試效能的雲端服務，使用 Visual Studio 分析工具"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />


# <a name="testing-the-performance-of-a-cloud-service"></a>測試雲端服務的效能 

##<a name="overview"></a>概觀

您可以透過下列方式來測試雲端服務的效能︰

- 若要收集要求及連線的相關資訊，並檢閱顯示服務如何從客戶的觀點來執行的網站統計資料，請使用 [Azure 診斷]。 若要開始使用，請參閱[設定診斷 Azure 雲端服務與虛擬機器]( http://go.microsoft.com/fwlink/p/?LinkId=623009)。

- 使用 Visual Studio 分析工具，以取得深入分析的服務的執行方式計算部分。 本文所述，您可以使用分析工具測量 Azure 服務會依照執行效能。 有關如何使用服務本機執行計算模擬器測量效能分析工具，請參閱[測試效能 Azure 雲端服務本機中計算模擬器使用 Visual Studio 分析工具](http://go.microsoft.com/fwlink/p/?LinkId=262845)。



## <a name="choosing-a-performance-testing-method"></a>選擇 [效能，測試方法

###<a name="use-azure-diagnostics-to-collect"></a>使用 Azure 診斷收集︰###

- 在網頁或服務，例如要求和連線的統計資料。

- 角色，例如角色必須重新啟動的頻率的統計資料。

- 整體的執行角色設定記憶體使用量，例如回收所需時間或記憶體的百分比的相關資訊。

###<a name="use-the-visual-studio-profiler-to"></a>使用 Visual Studio 分析工具︰###

- 決定哪些函數會採用最多時間。

- 測量程式計算資源的每一個部分花多少時間。

- 比較兩個版本的服務的詳細的效能報告。

- 分析比個別的記憶體配置的層級的更詳細的記憶體配置。

- 分析多執行緒程式碼中的並行問題。

當您使用分析工具時，您可以在本機或 Azure 中，執行雲端服務時收集資料。

###<a name="collect-profiling-data-locally-to"></a>收集分析資料的本機至︰###

- 測試雲端服務，例如的特定工作的角色，不需要的實際的模擬的負載所執行的組件的效能。

- 在隔離，控制的情況下測試雲端服務的效能。

- 您將其部署到 Azure 之前，請測試雲端服務的效能。

- 測試私下，而不會影響現有部署雲端服務的效能。

- 測試服務的效能，而不會執行 Azure 中的費用。

###<a name="collect-profiling-data-in-azure-to"></a>收集 Azure 來分析資料︰###

- 測試模擬或實際負載雲端服務的效能。

- 使用儀器的方法收集分析資料，本主題稍後所述。

- 在與服務當生產環境中執行相同的環境中測試服務的效能。

您通常模擬載入至測試雲端服務，請在 [標準底下或緊急情況。

## <a name="profiling-a-cloud-service-in-azure"></a>設定檔中 Azure 雲端服務

當您發佈的 Visual Studio 您雲端服務時，您可以設定檔服務，以及指定的分析的設定，讓您所要的資訊。 每個執行個體的角色開始分析工作階段。 如需有關如何發佈您的服務，從 Visual Studio 的詳細資訊，請參閱[發佈從 Visual Studio Azure 雲端服務](https://msdn.microsoft.com/library/azure/ee460772.aspx)。

若要瞭解關於效能剖析 Visual Studio 中的詳細資訊，請參閱[初學者指南效能分析](https://msdn.microsoft.com/library/azure/ms182372.aspx)及[分析應用程式的效能的使用分析工具](https://msdn.microsoft.com/library/azure/z9z62c29.aspx)。

>[AZURE.NOTE] 您可以啟用 IntelliTrace 或發佈您的雲端服務時，設定檔。 您無法啟用兩者。

###<a name="profiler-collection-methods"></a>分析工具集合方法

您可以使用不同的集合方法的分析，根據您的效能問題︰

- **CPU 取樣**這個方法會收集適用於初始分析的 CPU 使用率問題的應用程式統計資料。 CPU 取樣是啟動大部分的效能調查的建議的方法。 當您收集 CPU 取樣資料時，會設定檔的應用程式上有低的影響。

- **儀器**-這個方法會收集適用於焦點所在的分析，以及用於分析輸入輸出效能問題的詳細的時間資料。 儀器方法記錄每個項目、 離開及函數呼叫函數的模組中執行剖析期間。 這個方法是適用於收集資訊的程式碼] 區段的詳細預存時間以及了解輸入與輸出作業的應用程式效能的影響。 執行 32 位元作業系統的電腦已停用這種方式。 只有當您執行雲端服務 Azure 中不是在本機計算模擬器中使用此選項。

- **.NET 記憶體配置**此方法使用分析方法取樣收集.NET Framework 記憶體配置資料。 收集的資料包含的數字與配置物件的大小。

- **並行**這個方法會收集資源競爭資料以及分析多執行緒和多處理序應用程式中很有用的程序和執行緒執行資料。 並行方法收集資料區塊執行程式碼，例如執行緒等待時鎖定 access 應用程式資源釋放每項事件。 這個方法是適用於分析多執行緒應用程式。

- 您也可以啟用**層互動分析**，可提供同步 ADO.NET 的執行時間的其他資訊通話與一個或多個資料庫的多層應用程式的功能。 您可以使用任何分析方法收集層互動資料。 如需更多層互動設定檔的詳細資訊，請參閱[層互動檢視](https://msdn.microsoft.com/library/azure/dd557764.aspx)。

## <a name="configuring-profiling-settings"></a>設定剖析的設定

下圖顯示如何設定您的分析設定從 [發佈 Azure 應用程式] 對話方塊。

![設定設定檔設定](./media/vs-azure-tools-performance-profiling-cloud-services/IC526984.png)

>[AZURE.NOTE] 若要啟用**啟用分析**] 核取方塊，您必須安裝在您用來發佈您的雲端服務的本機電腦上的分析工具。 根據預設，當您安裝 Visual Studio 安裝分析工具。

### <a name="to-configure-profiling-settings"></a>若要設定剖析的設定

1. 在方案總管中開啟您 Azure 專案的快顯功能表，然後選擇**發佈**。 如何發佈雲端服務的詳細步驟，請參閱[發佈雲端服務使用 Azure 工具](http://go.microsoft.com/fwlink/p?LinkId=623012)。

1. 在 [**發佈 Azure 應用程式**] 對話方塊中，選擇 [**進階設定**] 索引標籤。

1. 若要啟用分析，選取 [**啟用分析**] 核取方塊。

1. 若要設定您的分析設定，選擇 [**設定**] 超連結]。 設定檔設定] 對話方塊隨即出現。

1. 從**您要使用何種方法的設定檔**] 選項按鈕，選擇 [設定檔，您需要的類型。

1. 若要收集層互動分析資料，請選取 [**啟用層互動分析**] 核取方塊。

1. 若要儲存設定，請選擇**[確定**] 按鈕。

    當您發佈此應用程式時，這些設定會用來建立分析工作階段，每一個角色。

## <a name="viewing-profiling-reports"></a>分析報表的檢視

在您的雲端服務中的角色的每個執行個體建立分析工作階段。 若要檢視的每個工作階段從 Visual Studio 分析報表，您可以檢視伺服器總管] 視窗，然後選擇 [以選取角色的執行個體的 Azure 計算節點。 然後，如下圖所示，您可以檢視分析報表。

![檢視從 Azure 分析報表](./media/vs-azure-tools-performance-profiling-cloud-services/IC748914.png)

### <a name="to-view-profiling-reports"></a>若要檢視分析報表

1. 若要在 Visual Studio 中檢視伺服器總管] 視窗，在功能表列上選擇 [檢視]，伺服器總管。

1. 選擇 [Azure 計算] 節點，然後選擇 [選取設定檔時您發佈的 Visual Studio 雲端服務的 Azure 部署節點。

1. 若要檢視的執行個體的分析報表，選擇角色服務中，開啟特定的執行個體的快顯功能表，然後選擇**檢視分析報表**。

    從 Azure 立即下載報表，.vsp 檔案]，並下載的狀態會出現在 Azure 活動記錄。 下載完成後，請分析報表就會出現的 Visual Studio 名為編輯器] 索引標籤中<Role name> _<Instance Number>_ <identifier>.vsp。 報表的摘要資料會出現。

1. 若要顯示不同的檢視的報表，在 [目前檢視] 清單中，選擇您要檢視的類型。 如需詳細資訊，請參閱[分析工具報告] 檢視](https://msdn.microsoft.com/library/azure/bb385755.aspx)。

## <a name="next-steps"></a>後續步驟

[偵錯雲端服務](https://msdn.microsoft.com/library/azure/ee405479.aspx)

[從 Visual Studio 發佈至 Azure 雲端服務](https://msdn.microsoft.com/library/azure/ee460772.aspx)

