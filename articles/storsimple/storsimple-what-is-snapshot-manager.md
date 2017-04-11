<properties 
   pageTitle="什麼是 StorSimple 快照管理員？ |Microsoft Azure"
   description="說明 StorSimple 快照管理員、 其架構，以及其功能。"
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

# <a name="what-is-storsimple-snapshot-manager"></a>什麼是 StorSimple 快照管理員？

## <a name="overview"></a>概觀

StorSimple 快照管理員是 Microsoft 管理主控台 (MMC) 嵌入式管理單元，可簡化資料保護與 Microsoft Azure StorSimple 環境中的備份管理。 使用 StorSimple 快照管理員中，您可以管理資料中心和雲端中的 Microsoft Azure StorSimple 資料做為單一整合式的儲存空間方案，因此簡化備份的程序，並降低成本。

此概觀介紹 StorSimple 快照管理員、 描述其功能，並說明其 Microsoft Azure StorSimple 中的角色。 

如需 for SharePoint，包括 StorSimple 裝置、 StorSimple 管理員服務、 StorSimple 快照管理員和 StorSimple 介面卡的整個 Microsoft Azure StorSimple 系統的概觀，請參閱[StorSimple 8000 系列︰ 混合式雲端儲存空間方案](storsimple-overview.md)。 
 
>[AZURE.NOTE] 
>
>- 您無法使用 StorSimple 快照管理員來管理 Microsoft Azure StorSimple 虛擬陣列 （也稱為 StorSimple 內部部署的虛擬裝置）。
>
>- 如果您打算 StorSimple 裝置上安裝 StorSimple 更新 2，請務必下載 StorSimple 快照管理員的最新版本，並將其安裝，**才能安裝 StorSimple 更新 2**。 為回溯相容 StorSimple 快照管理員的最新版本，以及搭配 Microsoft Azure StorSimple 所有發行版本。 如果您使用的舊版的 StorSimple 快照管理員，您必須更新 （您不需要解除安裝舊版本，才能安裝新版本）。

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>StorSimple 快照管理員用途和架構

StorSimple 快照管理員提供集中式管理主控台可供您建立一致的當地時間點備份與雲端資料。 例如，您可以使用主控台以︰

- 設定、 備份及刪除區。
- 設定大量群組，以確保備份資料是一致的應用程式。
- 管理備份原則，以便在預定的排程備份資料。
- 建立本機和雲端快照，可以儲存在雲端，並用於損毀修復。

StorSimple 快照管理員擷取註冊 VSS 提供者 」 主機上的應用程式清單。 然後，若要建立應用程式一致的備份，它會檢查是否有應用程式使用的區，並建議大量群組，以設定。 StorSimple 快照管理員會使用這些大量群組來產生應用程式一致的備份複本。 （應用程式的一致性存在所有相關的檔案，並會同步處理資料庫，而且代表特定點時間的應用程式，則為 true 的狀態）。 

StorSimple 快照管理員備份形式的累加快照，最後一個備份後擷取的變更。 如此一來，備份需要較少的儲存空間可以會建立並快速還原。 StorSimple 快照管理員會使用 Windows 大量陰影複製服務 (VSS)，以確保快照擷取應用程式一致的資料。 （如需詳細資訊，移至 Windows 區陰影複製服務] 區段的整合。）使用 StorSimple 快照管理員中，您可以建立備份排程或視需要立即引起其他的備份。 如果您需要從備份，StorSimple 快照管理員可讓您選取從目錄的本機或雲端快照集。 Azure StorSimple 還原的資料時所需視是，以免延遲資料可用性還原作業期間。）

