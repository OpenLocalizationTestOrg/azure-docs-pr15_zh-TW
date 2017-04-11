<properties 
   pageTitle="取代 StorSimple 裝置上的電池 |Microsoft Azure"
   description="說明如何移除、 取代和維護 StorSimple 裝置上的備份電池模組。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>取代 StorSimple 裝置上的備份電池模組

## <a name="overview"></a>概觀

主要圍繞符號 Power 及製冷模組 (PCM) 在您的 Microsoft Azure StorSimple 裝置上有其他電池套件。 此套件提供 power，讓 StorSimple 裝置影響電源至主要的圍繞符號時，可以儲存資料。 此電池套件稱為*備用模組*。 備用模組存在僅適用於 StorSimple 裝置 （EBOD 圍繞符號不包含備用模組） 中主要的圍繞符號。 

本教學課程說明如何︰

- 移除備用模組 
- 安裝新的備用模組
- 維護備用模組

>[AZURE.IMPORTANT] 移除取代備用模組之前, 檢閱[StorSimple 硬體元件取代簡介](storsimple-hardware-component-replacement.md)中的安全性資訊。

## <a name="remove-the-backup-battery-module"></a>移除備用模組

備用模組 StorSimple 裝置是欄位取代單位。 安裝於 PCM 之前，應該在其原始包裝儲存電池模組。 執行下列步驟，以移除備用。

#### <a name="to-remove-the-backup-battery-module"></a>若要移除的備份電池模組

1. 在 Azure 傳統入口網站中移至 [**裝置** > **進行的維修作業** > **硬體狀態**。 在 [**共用的元件**，看看電池的狀態。

2. 找出 PCM 電池失敗。 圖 1 顯示的 StorSimple 裝置。

    ![後擋板的裝置主要的圍繞符號模組](./media/storsimple-battery-replacement/IC740994.png)

    **圖 1**返回的主要裝置顯示 PCM 和控制站的模組

  	|標籤|描述|
  	|:----|:----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|控制站 0|
  	|4|控制站 1|

    如下圖所示的圖 2] 中的數字 3，則應該亮監控指標上 PCM 0 對應的 LED**電池**發生錯誤。

    ![後擋板的裝置 PCM 監控指標 Led](./media/storsimple-battery-replacement/IC740992.png)

    **圖 2**顯示監控指標 Led PCM 背面

  	|標籤|描述|
  	|:---|:-----------|
  	|1|AC 電源中斷|
  	|2|風扇失敗|
  	|3|電池錯誤|
  	|4|PCM [確定]|
  	|5|DC 電源中斷|
  	|6|狀況良好電池|

3. 若要移除的失敗電池 PCM，請遵循中[移除 PCM](storsimple-power-cooling-module-replacement.md#remove-a-pcm)的步驟進行。

4. 與移除 PCM，請拿起如同在下圖中，向上旋轉電池模組控點，並將其傳送至移除電池。

    ![電池移除 PCM](./media/storsimple-battery-replacement/IC741019.png)

    **圖 3**電池移除 PCM

5. 將模組包裝的單位] 欄位可取代。

6. 適當的服務和處理返回 Microsoft 損壞的單位。

## <a name="install-a-new-backup-battery-module"></a>安裝新的備用模組

執行下列步驟，以安裝中 StorSimple 裝置的主索引的圍繞符號 PCM 取代電池模組。

#### <a name="to-install-the-battery-module"></a>若要安裝電池模組

1. 將備用模組中 PCM 適當的方向。

2. 按下電池模組控點向座位連接器。

3. 在 [[取代和製冷 StorSimple 裝置上的模組](storsimple-power-cooling-module-replacement.md)的指導方針取代在主要的圍繞符號 PCM。

4. 取代項目的完成後，請移至 [**裝置** > **進行的維修作業** > Azure 傳統入口網站中的**硬體狀態**。 驗證，請確定已成功安裝電池的狀態。 綠色的狀態會指出電池狀況良好。

## <a name="maintain-the-backup-battery-module"></a>維護電池模組

在您 StorSimple 的裝置，備用模組會提供控制站 power power 遺失事件期間。 讓 StorSimple 裝置儲存之前關閉控制方式的重要資料。 使用 PCMs 中的兩個充滿電池，系統可以處理兩個連續遺失事件。

在 Azure 傳統入口網站中**進行的維修作業**] 頁面上的**硬體狀態**會指出電池發生故障或接近生活結束。 以**電池 PCM 0**或**電池 PCM 1 中的****共用的元件**] 下，表示電池狀態。 此頁面將顯示**降級**狀態結束的生活即將，並且**失敗**的生命週期結束。 

>[AZURE.NOTE] 只需要付費時，電池可以報告**失敗**。
 
如果**降級**狀態會變更，建議下列採取的動作︰

- 系統可能會遇到最近的電源中斷或電池進行定期進行的維修作業]。 您會看到系統 12 小時，再繼續執行。

    - 如果狀態是連續連線與控制站及執行 PCMs 電源 12 小時後仍**降級**，電池必須被取代。 請[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)取代電池模組。

    - 如果狀態會變成 [確定] 之後 12 小時，電池運作正常，並且只需要進行的維修作業收費將它加入。

- 如果尚未電源中相關聯的影響，PCM 已開啟，並連線到電源電池必須被取代。 [連絡 Microsoft 支援人員](storsimple-contact-microsoft-support.md)以順序排列取代備用模組。

>[AZURE.IMPORTANT] 美國國家和地區法規失敗電池的配置。 

## <a name="next-steps"></a>後續步驟

進一步瞭解[StorSimple 硬體元件取代](storsimple-hardware-component-replacement.md)。
