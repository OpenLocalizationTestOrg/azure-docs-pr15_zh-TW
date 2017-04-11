<properties 
   pageTitle="StorSimple 8000 數列更新 1.2 版本資訊 |Microsoft Azure"
   description="將新的功能、 議題及因應措施說明 StorSimple 8000 數列更新 1.2 版。"
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
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="storsimple-8000-series-update-12-release-notes"></a>StorSimple 8000 數列更新 1.2 版本資訊  

## <a name="overview"></a>概觀

以下版本說明描述的新功能，並找出要徑的開啟問題 StorSimple 8000 數列更新 1.2 版。 他們也包含 StorSimple 軟體、 驅動程式及隨附於此版本的磁碟韌體更新的清單。 

更新 1.2 可以套用至執行版本 (GA)、 0.1 會更新，更新 0.2 或更新 0.3 軟體任何 StorSimple 裝置。 更新 1.2 無法使用，如果您的裝置正在執行更新 1 或更新 1.1 （英文）。 如果您的裝置正在執行版本 (GA)，請[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)以協助您安裝此更新。

下表列出對應至更新 1、 1.1 （英文），並 1.2 的裝置軟體版本。

| 如果執行更新... | 這是您的裝置軟體版本。 |
|---------------------|---------------------------------------|
| 更新 1.2          | 6.3.9600.17584                        |
| 更新 1.1 （英文）          | 6.3.9600.17521                        |
| 更新 1.0          | 6.3.9600.17491                        |

請檢閱 StorSimple 方案中部署更新之前版本資訊中所包含的資訊。 如需詳細資訊，請參閱如何[安裝更新 1.2 StorSimple 裝置上](storsimple-install-update-1.md)。 

>[AZURE.IMPORTANT]
> 
- 需要大約要安裝此更新 （包括 Windows 更新） 5 到 10 個小時。 
- 更新 1.2 有軟體、 LSI 驅動程式和磁碟韌體更新。 若要安裝，請遵循中[安裝更新 1.2 StorSimple 裝置上](storsimple-install-update-1.md)的指示進行。
- 新的版本，您可能會看到更新立即由於階段的首展的更新。 掃描幾天一次更新為這些才可使用推出。


## <a name="whats-new-in-update-12"></a>在 [更新 1.2 的新增功能

有限的使用者所做的更新 1 第一次發行這些功能。 更新 1.2 的版本的 StorSimple 使用者最想看到的下列新功能及改良功能︰

