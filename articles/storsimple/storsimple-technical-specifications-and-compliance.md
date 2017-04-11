<properties 
   pageTitle="StorSimple 技術規格 |Microsoft Azure"
   description="將說明技術的規格及 StorSimple 硬體元件法規標準規範的資訊。"
   services="storsimple"
   documentationCenter="NA"
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

# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>技術的規格與規範 StorSimple 裝置

## <a name="overview"></a>概觀

您的 Microsoft Azure StorSimple 裝置的硬體元件遵守技術的規格與本文所述的法規標準。 技術規格描述 Power 及製冷模組 (PCMs)、 磁碟機、 儲存容量、 和附件。 規範資訊涵蓋等國際標準、 安全性及堵塞，以及纜線。


## <a name="power-and-cooling-module-specifications"></a>Power 及製冷模組規格  

StorSimple 裝置有兩個 100 240V 雙重風扇，SBB 標準的 Power 製冷模組 (PCMs)。 這會提供多餘 power 設定。 如果 PCM 失敗，請裝置會繼續在其他 PCM 正常運作，直到失敗的模組會被取代。  

EBOD 圍繞符號使用 580 的 W PCM，而主要的圍繞符號使用 764 的 W PCM。 下表列出技術規格 PCMs 相關聯。

| 規格           | 580 W PCM (EBOD)                                    | 764 W PCM （主要）                                |
|------------------------ | --------------------------------------------------- | -------------------------------------------------- |
| 最大成果 power    | 580 W                                               | 764                                                |
| 頻率               | 50/60 Hz                                            | 50/60 Hz                                           |
| 伏特選取範圍 | 自動範圍︰ 90 – 264 V AC、 47/63 Hz               | 自動範圍︰ 90-264 V 意見，47/63 Hz               |
| 目前的最大 inrush  | 20 A                                                | 20 A                                               |
| Power 因子變異數校正 | > 95 %nominal 輸入的伏特                          | > 95 %nominal 輸入的伏特                         |
| 和聲               | 符合 EN61000-3-2                                   | 符合 EN61000-3-2                                  |
| 輸出                  | 5V 待命伏特@2.0 A                          | 5V 待命伏特@2.7 A                         |
|                         | + 5V @ 42 A                                          | + 5V @ 40 A                                         |
|                         | + 12V @ 38 A                                         | + 12V @ 38 A                                        |
| 熱隨插即用           |  [是]                                                | [是]                                                |
| 切換參數與 Led       | AC 啟動/關閉] 切換參數及四個狀態指標 Led     | AC 啟動/關閉] 切換參數及六個狀態指標 Led     |
| 製冷圍繞符號       | 軸製冷風扇與變數風扇速度控制項  | 軸製冷風扇與變數風扇速度控制項 |

 
## <a name="power-consumption-statistics"></a>Power 耗用統計資料  

下表列出各種 StorSimple 裝置的模型的一般 power 消耗資料 （實際的值可能會有所不同已發佈）。 
 
 條件 | 240 V 意見 | 240 V 意見 | 240 V 意見 | 110 V 意見 | 110 V 意見 | 110 V 意見 
 ---------- | -------- | -------- | -------- | -------- | -------- | -------- 
 風扇變得很慢，磁碟機閒置 | 1.45 A  |0.31 kW | 1057.76 BTU/hr | 3.19 A | 0.34 kW | 1160.13 BTU/hr 
 存取風扇變得很慢，磁碟機 | 1.54 A | 0.33 kW | 1126.01 BTU/hr | 3.27 A | 0.36 kW | 1228.37 BTU/hr 
 風扇快速，磁碟機閒置，兩個 PSUs 可 | 2.14 A | 0.49 kW  | 1671.95 BTU/hr | 4.99 A | 0.54 kW | 1842.56 BTU/hr 
 快速，風扇磁碟機閒置、 一個 PSU 電源已閒置一 | 2.05 A | 0.48 kW | 1637.83 BTU/hr | 4.58 A | 0.50 kW | 1706.07 BTU/hr 
 存取，可的兩個 PSUs 風扇快速，磁碟機 | 2.26 A | 0.51 kW | 1740.19 BTU/hr | 4.95 A | 0.54 kW | 1842.56 BTU/hr 
 快速風扇，磁碟機存取，一個 PSU 可閒置一 | 2.14 A |0.49 kW | 1671.95 BTU/hr | 4.81 A  | 0.53 kW | 1808.44 BTU/hr 

