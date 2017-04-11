<properties 
   pageTitle="StorSimple 8000 數列更新 3 版本資訊 |Microsoft Azure"
   description="將新的功能、 議題及因應措施說明 StorSimple 8000 數列更新 3。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/14/2016"
   ms.author="alkohli" />

# <a name="storsimple-8000-series-update-3-release-notes"></a>StorSimple 8000 數列更新 3 版本資訊  

## <a name="overview"></a>概觀

以下版本說明描述的新功能，並 StorSimple 8000 數列更新 3 識別要徑的議題。 他們也包含這個版本中包含 StorSimple 軟體更新的清單。 

更新 3 可以套用到更新 2.2 版本 (GA) 或更新 0.1 會執行任何 StorSimple 裝置。 更新 3 相關聯的裝置版本是 6.3.9600.17759。

請檢閱 StorSimple 方案中部署更新之前版本資訊中所包含的資訊。

>[AZURE.IMPORTANT]
> 
> - 更新 3 有裝置軟體、 LSI 驅動程式和韌體和 Storport，而且太空更新。 需要大約要安裝此更新為 1.5 來計算 2 個小時。 

> - 新的版本，您可能會看到更新立即由於階段的首展的更新。 請等候幾天，，然後掃描更新一次為這些推出才可使用。


## <a name="whats-new-in-update-3"></a>在 [更新 3 的新增功能

已在更新 3 進行下列主要改良功能和修正錯誤。

 
- **自動空間回收變更**-啟動更新 3、 空間回收演算法執行產生更快速地執行系統的備用控制器上。 如需有關使用空間回收所需的連接埠的詳細資訊，請參閱[StorSimple 網路需求](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)。

- **改良效能**-更新 3 已經改良讀寫效能至雲端。

- **移轉相關的改良功能**-在此版本、 多個錯誤修正和改善已完成的移轉功能的 5000/7000 數列裝置 8000 數列裝置。 如需有關如何使用移轉功能的詳細資訊，請前往[從 8000 數列裝置 5000/7000 數列裝置移轉](https://www.microsoft.com/en-us/download/details.aspx?id=47322)。 

- **監控相關的修正**-在這個版本中，bug 相關監控圖表、 服務的儀表板] 和裝置的儀表板已固定。


## <a name="issues-fixed-in-update-3"></a>在 [更新 3 修正問題

下表提供更新 3 中所修正問題的摘要。    

| 無 | 功能                                    | 問題                                                                                                                                                                                                                                                                                        | 適用於實體裝置 | 適用於虛擬裝置 |
|----|--------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|---------------------------|
| 1  | Host （主機） 端的資料移轉                      | 在舊的版本，StorSimple 雲端應用裝置已離線期間主機端的資料移轉。 在此版本修正此問題。                                                | 無                        | [是]                        |
| 2  | 釘選至本機磁碟                     | 在先前的版本中，沒有 I/O 失敗，大量轉換失敗次數及資料路徑失敗固定在本機磁碟區的相關問題。 根導致這些問題，而且固定在此版本。                | [是]                        | 無                       |
| 3  | 監控                                    | 沒有報告單位和監視以及不正確的資訊已顯示固定在本機磁碟的裝置儀表板圖表相關的多個問題。 在這個版本中已修正這些問題。 | [是]                         | 無                       |
| 4  | 重寫 I/O                          | 使用時 StorSimple 負載涉及重寫的使用者會遇到不常用的 bug 位置工作集已被層到雲端。 在此版本修正這個問題。 | [是]                        | [是]                       |
| 5  | 備份                                     | 在某些少見的情況下，在舊版的軟體，當使用者進行遠端複本的備份，會遇到雲端錯誤及作業想查看的錯誤。 在這個版本中，修正問題，然後作業成功完成]。             | [是]                        | [是]                       |
| 6  | 備份原則                                     | 在特定少見的執行個體，先前的版本類似的軟體、 時發生錯誤相關的備份原則刪除。 在此版本修正此問題。       | [是]                        | [是]                       |



## <a name="known-issues-in-update-3"></a>更新 3 的已知的問題

下表提供此版本的已知問題的摘要。

