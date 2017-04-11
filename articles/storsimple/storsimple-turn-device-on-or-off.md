<properties 
   pageTitle="開啟或關閉 [StorSimple 裝置 |Microsoft Azure"
   description="說明如何新 StorSimple 裝置上開啟、 關閉或遺失 power，在裝置上開啟和關閉執行的裝置。"
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
   ms.date="08/23/2016"
   ms.author="alkohli" />

# <a name="turn-your-storsimple-device-on-or-off"></a>開啟或關閉 [StorSimple 裝置 

## <a name="overview"></a>概觀

關閉 Microsoft Azure StorSimple 裝置不需要做為標準系統作業的一部分。 不過，您可能需要開啟新的裝置或已關閉的裝置。 一般而言，在可讓您必須取代失敗的硬體、 實際移動單位，或請登出服務裝置的情況下需要關閉。 本教學課程說明開啟和關閉 StorSimple 裝置在不同的案例中的必要程序。

下表列出各種情況開啟和關閉 StorSimple 裝置，並提供適當的程序的連結。

|案例|參考主題|
|:-------|:---------------|
|開啟新的裝置|[開啟新的裝置](#turn-on-a-new-device)<ul><li>[新裝置以搭配只有主要圍繞符號](#new-device-with-primary-enclosure-only)</li><li>[新裝置以搭配 EBOD 圍繞符號](#new-device-with-ebod-enclosure)</li></ul>|
|關閉後，開啟 [裝置|[關閉後，開啟 [裝置](#turn-on-a-device-after-shutdown)<ul><li>[只有主要圍繞符號裝置](#device-with-primary-enclosure-only)</li><li>[裝置以搭配 EBOD 圍繞符號](#device-with-ebod-enclosure)</li></ul>|
|開啟電源中斷之後的裝置|[開啟電源中斷之後的裝置](#turn-on-a-device-after-a-power-loss)<ul><li>[只有主要圍繞符號裝置](#8100)</li><li>[裝置以搭配 EBOD 圍繞符號](#8600)</li></ul>|
|主要的圍繞符號之後的裝置上開啟並 EBOD 連線已中斷|[在裝置上開啟後主要及 EBOD 圍繞符號連線已中斷](#turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost)|
|關閉執行的裝置|[關閉執行裝置](#turn-off-a-running-device)<ul><li>[只有主要圍繞符號裝置](#8100a)</li><li>[裝置以搭配 EBOD 圍繞符號](#8600a)</li></ul>|

## <a name="turn-on-a-new-device"></a>開啟新的裝置

第一次開啟 StorSimple 裝置的步驟取決於裝置是否 8100 或從 8600 模型。 8100 具有單一的主要圍繞符號]，而從 8600 是雙圍繞符號裝置與主要的圍繞符號的 EBOD 圍繞符號。 下列各節中包含這兩個模型的詳細的步驟。

- [新裝置以搭配只有主要圍繞符號](#new-device-with-primary-enclosure-only)

- [新裝置以搭配 EBOD 圍繞符號](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>新裝置以搭配只有主要圍繞符號

StorSimple 8100 模型是以單一圍繞符號裝置。 您的裝置包含重複和製冷模組 (PCMs)。 這兩個 PCMs 必須安裝並連線到不同的電源來源，以確保高可用性。

執行下列步驟，以纜線 power 您的裝置。

[AZURE.INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

>[AZURE.NOTE]完成的裝置設定和纜線指示，請移至[您 StorSimple 8100 裝置上安裝](storsimple-8100-hardware-installation.md)。 請確定您完全遵循指示進行。

### <a name="new-device-with-ebod-enclosure"></a>新裝置以搭配 EBOD 圍繞符號

StorSimple 從 8600 模型有主要的圍繞符號和 EBOD 圍繞符號。 需要會放在一起的循序附加 SCSI (SA) 連線與 power 妥的單位。

時設定第一次此裝置，請執行步驟的第一次 SA 纜線，然後完成 [power 纜線的步驟。

[AZURE.INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

>[AZURE.NOTE]完成的裝置設定和纜線指示，請移至[您 StorSimple 從 8600 裝置上安裝](storsimple-8600-hardware-installation.md)。 請確定您完全遵循指示進行。

## <a name="turn-on-a-device-after-shutdown"></a>關閉後，開啟 [裝置

具有已關閉後，開啟 StorSimple 裝置上的步驟會有所不同裝置是否 8100 或從 8600 模型項目。 8100 具有單一的主要圍繞符號]，而從 8600 是雙圍繞符號裝置與主要的圍繞符號的 EBOD 圍繞符號。

- [只有主要圍繞符號裝置](#device-with-primary-enclosure-only)

- [裝置以搭配 EBOD 圍繞符號](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>只有主要圍繞符號裝置

關閉之後, 使用下列程序來開啟主要的圍繞符號與沒有 EBOD 圍繞符號 StorSimple 裝置上。

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>若要開啟的主要的圍繞符號只在裝置上

1. 請確認電源切換這兩個電源，且製冷模組 (PCMs) 位於 [關閉] 位置。 如果選項不是在 [關閉] 位置，然後翻轉至 [關閉] 位置，並等待燈到。

2. 開啟裝置上兩個 PCMs 至 [開啟] 位置翻轉 power 選項。 裝置應該開啟。

3. 檢查以下項目以驗證您的裝置上完全是︰

    1. 在這兩個 PCM 模組確定 Led 為綠色。

    2. 兩個控制站的狀態 Led 是實心綠色。

    3. 在其中一個控制站，藍色 LED 閃爍，表示控制器是作用中。

    如果無法滿足以下任一情況，則無法健全您的裝置。 請[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)。

### <a name="device-with-ebod-enclosure"></a>裝置以搭配 EBOD 圍繞符號

關閉之後, 使用下列程序來開啟主要的圍繞符號與 EBOD 圍繞符號 StorSimple 裝置上。 完全所述的順序執行每個步驟。 執行此作業可能會造成資料遺失。

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>若要開啟的裝置主要及 EBOD 圍繞符號

1. 請確定 EBOD 圍繞符號已連線至主要的圍繞符號。 如需詳細資訊，請參閱[您 StorSimple 從 8600 裝置上安裝](storsimple-8600-hardware-installation.md)。

2. 請確認和製冷模組 (PCMs) EBOD 和主要的隨函附件上的 [關閉] 位置。 如果選項不是在 [關閉] 位置，然後翻轉至 [關閉] 位置，並等待燈到。

3. 開啟 EBOD 圍繞符號第一個翻轉 power 切換至 [開啟] 位置的兩個 PCMs 上。 PCM Led 應為綠色。 此單位綠色 EBOD 控制器 LED 指出 EBOD 圍繞符號上。

4. 翻轉 power 切換至 [開啟] 位置的兩個 PCMs 上開啟主要的圍繞符號。 整個系統現在應該上。

5. 請確認 SA Led 綠色，以確保 EBOD 圍繞符號與主要的圍繞符號之間的連線已就緒。

## <a name="turn-on-a-device-after-a-power-loss"></a>開啟電源中斷之後的裝置

電源中斷，可以關閉 StorSimple 裝置。 在 power 提供的其中一項或兩個電源供應上就會發生電源中斷。 修復步驟會有所不同裝置是否 8100 或從 8600 的模型。 8100 具有單一的主要圍繞符號]，而從 8600 是雙圍繞符號裝置與主要的圍繞符號的 EBOD 圍繞符號。 本節描述每個案例的修復程序。

- [只有主要圍繞符號裝置](#8100)

- [裝置以搭配 EBOD 圍繞符號](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>只有主要圍繞符號裝置<a name="8100">

如果其中一個其電源供應 power 遺失，系統可以繼續的正常作業。 不過，若要確保可用性的裝置，還原 power 電源越快越。

如果沒有電源中斷或在兩個電源供應電源中斷，系統會關閉整齊有序及控制的方式。 還原 power 時，系統會自動開啟。

### <a name="device-with-ebod-enclosure-a-name8600"></a>裝置以搭配 EBOD 圍繞符號<a name="8600">

#### <a name="power-loss-on-one-power-supply"></a>提供一個 power 電源中斷

如果其中一個主要的圍繞符號或 EBOD 圍繞符號上其電源供應 power 遺失，系統可以繼續的正常作業。 不過，若要確保可用性的裝置，請還原 power 電源越快越。

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>在主要及 EBOD 隨函附件兩個電源供應電源中斷

如果沒有電源中斷或在兩個電源供應電源中斷，EBOD 圍繞符號會立即關閉和主要的圍繞符號會關閉整齊有序及控制的方式。 還原 power 時，應用裝置會自動啟動。

如果手動切換 power，然後執行下列步驟系統還原 power。

1. 開啟 [EBOD 圍繞符號]。

2. 在 EBOD 圍繞符號後，開啟主要的圍繞符號。

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>在 EBOD 圍繞符號上兩個電源供應電源中斷

當您設定您的纜線時，您必須確定的 EBOD 從未連線單獨到不同的 PDU。 如果 EBOD 和主要的圍繞符號無法同時，系統會將復原。

如果只在這兩個電源供應上失敗，EBOD 圍繞符號，系統將不會自動回復。 採取下列步驟，以開啟 [系統]，並將它還原為健全狀態︰

1. 如果主要的圍繞符號已開啟，關閉 Power 和製冷模組 (PCMs)。

2. 等待幾分鐘讓系統關閉。

3. 開啟 [EBOD 圍繞符號]。

4. 在 EBOD 圍繞符號後，開啟主要的圍繞符號。

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>在裝置上開啟後主要及 EBOD 圍繞符號連線已中斷

如果連線是遺失備用控制器與相對應的 EBOD 控制器之間，裝置仍然有效。 如果系統作用中控制器與相對應的 EBOD 控制器之間的連線會遺失，會發生錯誤後移轉，裝置應該繼續運作正常。

當兩個序列附加 SCSI (SA) 纜線，將會移除或 EBOD 圍繞符號與主要的圍繞符號之間的連線會嚴重損毀時，裝置便會停止運作。 此時，請執行下列步驟。

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>若要在裝置上開啟連線之後會遺失

1. Access 的裝置。

2. 如果中斷 SA 纜線之間的連線 EBOD 圍繞符號與主要的圍繞符號，EBOD 圍繞符號上所有的 SA 通道 Led 會關閉。

3. 關閉 Power 和製冷模組 (PCMs)，EBOD 圍繞符號及主要的圍繞符號。

4. 請等待上的兩個附件的所有指示燈都關閉。

5. 重新插入 SA 纜線，並確保已 EBOD 圍繞符號與主要的圍繞符號之間良好的連線。

6. 開啟 EBOD 圍繞符號前的兩個 PCM 會切換至 [開啟] 位置。

7. 請確定 EBOD 圍繞符號上，請核取 [綠色的 LED 為開啟。

8. 開啟 [主要的圍繞符號]。

9. 請確定主要的圍繞符號上，請核取 [綠色控制器 LED 為開啟。

10. 確認主要的圍繞符號 EBOD 圍繞符號連線很好，請核取 [SA 通道 Led （每個 EBOD 控制站四） 的所有開啟。

>[AZURE.IMPORTANT] 如果 SA 纜線有瑕疵或 EBOD 圍繞符號與主要的圍繞符號之間的連線不好的當您開啟系統時，它會進入復原] 模式。 如果發生這種情況，請[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)。

## <a name="turn-off-a-running-device"></a>關閉執行裝置

在執行 StorSimple 裝置可能需要關閉如果移動，從服務，或將需要更換的元件。 步驟會 StorSimple 裝置是否 8100，或從 8600 模型而有所不同。 8100 具有單一的主要圍繞符號]，而從 8600 是雙圍繞符號裝置與主要的圍繞符號的 EBOD 圍繞符號。 本節詳細步驟以關閉執行的裝置。

- [裝置與主要的圍繞符號](#8100a)

- [裝置以搭配 EBOD 圍繞符號](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>裝置與主要的圍繞符號<a name="8100a"> 

若要關閉該裝置，整齊有序及控制的方式，您可以執行透過 Azure 傳統入口網站，或透過 Windows PowerShell 的 StorSimple。 

>[AZURE.IMPORTANT] 不會關閉執行裝置的裝置上使用 [power] 按鈕。
>
>之前關閉裝置，請確認所有裝置元件健全。 在 Azure 傳統的入口網站中，瀏覽至**裝置** > **進行的維修作業** > **硬體狀態**，並確認所有元件的狀態為 [綠色。 這是僅適用於良好的系統，則為 true。 如果系統會被關機]，以取代將元件，您會看到失敗 （紅色） 或降低 （黃色）**硬體狀態**各元件的狀態。

您存取 Windows PowerShell StorSimple 或 Azure 傳統入口網站後，請遵循[關閉 StorSimple 裝置](storsimple-manage-device-controller.md#shut-down-a-storsimple-device)中的步驟進行。 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>裝置以搭配 EBOD 圍繞符號<a name="8600a">

>[AZURE.IMPORTANT] 關閉主要的圍繞符號和 EBOD 圍繞符號之前, 確認裝置的所有元件都都健全。 在 Azure 傳統的入口網站中，瀏覽至 [**裝置]** > **進行的維修作業** > **硬體狀態**，並確認健全的所有元件。

#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>若要關閉 EBOD 圍繞符號的執行裝置

1. 請遵循列出的主要的圍繞符號]，[關閉 StorSimple 裝置](storsimple-manage-device-controller.md#shut-down-a-storsimple-device)中的所有步驟。

2. 關閉主要的圍繞符號之後，請關閉 EBOD 翻轉關閉 Power] 與 [製冷模組 (PCM) 選項。

3. 若要確認 EBOD 已關閉，核取所有指示燈背面的 EBOD 圍繞符號都會關閉。

>[AZURE.NOTE] 關閉系統之後應該用來將 EBOD 圍繞符號連線到主要的圍繞符號的 SA 纜線移除為止。

## <a name="next-steps"></a>後續步驟

[連絡 Microsoft 支援人員](storsimple-contact-microsoft-support.md)如果發生問題時開啟或關閉 [StorSimple 裝置。

