<properties
    pageTitle="結束端對端疑難排解使用 Azure 儲存指標和記錄、 AzCopy 及郵件分析器 |Microsoft Azure"
    description="示範 Azure 儲存體分析、 AzCopy，與 Microsoft 訊息分析程式的端對端疑難排解教學課程"
    services="storage"
    documentationCenter="dotnet"
    authors="robinsh"
    manager="carmonm"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>


# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>使用 Azure 儲存指標和記錄、 AzCopy 及郵件分析器的端對端疑難排解

[AZURE.INCLUDE [storage-selector-portal-e2e-troubleshooting](../../includes/storage-selector-portal-e2e-troubleshooting.md)]

## <a name="overview"></a>概觀

診斷和疑難排解是建立與使用 Microsoft Azure 儲存體的用戶端應用程式支援的金鑰技能。 Azure 應用程式的分散特性，因為診斷和疑難排解錯誤與效能問題可能會比在傳統的環境中更複雜。

在本教學課程中，我們將示範如何識別用戶端的特定錯誤，可能會影響效能，及疑難排解這些錯誤的端對端才能最佳化用戶端應用程式使用 Microsoft 和 Azure 儲存所提供的工具。

本教學課程中提供端對端疑難排解案例實作的研究。 若要疑難排解 Azure 儲存應用程式在您採取進階概念性指南，請參閱[監視器，診斷，及疑難排解 Microsoft Azure 儲存體](storage-monitoring-diagnosing-troubleshooting.md)。

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Azure 儲存體應用程式疑難排解工具

若要疑難排解使用 Microsoft Azure 儲存體的用戶端應用程式，您可以使用工具的組合，若要判斷在發生問題時，以及問題的原因可能是。 這些工具包括︰

