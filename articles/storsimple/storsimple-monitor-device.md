<properties 
   pageTitle="監控 StorSimple 裝置 |Microsoft Azure"
   description="說明如何使用 StorSimple 管理員服務來監控 I/O 效能、 容量使用量、 網路生產力和裝置的效能。"
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
   ms.date="08/16/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-monitor-your-storsimple-device"></a>使用 StorSimple 管理員服務來監控您的 StorSimple 裝置 

## <a name="overview"></a>概觀

您可以使用 「 StorSimple 管理員服務監控 StorSimple 方案中的特定的裝置。 您可以建立自訂的圖表，根據 I/O 效能、 容量使用量、 網路生產力和裝置效能指標。 

若要檢視特定裝置的監控資訊 Azure 傳統入口網站中，選取 [StorSimple 管理員服務。 按一下 [**監視器**] 索引標籤，然後選取清單中的裝置。 [**監視器**] 頁面包含下列資訊。

## <a name="io-performance"></a>I/O 效能 

**I/O 效能**追蹤指標相關的閱讀數上和寫入作業之間可能 iSCSI 啟動器介面主機伺服器和裝置或裝置雲端。 特定的大量、 特定大量容器，或所有大量容器測量此效能。

下圖顯示至您的裝置生產裝置的相關的所有磁碟區啟動器 I/O。 繪製指標讀取及撰寫秒位元組、 讀取與寫入每秒 IO 作業和讀取和寫入延遲。

![從啟動器至裝置的 IO 效能](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

I/O 作業同一個裝置，例如從裝置至所有大量容器的雲端的資料。 在此裝置上的資料僅位於線性層，並不具有溢出至雲端。 沒有讀寫作業從裝置發生至雲端。 因此，在圖表中的尖峰是在 5 分鐘的時間間隔，對應到哪些活動訊號已核取 [裝置和服務之間的頻率。 

![從雲端裝置 IO 效能](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)


同一個裝置，雲端快照的大量資料開始在 2:00 pm。 這會導致從裝置傳送至雲端的資料。 讀取寫入到雲端服務此工期。 IO 圖表會顯示最大使用量中各種不同的指標對應至時快照的時間。 

![雲端快照之後的裝置 IO 效能](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)


## <a name="capacity-utilization"></a>容量充分利用 

**容量使用量**追蹤指標相關的區，大量容器或裝置使用的資料儲存空間量。 您可以建立根據您主要的儲存空間，您的雲端儲存空間，或您裝置的儲存空間容量使用量的報表。 在特定的大量、 特定大量容器或所有大量容器測量容量充分利用。


主要雲端，並可描述裝置儲存容量，如下所示︰

###<a name="primary-storage-capacity-utilization"></a>主要儲存容量使用量
 
這些圖表顯示資料是 deduplicated 和壓縮之前 StorSimple 區寫入的資料的量。 所有的磁碟區或單一磁碟區，您可以檢視主要儲存使用量。

當您檢視所有的磁碟區與每個個別區主要儲存大量容量使用量的圖表，並加總的主資料的情況時，兩個數字之間可能有不相符。 在所有的磁碟上的總主資料可能不會新增至個別區的主資料的總和中。 這可能是因為下列其中一項︰

- **包含所有磁碟區的快照資料**︰ 如果您執行的版本早於更新 3，看到這種行為。 顯示所有區的主資料是每個大量的主資料和快照資料的總和。 指定區所顯示的主資料對應至的配置在大量的資料量 （而不包含對應的大量快照資料）。

    這也說明由下列方程式︰

    *主要資料 （所有磁碟區） = Sum （主要資料 （大量 i） + 大小的快照集資料 （大量 i））*
    
    *主要資料 （大量 i） 的位置 = 配置給大量我的主要資料的大小*
 
    如果快照會刪除服務，請刪除完成非同步背景中。 可能需要一些時間追蹤快照刪除更新的大量資料大小。 

    如果 3 或更新版本，請執行更新，然後快照資料不是包含在大量資料。 與主要的使用狀況的計算方式為︰

    * 主要資料 （所有磁碟區） = Sum （主要資料的 （大量 i）
    
    *主要資料 （大量 i） 的位置 = 配置給大量我的主要資料的大小*
 
- **包含在所有的磁碟區監視停用區**︰ 如果您有大量內容的監控已關閉您的裝置上時，這些個別的磁碟的監控資料將無法使用的圖表。 然而，所有的磁碟區圖表中的資料包括其監視已關閉。 
 
- **刪除區即時相關聯的備份，包含所有磁碟區**︰ 如果包含資料的快照集區會刪除，但仍然存在相關聯的快照，然後您可能會看到不相符。

- **刪除區包含所有磁碟區**︰ 在某些情況下，即使這些已刪除的郵件，可能有舊的區。 不會出現的 [刪除效果並裝置可能會顯示較低可用的容量。 您需要連絡 Microsoft 支援服務，若要移除這些磁碟。

下列圖表顯示主要儲存容量 StorSimple 裝置的使用狀況之前, 和之後雲端快照。 這是只大量資料，請為雲端快照不應該變更主要的儲存空間。 如您所見，圖表會顯示當做概覽雲端的主要容量使用率沒有差別。 雲端快照開始在大約 2:00 pm 的裝置上。

![雲端快照之前的主要容量使用量](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)

![雲端快照之後的主要容量使用量](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)

如果您執行的更新 2 或更高，您可以細分主要儲存容量使用量的個別的音量，所有的磁碟、 所有分層磁碟區，所有本機固定的區和如下所示。 分解所有固定在本機磁碟，可讓您快速確定 [本機層中有多少使用。

![所有的固定在本機磁碟區的主要容量使用量](./media/storsimple-monitor-device/localvolumes.png)


###<a name="cloud-storage-capacity-utilization"></a>雲端儲存空間容量使用量

這些圖表顯示使用雲端儲存空間量。 此資料 deduplicated，而壓縮。 在這段包含雲端快照集可能包含資料，不會反應在任何主要大量與保留舊版或需要保留用途。 您可以比較主要並雲端儲存空間消耗量數字的資料降低工資率，了解雖然無法完全數字。 下列圖表顯示雲端儲存空間容量使用率 StorSimple 裝置之前和之後雲端快照。 在啟動雲端快照大約 2:00 pm 的裝置上，您可以看到雲端容量使用量擷取畫面同時，增加 5.73 mb 4.04 gb。

![雲端快照之前的雲端容量使用量](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

![雲端後雲端快照的容量使用量](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)


###<a name="device-storage-capacity-utilization"></a>裝置儲存空間容量使用量

這些圖表顯示用的裝置，會利用多個主要的存放區，因為它包含 SSD 線性層的整體的使用狀況。 這一層包含的資料，也存在於金額裝置的其他層。 在 SSD 線性層容量重新 HDD 層 （此時它是 deduplicated 和壓縮） 及後續至雲端，新的資料時，要移動舊的資料。

一段時間，主要容量使用量和裝置容量使用量會很可能會增加一起直到資料開始層至雲端。 此時，裝置容量使用量可能會開始時光，但主要容量使用率會增加寫詳細資料。

下列圖表顯示主要儲存容量 StorSimple 裝置的使用狀況之前, 和之後雲端快照。 雲端快照在 2:00 pm 和裝置容量使用量開始在該時間降低。 裝置儲存空間容量使用量已關閉從 11.58 GB 7.48 gb。 這表示很可能會在線性 SSD 層壓縮的資料已 deduplicated、 壓縮，移到 HDD 層。 請注意，是否使用的裝置已有大量的資料，SSD 」 和 「 HDD 層中，您可能不會出現此減少。 在此範例中，裝置有少量的資料。

![雲端快照之前的裝置容量使用量](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

![雲端快照之後的裝置容量使用量](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)


## <a name="network-throughput"></a>網路生產力

**網路生產力**追蹤指標相關傳輸 iSCSI 啟動器網路介面從主機伺服器和裝置和裝置與雲端之間的資料量。 您可以在裝置上的每個 iSCSI 網路介面監視此計量。

下列圖表顯示網路生產力資料 0 與資料 4，您的裝置上的兩個 1 GbE 網路介面。 在此情況下，0 是雲端啟用資料而資料 4 已 iSCSI 啟用。 您可以看到的輸入與輸出流量 StorSimple 裝置。 從 3:24 pm 開始圖表中的一般行 owing 事實我們只每 5 分鐘收集資料，並應忽略。 

![網路生產力的 Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![網路生產力的 Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)


## <a name="device-performance"></a>裝置的效能 

**裝置的效能**追蹤與您的裝置的效能相關的指標。 下圖顯示在生產環境中的 CPU 使用率裝置的相關統計資料。

![裝置的 CPU 使用率](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## <a name="next-steps"></a>後續步驟

- 瞭解如何[使用 StorSimple 管理員服務裝置儀表板](storsimple-device-dashboard.md)。

- 瞭解如何[使用 StorSimple 管理員服務，來管理您的 StorSimple 裝置](storsimple-manager-service-administration.md)。
