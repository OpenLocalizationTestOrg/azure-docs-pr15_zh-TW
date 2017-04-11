<properties 
   pageTitle="檢視及管理 StorSimple 虛擬陣列工作 |Microsoft Azure"
   description="描述 [StorSimple 管理員服務的工作] 頁面，以及如何使用它來追蹤 StorSimple 虛擬陣列的最近和目前的工作。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>若要檢視工作 StorSimple 虛擬陣列使用 StorSimple 管理員服務

## <a name="overview"></a>概觀

[**工作**] 頁面會提供單一的中央入口網站檢視及管理會啟動連線到您的 StorSimple 管理員服務的虛擬陣列 （也稱為內部部署虛擬裝置） 上的工作。 您可以檢視多個虛擬裝置執行、 完成和失敗的工作。 結果會顯示在表格的格式。 

![工作] 頁面](./media/storsimple-ova-manage-jobs/ovajobs1.png)

您也能快速找到您有興趣，例如篩選的欄位的工作︰

- **狀態**-您可以搜尋所有，執行、 完成或失敗的工作。
- **的**– 您可以根據日期和時間範圍篩選的工作。
- **輸入**– 的工作類型可以所有備份，還原容錯移轉、 下載更新，或安裝更新。
- **裝置**– 工作發起的租用戶上特定裝置連線到您的服務。 已篩選的工作是即然後形成表格根據下列屬性︰

    - **輸入**– 的工作類型可以所有備份，還原容錯移轉、 下載更新，或安裝更新。

    - **狀態**-工作可以為所有執行中，完成，或失敗。

    - **實體**– 工作可與大量、 共用] 或裝置相關聯。 

    - **裝置**– 裝置的名稱上啟動工作。

    - **開始**-開始工作時的時間。

    - **進度**– 執行中的工作完成百分比。 已完成的工作，應該一律是 100%。

工作清單會重新整理每隔 30 秒。

## <a name="view-job-details"></a>檢視工作詳細資料

執行下列步驟，以檢視的任何工作詳細資料。

#### <a name="to-view-job-details"></a>若要檢視工作詳細資料

1. 在 [**工作**] 頁面上顯示的作業都是您有興趣的適當的篩選執行查詢。 您可以搜尋已完成或執行工作。

2. 從表格式的工作清單中選取工作。

3. 在頁面底部，按一下 [**詳細資料**]。

4. 在 [**詳細資料**] 對話方塊中，您可以檢視狀態的詳細資訊，與時間統計資料。 下圖顯示 [**備份工作詳細資料**] 對話方塊的範例。
 
    ![工作詳細資料] 頁面](./media/storsimple-ova-manage-jobs/ovajobs2.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>虛擬機器中 hypervisor 暫停時的工作失敗

在工作時進行 StorSimple 虛擬陣列和 （虛擬機器中 hypervisor 佈建後） 的裝置上已暫停超過 15 分鐘，工作將會失敗。 這因為 StorSimple 虛擬陣列時間之 Microsoft Azure 時間與同步處理。 還原工作失敗的範例會顯示在以下的螢幕擷取畫面。

![還原工作失敗。](./media/storsimple-ova-manage-jobs/restorejobfailure.png)

備份與還原、 更新、 容錯移轉工作會套用這些失敗。 如果您的虛擬機器在 HYPER-V 佈建後，電腦會最後與同步處理的時間您 hypervisor。 在發生這種情況，您可以重新啟動您的工作。 

## <a name="next-steps"></a>後續步驟

[瞭解如何使用本機網站來管理您的 StorSimple 虛擬陣列的使用者介面](storsimple-ova-web-ui-admin.md)。
