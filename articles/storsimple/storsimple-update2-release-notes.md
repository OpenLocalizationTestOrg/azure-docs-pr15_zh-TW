<properties 
   pageTitle="StorSimple 8000 數列更新 2 版本資訊 |Microsoft Azure"
   description="將新的功能、 議題及因應措施說明 StorSimple 8000 數列更新 2。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="storsimple-8000-series-update-2-release-notes"></a>StorSimple 8000 數列更新 2 版本資訊  

## <a name="overview"></a>概觀

以下版本說明描述的新功能，並找出要徑的開啟問題 StorSimple 8000 數列更新 2。 他們也包含 StorSimple 軟體、 驅動程式，並包含在此版本的磁碟韌體更新的清單。 

更新 2 可以套用到更新 1.2 版本 (GA) 或更新 0.1 會執行任何 StorSimple 裝置。 更新 2 相關聯的裝置版本是 6.3.9600.17673。

請檢閱 StorSimple 方案中部署更新之前版本資訊中所包含的資訊。

>[AZURE.IMPORTANT]
> 
- 需要大約 4-7 小時安裝此更新 （包括 Windows 更新）。 
- 更新 2 有軟體、 LSI 驅動程式，以及 SSD 韌體更新。
- 新的版本，您可能會看到更新立即由於階段的首展的更新。 請等候幾天，，然後掃描更新一次為這些推出才可使用。


## <a name="whats-new-in-update-2"></a>在 [更新 2 的新增功能

更新 2 介紹的下列新功能。

- **本機釘選區**– 在舊版 StorSimple 8000 數列的資料區塊已層雲端基礎使用方式。 沒有保證區塊會保持在本機方法。 在更新 2 中，當您建立區，您可以指定大量，為本機固定，及主要的大量資料不會層至雲端。 仍會將固定在本機磁碟的快照複製到雲端備份，以便雲端可用於資料行動性和損壞修復之用。 此外，您可以變更大量類型 （也就是轉換層固定在本機磁碟區及轉換本機釘選磁碟區層）。 

- **StorSimple 虛擬裝置改善**-先前，StorSimple 8000 數列放置虛擬裝置做為損毀復原或開發/測試方案。 沒有只有一個模型的虛擬裝置 （模型 1100年）。 更新 2 引進了兩個虛擬裝置模型︰ 

     - 8010 （之前稱為 1100年） – 沒有變更。30 TB 的容量，並使用 Azure 標準的儲存空間。
     - 8020 – 具有 64 TB 的容量，並使用 Azure 進階版儲存空間以改善效能。

    有兩種虛擬裝置模型 (8010/8020) 單一 VHD。 當您第一次啟動虛擬裝置時，它會偵測到的平台參數，並套用正確的模型版本。

- **網路的改良功能**-更新 2 包含下列網路的改良功能︰

     - 多個 Nic 可以啟用雲端，以便容錯移轉可能在 NIC 會失敗。
     - 路由的改良功能，與固定單位的雲端啟用區塊。
     - 線上重試] 的容錯移轉前失敗的資源。
     - 新的服務失敗通知。

- 透過兩個頻道更新**更新改善**-更新 1.2 及舊版，StorSimple 8000 系列中︰ Windows 更新叢集、 iSCSI，及等等，以及 Microsoft Update 的二進位檔案和韌體。
    更新 2 使用 Microsoft Update，所有更新套件。 這應該會導致修正，或執行容錯移轉較少的時間。 

- **韌體更新**– 下列韌體更新所包含︰
    - LSI: lsi_sas2.sys 2.00.72.10 的產品版本
    - 僅限 SSD （沒有 HDD 更新）︰ XMGG、 XGEG、 KZ50、 F6C2 及 VR08

- **主動式支援**– 更新 2 讓 Microsoft 提取其他裝置的診斷資訊。 我們的作業小組另有問題的裝置，我們會更有效地配備從裝置收集的資訊和診斷問題。 **您允許我們提供此主動式支援接受更新 2**。    
 

## <a name="issues-fixed-in-update-2"></a>在 [更新 2 修正問題

下表提供更新 2 中所修正問題的摘要。    

| [否]。 | 功能 | 問題 | 適用於實體裝置 | 適用於虛擬裝置 |
|-----|---------|-------|--------------------------------|--------------------------------|
| 1 | 網路介面 | 更新 1 升級後的 StorSimple 管理員服務報告的 Data2 和 Data3 連接埠失敗一個控制器上。 已經修正此問題。 | [是] | 無 |
| 2 | 更新 | 升級至更新 1 後, 音效警示通知發生在多個裝置上 Azure 傳統入口網站。 已經修正此問題。 | [是] | 無 |
| 3 | Openstack 驗證 | 時使用 Openstack 做為您的雲端服務提供者，您可能會收到錯誤，您的雲端驗證字串是太長。 這個已經修正。 | [是] | 無 |


