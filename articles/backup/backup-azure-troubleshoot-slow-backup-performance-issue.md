<properties
   pageTitle="疑難排解變得很慢備份檔案與資料夾 Azure 備份 |Microsoft Azure"
   description="提供疑難排解的指導方針可協助您診斷 Azure 備份效能問題的原因"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="jimpark"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="genli"/>

# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>疑難排解變得很慢備份中 Azure 備份檔案和資料夾

本文提供疑難排解的指導方針可協助您診斷檔案和資料夾的備份效能變慢的原因，當您使用的 Azure 備份。 當您使用 Azure 備份代理程式備份檔案時，備份程序可能會超過預期時間。 此延遲可能會因下列一或多項動作︰

-   [正在備份的電腦上有效能瓶頸。](#cause1)
-   [另一個處理程序或防毒軟體，會干擾 Azure 備份程序。](#cause2)
-   [Azure 虛擬機器 (VM) 執行備份代理程式。](#cause3)  
-   [您要備份檔案有很多 （百萬）。](#cause4)

在啟動問題的疑難排解之前，建議您下載並安裝[最新的 Azure 備份代理程式](http://aka.ms/azurebackup_agent)。 我們會將經常更新進行備份代理程式修正各種問題、 新增功能，以及改善效能。

我們也強烈建議您檢閱[Azure 備份服務常見問題集](backup-azure-backup-faq.md)，請確定您不發生上述任何一般設定問題。

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>
## <a name="cause-performance-bottlenecks-on-the-computer"></a>原因︰ 在電腦上的效能瓶頸

瓶頸正在備份的電腦上可能會導致延遲狀況。 例如，電腦可以讀取或寫入到或可用的頻寬，可以將資料傳送到網路，可能會導致瓶頸。

Windows 提供具有稱為[效能監視器](https://technet.microsoft.com/magazine/2008.08.pulse.aspx)（效能） 偵測這些瓶頸的內建工具。

以下是一些效能計數器及很有幫助診斷瓶頸的最佳備份的範圍。

| [計數器  | 狀態  |
|---|---|
|邏輯磁碟 （實體磁碟）-閒置 %   | • 100%閒置為 50%閒置 = 狀況良好</br>• 49%閒置為 20%閒置 = 警告或監視器</br>• 19%閒置為 0%閒置 = 要徑或的規格|
|  邏輯磁碟 （實體磁碟）-%平均 磁碟 Sec 讀取或寫入 |  • 0.001 毫秒 0.015 ms = 狀況良好</br>• 以 0.025 ms 0.015 ms = 警告或監視器</br>• 0.026 ms 或再 = [要徑或的規格|
|  邏輯磁碟 （實體磁碟）-目前磁碟佇列長度 （適用於所有執行個體） | 超過 6 分鐘 80 要求 |
| 記憶體--集區非分頁位元組|• 小於 60%耗用的集區 = 狀況良好<br>• 61%到 80%耗用的集區 = 警告或監視器</br>• 大於 80%資料庫 consumed = 要徑或的規格|
| 記憶體--資料庫分頁位元組 |• 小於 60%耗用的集區 = 狀況良好</br>• 61%到 80%耗用的集區 = 警告或監視器</br>• 大於 80%資料庫耗用 = 要徑或的規格|
| 記憶體--可用 mb 數| • 50%免費可用的記憶體或更多 = 狀況良好</br>• 25%免費可用的記憶體 = 監視器</br>• 10%免費可用的記憶體 = 警告</br>• 小於 100 MB] 或 [可用的記憶體 5 百分比 = [要徑或的規格|
|處理器-\%處理器時間 （所有執行個體）|• 小於耗用 60%= 狀況良好</br>• 61%到 90 %consumed = 監視器] 或 [警告</br>• 91%到 100%耗用 = 要徑|


> [AZURE.NOTE] 如果您判斷基礎結構的問題，我們建議您重組磁碟定期較佳的效能。

<a id="cause2"></a>
## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>原因︰ 另一個處理程序或干擾 Azure 備份的防毒軟體

我們看到多個執行個體其他處理程序中的 Windows 系統負面影響效能 Azure 備份代理程式程序的位置。 例如，如果您使用 Azure 備份代理程式和其他程式備份資料，或如果正在執行防毒軟體，且檔案備份已鎖定，多鎖定上檔案可能會導致競爭。 在此情況下，可能會失敗備份，或作業可能會超過預期時間。

最佳在這個案例中建議您關閉 [其他備份程式，以查看是否要變更備份 Azure 代理程式的備份時間。 通常，確定不同時執行備份的多個工作是不足，無法防止影響彼此。

防毒程式，我們建議您排除下列檔案及位置︰

- C:\Program Files\Microsoft Azure 復原服務 Agent\bin\cbengine.exe 程序
- C:\Program Files\Microsoft Azure 復原服務 Agent\ 資料夾
- 可用的位置 （如果您不使用標準的位置）

<a id="cause3"></a>
## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Azure 虛擬機器上執行的原因︰ 備份代理程式

如果您執行的在 VM 備份代理程式，效能會比在實體的電腦上您執行的當慢。 這必須是由於 IOPS 限制。  不過，您也可以切換至 Azure 進階版儲存體備份資料磁碟機最佳化效能。 我們正在修正此問題，並修正工具將可在未來的版本。

<a id="cause4"></a>
## <a name="cause-backing-up-a-large-number-millions-of-files"></a>原因︰ 備份檔案有很多 （百萬）

移動大量的資料將會超過移動較小的大量的資料。 在某些情況下，不只的資料，但也數檔案或資料夾大小相關備份的時間。 備份數百萬個小的檔案 （幾個 kb 到幾個位元組） 時，這是如此。

Azure 目前時您備份資料，並將其移至 Azure，同時分類您的檔案，就會發生此問題。 在某些少見的情況下，[目錄] 作業可能會超過預期時間。

下列指標可以協助您瞭解瓶頸，並會相應地處理的下一個步驟︰

- **使用者介面會顯示的資料傳輸進度**。 仍傳輸的資料。 網路頻寬或資料的大小可能會導致延遲狀況。

- **UI 未顯示的資料傳輸進度**。 開啟位於 C:\Microsoft Azure 復原服務 Agent\Temp 記錄並檢查記錄中的 FileProvider::EndData 項目。 這個項目表示的資料傳輸完成和新鮮事目錄作業。 沒有取消備份工作。 不過，等待得較長的目錄作業完成時間。 如果問題持續發生，請連絡[支援 Azure](https://portal.azure.com/#create/Microsoft.Support)。
