<properties 
   pageTitle="檢視及管理 StorSimple 工作 |Microsoft Azure"
   description="描述 [StorSimple 管理員服務的工作] 頁面，以及如何使用它來追蹤最近與目前排程的備份工作。"
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
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs-update-2"></a>使用 StorSimple 管理員服務來檢視及管理 StorSimple 工作 (更新 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>概觀

[**工作**] 頁面會提供單一的中央入口網站檢視及管理工作已在裝置上啟動的連線到您 StorSimple 管理員服務。 您可以檢視多個裝置的排程、 執行、 完成、 取消，及失敗工作。 結果會顯示在表格的格式。 

![工作] 頁面](./media/storsimple-manage-jobs-u2/jobs.png)

您也能快速找到您有興趣，例如篩選的欄位的工作︰

- **狀態**-工作可執行，完成、 取消、 失敗、 取消，或已完成但發生錯誤。
- **的**– 您可以根據日期和時間範圍篩選的工作。
- **輸入**– 的工作類型可備份，手動備份、 還原、 複製、 裝置容錯移轉時，建立本機固定的大量、 修改大量、 更新、 套件或虛擬裝置提供支援。

- **裝置**– 工作連線到您的服務特定裝置上啟動。
已篩選的工作是即然後形成表格根據下列屬性︰

    - **輸入**– 備份，手動備份、 還原、 複製、 裝置容錯移轉時，建立本機固定的大量、 修改大量、 更新、 套件或虛擬裝置提供支援。

    - **狀態**-執行，完成、 取消、 失敗、 取消，或已完成但發生錯誤。

    - **實體**– 工作可大量、 備份的原則或裝置與相關聯。 例如，複製工作是音量]，與相關聯，而排定的備份工作是備份原則相關聯。 建立裝置工作當做損毀修復 (DR)] 或 [還原作業。

    - **裝置**– 裝置的名稱上啟動工作。

    - **開始**-開始工作時的時間。

    - **進度**– 執行中的工作完成百分比。 已完成的工作，應該一律是 100%。

工作清單會重新整理每隔 30 秒。

您可以在此頁面上，執行下列工作相關動作︰

- 檢視工作詳細資料

- 取消工作

## <a name="view-job-details"></a>檢視工作詳細資料

執行下列步驟，以檢視的任何工作詳細資料。

#### <a name="to-view-job-details"></a>若要檢視工作詳細資料

1. 在 [**工作**] 頁面上顯示的作業都是您有興趣的適當的篩選執行查詢。 您可以搜尋，請執行，或取消作業。

2. 選取 [工作]。

3. 在頁面底部，按一下 [**詳細資料**]。

4. 在 [**備份工作詳細資料**] 對話方塊中，您可以檢視狀態、 詳細資料、 時間統計資料，以及資料的統計資料。
 
    ![工作詳細資料] 頁面](./media/storsimple-manage-jobs-u2/JobDetails.png)

## <a name="cancel-a-job"></a>取消工作

執行下列步驟，以取消正在執行的工作。

>[AZURE.NOTE] 某些工作，例如修改變更大量類型音量或展開 [音量]，無法取消。

### <a name="to-cancel-a-job"></a>若要取消工作

1. 在 [**工作**] 頁面會顯示您想要執行查詢，使用適當的篩選，取消執行作業。

1. 選取工作]。

1. 在頁面底部，按一下 [**取消**]。

1. 當畫面提示您確認，按一下 [**是**]。

現在就會取消此工作。

## <a name="next-steps"></a>後續步驟

- 瞭解如何[管理您的 StorSimple 備份原則](storsimple-manage-backup-policies.md)。

- 瞭解如何[使用 StorSimple 管理員服務，來管理您的 StorSimple 裝置](storsimple-manager-service-administration.md)。
