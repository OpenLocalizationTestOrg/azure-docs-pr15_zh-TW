<!--author=alkohli last changed: 12/15/15-->

| 限制識別碼 | 限制 | 註解 |
|----------------- | ------|--------- |
| 最大的數字的儲存空間帳戶認證 | 64 | |
| 大量容器的數目上限 | 64 | |
| 區數目上限 | 255 | |
| 排程每頻寬範本的最大的數字 | 168 | 每小時、 週 (24 * 7) 中的每日排程。 |
| 分層實體裝置上區的大小上限 | 64 TB 8100 和從 8600 | 8100 和從 8600 是實體裝置。 |
| 分層中 Azure 虛擬裝置上區的大小上限 | 30 TB 的 8010 <br></br> 64 TB 的 8020 | 8010 和 8020 是在 Azure 虛擬分別使用標準的儲存空間，進階版的儲存空間的裝置。 |
| 本機固定在實體裝置上區的大小上限 | 9 TB 的 8100 <br></br> 從 8600 的 24 TB | 8100 和從 8600 是實體裝置。 |
| ISCSI 連線數目上限 | 512 | |
| 從啟動器 iSCSI 連線數目上限 | 512 | |
| 每個裝置存取控制記錄數目上限 | 64 | |
| 每個備份原則區數目上限 | 24 | |
| 最大的數字的保留原則備份的備份 | 64 | |
| 每個備份原則排程的數目上限 | 10 | |
| 最大數目可以保留每大量任何類型的快照集 | 256 | 這包含本機快照與雲端快照集。 |
| 可以在任何裝置中出現的快照的數目上限 | 10000 | |
| 可以處理平行備份，還原，或複製區的數目上限 | 16 |<ul><li>如果有多個 16 的區，請進行處理序隨著處理的位置。</li><li>新的備份的複製或還原分層的大量無法發生直到在完成作業。 不過，對於本機磁碟區，備份允許之後音量處於線上狀態。</li></ul>|
| 還原並複製分層區復原時間 | < 2 分鐘 | <ul><li>音量可還原或複製的作業，無論大量大小的 2 分鐘內。</li><li>大量效能一開始可能會比標準為大部分的資料及中繼資料仍會保留在雲端慢。 效能可能會增加為 StorSimple 裝置雲端的資料流。</li><li>若要下載的中繼資料的總時間，取決於配置的大量大小。 中繼資料會自動納入中的每個 TB 配置的大量資料的 5 分鐘速率背景的裝置。 這個速度可能受至雲端的網際網路頻寬。</li><li>在裝置上所有的中繼資料時，還原或複製操作已完成。</li><li>無法執行備份的作業，直到還原或複製操作全部完成。|
| 還原復原固定在本機磁碟的時間 | < 2 分鐘 | <ul><li>音量可還原操作，無論大量大小的 2 分鐘內。</li><li>大量效能一開始可能會比標準為大部分的資料及中繼資料仍會保留在雲端慢。 效能可能會增加為 StorSimple 裝置雲端的資料流。</li><li>若要下載的中繼資料的總時間，取決於配置的大量大小。 中繼資料會自動納入中的每個 TB 配置的大量資料的 5 分鐘速率背景的裝置。 這個速度可能受至雲端的網際網路頻寬。</li><li>與分層區，若是固定在本機磁碟，不同的大量資料是也下載至本機裝置上。 所有的大量資料已經集結裝置時，還原已完成。</li><li>還原作業可能長，才會完成還原的總時間取決於提供的本機磁碟區，您的網際網路頻寬和裝置上的現有資料的大小。 在進行還原作業時允許固定在本機磁碟區上的備份作業。|
| 精簡還原可用性 | 最後一個容錯移轉 | |
| 最大的用戶端讀/寫處理量 （時從 SSD 層服務） * | 920/720 MB/s 和單一 10GbE 網路介面 | 最多 2 x 與 MPIO 和兩個網路介面。 |
| 最大的用戶端讀/寫處理量 （時從 HDD 層服務） * | 120/250 MB/s |
| 最大的用戶端讀/寫處理量 （當有來自雲端層） * | 11/41 MB/s | 閱讀處理量取決於用戶端產生及維護足夠 I/O 佇列深度。 |

& #42。這裡計算每個 I/O 類型的最大處理量時使用 100%讀取和寫入 100%案例。 實際處理量可能較低，取決於 I/O 混合和網路條件。