## <a name="disk-drive-specifications"></a>磁碟機規格  

StorSimple 裝置支援最多 12 3.5 英吋表單因子變異數循序附加 SCSI (SA) 磁碟機。 實際的磁碟機可混合 solid-state 磁碟機 (SSDs) 或硬碟機 (HDDs)，根據產品設定。 設定圍繞符號前面，3，4 位於 12 的磁碟位置。 EBOD 圍繞符號可讓您的另一個 12 的磁碟機的額外儲存空間。 這些都 HDDs。  

## <a name="storage-specifications"></a>儲存空間的規格
StorSimple 裝置 8100] 和 [從 8600 擁有的硬碟 solid-state 磁碟機。 8100 和從 8600 可用容量總數約 15 TB 分別是及 38 TB。 下表的文件 SSD、 HDD 及雲端容量 StorSimple 解決方案容量的內容中的詳細資料。

| 裝置型號 / 容量                         | 8100                                                    | 從 8600                                                    |
|------------------------------------------------|---------------------------------------------------------|---------------------------------------------------------|
| 硬碟 (HDDs) 的數字              |   8                                                     |  19                                                     |
| Solid-state 磁碟機 (SSDs) 的數字            |   4                                                     |  5                                                      |
| 單一 HDD 容量                            |   4 TB                                                  |  4 TB                                                   |
| 單一 SSD 容量                            |   400 GB                                                |  800 GB                                                 |
| 備用容量                                 |   4 TB                                                  | 4 TB                                                    |
| 可用的 HDD 容量                            | 14 TB                                                   | 36 TB                                                   |
| 可用的 SSD 容量                            | 800 GB                                                  | 2 TB                                                    |
| 總的可用容量 *                         | ~ 15 TB                                                 | ~ 38 TB                                                 |
| 最大的解決方案容量 （包括雲端）    | 200 TB                                                  | 500 TB                                                  |


<sup> * </sup>- *可用的容量總數包含資料、 中繼資料和 buffers.* 可用的容量

## <a name="enclosure-dimensions-and-weight-specifications"></a>圍繞符號維度和粗細規格  

下表列出的各種不同的圍繞符號規格維度和粗細。  

### <a name="enclosure-dimensions"></a>圍繞符號尺寸
下表列出圍繞公釐和英吋為單位的維度。

|圍繞符號 |毫米 |英吋為單位 |
|----------|------------|-------| 
| 高度 |87.9 | 3.46 |
| 跨裝載法蘭的寬度 | 483 | 19.02 |
| 在本文的圍繞符號的寬度 | 443 | 17.44 |
| 從前端裝載法蘭深度 extremity 的圍繞符號本文 | 577 | 22.72 |
| 從 [作業] 面板深度本期 extremity 的圍繞符號 | 630.5 | 24.82 |
| 從裝載法蘭深度本期 extremity 的圍繞符號 |   603 | 23.74 |

### <a name="enclosure-weight"></a>圍繞符號粗細  

根據設定，完全載入的主要圍繞符號可以從 21 衡量至 33 kgs，需要兩個其他人的處理方式。 
 
| 圍繞符號 | 粗細 |
|-----------|--------| 
| 最大的寬度 （設定而定） |30 公斤 – 33 公斤 |
| 空白 （沒有符合的磁碟機） |21-23 公斤 |

## <a name="enclosure-environment-specifications"></a>圍繞符號環境規格  

本節會列出相關的圍繞符號環境的相關規格。 溫度、 濕度、 高度、 衝擊、 震動、 方向、 安全和電磁的相容性 (EMC) 會包含此類別中。  

### <a name="temperature-and-humidity"></a>溫度和濕度

| 圍繞符號        | 環境溫度範圍  | 環境相對於濕度 | 最大濕的燈泡   |
|------------------|----------------------------|---------------------------|--------------------|
| 操作      | 5 ° C-35 ° C (41 度 F-95 度 F)    | 20%-80%-壓縮非 | 28 ° C (82 度 F)        |
| 非操作  | -40 度 C-70 ° C (40 度 F-158 度 F) | 5-100%非-壓縮  | 29 ° C (84 度 F)        |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>氣流高度、 衝擊、 震動、 方向、 安全和 EMC
 
| 圍繞符號          | 操作規格                                                |
|--------------------|---------------------------------------------------------------------------| 
| 氣流            | 系統氣流是最上層] 以後方。 系統必須營運 low-pressure、 後方排氣安裝中。 返回壓力建立機架門與時遇到的障礙不可超過 5 pascals （0.5 mm 水儀表）。 |
| 高度操作  | 以最大作業的溫度就能取消評等 5 度 C 7000 英呎以上所使用的 3045 公尺 （到 10000 英呎-100 英呎）-30 公尺。 |
| 高度，非操作  | 若要 12,192 公尺 （以 40000 英呎-1,000 英呎）-305 公尺 |
| 衝擊操作  | 5 g 10 ms ½ 正弦值 |
| 衝擊，非操作  | 30 g 10 ms ½ 正弦值 |
| 震動操作  | 0.21 g RMS 5 500 Hz 隨機 |
| 震動，非操作  | 為 1.04 g RMS 2 200 Hz 隨機 |
| 震動，重新配置  | 3g 2 200 Hz 正弦值 |
| 方向及裝載  | 19 」 框架承載 （2 EIA 單位） |
| 機架滑軌  | 若要符合最低 700 公釐 （31.50 英吋） 深度機架符合 IEC 297 |
| 安全性和核准  |   CE 和 UL EN 61000-3，IEC 61000-3，UL 61000-3 |
| EMC  | EN55022 (CISPR-A，) 格式一致性檢查 A |