## <a name="known-issues-in-update-2"></a>更新 2] 中的已知的問題

下表提供此版本的已知問題的摘要。

| [否]。 | 功能 | 問題 | 註解 / 因應措施 | 適用於實體裝置 | 適用於虛擬裝置 |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | 磁碟仲裁 | 少數情況下，如果大部分 8600 裝置的 EBOD 圍繞符號中的已中斷連線，導致沒有磁碟仲裁，然後儲存集區會離線。 即使磁碟重新連線時，它會保持離線。 | 您必須重新啟動裝置。 如果問題持續發生，請連絡 Microsoft 支援服務的下一個步驟。 | [是] | 無 |
| 2 | 不正確的控制器識別碼 | 執行控制器替代方案時，控制器 0 可能會顯示為控制器 1。 控制站取代時從對等節點，載入圖像控制器識別碼可以顯示一開始為等控制站的識別碼。 少數情況下，此行為也可能會出現後重新啟動系統。 | 使用者不不需要任何動作。 控制站取代完成後，這種情況下會自行解決。 | [是] | 無 |
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
| 14 | 移轉 | 沒有相關聯的磁碟區大量群組時，可能會失敗 5000 7000 數列裝置上設定移轉的備份。 | 刪除任何相關聯的磁碟的所有空白大量群組，然後再試一次設定備份。| [是] | 無 |
| 15 | Azure PowerShell cmdlet 和固定在本機磁碟區 | 您無法建立本機固定的區透過 Azure PowerShell cmdlet。 （將層您建立透過 PowerShell 的 Azure 任何大量）。 |一律使用 StorSimple 管理員服務設定固定在本機磁碟。| [是] | 無 |
| 16 |釘選至本機磁碟空間 | 如果您刪除本機固定的音量，可能不會立即更新新的區可用的空間。 StorSimple 管理員服務更新本機的可用空間約每小時。| 等候一小時，再嘗試建立新的區。 | [是] | 無 |
| 17 | 釘選至本機磁碟 | 還原工作公開暫存的快照備份在備份的目錄中，但僅適用於還原工作的工期。 此外，它公開首碼**tmpCollection** **備份原則**] 頁面上，但僅適用於還原作業期間的虛擬磁碟群組。 | 如果您還原工作具有只會在本機釘選區或本機釘選和分層區混合，會發生此問題。 如果還原工作包括只分層，就不會發生這個問題。 需要沒有使用者互動。 | [是] | 無 |
| 18 | 釘選至本機磁碟 | 如果您取消還原工作] 和 [控制器容錯移轉就會立即之後，還原工作會顯示**失敗**，而不是**已取消**。 如果還原工作失敗，而且之後，還原工作會顯示，而**失敗**的不是 [**已取消**，請立即發生控制器容錯移轉。 | 如果您還原工作具有只會在本機釘選區或本機釘選和分層區混合，會發生此問題。 如果還原工作包括只分層，就不會發生這個問題。 需要沒有使用者互動。 | [是] | 無 |
| 19 |釘選至本機磁碟 | 如果您取消還原工作或無法還原發生控制器容錯移轉然後，如果其他還原工作就會出現在 [**工作**] 頁面上。 | 如果您還原工作具有只會在本機釘選區或本機釘選和分層區混合，會發生此問題。 如果還原工作包括只分層，就不會發生這個問題。 需要沒有使用者互動。 | [是] | 無 |
| 20 |釘選至本機磁碟 | 如果您嘗試將分層的區 （建立與使用更新 1.2 複製或更舊版本） 轉換成本機固定的音量和您的裝置空間不足或雲端中斷，可以損毀 clone(s)。| 只使用區已建立及使用複製更新前 2 軟體，會發生這個問題。 應該是不常見的案例。|
| 21 | 大量轉換 | 不會更新附加至大量進行大量轉換時 ACRs (本機釘選到層或反向操作)。 更新 ACRs 可能會導致資料損毀。 | 如有需要更新之前大量轉換 ACRs 但不要讓進一步 ACR 更新進行轉換時。 |

## <a name="controller-and-firmware-updates-in-update-2"></a>在 [更新 2 控制器和韌體更新

此版本更新驅動程式，並在您的裝置上的磁碟韌體。
 
- 如需有關 LSI 韌體更新，請參閱 Microsoft 知識庫文章 3121900。 
- 如需有關磁碟韌體更新，請參閱 Microsoft 知識庫文章 3121899。
 
## <a name="virtual-device-updates-in-update-2"></a>在 [更新 2 的虛擬裝置更新

虛擬裝置無法套用此更新。 需要建立新的虛擬裝置。 

## <a name="next-step"></a>下一步

瞭解如何在 StorSimple 裝置上的 [[安裝更新 2](storsimple-install-update-2.md) 。
