<properties 
   pageTitle="取代 StorSimple 裝置上的 PCM |Microsoft Azure"
   description="說明如何移除，並以 [StorSimple 裝置上的 [Power 及製冷模組 (PCM)"
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
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>取代和製冷模組 StorSimple 裝置上

## <a name="overview"></a>概觀

Power 及製冷模組 (PCM) 在您的 Microsoft Azure StorSimple 裝置中包含電源及製冷透過主要及 EBOD 隨函附件控制的風扇。 有一個模型 PCM 經認證的每個圍繞符號。 主要的圍繞符號經認證的 764 的 W PCM 和 EBOD 圍繞符號經認證的 580 的 W PCM。 雖然主要的圍繞符號和 EBOD 圍繞符號 PCMs 不同，是相同的替代程序。

本教學課程說明如何︰

- 移除 PCM
- 安裝取代 PCM

>[AZURE.IMPORTANT] 移除，取代 PCM 之前檢閱[StorSimple 硬體元件替代](storsimple-hardware-component-replacement.md)的安全性資訊。

## <a name="before-you-replace-a-pcm"></a>取代 PCM 之前，先

取代您 PCM 之前，先，則請注意下列重要問題︰

- 如果 PCM 的電源失敗，請離開錯誤的模組安裝，但移除電源線。 風扇會繼續收到 power 圍繞符號並繼續提供適當製冷。 如果扇失敗，請 PCM 需要立即取代。

- 移除之前 PCM，電源中斷 PCM，關閉主切換 （如果有的話），或實際移除電源線。 這會提供您的系統警告 power 關機即將。

- 請確定其他 PCM 取代錯誤 PCM 前繼續的系統作業的功能。 必須由完全操作的 PCM 越快越取代錯誤的 PCM。

- PCM 模組取代只需要幾分鐘即可完成，但必須先完成的移除以防止都會失敗的 PCM 10 分鐘內。

- 請注意，從工廠出貨取代 764 W PCM 模組不包含電池模組。 您必須要移除您有瑕疵 PCM 電池，然後將它插入之前執行取代項目的 [取代] 模組。 如需詳細資訊，請參閱如何[移除並插入備用模組](storsimple-battery-replacement.md)。


## <a name="remove-a-pcm"></a>移除 PCM

當您準備好要從 Microsoft Azure StorSimple 裝置上移除和製冷模組 (PCM) 時，請依照這些指示。

>[AZURE.NOTE] 移除您 PCM 之前，請確認您已正確取代 (764 主要的圍繞符號 W) 或 580 W 的 EBOD 圍繞符號。

#### <a name="to-remove-a-pcm"></a>若要移除 PCM

