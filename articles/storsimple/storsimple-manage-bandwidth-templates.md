<properties
   pageTitle="管理您的 StorSimple 頻寬範本 |Microsoft Azure"
   description="說明如何管理 StorSimple 頻寬範本，讓您控制頻寬使用量。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-storsimple-bandwidth-templates"></a>使用 StorSimple 管理員服務來管理 StorSimple 頻寬範本

## <a name="overview"></a>概觀

頻寬範本可讓您設定的網路頻寬使用量跨多個時間每日排程層 StorSimple 裝置至雲端的資料。

使用頻寬節流排程，您可以︰

- 指定自訂的頻寬排程根據工作負載的網路使用方式。

- 集中管理並重複排程在多個裝置上使用容易及順暢的方式。

> [AZURE.NOTE] 此功能僅適用於 StorSimple 實體裝置並不會用於虛擬裝置。

您的服務的所有頻寬範本會顯示在表格的格式，並包含下列資訊︰

- **名稱**– 分派給頻寬範本所建立的唯一名稱。

- **排程**– 排程中指定的頻寬範本所包含的數目。

- **使用由**– 區使用頻寬範本的數目。

使用 [StorSimple 管理員服務**設定**] 頁面中 Azure 傳統入口網站來管理頻寬範本。

您也可以找到其他資訊，協助您設定中的頻寬範本︰

- 問與答頻寬範本
- 頻寬範本的最佳作法

## <a name="add-a-bandwidth-template"></a>新增頻寬範本

執行下列步驟，以建立新的頻寬範本。

#### <a name="to-add-a-bandwidth-template"></a>若要新增的頻寬範本

1. 在 [StorSimple 管理員服務**設定**] 頁面中，按一下 [**新增/編輯頻寬範本**]。