- **Azure 儲存體分析**。 [Azure 儲存體分析](http://msdn.microsoft.com/library/azure/hh343270.aspx)提供指標和 Azure 儲存體的記錄。
    - **儲存指標**追蹤交易指標的儲存空間帳戶的容量指標。 使用的度量，您可以判斷您的應用程式執行許多不同的量值的方式。 如需有關的儲存空間分析所追蹤的指標類型，請參閱[儲存分析指標表格的結構描述](http://msdn.microsoft.com/library/azure/hh343264.aspx)。

    - **儲存記錄**記錄的每個要求 Azure 儲存體服務的伺服器端記錄檔。 記錄會追蹤每個要求，包括執行的作業、 作業，以及延遲的資訊狀態的詳細的資料。 如需要求與回應資料的儲存空間分析寫入記錄檔的相關資訊，請參閱[儲存分析的記錄格式](http://msdn.microsoft.com/library/azure/hh343259.aspx)。

> [AZURE.NOTE] 儲存與複寫類型區域多餘儲存空間 (ZRS) 的帳戶沒有 [公制] 或 [在此時間啟用記錄功能。 

- **Azure 入口網站**。 儲存帳戶[Azure 入口網站](https://portal.azure.com)中，您可以設定度量資訊和記錄。 您也可以檢視圖表和圖形，顯示一段時間，如何執行您的應用程式，並設定提醒，如果您的應用程式來執行如預期，指定的度量，請通知您。

    如需設定監視 Azure 入口網站中的資訊，請參閱[監視器 Azure 入口網站中的儲存空間帳戶](storage-monitor-storage-account.md)。

- **AzCopy**。 Azure 儲存體 server 記錄檔儲存為二進位大型物件，，讓您可以使用 AzCopy 將記錄二進位大型物件複製到本機目錄使用 Microsoft 訊息分析器分析。 如需 AzCopy 相關資訊，請參閱[傳送以 AzCopy 命令列公用程式的資料](storage-use-azcopy.md)。

- **Microsoft 訊息分析**。 郵件分析器是使用記錄檔，並顯示記錄資料，方便您要篩選、 搜尋及群組記錄資料成實用組，您可以用來分析錯誤與效能問題的視覺化格式工具。 如需相關訊息分析資訊，請參閱[Microsoft 訊息分析器作業系統指南](http://technet.microsoft.com/library/jj649776.aspx)。

## <a name="about-the-sample-scenario"></a>關於範例案例

在此教學課程中，我們會檢查的情況 Azure 儲存指標指出低百分比的成功率呼叫 Azure 儲存應用程式的位置。 （顯示為**PercentSuccess** [Azure 入口網站](https://portal.azure.com)和指標資料表中） 低的百分比成功工資率公制追蹤的成功，但會傳回 HTTP 狀態碼大於 299 的作業。 伺服器端儲存記錄檔]，在這些作業會記錄具有**ClientOtherErrors**交易狀態。 如需低的百分比成功公制的詳細資訊，請參閱[指標顯示低 PercentSuccess 或分析記錄項目有 ClientOtherErrors 交易狀態的作業](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success)。

Azure 儲存作業可能會傳回 HTTP 狀態碼大於 299 為其標準功能的一部分。 但在某些情況下這些錯誤指出，您可能可以最佳化您的用戶端應用程式，以改善效能。

在此案例中，我們會考慮低百分比成功率為 100%] 下方的任何項目。 您可以根據您的需求，不過，選擇不同的公制層級。 建議的期間測試您的應用程式，您所建立的比較基準了您的關鍵效能指標。 例如，您可能會決定，根據測試，您的應用程式應有一致百分比成功率 90 百分比] 或 [鍵入 [85%。 如果指標資料顯示的應用程式偏離該數字，然後您可以調查可能造成增加。

範例案例中，我們已建立的百分比成功工資率公制低於 100%，我們會檢查尋找錯誤的相互關聯計量，以及用來找出導致較低的百分比成功率的記錄。 我們會查看特別 400 範圍中的錯誤。 然後，我們會進一步調查 404 （找不到） 的錯誤。

### <a name="some-causes-of-400-range-errors"></a>400 範圍錯誤的一些原因

下列範例會顯示對 Azure Blob 儲存體，與他們可能的原因要求的一些 400 範圍錯誤的範例。 任何這些錯誤，以及錯誤 300 的範圍和 500 的範圍，即可參與低的百分比成功率。

請注意，下列清單並非完整。 請參閱[狀態，出現錯誤碼](http://msdn.microsoft.com/library/azure/dd179382.aspx)MSDN 上關於一般 Azure 儲存體錯誤及儲存服務的每個特定錯誤的詳細資料。

**狀態碼 404 （找不到） 範例**

當對容器或 blob 讀取的作業失敗，因為 blob 或容器找不到。

- 如果此要求之前的另一個用戶端已刪除的容器或 blob 就會發生。
- 如果您使用的 API 呼叫所建立的容器或 blob 檢查是否存在後，就會發生。 CreateIfNotExists Api 撥打電話不對第一次以檢查存在的容器或 blob;如果不存在，傳回 404 錯誤時，，並在第二個放入通話然後對撰寫容器或 blob。

**狀態碼 409 （衝突） 範例**

- 如果您使用建立 API 先檢查存在，建立新的容器或 blob，已經存在的容器或名稱 blob 便會發生。
- 如果刪除容器時，且您嘗試建立新的容器具有相同名稱上，執行刪除操作完成之前，就會發生。
- 如果您指定租用容器或 blob，則表示已經有租用便會發生。

**狀態碼 412 （指定條件無法） 範例**

- 不符合指定條件的標頭的條件時，就會發生。
- 當指定租用識別碼不符合容器或 blob 租用識別碼。

## <a name="generate-log-files-for-analysis"></a>產生分析的記錄檔

在本教學課程中，我們將使用三個不同的類型的記錄檔]，使用郵件分析，雖然您無法選擇使用下列任一項︰

- **伺服器記錄**，當您啟用 Azure 儲存體記錄時建立。 伺服器登入包含 Azure 儲存體服務-blob、 佇列、 表格和檔案的其中一個名為每個作業相關資料。 稱為作業以及哪些狀態碼傳回，以及其他的詳細資訊要求與回應，則會指出 server 記錄檔。
- **.NET 用戶端記錄**，當您在.NET 應用程式中啟用從用戶端記錄時建立。 用戶端記錄包含在用戶端備妥要求及接收及處理回應的方式的詳細的資訊。
- **HTTP 網路追蹤記錄檔**，它會收集資料，在 HTTP/HTTPS 要求與回應資料，包括的作業 Azure 儲存體。 在本教學課程中，我們會產生網路追蹤，透過訊息分析。

### <a name="configure-server-side-logging-and-metrics"></a>設定伺服器端記錄指標

首先，我們會需要設定 Azure 儲存體記錄及指標，可以從用戶端應用程式來分析資料。 您可以在各種不同的方式-透過[Azure 入口網站](https://portal.azure.com)中，設定記錄及指標，使用 PowerShell，或以程式設計方式。 請參閱[啟用儲存指標和檢視指標資料](http://msdn.microsoft.com/library/azure/dn782843.aspx)和[啟用記錄儲存和存取記錄資料](http://msdn.microsoft.com/library/azure/dn782840.aspx)MSDN 上的 [設定記錄及指標的詳細資料。

**透過 Azure 入口網站**

若要設定記錄及儲存帳戶使用[Azure 入口網站](https://portal.azure.com)的標準，請遵循在[監視 Azure 入口網站中的儲存空間帳戶](storage-monitor-storage-account.md)的指示進行。

> [AZURE.NOTE] 無法設定 minute 指標使用 Azure 入口網站。 不過，我們建議您執行設定其進行此教學課程和調查的應用程式的效能問題。 您可以設定 minute 指標使用 PowerShell 如下所示，或使用的儲存空間用戶端文件庫，以程式設計方式。
>
> 請注意 Azure 入口網站無法顯示 minute 指標，只下限指標。

**透過 PowerShell**

若要開始使用 PowerShell 的 Azure，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。

1. 若要將您的 Azure 的使用者帳戶新增至 PowerShell] 視窗中使用[新增 AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) cmdlet:

    ```
    Add-AzureAccount
    ```

2. 在 [**登入 Microsoft Azure** ] 視窗中，輸入電子郵件地址和密碼與您的帳戶相關聯。 Azure 送給儲存的認證資訊，並關閉視窗。
3. 您使用教學課程藉由執行下列命令 PowerShell 視窗中的儲存空間帳戶設定預設儲存帳戶︰

    ```
    $SubscriptionName = 'Your subscription name'
    $StorageAccountName = 'yourstorageaccount'
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    ```

4. 啟用 Blob 服務的儲存空間記錄︰

    ```
    Set-AzureStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations Read,Write,Delete -PassThru -RetentionDays 7 -Version 1.0
    ```
5. 啟用 [儲存指標 Blob 服務，並確認其設定為**-MetricsType** `Minute`:

    ```
    Set-AzureStorageServiceMetricsProperty -ServiceType Blob -MetricsType Minute -MetricsLevel ServiceAndApi -PassThru -RetentionDays 7 -Version 1.0
    ```

### <a name="configure-net-client-side-logging"></a>設定.NET 用戶端記錄

若要設定的.NET 應用程式的用戶端記錄，讓.NET 診斷 （web.config 或 app.config） 應用程式的設定檔中。 如需詳細資訊請[用戶端記錄與.NET 儲存用戶端的文件庫](http://msdn.microsoft.com/library/azure/dn782839.aspx)與[用戶端記錄與 Microsoft Azure 儲存體 SDK java](http://msdn.microsoft.com/library/azure/dn782844.aspx)參閱 MSDN 上。

用戶端記錄包含在用戶端備妥要求及接收及處理回應的方式的詳細的資訊。

儲存空間的用戶端程式庫 （web.config 或 app.config） 應用程式的設定檔中指定的位置儲存用戶端記錄資料。

### <a name="collect-a-network-trace"></a>收集網路追蹤

您可以使用郵件分析您的用戶端應用程式正在執行時收集 HTTP/HTTPS 網路追蹤。 郵件分析程式會使用的後端[Fiddler](http://www.telerik.com/fiddler) 。 收集網路追蹤之前，建議您設定 Fiddler 錄製加密的 HTTPS 傳輸︰

1. 安裝[Fiddler](http://www.telerik.com/download/fiddler)。
2. 啟動 Fiddler。
2. 選取 [**工具 |Fiddler 選項**。
3. 在 [選項] 對話方塊中，確定的**擷取 HTTPS 連線**並**解密 HTTPS 流量**兩者都有選取，如下所示。

![設定 Fiddler 選項](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

教學課程中，「 收集網路追蹤第一次儲存郵件分析器]，然後建立分析工作階段，分析追蹤和記錄。 收集網路追蹤中郵件分析︰

1. 在郵件分析器中，選取 [**檔案 |快速追蹤 |加密 HTTPS**。
2. 就會立即開始追蹤。 選取 [停止追蹤，以便我們可以將其設定追蹤儲存資料傳輸的 [**停止**]。
3. 選取 [**編輯**] 以編輯追蹤工作階段。
4. 選取右邊的**Microsoft-Pef-WebProxy** ETW 提供者的 [**設定**] 連結。
5. 在 [**進階設定**] 對話方塊中，按一下 [**提供者**] 索引標籤。
6. 在 [**主機名稱篩選**] 欄位中，指定您儲存結束點，以空格分隔。 例如，您可以指定端點，如下所示。變更`storagesample`您儲存的帳戶的名稱︰

    ```
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. 結束對話方塊，然後按一下 [若要開始收集追蹤的位置，主機名稱篩選的 [**重新啟動**以便追蹤中所包含唯一 Azure 儲存體網路流量。

>[AZURE.NOTE] 當您完成收集您的網路追蹤之後，我們強烈建議您還原的設定，您可能會變更 Fiddler 解密 HTTPS 流量。 在 Fiddler 選項] 對話方塊中，取消選取的**擷取 HTTPS 連線**並**解密 HTTPS 流量**核取方塊。

請參閱[使用網路追蹤功能](http://technet.microsoft.com/library/jj674819.aspx)Technet 上的 [更多詳細資料。

## <a name="review-metrics-data-in-the-azure-portal"></a>檢閱指標 Azure 入口網站中的資料

一旦您的應用程式已經執行一段時間了，您可以檢閱出現在[Azure 入口網站](https://portal.azure.com)中看到您的服務如何已執行的標準圖表。 首先，請瀏覽至您 Azure 入口網站中的儲存空間帳戶，並新增**成功百分比**公制的圖表。

在 Azure 入口網站，您現在會看到**成功百分比**監控圖表，以及任何其他您可能已新增的指標。 在此案例我們會調查下一步分析訊息分析器中的記錄、 百分比的成功率有點下方 100%。

如需新增指標監控頁面的詳細資訊，請參閱[如何︰ 指標表格加上指標](storage-monitor-storage-account.md#how-to-add-metrics-to-the-metrics-table)。

> [AZURE.NOTE] 可能需要一些時間指標資料顯示 Azure 入口網站中啟用之後，您儲存指標。 這是因為每小時指標的前一個小時不會顯示在 Azure 入口網站，直到目前小時。 此外，minute 指標目前並未顯示在 Azure 入口網站。 因此而定當您啟用指標，可能需要兩個小時，若要查看指標資料。

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>使用 AzCopy 將 server 記錄檔複製到本機的目錄

Azure 儲存體寫入 server 記錄檔資料二進位大型物件，當指標會寫入到資料表。 記錄 blob 可在已知`$logs`容器，您儲存的帳戶。 記錄二進位大型物件的命名為階層年、 月、 日和小時，以便您輕鬆找出您想要調查的時間範圍。 例如，在`storagesample`01/02/2015，從 8 9 am 記錄 blob 的容器的帳戶是`https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`。 容器中的個別二進位大型物件的開頭依序命名`000000.log`。

若要下載這些伺服器端記錄檔到您所選擇的位置，在您的本機電腦上，您可以使用 AzCopy 命令列工具。 例如，您可以使用下列命令以下載資料夾位置原本在 2015 年 1 月 2 日的 blob 作業的記錄檔`C:\Temp\Logs\Server`;取代`<storageaccountname>`與您的儲存體帳戶名稱，然後`<storageaccountkey>`與您的帳戶便捷鍵︰

    AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V

AzCopy 是可供下載[Azure 下載](https://azure.microsoft.com/downloads/)頁面上。 如需瞭解如何使用 AzCopy 的詳細資訊，請參閱[將資料以 AzCopy 命令列公用程式傳送](storage-use-azcopy.md)。

如需下載伺服器端記錄的詳細資訊，請參閱[下載儲存記錄記錄資料](http://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata)。

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>使用 Microsoft 訊息分析來分析記錄的資料

Microsoft 訊息分析器是用來擷取、 顯示，及分析通訊協定訊息流量、 活動和其他系統或應用程式的訊息，疑難排解和診斷案例中的工具。 郵件分析器也可讓您載入、 總計、 和分析資料的記錄，並儲存追蹤檔案。 如需有關郵件分析程式的詳細資訊，請參閱[Microsoft 訊息分析器作業系統指南](http://technet.microsoft.com/library/jj649776.aspx)。

郵件分析器包含資產 Azure 儲存空間，可協助您分析伺服器、 用戶端，以及網路記錄。 在此區段中，我們將討論如何使用這些工具低的百分比成功儲存記錄檔中的問題。

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>下載並安裝訊息分析和 Azure 儲存體資產

1. 下載[郵件分析器](http://www.microsoft.com/download/details.aspx?id=44226)從 Microsoft 下載中心，並執行安裝程式。
2. 啟動訊息分析。
3. 從 [**工具**] 功能表中，選取 [**資產管理員**]。 在 [**資產管理員**] 對話方塊中，選取 [**下載**]，然後篩選**Azure 儲存體**]。 下圖所示，您會看到 Azure 儲存體資產。
4. 按一下 [安裝 Azure 儲存體資產的**同步處理所有顯示的項目**]。 可用的資產包括︰
    - **Azure 儲存色彩規則︰**Azure 儲存體色彩規則可讓您定義以醒目提示包含在追蹤中的特定資訊的郵件中使用色彩、 文字和字型樣式的特殊篩選。
    - **Azure 儲存圖表︰**Azure 儲存圖表的預先定義的圖表的圖形 server 記錄檔資料。 請注意，這一次使用 Azure 儲存體圖表，您可能只載入 server 記錄檔分析格線。
    - **Azure 儲存體剖析器︰**Azure 儲存體剖析器剖析 Azure 儲存體用戶端、 伺服器，以及 HTTP 記錄，以分析方格中顯示它們。
    - **Azure 儲存篩選︰**Azure 儲存的篩選是來查詢您的資料分析方格中，您可以使用的預先定義的準則。
    - **Azure 儲存體] 檢視的版面配置︰**Azure 儲存體] 檢視的版面配置的預先定義的欄版面配置和分析方格中的群組。
4. 安裝 「 資產 」 後，請重新啟動訊息分析。

![訊息分析器資產管理員](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [AZURE.NOTE] 安裝所有進行本教學課程中所顯示的 Azure 儲存體資產。

### <a name="import-your-log-files-into-message-analyzer"></a>將郵件分析匯入您的記錄檔

您可以匯入所有您已儲存的記錄檔 （伺服器端、 用戶端和網路） 在 Microsoft 訊息分析器分析的單一工作階段。

1. 在 [Microsoft 訊息分析程式中的 [**檔案**] 功能表中，按一下**新的工作階段**，然後再按一下**空白工作階段**。 在 [**新的工作階段**] 對話方塊中，輸入您的分析工作階段的名稱。 在 [**工作階段的詳細資料**] 面板中，按一下 [**檔案**] 按鈕。
1. 若要載入訊息分析所產生的網路追蹤資料，按一下 [**新增**檔案、 瀏覽至您的網路追蹤工作階段從儲存.matp 檔案的位置、 選取.matp 檔案，並按一下 [**開啟**]。
1. 若要載入伺服器端記錄資料，按一下 [**新增**檔案、 瀏覽至您已下載您的伺服器端記錄的位置，選取您要分析的時間範圍的記錄檔和按一下 [**開啟**]。 然後，在**工作階段的詳細資料**] 面板中，將**文字記錄設定**每個伺服器端記錄檔] 下拉式清單為**AzureStorageLog** ，以確保 Microsoft 訊息分析程式可正確剖析的記錄檔。
1. 若要載入的用戶端記錄資料，按一下 [**新增**檔案、 瀏覽] 以儲存您的用戶端記錄的位置，選取您要分析的記錄檔和按一下 [**開啟**]。 然後，在 [**工作階段的詳細資料**] 面板中，將**文字記錄設定**每個用戶端記錄檔] 下拉式清單為**AzureStorageClientDotNetV4** ，以確保 Microsoft 訊息分析程式可正確剖析的記錄檔。
1. 載入和剖析的記錄資料的 [**新的工作階段**] 對話方塊中，按一下 [**開始**]。 記錄資料會顯示訊息分析器分析方格中。

下圖顯示伺服器、 用戶端，與網路追蹤記錄檔的範例工作階段。

![設定郵件分析工作階段](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

請注意訊息分析程式會將記憶體載入記錄檔。 如果您有大型的記錄檔資料集，您會想要篩選，以獲得最佳效能，從郵件分析。

首先，決定您感興趣檢閱，時間範圍，並保留此時間範圍越小越。 在許多情況下，您會想要檢閱分鐘] 或 [最小時一段。 匯入的記錄可以符合您需求的最小的設定。

如果您仍有大量的記錄資料，您可能會想要指定您之前的記錄檔資料載入的工作階段篩選器來篩選器。 在 [**工作階段篩選**] 方塊中，選取 [**文件庫**] 按鈕來選擇預先定義的篩選器。例如，選擇**全域時間篩選 I**從 Azure 儲存體篩選器篩選上的時間間隔。 然後，您可以編輯指定的開始和結束時間戳記，針對您想要查看間隔的篩選準則。 您也可以篩選特定的狀態碼。例如，您可以選擇載入只狀態碼哪裡 404 的日誌項目。

如需記錄資料匯入 Microsoft 訊息分析程式的詳細資訊，請參閱 TechNet 上的[擷取郵件資料](http://technet.microsoft.com/library/dn772437.aspx)。

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>使用用戶端要求識別碼建立記錄檔資料的關聯

Azure 儲存用戶端的文件庫會自動產生唯一的用戶端要求 ID 針對每個要求。 此值會寫入用戶端記錄 server 記錄檔，與網路追蹤，，讓您可以使用其相互關聯上所有的三個記錄檔中郵件分析的資料。 請參閱[用戶端要求識別碼](storage-monitoring-diagnosing-troubleshooting.md#client-request-id)，如需用戶端要求識別碼]。

以下各節說明如何使用預先設定和自訂的版面配置檢視來建立關聯，並根據用戶端要求識別碼的群組資料

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>選取要顯示在 [分析方格中檢視版面配置

郵件分析器的儲存空間資產包括 Azure 儲存檢視版面配置，也就是預先設定的檢視，您可以用來顯示您實用的群組與不同情況的資料行的資料。 您也可以建立自訂檢視的版面配置，並加以儲存以重複使用。

下圖顯示 [**檢視版面配置**] 功能表中使用從工具列功能區中選取**檢視的版面配置**。 Azure 儲存體的檢視版面配置進行分組] 功能表中的 [ **Azure 儲存體**] 節點。 您可以搜尋`Azure Storage`在搜尋方塊中，若要在 Azure 儲存體篩選檢視僅版面配置。 您也可以選取的檢視版面配置，使最愛項目，並顯示在頂端的功能表] 旁的星星。

![版面配置檢視] 功能表](./media/storage-e2e-troubleshooting/view-layout-menu.png)

開始先選取**ClientRequestID 和模組的群組**。 第一次以用戶端要求識別碼，然後來源記錄檔 （或在郵件分析器**模組**） 來時，此檢視的版面配置群組將記錄所有三個記錄檔中的資料。 此檢視中，您可以向下切入至特定的用戶端要求識別碼，看看資料的用戶端要求所有三個記錄檔的識別碼]。

下方顯示的圖片，此版面配置檢視會套用至記錄資料範例顯示的資料行的子集。 您可以看到特定的用戶端要求 id，分析方格顯示從用戶端記錄、 伺服器記錄，以及網路追蹤的資料。

![Azure 儲存體] 檢視的版面配置](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

>[AZURE.NOTE] 不同的記錄檔有不同的資料行，因此分析方格中顯示來自多個記錄檔的資料時，某些資料欄不能包含任何指定的資料列的資料。 比方說，上方的圖片，在用戶端記錄列 [不要顯示任何資料的**時間戳記**、 **TimeElapsed**、**來源**和**目的**資料行，因為這些欄不存在於用戶端記錄中，但網路追蹤中存在。 同樣地的**時間戳記**] 欄位顯示的時間戳記伺服器記錄，資料，但沒有資料會顯示**TimeElapsed**、**來源**和**目的**資料行，這不是伺服器記錄的一部分。

除了使用 Azure 儲存體] 檢視的版面配置，您也可以定義並儲存您的檢視版面配置。 您可以選擇其他所要的欄位群組的資料，並將群組儲存為您自訂的配置的一部分。

### <a name="apply-color-rules-to-the-analysis-grid"></a>將色彩規則套用至分析方格

儲存資產也包含色彩規則，提供視覺表示來識別不同類型的分析方格中的錯誤。 預先定義的色彩套用至 HTTP 錯誤，使其出現僅適用於 server 記錄檔和網路追蹤。

若要套用色彩規則，請從工具列功能區中選取**色彩規則**。 您會看到的功能表中的 Azure 儲存體色彩規則。 教學課程中，選取**用戶端錯誤 (StatusCode 400 和 499 之間)**，如下圖所示。

![Azure 儲存體] 檢視的版面配置](./media/storage-e2e-troubleshooting/color-rules-menu.png)

除了使用 Azure 儲存體色彩規則，您也可以定義並儲存您自己的色彩規則。

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>若要尋找 400 範圍錯誤的群組及篩選記錄資料

接下來，我們會群組及篩選的記錄檔資料 400 範圍中尋找所有錯誤。

1. 找出**StatusCode**欄分析方格中，以滑鼠右鍵按一下欄標題，並選取**群組**。
2. 下一張、 上**ClientRequestId**欄] 群組。 您會看到狀態碼及用戶端要求識別碼，現在組織分析方格中的資料
1. 如果尚未顯示，顯示 [檢視篩選器工具] 視窗。 在工具列功能區中，選取**工具視窗**，然後**檢視篩選**。
2. 若要篩選以顯示 [只有 400 範圍錯誤記錄檔資料，以**檢視篩選**] 視窗中，新增下列篩選準則，然後按一下 [**套用**︰

        (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)

下圖顯示此群組及篩選的結果。 展開 [ **ClientRequestID** ] 欄位下方的狀態碼 409 分組，例如，會顯示作業所產生的狀態碼中。

![Azure 儲存體] 檢視的版面配置](./media/storage-e2e-troubleshooting/400-range-errors1.png)

套用此篩選器之後，您會看到的排除從用戶端記錄的列，與用戶端記錄不包含**StatusCode**資料行。 問題，我們將會在 [伺服器] 和 [網路追蹤的記錄檔找出 404 錯誤，檢視，然後再我們會回到用戶端記錄] 來檢視這些主導的用戶端作業。

>[AZURE.NOTE] 您可以篩選**StatusCode**欄，並仍顯示所有三個記錄檔，包括用戶端記錄中，如果您新增包含日誌項目，其中的狀態碼是空值的篩選運算式的資料。 若要建立此篩選運算式，使用︰
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> 這個篩選器會傳回所有資料列從用戶端記錄與 server 記錄檔和 HTTP 記錄的資料列的狀態碼大於 400。 如果您套用用戶端要求識別碼和模組依群組檢視版面配置，您可以搜尋，或捲動到尋找項目都所有的三筆記錄的記錄項目。   

### <a name="filter-log-data-to-find-404-errors"></a>若要尋找 404 錯誤的篩選記錄資料

儲存資產包含預先定義的篩選，您可以用來縮小尋找錯誤或趨勢所需的記錄資料。 接下來，我們會套用預先定義的兩個篩選︰ 篩選 [伺服器] 和 [404 錯誤，網路追蹤記錄的項目和所篩選的資料上指定的時間範圍。

1. 如果尚未顯示，顯示 [檢視篩選器工具] 視窗。 在工具列功能區中，選取**工具視窗**，然後**檢視篩選**。
2. 在 [檢視篩選] 視窗中，選取**文件庫**，並搜尋`Azure Storage`尋找 Azure 儲存體篩選。 選取 [ **404 （找不到） 的郵件，在所有記錄中**的篩選]。
3. 同樣地，顯示 [**文件庫**] 功能表並找出並選取**通用的 [時間] 篩選**。
4. 編輯您想要檢視範圍篩選器中顯示的時間戳記。 這是為了要分析的資料範圍，縮小。
5. 篩選看起來應該類似下面的範例。 按一下 [**套用]**以套用篩選至分析格線。

        ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
        (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)

![Azure 儲存體] 檢視的版面配置](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>分析記錄資料

現在，您有分組，篩選資料，您可以檢查個別要求產生 404 錯誤的詳細的資料。 在目前的檢視版面配置，被群組的用戶端要求識別碼，然後以記錄來源的資料。 我們會要求的篩選 [StatusCode] 欄位包含 404 的位置，因為我們會看到僅限伺服器和網路追蹤資料，而非用戶端記錄資料。

下圖顯示特定的要求，因為不存在 blob 取得 Blob 作業產生 404 的位置。 請注意，某些資料欄已從 [標準] 檢視以顯示相關資料。

![篩選的伺服器和網路追蹤記錄檔](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

接下來，我們會與此用戶端要求 ID 若要查看的用戶端發生錯誤時進行什麼動作的用戶端記錄資料相互關聯。 您可以顯示新的分析方格檢視以檢視用戶端記錄資料，在第二個索引標籤中開啟此工作階段︰

1. 第一次，將**ClientRequestId**欄位的值複製到剪貼簿。 您可以選取任一列、 尋找**ClientRequestId**欄位，以滑鼠右鍵按一下資料值，並選擇**複製 'ClientRequestId'**。
1. 工具列功能區上選取**新的檢視器**，然後選取**分析方格**中，開啟新的索引標籤。 [新增] 索引標籤會顯示您記錄檔]，而不分組、 篩選或色彩規則中的所有資料。
2. 工具列功能區上的選取**檢視的版面配置**]，然後選取**所有的.NET 用戶端欄** **Azure 儲存體**] 區段下。 此檢視版面配置會顯示從用戶端的資料記錄以及伺服器和網路追蹤記錄檔。 依預設會排序在**MessageNumber**資料行。
3. 接下來，用戶端要求識別碼搜尋用戶端記錄 工具列功能區上的選取**尋找郵件**]，然後在 [**尋找**] 欄位中的用戶端要求識別碼指定自訂篩選]。 使用此語法篩選，指定您自己的用戶端要求識別碼︰

        *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"

郵件分析找出並選取第一個記錄項目位置的搜尋準則比對用戶端要求識別碼。 在用戶端記錄中，有幾個項目，每個用戶端要求 id，，因此您可能要分組，使其更容易看到這些一起**ClientRequestId**欄位。 下圖顯示的所有郵件用戶端記錄中指定的用戶端要求識別碼。

![用戶端記錄顯示 404 錯誤](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

您可以使用下列兩個索引標籤中的檢視版面配置中顯示的資料，來分析來決定可能會導致錯誤的要求資料。 您也可以查看此項目，若要查看的 404 錯誤，可能會有主導的上一個事件之前的要求。 例如，您可以檢閱前面此用戶端要求識別碼，來判斷是否 blob 可能已刪除，或如果錯誤是因為容器或 blob 上呼叫 CreateIfNotExists API 用戶端應用程式的用戶端記錄項目。 在用戶端記錄中，您可以在 [**描述**] 欄位中，找到 blob 的地址在伺服器和網路追蹤記錄檔，這項資訊會出現在 [**摘要**] 欄位。

一旦您知道產生 404 錯誤 blob 的地址，您可以進一步調查。 如果您要搜尋其他郵件上相同的 blob 的作業相關聯的記錄項目，您可以檢查是否用戶端先前刪除的實體。

## <a name="analyze-other-types-of-storage-errors"></a>分析其他類型的儲存空間錯誤

現在，您熟悉使用郵件分析來分析記錄資料時，您就可以分析其他類型的使用檢視錯誤版面配置、 色彩規則]，並搜尋/篩選。 下表列出一些您可能會遇到的問題，您可以用來尋找篩選準則。 如需有關如何建構篩選和篩選語言訊息分析程式的詳細資訊，請參閱[篩選郵件資料](http://technet.microsoft.com/library/jj819365.aspx)。

|    若要調查...                                                                                               |    使用篩選運算式...                                                                                                                                                                                                                                        |    運算式適用於記錄 (用戶端、 伺服器、 網路，所有)    |
|------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|
|    在 [郵件傳遞佇列上未預期的延遲                                                              |    AzureStorageClientDotNetV4.Description 包含 「 Retrying 失敗作業 」。                                                                                                                                                                                |    用戶端                                                        |
|    HTTP 增加 PercentThrottlingError                                                                       |    HTTP。Response.StatusCode = = 500 與 #124; 與 #124;HTTP。Response.StatusCode = = 503                                                                                                                                                                                          |    網路                                                       |
|    增加 PercentTimeoutError                                                                               |    HTTP。Response.StatusCode = = 500                                                                                                                                                                                                                             |    網路                                                       |
|    增加 PercentTimeoutError （全部）                                                                         |    * StatusCode = = 500                                                                                                                                                                                                                                          |    所有                                                           |
|    增加 PercentNetworkError                                                                               |    AzureStorageClientDotNetV4.EventLogEntry.Level < 2                                                                                                                                                                                                          |    用戶端                                                        |
|    HTTP 403 （禁止） 郵件                                                                                 |    HTTP。Response.StatusCode = = 403                                                                                                                                                                                                                             |    網路                                                       |
|    HTTP 404 （找不到） 郵件                                                                                 |    HTTP。Response.StatusCode = = 404                                                                                                                                                                                                                             |    網路                                                       |
|    404 （全部）                                                                                                     |    * StatusCode = = 404                                                                                                                                                                                                                                          |    所有                                                           |
|    共用 Access 簽章 (SA) 的授權問題                                                             |    AzureStorageLog.RequestStatus = = 「 SASAuthorizationError 」                                                                                                                                                                                                     |    網路                                                       |
|    HTTP 409 （衝突） 郵件                                                                                  |    HTTP。Response.StatusCode = = 409                                                                                                                                                                                                                             |    網路                                                       |
|    409 （全部）                                                                                                     |    * StatusCode = = 409                                                                                                                                                                                                                                          |    所有                                                           |
|    低 PercentSuccess 或分析日誌項目有 ClientOtherErrors 交易狀態作業    |    AzureStorageLog.RequestStatus = = 「 ClientOtherError 」                                                                                                                                                                                                         |    伺服器                                                        |
|    Nagle 警告                                                                                               |    ((AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1.5)) 和 (AzureStorageLog.RequestPacketSize < 1460年) 和 (AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS > = 200)        |    伺服器                                                        |
|    在伺服器和網路的記錄中的時間範圍                                                                    |    #時間戳記 > = 2014年-10-20T16:36:38 和 #Timestamp < = 2014年-10-20T16:36:39                                                                                                                                                                                     |    伺服器、 網路                                               |
|    Server 記錄檔中的時間範圍                                                                                |    AzureStorageLog.Timestamp > = 2014年-10-20T16:36:38 和 AzureStorageLog.Timestamp < = 2014年-10-20T16:36:39                                                                                                                                                     |    伺服器                                                        |


## <a name="next-steps"></a>後續步驟

如需疑難排解 Azure 儲存體中的端對端案例的詳細資訊，請參閱下列資源︰

- [監控、 診斷，及疑難排解 Microsoft Azure 儲存體](storage-monitoring-diagnosing-troubleshooting.md)
- [儲存空間狀況分析](http://msdn.microsoft.com/library/azure/hh343270.aspx)
- [監控 Azure 入口網站中的儲存空間帳戶](storage-monitor-storage-account.md)
- [傳送以 AzCopy 命令列公用程式的資料](storage-use-azcopy.md)
- [Microsoft 訊息分析器作業系統指南](http://technet.microsoft.com/library/jj649776.aspx)