## <a name="international-standards-compliance"></a>國際標準法規遵循
下列國際標準符合您的 Microsoft Azure StorSimple 裝置︰  

- CE-EN-US 60950-1  
- IEC 60950-1 CB 報表  
- UL 和 cUL 至 UL 60950 1  

## <a name="safety-compliance"></a>安全法規遵循  

Microsoft Azure StorSimple 裝置符合下列安全評等︰  

- 系統的產品類型核准︰ UL，cUL，CE  
- 安全規範︰ UL 60950 IEC 60950，EN 60950  

## <a name="emc-compliance"></a>EMC 法規遵循 

Microsoft Azure StorSimple 裝置符合下列 EMC 評等。  

### <a name="emissions"></a>堵塞

進行和 radiated 堵塞層級的裝置是 EMC 相容。  

- 進行的堵塞限制等級︰ CFR 47 組件 15B 課程 EN55022 課程 CISPR 類別  
- Radiated 的堵塞限制等級︰ CFR 47 組件 15B 課程 EN55022 課程 CISPR 類別   

### <a name="harmonics-and-flicker"></a>和聲和閃爍  

裝置符合 EN61000-3-2/3。  

### <a name="immunity-limit-levels"></a>Immunity 限制層級  
裝置符合 EN55024。  

## <a name="ac-power-cord-compliance"></a>AC 電源線法規遵循
  
外掛程式和完成電源線組件，則必須符合的國家/地區的使用的裝置，適當的標準，而且必須在該國家/地區中可接受的安全核准。 下表列出美國、 歐洲的標準。  

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>AC 電源線 USA （必須是 NRTL 列）

| 元件       | 規格                                                     |
| --------------- | ----------------------------------------------------------------- | 
| 電源線類型       | SV 或 SVT 18 AWG 最小值、 3 導線，2.0 公尺長度上限 |
| 外掛程式            | 按比例分配 120 V，10 A; NEMA 5-15 P 涉及類型附件外掛程式或 IEC 320 C14，250 V，10 A |
| 通訊端          | IEC 320 C-13，250 V，10 A                                         |

### <a name="ac-power-cords---europe"></a>AC 電源線 Europe

| 元件       | 規格                                                     |
| --------------- | ----------------------------------------------------------------- | 
| 電源線類型       | 協調、 H05-VVF-3G1.0                                         |
| 通訊端          | IEC 320 C-13，250 V，10 A                                         |

## <a name="supported-network-cables"></a>支援的網路纜線  

10 GbE 網路介面，資料 2 和 3 的資料，請參閱[支援的網路纜線和模組的清單](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)。

## <a name="next-steps"></a>後續步驟

您已準備好要部署在您的資料中心的 StorSimple 裝置。 如需詳細資訊，請參閱[部署您內部部署的裝置](storsimple-deployment-walkthrough-u2.md)。  