2. 在 [**新增/編輯頻寬範本**] 對話方塊中︰

   1. 從 [**範本**] 下拉式清單中，選取 [**建立新**若要新增新的頻寬範本。
   2. 指定頻寬範本的唯一名稱。

3. 定義**頻寬排程**。 若要建立排程︰

   1. 從下拉式清單中，選擇 [設定排程週的日子。 您可以選取多個天選取位於清單中的個別工作日之前核取方塊。
   2. 如果強制整個一天的排程，請選取 [**全天**] 選項。 選取此選項，您可以不再指定**開始時間**] 或 [**結束時間**。 12:00 AM 執行排程 11:59 PM。
   3. 從下拉式清單中，選取 [**開始時間**]。 這是時就會開始排程。
   4. 從下拉式清單中，選取 [**結束時間**。 這是時排程將會停止。

         > [AZURE.NOTE] 不允許重疊的排程。 如果重疊的排程將會產生的開始和結束時間，您會看到錯誤訊息。

   5. 指定**頻寬工資率**。 這是秒 (Mbps StorSimple 裝置涉及雲端 （上傳和下載） 作業中所使用) 的頻寬以百萬位元組。 提供 1 和 1000 這個欄位之間的數字。

   6. 按一下核取圖示![檢查圖示](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png)。 您所建立的範本會新增至 [服務**設定**] 頁面上的頻寬範本的清單。

    ![建立新的頻寬範本](./media/storsimple-manage-bandwidth-templates/HCS_CreateNewBT1.png)

4. 按一下 [底部的頁面的 [**儲存**]，然後按一下**[是]**出現確認提示時。 這會儲存您所做的變更設定。

## <a name="edit-a-bandwidth-template"></a>編輯頻寬範本

執行下列步驟，以編輯頻寬範本。

### <a name="to-edit-a-bandwidth-template"></a>若要編輯的頻寬範本

1. 按一下 [**新增/編輯頻寬範本**。

2. 在 [**新增/編輯頻寬範本**] 對話方塊中︰

   1. 從 [**範本**] 下拉式清單中，選擇您想要修改現有的頻寬範本。
   2. 完成您的變更。 （您可以修改任何現有的設定）。
   3. 按一下 [檢查] 圖示 ![檢查圖示](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). 在 [服務設定] 頁面上，您會看到頻寬範本的清單中修改的範本。

3. 若要儲存您的變更，按一下 [在頁面底部的 [**儲存**]。 按一下 [ **]**確認出現提示時。

> [AZURE.NOTE] 您不允許儲存變更，如果您要修改的頻寬範本中的現有排程與重疊的編輯的排程。

## <a name="delete-a-bandwidth-template"></a>刪除頻寬範本

執行下列步驟，以刪除頻寬範本。

#### <a name="to-delete-a-bandwidth-template"></a>若要刪除的頻寬範本

1. 在 [您的服務的頻寬範本的 [表格式] 清單中，選取您想要刪除的範本。 (**X**) 上的 [刪除] 圖示會出現非常右側的選取範本。 按一下 [刪除範本的 [ **x** ] 圖示。

2. 系統會提示您確認。 按一下**[確定]**以繼續]。

如果範本是使用任何區中，您將無法將其刪除。 您會看到錯誤訊息，指出正在使用的範本。 錯誤訊息] 對話方塊會顯示通知您應該會移除範本的所有參考。

您可以刪除範本的所有參考存取**大量容器**頁面及修改使用這個範本，讓他們使用另一個範本，或使用自訂或不受限制的頻寬設定大量容器。 當所有參照已經都移除時，您可以刪除範本。

## <a name="use-a-default-bandwidth-template"></a>使用預設頻寬範本

在預設頻寬範本提供與大量容器預設用來存取雲端時，強制執行頻寬控制項。 預設範本也做為好參照的使用者建立自己的範本。 此預設範本的詳細資料如下︰

- **名稱**– 不受限制的晚上週末

- **排程**– 單一排程從星期一至星期五套用 8 上午與下午 5 裝置時間之間 1 Mbps 頻寬工資率。 無限制設定頻寬為每週的其餘部分。

可以編輯預設範本。 追蹤此範本 （包括編輯的版本） 的使用方式。

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>建立全天頻寬範本開始在指定的時間

請遵循此程序，以建立排程開始在指定的時間，並執行全天。 在範例中，排程開始編號，9 AM 上午，並執行 9 am 隔天。 請務必注意的指定的排程的開始和結束時間必須同時包含相同的 24 小時制排程，並無法橫跨多個的天數。 如果您需要設定橫跨多個天的頻寬範本，您必須使用多個時間表 （如下所示）。

#### <a name="to-create-an-all-day-bandwidth-template"></a>若要建立全天的頻寬範本

1. 建立在上午 9 AM 會啟動並執行直到午夜的排程。

2. 新增另一個排程。 設定從午夜執行上午 9 am，第二個排程。

3. 儲存頻寬範本。

複合索引的排程然後，您所選擇的一次啟動與執行全天。

## <a name="questions-and-answers-about-bandwidth-templates"></a>問與答頻寬範本

**Q**. 會發生什麼情況頻寬控制項後之間的排程？ （已結束排程與另一個尚未啟動）。

**A**. 在這種情況下，將會採用沒有頻寬控制項。 這就表示裝置在 tiering 至雲端的資料時，可以使用不受限制的頻寬。

**Q**. 您可以修改離線的裝置上的頻寬範本嗎？

**A**. 您無法修改區容器的頻寬範本，如果對應的裝置已離線。

**Q**. 您可以編輯的相關聯的區離線時，大量容器與相關聯的頻寬範本嗎？

**A**. 您可以修改大量容器，使其在離線與相關聯的頻寬範本。 請注意，使離線時，沒有資料將會層從裝置至雲端。

**Q**. 您可以刪除預設範本？

**A**. 雖然您可以刪除預設範本，但不建議這麼做。 追蹤的預設範本，包括編輯的版本，使用方式。 追蹤資料分析和在過程中的時間，用來改善的預設範本。

**Q**. 如何判斷您的頻寬範本需要修改？

**A**. 您需要修改頻寬範本符號是當您啟動看到變得緩慢或淺壓深多次中某一天的網路。 如果這種情況，監控儲存及使用方式網路查看 I/O 效能和網路生產力的圖表。

從網路處理量資料，找出的時間和網路瓶頸會發生的大量容器。 如果發生這種情況時資料會被層至雲端 （從雲端裝置適用的所有大量容器的 I/O 效能取得這項資訊），然後您會需要修改您的大量容器相關聯的頻寬範本。

已修改的範本都在使用之後，您必須監視網路再次的重要的延遲時間。 如果這些仍然存在，您會需要重新瀏覽頻寬範本。

**Q**. 如果有多個裝置上的大量容器，會發生什麼情況排程的重疊但不同的限制會套用到各個？

**A**. 例如，假設您已有 3 大量容器的裝置。 重疊完全這些容器與相關聯的排程。 針對每個容器，使用的頻寬限制的 5、 10 和 15 Mbps 分別。 當同時在所有這些容器上發生 I/o 時，可能會套用 3 的頻寬限制的最小值︰ 在此情況下，為這些輸出 I/O 要求 5 Mbps 共用相同的佇列。

## <a name="best-practices-for-bandwidth-templates"></a>頻寬範本的最佳作法

請遵循這些最佳做法 StorSimple 裝置︰

- 若要啟用變數節流網路生產力的裝置在不同的時間，一天的裝置上設定頻寬範本。 這些頻寬範本搭配備份排程時有效地可以在離峰運用雲端運算的額外的網路頻寬。

- 計算實際的頻寬所需的特定的部署，根據部署和必要的復原時間目標 (RTO) 的大小。

## <a name="next-steps"></a>後續步驟

瞭解更多關於[使用 StorSimple 管理員服務來管理您的 StorSimple 裝置](storsimple-manager-service-administration.md)。