![StorSimple 快照管理員架構](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**StorSimple 快照管理員架構** 

## <a name="support-for-multiple-volume-types"></a>多個大量類型的支援

您可以使用 [StorSimple 快照管理員來設定和備份下列類型的區︰ 

- **基本區**-基本的大量是基本磁碟上的單一資料分割。 

- **簡單的區**– 簡單的大量是動態區包含單一的動態磁碟上的磁碟空間。 簡單的大量是由磁碟上的一個區域或連結在一起相同的磁碟的多個區域所組成。 （您可以只在動態磁碟上建立簡單的區）。簡單的區不會容錯。

- **動態區**– 動態區是建立動態磁碟上的磁碟。 動態磁碟使用資料庫上的電腦中的動態磁碟追蹤區所包含的相關資訊。 

- **動態磁碟區鏡像**– 鏡像的動態磁碟區是內建在 raid-1 架構。 RAID 1，兩個或多個磁碟，產生的左右對稱的設定寫入相同的資料。 然後可由包含要求的資料的任何磁碟處理讀取的要求。

- **叢集共用區**– 叢集共用區 (CSVs)，使用多個節點容錯移轉叢集可以同時閱讀或撰寫相同的磁碟。 從一個節點容錯移轉到另一個節點就會發生快速，而不需要磁碟機擁有權或裝載、 卸載，及移除大量的變更。 

>[AZURE.IMPORTANT] 請勿混合 CSVs 和非 CSVs 中相同的快照。 不支援混合 CSVs 和非 CSVs 的快照。 
 
您可以使用 StorSimple 快照管理員還原整個大量群組或複製個別區並復原個別檔案。

- [區和大量群組](#volumes-and-volume-groups) 
- [備份類型和備份原則](#backup-types-and-backup-policies) 

如需有關 StorSimple 快照 Manager 功能和使用方式的詳細資訊，請參閱[StorSimple 快照管理員使用者介面](storsimple-use-snapshot-manager.md)。

## <a name="volumes-and-volume-groups"></a>區和大量群組

使用 StorSimple 快照管理員中，您建立區並將音量群組設定。 

StorSimple 快照管理員會使用大量群組，來建立應用程式一致的備份複本。 當所有相關檔案和資料庫會同步處理及代表應用程式的特定點時間，則為 true 狀態時，就會出現應用程式的一致性。 大量群組 （又稱為是*一致性群組*） 來備份或還原工作。

大量群組不是大量容器相同。 大量容器包含一或多個共用的雲端儲存空間帳戶及其他屬性，例如加密和頻寬使用量的區。 單一大量容器可以包含多達 256 個狗熊能夠的 StorSimple 區。 如需大量容器的詳細資訊，請移至[管理您的大量容器](storsimple-manage-volume-containers.md)。 大量群組是您設定以促進備份作業的區的集合。 如果您選取兩個區屬於不同的大量容器，將欄位放置在單一音量] 群組中，然後建立備份大量的原則，將會在適當的大量容器中，使用適當的儲存帳戶備份每個音量。

>[AZURE.NOTE] [音量] 群組中的所有磁碟區必須來自單一雲端服務提供者。

## <a name="integration-with-windows-volume-shadow-copy-service"></a>與 Windows 區陰影複製服務整合

StorSimple 快照管理員會使用 Windows 大量陰影複製服務 (VSS)，來擷取應用程式一致的資料。 以進行共同作業的累加快照建立 VSS 注意應用程式與通訊 VSS 幫助您應用程式的一致性。 VSS 可確保應用程式暫時停用或靜止時的快照。 

VSS StorSimple 快照管理員實作可用於 SQL Server 和一般 NTFS 磁碟區。 程序如下所示︰ 

1. 要求者，通常是資料管理及保護解決方案 （例如 StorSimple 快照管理員） 或備份的應用程式，叫用 VSS，並讓其進行從目標應用程式中的作者軟體收集資訊。

2. VSS 連絡人作者元件來擷取資料的描述。 作者會傳回的資料来備份的描述。 

3. VSS 訊號撰寫者準備備份的應用程式。 作者更新交易記錄等，藉由完成開啟的交易，準備備份資料，並通知 vss。

4. VSS 指示要暫時停止應用程式的資料存放區，並確定沒有資料會寫入音量時建立陰影複製作者。 此步驟可確保資料的一致性，且不超過 60 秒。

5. VSS 指示建立陰影複製的提供者。 提供者，可軟體或硬體為基礎，管理是正在執行，並視需要建立這些陰影複製的區。 提供者所建立的陰影複本，並完成時，會通知 VSS。

6. VSS 連絡人作者通知 I/O 可以繼續使用的應用程式並確認 I/O 已經成功暫停在陰影複製建立。 

7. 如果複製成功，VSS 要求傳回複本的位置。 

8. 如果資料寫入時所建立的陰影複製，備份會有不一致。 VSS 會刪除陰影複本，並通知要求者。 要求者可以自動重複執行備份的程序或通知系統管理員，再試一次在稍後。

請參閱下列圖例。

![VSS 程序](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Windows 區陰影複製服務程序** 

## <a name="backup-types-and-backup-policies"></a>備份類型和備份原則

使用 StorSimple 快照管理員中，您可以先備份資料，並將其儲存至本機和雲端中。 您可以使用 StorSimple 快照管理員備份資料，或者您可以使用備份的原則，以建立百備份自動排程。 備份原則也可讓您指定多少快照仍會保留。 

### <a name="backup-types"></a>備份的類型

若要建立下列類型的備份，您可以使用 StorSimple 快照管理員︰

- **本機快照**– 本機快照是儲存在 StorSimple 裝置的大量資料的時間點複本。 一般而言，您可以建立及還原快速此類型的備份。 您可以使用區域的快照，即本機備份複本。

- **雲端快照**– 雲端快照是時間點的複本會儲存在雲端的大量資料。 雲端快照等於不同、 離線儲存系統上所複製的快照。 雲端快照就非常有用損毀修復狀況。

### <a name="on-demand-and-scheduled-backups"></a>視需要與排程的備份

使用 StorSimple 快照管理員中，您可以進行一次性立即，建立備份，或者您可以使用備份的原則，排程週期性備份的作業。

備份原則是一組自動排程定期備份，您可以使用的規則。 備份原則可讓您定義的頻率和參數特定大量群組的快照。 您可以使用原則，來指定開始日期和到期日期、 時間、 頻率，以及資料保留要求，兩個本機與雲端快照集。 您將它定義後立即套用原則。 

設定或重新設定備份的原則，必要時，您可以使用 StorSimple 快照管理員。 

您設定您建立的每個備份原則的下列資訊︰

- **名稱**– 所選取的備份原則的唯一名稱。

- **輸入**– 備份原則的類型本機快照或雲端快照。

- **大量群組**– 指派給所選取的備份原則的 [音量] 群組。

- **保留**– 若要保留的備份複本的數目。 如果您核取 [**全部**] 方塊，所有的備份複本會保留直到達到每大量的備份複本的數目上限，而此時原則會失敗，並產生錯誤訊息。 或者，您可以指定備份 （1 到 64） 之間保留的數字。

- **日期**-建立備份原則時的日期。

設定備份原則的相關資訊，請移至[使用 StorSimple 快照管理員來建立和管理備份原則](storsimple-snapshot-manager-manage-backup-policies.md)。

### <a name="backup-job-monitoring-and-management"></a>備份工作監控和管理

您可以使用 [StorSimple 快照管理員監控和管理即將來臨、 排程與已完成的備份工作。 此外，StorSimple 快照管理員提供最多 64 完成備份的目錄。 若要尋找和還原區或個別檔案，您可以使用目錄]。 

監視備份工作的相關資訊，請移至[使用 StorSimple 快照管理員檢視及管理備份工作](storsimple-snapshot-manager-manage-backup-jobs.md)。


## <a name="next-steps"></a>後續步驟

- 進一步瞭解[使用 StorSimple 快照管理員來管理您的 StorSimple 解決方案](storsimple-snapshot-manager-admin.md)。

- 下載[StorSimple 快照管理員](https://www.microsoft.com/download/details.aspx?id=44220)。
