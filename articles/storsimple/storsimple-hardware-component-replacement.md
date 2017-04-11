<properties 
   pageTitle="更換 StorSimple 硬體元件 |Microsoft Azure"
   description="說明如何安全地取代 PCMs、 電池、 控制器模組、 EBOD 控制站、 磁碟機和 StorSimple 裝置的底座。"
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
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="storsimple-hardware-component-replacement"></a>更換 StorSimple 硬體元件

## <a name="overview"></a>概觀

硬體元件取代教學課程說明硬體元件，您的 Microsoft Azure StorSimple 8000 數列裝置和移除並取代所需的步驟。 本文說明安全圖示、 提供指標詳細的教學課程，並列出可替換元件。

>[AZURE.IMPORTANT] 請先移除或取代任何 StorSimple 元件，請確定您檢閱[安全圖示慣例](#safety-icon-conventions)，以及其他[安全預防措施](storsimple-safety.md)。
 
### <a name="safety-icon-conventions"></a>安全圖示慣例

下表說明這些教學課程中使用的安全圖示。 當您進行的步驟來移除並取代裝置元件，則請注意下列安全圖示。

| 圖示 | 文字 | 其他資訊 |
|:---- |:---- |:-----------|
|![警告圖示](./media/storsimple-hardware-component-replacement/Warning.png)| **擔心 ！** | 指出危險的情況，如果不避免，就會導致死亡或嚴重受傷。 這個訊號單字僅限於最的情況。|
|![警告圖示](./media/storsimple-hardware-component-replacement/Warning.png)| **警告 ！** | 指出危險的情況，如果不避免，可能會導致死亡或嚴重受傷。|
|![警告圖示](./media/storsimple-hardware-component-replacement/Caution.png)| **注意 ！** |指出危險的情況，如果不避免，可能會導致稍微或中等傷害。|
|![通知圖示](./media/storsimple-hardware-component-replacement/NoticeIcon.png)| **請注意︰** | 指出視為很重要，但不是障礙相關的資訊。|
|![電子衝擊圖示](./media/storsimple-hardware-component-replacement/Electric.png) | **電子衝擊障礙** | 指出高伏特。|
|![高負載圖示](./media/storsimple-hardware-component-replacement/Weight.png)| **高負載**| |
|![使用者容易維護的組件的圖示](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png)| **任何使用者容易維護的組件** | 不會存取除非正確訓練。|
|![閱讀指示圖示](./media/storsimple-hardware-component-replacement/ReadInstructions.png)|**請先閱讀所有的指示**| |
|![提示障礙圖示](./media/storsimple-hardware-component-replacement/TipHazard.png)|**秘訣障礙**| |

### <a name="before-you-begin"></a>開始之前

瞭解您在本教學課程中使用的裝置和安全圖示的安全性資訊。 如需完整資訊，以移至[安全地安裝與操作 StorSimple 裝置](storsimple-safety.md)。 請務必使用準則修改[安全預防措施](storsimple-safety.md#handling-precautions)處理 StorSimple 裝置。 

您嘗試取代元件之前，請考慮下列資訊。

![警告圖示](./media/storsimple-hardware-component-replacement/Warning.png)![電子衝擊圖示](./media/storsimple-hardware-component-replacement/Electric.png)**警告 ！** 

- 以自己接地正確地使用 electrostatic 排放或 antistatic 墊子來處理模組和 StorSimple 裝置的元件。

- 沒有觸碰任何電路。 使用所提供的控點與輔助線同時處理可能會有公開電路的元件。

![警告圖示](./media/storsimple-hardware-component-replacement/Warning.png)![請注意圖示](./media/storsimple-hardware-component-replacement/NoticeIcon.png)**通知︰**

當您取代模組，請**不要將留空白區中的 [圍繞符號後方**。 移除問題組件之前，先取得取代或空白的模組。

## <a name="hardware-component-replacement-procedures"></a>硬體元件取代程序

主要及/或 EBOD 隨函附件中的多個外掛程式模組包含 StorSimple 8000 數列裝置。 8100 具有單一的主要圍繞符號]，而從 8600 雙重圍繞符號裝置與主要的圍繞符號的 EBOD 圍繞符號。

下表摘要列出您的裝置中的主要硬體元件。 按一下 [**取代程序**] 欄，以移至相關聯的教學課程中的連結]。

|元件|# 簡報|外掛程式模組嗎？|取代程序
|:---------|:--------|:--------------|:---------------------|
| 底座|1|無|[取代 StorSimple 裝置上底座](storsimple-chassis-replacement.md) |
|主要控制站|2|[是]| [取代控制器模組 StorSimple 裝置上](storsimple-controller-replacement.md) |
|764W 和製冷模組 (PCMs)|2|[是]| [取代和製冷模組 StorSimple 裝置上](storsimple-power-cooling-module-replacement.md) |
|備用|2|[是]| [取代 StorSimple 裝置上的備份電池模組](storsimple-battery-replacement.md) |
|磁碟機|12|[是]| [取代磁碟機 StorSimple 裝置上](storsimple-disk-drive-replacement.md) |

**資料表 1**主要的圍繞符號中的硬體元件

主要的圍繞符號和 EBOD 圍繞符號的其 I/O 模組不同。 此外，PCMs 會有不同的 wattage。 在主要的圍繞符號 PCMs 是 764 W，，而在 EBOD 圍繞符號就是 580 工時在 [主要的圍繞符號 PCMs 也包含備用模組。

|元件|# 簡報|外掛程式模組嗎？| 取代程序
|:---------|:--------|:--------------|:---------------------|
|底座|1|無| [取代 StorSimple 裝置上底座](storsimple-chassis-replacement.md) |
|EBOD 控制站|2|[是]| [取代 StorSimple 裝置上的 EBOD 控制器](storsimple-ebod-controller-replacement.md) |
|580W 和製冷模組 (PCMs)|2|[是]| [取代和製冷模組 StorSimple 裝置上](storsimple-power-cooling-module-replacement.md) |
|磁碟機|12|[是]| [取代磁碟機 StorSimple 裝置上](storsimple-disk-drive-replacement.md) |

**資料表 2**EBOD 圍繞符號中的硬體元件

在裝置上的外掛程式模組會醒目提示中的下列正面及後的圖表。 您可以使用這些圖表來決定各種外掛程式模組的位置，如果是必要的替代方案。 使用正面與圖表會顯示磁碟機，以及後的圖表 EBOD 圍繞符號] 和 [主要的圍繞符號顯示外掛程式的模組。

![磁碟機與裝置的 Frontplane](./media/storsimple-hardware-component-replacement/IC741028.png)

**圖 1**最上層的裝置

|標籤|描述|
|:----|:----------|
|0-11|磁碟機 （12 總計）|

主要的圍繞符號] 與 [EBOD 圍繞符號有磁碟機航空公司模組。 底座儲存了十二個待 3.5 「 磁碟機排列 3，4 格式。

![後擋板的裝置主要的圍繞符號模組](./media/storsimple-hardware-component-replacement/IC740994.png)

**圖 2**背面主要的圍繞符號

|標籤|描述|
|:----|:----------|
|1|PCM 0|
|2|PCM 1|
|3|控制站 0|
|4|控制站 1|

![後擋板的裝置 EBOD 圍繞符號外掛程式模組](./media/storsimple-hardware-component-replacement/IC769599.png)

**圖 3**背面 EBOD 圍繞符號

|標籤|描述|
|:----|:----------|
|1|PCM 0|
|2|PCM 1|
|3|EBOD 控制器 0|
|4|EBOD 控制站 1|

## <a name="field-replaceable-units"></a>功能變數取代單位

下列功能變數取代單位 (Fru) 可供您 StorSimple 的裝置︰

- 底座 （包括 [整合式的作業] 面板）

- 764 W AC PCM

- 580 W AC PCM

- 硬碟的磁碟機航空公司模組

- 控制站的模組

- EBOD 控制器模組

- 備用模組

- 機架裝載 rail 損毀套件

請[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)以這些替代單位的任何順序排列。

## <a name="next-steps"></a>後續步驟

在您嘗試取代 StorSimple 硬體元件，請檢閱所有[安全性資訊](storsimple-safety.md)。