1. 在 [Azure 傳統的入口網站中，按一下 [**裝置** > **進行的維修作業** > **硬體狀態**。 請在 [**共用的元件**來識別 PCM 具有失敗 PCM 元件的狀態︰

     - 如果失敗 PCM 0 中的電源，**電 PCM 0 中**的狀態會變成紅色。

     - 如果已失敗 PCM 1 的電源，**電 PCM 1 中**的狀態會變成紅色。

     - 如果在 PCM 1 風扇失敗，**製冷 PCM 0 為 0**或**製冷 1 PCM 0**的狀態會變成紅色。

2. 找出失敗的 PCM 背面的主要的圍繞符號。 如果您執行的從 8600 模型，請查看顯示面板上顯示系統單位識別碼來識別主要的圍繞符號。 預設單位識別碼顯示在主要的圍繞符號會是**00**，而單位識別碼 EBOD 圍繞符號所顯示的預設值為**01**。 下列圖表和表格說明 [顯示的面板。

    ![選項面板上的 [系統識別碼](./media/storsimple-power-cooling-module-replacement/IC740991.png)

     **圖 1**裝置的前端面板  

  	|標籤|描述|
  	|:---|:-----------|
  	|1|[靜音] 按鈕|
  	|2|系統電源|
  	|3|模組錯誤|
  	|4|邏輯錯誤|
  	|5|單位識別碼顯示|

3. 監控指標 Led 背面主要的圍繞符號也可以用於識別有瑕疵 PCM。 請參閱下的 [圖表] 和 [若要瞭解如何使用 Led 找出錯誤 PCM 的資料表。 例如，如果亮對應**風扇失敗**的 LED，風扇失敗。 同樣地，如果要亮對應至**AC 會失敗**，LED，電源失敗。 

    ![後擋板的裝置 PCM 監控指標 Led](./media/storsimple-power-cooling-module-replacement/IC740992.png)

     **圖 2**有指標 Led PCM 背面

  	|標籤|描述|
  	|:---|:-----------|
  	|1|AC 電源中斷|
  	|2|風扇失敗|
  	|3|電池錯誤|
  	|4|PCM [確定]|
  	|5|DC 電源中斷|
  	|6|狀況良好電池|

4. 請參閱以下的上一步]，找出失敗的 PCM 模組 StorSimple 裝置的圖表。 PCM 0 的左側，而 PCM 1 會在右側。 下表說明模組。

     ![後擋板的裝置主要的圍繞符號模組](./media/storsimple-power-cooling-module-replacement/IC740994.png)

     **圖 3**背面裝置以搭配外掛程式的模組 

  	|標籤|描述|
  	|:---|:-----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|控制站 0|
  	|4|控制站 1|

5. 關閉錯誤 PCM 和中斷連線供應電源線。 您現在可以移除 PCM。

6. 鎖定和旁的縮圖和食指，之間的 PCM 控點並擠以開啟控點。

    ![開啟 PCM 控點](./media/storsimple-power-cooling-module-replacement/IC740995.png)

    **圖 4**開啟 PCM 控點

7. 不要緊控點，並移除 PCM。

    ![移除裝置 PCM](./media/storsimple-power-cooling-module-replacement/IC740996.png)

    **圖 5**移除 PCM

## <a name="install-a-replacement-pcm"></a>安裝取代 PCM

請遵循這些指示來安裝 PCM StorSimple 裝置。 請確定您已插入備用模組之前安裝取代 PCM （適用於只 W PCMs 764）。 如需詳細資訊，請參閱如何[移除並插入備用模組](storsimple-battery-replacement.md)。

#### <a name="to-install-a-pcm"></a>若要安裝 PCM

1. 確認您有正確取代 PCM 此圍繞符號。 主要的圍繞符號 764 的 W PCM，並且 EBOD 圍繞符號需要 580 的 W PCM。 您應該嘗試使用 580 W PCM 在主要的圍繞符號] 或 764 W PCM 中 EBOD 圍繞符號。 下圖顯示識別為套 PCM 上的標籤上的以下資訊的位置。

    ![裝置 PCM 標籤](./media/storsimple-power-cooling-module-replacement/IC740973.png)

    **圖 6**PCM 標籤

2. 核取要圍繞符號] 特定注意連接線損毀。 
                                        
    >[AZURE.NOTE] **如果彎曲連接器 pin，請不要安裝模組。**

3. 在開啟的位置的 PCM 控點，投影片到圍繞符號的模組。

    ![安裝裝置 PCM](./media/storsimple-power-cooling-module-replacement/IC740975.png)

    **圖 7**安裝 PCM

4. 手動關閉 PCM 控點。 您應該會為控點鎖定會聽到按一下。 
                                        
    >[AZURE.NOTE] 若要確保連接器 pin 有參與，您可以輕輕 tug 的控點按住鎖定。 如果 PCM 投影片] 時，就表示已關閉鎖定之前連接線參與。

5. 連接 power 纜線，power 來源和 PCM。

6. 浮雕 bales 安全疲勞。 

7. 開啟 [PCM。

8. 確認取代已順利完成︰ 在您 StorSimple 管理員服務 Azure 傳統入口網站，瀏覽至**裝置** > **進行的維修作業** > **硬體狀態**。 在 [**共用的元件**，PCM 的狀態] 應為綠色。 
                                        
    >[AZURE.NOTE] 可能需要幾分鐘的時間，取代 PCM 完全初始化。

## <a name="next-steps"></a>後續步驟

進一步瞭解[StorSimple 硬體元件取代](storsimple-hardware-component-replacement.md)。
