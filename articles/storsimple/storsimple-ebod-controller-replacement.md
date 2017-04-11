<properties 
   pageTitle="取代 StorSimple EBOD 控制器 |Microsoft Azure"
   description="說明如何移除並取代 StorSimple 從 8600 裝置上的一個或兩個 EBOD 控制站。"
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

# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>取代 StorSimple 裝置上的 EBOD 控制器

## <a name="overview"></a>概觀

本教學課程說明如何取代您的 Microsoft Azure StorSimple 裝置上的區域 EBOD 控制器模組。 若要取代 EBOD 控制器模組，您需要︰

- 移除有錯誤的 EBOD 控制器
- 安裝新的 EBOD 控制站

開始之前，請考慮下列資訊︰

- 空白的 EBOD 模組都必須插入所有未使用的位置。 如果位置保留為開啟，不會正確不錯的圍繞符號。

- EBOD 控制器是熱切換，可以移除或取代。 請勿移除失敗的模組，直到您有替代方案。 當您起始取代程序時，您必須完成其內 10 分鐘的時間。

>[AZURE.IMPORTANT] 請先移除或取代任何 StorSimple 元件，請確定您檢閱[安全圖示慣例](storsimple-safety.md#safety-icon-conventions)，以及其他[安全預防措施](storsimple-safety.md)。

## <a name="remove-an-ebod-controller"></a>移除 EBOD 控制器

取代前失敗的 EBOD 控制器模組，在您 StorSimple 的裝置，請確定其他 EBOD 控制器模組 active 和執行。 下列程序和資料表會說明如何移除 EBOD 控制器模組。

#### <a name="to-remove-an-ebod-module"></a>若要移除 EBOD 模組

1. 開啟 [Azure 傳統入口網站。

2. 瀏覽至**裝置** > **進行的維修作業** > **硬體狀態**，並確認作用中的 EBOD 控制器模組 LED 的狀態是 [綠色] 和 [失敗 EBOD 控制器模組 LED 為紅色。

3. 找出失敗的 EBOD 控制器模組後面的裝置。

4. 移除採取 EBOD 模組，系統不前，先將 EBOD 控制器模組連接至控制器纜線。

5. 記下已連接至控制器 EBOD 控制器模組的確切 SA 連接埠。 您將必須取代 EBOD 模組之後，還原這項設定系統。 

    >[AZURE.NOTE] 一般而言，這就是在下圖會標示為**主機**的連接埠 A。

    ![後擋板的 EBOD 控制器](./media/storsimple-ebod-controller-replacement/IC741049.png)

     **圖 1**背面 EBOD 模組

  	|標籤|描述|
  	|:----|:----------|
  	|1|故障 LED|
  	|2|Power LED|
  	|3|SA 連接器|
  	|4|SA Led|
  	|5|僅限工廠使用循序連接埠|
  	|6|連接埠 （主機）|
  	|7|連接埠 B （出主機）|
  	|8|連接埠 C （工廠使用）|

## <a name="install-a-new-ebod-controller"></a>安裝新的 EBOD 控制站

下列程序和資料表會說明如何安裝 EBOD 控制器模組中 StorSimple 裝置。

#### <a name="to-install-an-ebod-controller"></a>安裝 EBOD 控制站

1. 核取損毀，尤其是介面連接器 EBOD 的裝置。 如果彎曲 pin 無法安裝新的 EBOD 控制器。

2. 在開啟的位置閂鎖，投影片的模組到圍繞符號鍵，直到閂鎖加入。

    ![安裝 EBOD 控制站](./media/storsimple-ebod-controller-replacement/IC741050.png)

    **圖 2** 安裝 EBOD 控制器模組

3. 關閉鎖定。 鎖定出現時，您應該聽到按一下。

    ![放開 EBOD 鎖定](./media/storsimple-ebod-controller-replacement/IC741047.png)

    **圖 3** 關閉 EBOD 模組鎖定

4. 重新連線纜線。 使用完全設定之前取代項目的出現。 請參閱下列的圖表和表格，如需詳細資訊，了解如何連線纜線。

    ![纜線 4U 裝置的電源](./media/storsimple-ebod-controller-replacement/IC770723.png)

    **圖 4**。 重新連線纜線

  	|標籤|描述|
  	|:----|:----------|
  	|1|主要的圍繞符號|
  	|2|PCM 0|
  	|3|PCM 1|
  	|4|控制站 0|
  	|5|控制站 1|
  	|6|EBOD 控制器 0|
  	|7|EBOD 控制站 1|
  	|8|EBOD 圍繞符號|
  	|9|Power 分配單位|

## <a name="next-steps"></a>後續步驟

進一步瞭解[StorSimple 硬體元件取代](storsimple-hardware-component-replacement.md)。