| [否]。 | 功能 | 問題 | 註解 / 因應措施 | 適用於實體裝置 | 適用於虛擬裝置 |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | 磁碟仲裁 | 少數情況下，如果大部分 8600 裝置的 EBOD 圍繞符號中的已中斷連線，導致沒有磁碟仲裁，然後儲存集區會離線。 即使磁碟重新連線時，它會保持離線。 | 您必須重新啟動裝置。 如果問題持續發生，請連絡 Microsoft 支援服務的下一個步驟。 | [是] | 無 |
| 2 | 不正確的控制器識別碼 | 執行控制器替代方案時，控制站 0 可能會顯示為控制站 1。 控制站取代時從對等節點，載入圖像控制器識別碼可以顯示一開始為等控制站的識別碼。 少數情況下，此行為也可能會出現後重新啟動系統。 | 使用者不不需要任何動作。 控制站取代完成後，這種情況下會自行解決。 | [是] | 無 |
| 3 | 儲存帳戶 | 若要刪除的儲存空間帳戶使用儲存服務是不受支援的案例。 這會導致無法擷取使用者資料在其中一種情況。|  | [是] | [是] |
| 4 | 裝置容錯移轉 | 不支援在不同的目標裝置從相同的來源裝置音量容器的多個錯誤後移轉。 從單一無裝置容錯移轉到多個裝置，就能在第一個裝置上無法大量容器會遺失資料擁有權。 後這類移轉後，會顯示這些大量容器，或將其行為，當您在 Azure 傳統入口網站檢視中。 | | [是] | 無 |
| 5 | 安裝 | 期間 StorSimple 介面卡的 SharePoint 安裝，您需要提供裝置 IP 中順利完成安裝]。    | | [是] | 無 |
| 6 | 網頁 proxy | 如果您的 web proxy 設定為指定的通訊協定 HTTPS，然後會影響您的裝置-服務通訊，並將離線使用的裝置。 在 [程序，使用您的裝置上的大量資源，也會產生支援套件。 | 請確定網址 proxy 設為指定的通訊協定 HTTP。 如需詳細資訊，移至[您的裝置設定網頁 proxy](storsimple-configure-web-proxy.md)。 | [是] | 無 |
| 7 | 網頁 proxy | 如果您設定，並啟用註冊裝置上的網頁 proxy，您會需要重新啟動您的裝置上使用中的控制站。 | | [是] | 無 |
| 8 | 高雲端延遲和高 I/O 工作量 | 當您 StorSimple 裝置遇到更高的雲端延遲 （順序 （秒）） 與高 I/O 工作負載的組合時，裝置區進入降級狀態和 I/o 可能失敗的 「 裝置尚未準備 」 錯誤。 | 您必須以手動方式重新啟動裝置控制器或執行裝置容錯移轉修復這種情況。 | [是] | 無 |
| 9 | Azure PowerShell | 當您使用 StorSimple cmdlet**取得 AzureStorSimpleStorageAccountCredential & #124;選取物件的第一次 1-等待**若要讓您可以建立新的**VolumeContainer**物件，請選取第一個物件，指令程式會傳回的所有物件。 | 如下所示換括號括住的 cmdlet: **(取得-Azure-StorSimpleStorageAccountCredential) & #124;選取物件的第一次 1-等待** | [是] | [是] |
| 10| 移轉 | 多個大量容器傳遞時移轉，最新的備份知道正確無誤僅適用於第一個大量容器。 此外，在第一個大量容器中的第一次 4 備份會移轉之後，就會開始平行移轉。 | 我們建議您一次移轉一個大量容器。 | [是] | 無 |
| 11| 移轉 | 復原之後，區不會加入至備份原則或虛擬磁碟群組。 | 您必須將這些磁碟備份的原則，才能建立備份。 | [是] | [是] |
| 12| 移轉 | 完成移轉後，5000/7000 數列裝置必須存取移轉的資料容器。 | 我們建議您完成及已確認移轉之後，刪除移轉的資料容器。 | [是] | 無 |
| 13| 複製及 DR | 執行更新 1 StorSimple 裝置無法複製，或執行損毀修復執行更新前 1 軟體的裝置。 | 您需要更新 1 允許這些作業更新的目標裝置 | [是] | [是] |
| 14 | 移轉 | 沒有相關聯的磁碟區大量群組時，可能會失敗 5000 7000 數列裝置上設定備份移轉。 | 刪除任何相關聯的磁碟的所有空白大量群組，然後再試一次設定備份。| [是] | 無 |
| 15 | Azure PowerShell cmdlet 和固定在本機磁碟區 | 您無法建立本機固定的區透過 Azure PowerShell cmdlet。 （將層您建立透過 PowerShell 的 Azure 任何大量）。 |一律使用 StorSimple 管理員服務設定固定在本機磁碟。| [是] | 無 |
| 16 |釘選至本機磁碟空間 | 如果您刪除本機固定的音量，可能不會立即更新新的區可用的空間。 StorSimple 管理員服務更新本機的可用空間約每小時。| 等候一小時，再嘗試建立新的區。 | [是] | 無 |
| 17 | 釘選至本機磁碟 | 還原工作公開暫時快照備份在備份的目錄中，但僅適用於還原工作的工期。 此外，它公開首碼**tmpCollection** **備份原則**] 頁面上，但僅適用於還原作業期間的虛擬磁碟群組。 | 如果您還原工作具有只會在本機釘選區或本機釘選和分層區混合，會發生此問題。 如果還原工作包括只分層，就不會發生這個問題。 需要沒有使用者互動。 | [是] | 無 |
| 18 | 釘選至本機磁碟 | 如果您取消還原工作] 和 [控制器容錯移轉就會立即之後，還原工作會顯示**失敗**，而不是**已取消**。 如果還原工作失敗，而且之後，還原工作會顯示，而**失敗**的不是 [**已取消**，請立即發生控制器容錯移轉。 | 如果您還原工作具有只會在本機釘選區或本機釘選和分層區混合，會發生此問題。 如果還原工作包括只分層，就不會發生這個問題。 需要沒有使用者互動。 | [是] | 無 |
| 19 |釘選至本機磁碟 | 如果您取消還原工作或無法還原發生控制器容錯移轉然後，如果其他還原工作就會出現在 [**工作**] 頁面上。 | 如果您還原工作具有只會在本機釘選區或本機釘選和分層區混合，會發生此問題。 如果還原工作包括只分層，就不會發生這個問題。 需要沒有使用者互動。 | [是] | 無 |
| 20 |釘選至本機磁碟 | 如果您嘗試將分層的區 （建立與使用更新 1.2 複製或更舊版本） 轉換成本機固定的音量和您的裝置空間不足或雲端中斷，可以損毀 clone(s)。| 只使用區已建立及使用複製更新前 2.1 軟體，會發生這個問題。 應該是不常見的案例。|
| 21 | 大量轉換 | 不會更新附加至大量進行大量轉換時 ACRs (本機釘選到層或反向操作)。 更新 ACRs 可能會導致資料損毀。 | 如有需要更新之前大量轉換 ACRs 但不要讓進一步 ACR 更新進行轉換時。 |
| 22 | 更新 | 套用更新 3 時, Azure 傳統入口網站中的 [**進行的維修作業**] 頁面會顯示下列訊息相關更新 2-「 StorSimple 8000 數列更新 2 包含 Microsoft 主動收集記錄資訊從您的裝置，我們偵測潛在問題時，能力 」。 這會造成誤解，因為它會指出裝置的更新來更新 2。 裝置 succeesfully 更新，以更新 3 之後，就會消失這則訊息。 | 將在未來版本修正這個問題。 | [是] | 無 |


## <a name="controller-and-firmware-updates-in-update-3"></a>控制站和韌體的更新，更新 3

這個版本有 LSI 驅動程式 」 和 「 韌體更新。 如需有關如何安裝 LSI 驅動程式和韌體更新的詳細資訊，請參閱[安裝更新 3](storsimple-install-update-3.md) StorSimple 裝置上。

 
## <a name="virtual-device-updates-in-update-3"></a>虛擬裝置的更新，更新 3

此更新無法套用到 StorSimple 雲端應用裝置 （也稱為虛擬裝置）。 需要建立新的虛擬裝置。 


## <a name="next-step"></a>下一步

瞭解如何在 StorSimple 裝置上的 [[安裝更新 3](storsimple-install-update-3.md) 。