- **從 8000 數列裝置 5000 7000 數列移轉**– 這個版本中會介紹新的移轉功能可讓 StorSimple 5000 7000 數列應用裝置使用者將其資料移轉到 StorSimple 8000 數列實體應用裝置或虛擬應用裝置。 移轉功能具有兩個索引鍵值建議︰                                                                  

    - **業務連續性**，讓移轉至 8000 數列就 5000 7000 數列裝置上的現有資料。
    - **改善功能方案的 8000 數列設備**，有效集中管理多個裝置，透過 StorSimple 管理員服務，例如更好的硬體類別，並更新韌體與虛擬就、 資料行動力，未來的藍圖的功能。

    請參閱[升級指南 》](http://www.microsoft.com/download/details.aspx?id=47322)的詳細說明如何將 StorSimple 5000 7000 數列移轉到 8000 數列裝置。 

- **Azure 政府版入口網站中的可用性**– StorSimple 現提供 Azure 政府版入口網站。 請參閱如何[部署 StorSimple 裝置 Azure 政府版入口網站中的](storsimple-deployment-walkthrough-gov.md)。

- **其他雲端服務提供者支援**– 其他雲端服務提供者支援是 Amazon S3，Amazon S3 給、 HP，與 OpenStack （beta 版）。

- **最新的儲存空間 api 的更新**– 在這個版本中，StorSimple 已更新至最新的 Azure 儲存體服務 Api。 StorSimple 8000 數列裝置執行更新前 1 軟體版本 (版本 0.1 與 0.2，0.3) 使用 Azure 儲存體服務 Api 早於 2009 年 7 月 17，版本。 2016 年 8 月 1 日，以說明更新的[消息移除的儲存空間服務版本](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx)，這些 Api 會遭取代。 請務必您套用 StorSimple 8000 數列更新 1，2016 年 8 月 1 日之前。 如果您無法執行此作業，StorSimple 裝置會停止運作正常。

- **支援的區域多餘儲存空間 (ZRS)** – 升級為最新版本的儲存空間 Api，StorSimple 8000 數列將支援區域多餘儲存空間 (ZRS)，除了本機多餘儲存空間 (LRS) 和地理多餘的儲存空間 (GRS)。 請參閱此[文章 Azure 儲存體重複選項](../storage/storage-redundancy.md)的 ZRS 詳細資料。

- **增強型初始部署和更新的體驗**– 在此版本、 安裝和更新程序加強了。 若要提供意見反應給使用者，如果網路設定和防火牆設定不正確改進設定精靈的安裝。 已有其他診斷 cmdlet，以協助您疑難排解網路的裝置。 請參閱[疑難排解部署文章](storsimple-troubleshoot-deployment.md)瞭解用於疑難排解新診斷 cmdlet 的詳細資訊。

## <a name="issues-fixed-in-update-12"></a>在 [更新 1.2 修正問題

下表提供更新 1.2、 1.1 （英文） 及 1 中所修正問題的摘要。    


| [否]。 | 功能 | 問題 | 在 [固定的更新 | 適用於實體裝置 | 適用於虛擬裝置 |
|-----|---------|-------|-----------------|---------------------------------|--------------------------------|
| 1 | Windows PowerShell 的 StorSimple | 使用者從遠端 StorSimple 使用 Windows PowerShell 來存取 StorSimple 裝置，然後再啟動設定精靈，當機發生越 IP 所輸入的資料 0。 更新 1 中現在修正這個問題。 | 更新 1 | [是] | [是] |
| 2 | 工廠重設 | 在某些情況下，當您執行工廠重設 StorSimple 裝置變成無法解決問題並顯示這則訊息︰**重設為原廠位於進度 （階段 8）**。 發生此問題。 如果進行 cmdlet 時，按下 CTRL + C。 現在已經修正這個問題。| 更新 1 | [是] | 無 |
| 3 | 工廠重設 | 重設失敗雙重控制器工廠後，允許您繼續裝置註冊。 這會導致不支援的系統設定。 更新 1 中，會顯示錯誤訊息，並登錄遭到封鎖裝置上的已失敗的工廠重設。 | 更新 1 | [是] | 無 |
| 4 | 工廠重設 | 在某些情況下，引發的 false 正數不相符的通知。 在執行更新 1 裝置上，將不再會產生不正確的不符警示。 | 更新 1 | [是] | 無 |
| 5 | 工廠重設 | 如果工廠重設遭到中斷完成之前，請輸入 [復原] 模式裝置，且不允許您存取 Windows PowerShell 的 StorSimple。 現在已經修正這個問題。 | 更新 1 | [是] | 無 |
| 6 | 修復損毀 | 損毀修復 (DR) bug 修正該處 DR 會失敗期間的目標裝置上的備份。 | 更新 1 | [是] | [是] |
| 7 | 監控 Led | 在某些情況下，監控後面應用裝置的 Led 沒有指出正確的狀態。 藍色 LED 為關閉狀態。 資料 0 與資料 1 Led 已閃爍即使當這些介面沒有設定。 已修正問題，並監控 Led 現在指出正確的狀態。  | 更新 1 | [是] | 無 |
| 8 | 監控 Led | 在某些情況下，套用更新 1 之後藍色精簡作用中的控制器上關閉藉此讓您難以辨識的作用中的控制站。 已在此修補程式版本修正此問題。| 更新 1.2 | [是] | 無 |
| 9 | 網路介面 | 在舊版本，以非舉例來說閘道器設定 StorSimple 裝置無法離線。 在此版本中已資料 0 的路由公制最低; 做因此，即使其他網路介面雲端功能，將會透過資料 0 路由從裝置的雲端流量。 | 更新 1 | [是] | [是] | 
| 10 | 備份 | 已修補程式版本更新 1.1 （英文） 修正導致失敗 24 天後的備份更新 1 中的錯誤。 | 更新 1.1 （英文） | [是] | [是] |
| 11 | 備份 | 舊版本中的錯誤導致低變更率較雲端快照的效能不佳。 已在此修補程式版本中修正這個問題。| 更新 1.2 | [是] | [是] |
| 12 | 更新 | 此修補程式版本中已修正的報告升級失敗，導致控制站，進入 [復原] 模式中，更新 1 中的錯誤。| 更新 1.2 | [是] | [是] |


## <a name="known-issues-in-update-12"></a>更新 1.2 的已知的問題

下表提供此版本的已知問題的摘要。

| [否]。 | 功能 | 問題 | 註解/因應措施 | 適用於實體裝置 | 適用於虛擬裝置 |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | 磁碟仲裁 | 少數情況下，如果大部分的磁碟 8600 裝置的 EBOD 圍繞符號會中斷連線，導致沒有磁碟仲裁，然後儲存集區會是離線。 即使磁碟重新連線時，它會保持離線。 | 您必須重新啟動裝置。 如果問題持續發生，請連絡 Microsoft 支援服務的下一個步驟。 | [是] | 無 |
| 2 | 不正確的控制器識別碼 | 執行控制器替代方案時，控制站 0 可能會顯示為控制站 1。 控制站取代時從對等節點，載入圖像控制器識別碼可以顯示一開始為等控制站的識別碼。 少數情況下，此行為也可能會出現後重新啟動系統。 | 使用者不不需要任何動作。 控制站取代完成後，這種情況下會自行解決。 | [是] | 無 |
| 3 | 儲存帳戶 | 若要刪除的儲存空間帳戶使用儲存服務是不受支援的案例。 這會導致無法擷取使用者資料在其中一種情況。 | [是] | [是] |
| 4 | 裝置容錯移轉 | 不支援在不同的目標裝置從相同的來源裝置音量容器的多個錯誤後移轉。 從單一無裝置到多個裝置的裝置容錯移轉，就能在第一個裝置上無法大量容器會遺失資料擁有權。 後這類移轉後，會顯示這些大量容器，或將其行為，當您在 Azure 傳統入口網站檢視中。 | | [是] | 無 |
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

## <a name="physical-device-updates-in-update-12"></a>更新 1.2 的實體裝置更新

如果更新修補程式 1.2 會套用至實體裝置 （執行更新 1 之前的版本），6.3.9600.17584 會變更的軟體版本。

## <a name="controller-and-firmware-updates-in-update-12"></a>在更新 1.2 控制器和韌體更新

此版本更新驅動程式，並在您的裝置上的磁碟韌體。
 
- 如需有關 SA 控制器更新的詳細資訊，請參閱[Microsoft Azure StorSimple 應用裝置中的 LSI SA 控制站的更新 1](https://support.microsoft.com/kb/3043005)。 

- 如需有關磁碟韌體更新，請參閱[磁碟韌體更新 1 的 Microsoft Azure StorSimple 應用裝置](https://support.microsoft.com/kb/3063416)。
 
## <a name="virtual-device-updates-in-update-12"></a>在 [更新 1.2 的虛擬裝置更新

虛擬裝置無法套用此更新。 需要建立新的虛擬裝置。 

## <a name="next-steps"></a>後續步驟

- [在您的裝置上安裝更新 1.2](storsimple-install-update-1.md)。
 