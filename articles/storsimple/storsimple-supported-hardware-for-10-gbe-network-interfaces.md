<properties 
   pageTitle="硬體 StorSimple 10 GbE 介面 |Microsoft Azure"
   description="說明支援的小型表單係數隨插即用 (SFP) 收發、 纜線，以及 10 GbE 網路介面的切換參數 StorSimple 裝置上。"
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
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>支援硬體 StorSimple 裝置上之 10 GbE 網路介面

## <a name="overview"></a>概觀

本文提供可搭配您的 Microsoft Azure StorSimple 裝置使用的增補硬體的相關資訊。

## <a name="list-of-devices-tested-by-microsoft"></a>Microsoft 測試裝置的清單

Microsoft 測試下列小型表單因素隨插即用 (SFP) 收發纜線，與切換至確保其以最佳方式運作的裝置。 （下表將會更新為新的硬體測試。）

### <a name="sfp-transceivers"></a>SFP + 收發

|進行|模型|
|---|---|
|Cisco|SFP-10G-SR|

### <a name="cables"></a>纜線

|S 鍵。 [否]。 |進行|模型|
|---|---|---|
| 1。|Cisco|SFP-H10GB-CU1M|
| 2。|Cisco|SFP-H10GB-CU2M|
| 3。|Cisco|SFP-H10GB-CU3M|
| 4。|Tripp 精簡|N820-05 M (OM3)|

### <a name="switches"></a>切換參數

|S 鍵。 [否]。|進行|模型|
|---|---|---|
| 1。 |Cisco|N3K-C3172PQ-10GE|
| 2。 |Cisco|N3K C3048-ZM F|
| 3。 |Cisco|N5K-C5596UP FA 須相符|

## <a name="list-of-devices-tested-in-the-field"></a>在欄位的裝置清單中的進行測試

本節中的裝置已成功部署] 欄位中 StorSimple 客戶清單中。 這些尚未經過測試 microsoft，但可能會使用您 StorSimple 的裝置。
 
| 參數                         | 值                                    |
|-----------------------------------|------------------------------------------|
| 進行切換                     | Juniper                                  |
| 切換模型                    | ex4550 32F                               |
| 切換作業系統版本 | JunOS 12.3R9.4                           |
| 刀模型                     | 內建連接埠 (PIC 0)                    |
| 收發讓                  | Juniper                                  |
| 收發模型               | 組件編號 740 021308 <br></br> 組件編號 740 030658                   |
| 收發韌體版本    | Rev 01 版本 0.0 （報告）            |
| 纜線模型                     | 雙面跳 LC/LC 50/125µ，OM3，LSZH |
| StorSimple 模型                | 從 8600                                     |
| StorSimple 軟體版本     | 6.3.9600.17491                           |


## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>測試 OEM 提供者 (Mellanox) 裝置的清單  

Mellanox 已經測試下列小型表單係數隨插即用 (SFP) 收發纜線，與以確保其以最佳方式運作 Mellanox 網路介面，例如 10 GbE 網路介面 StorSimple 裝置上的選項。

### <a name="cables-and-modules-supported-by-mellanox"></a>纜線和支援 Mellanox 的模組 

下表列出纜線和支援 Mellanox 的模組。 這些尚未經過測試 microsoft，但可能會使用您 StorSimple 的裝置。

| S 鍵。 [否]。 | 速度 | 模型                 | 描述                                            | 進行                |
|--------|-------|-----------------------|--------------------------------------------------------|-----------------------|
| 1。     | 10 GbE| 封包-SFP-SFP-1 M        | 被動式 copper 纜線 SFP + 10 Gb/s 1m                   | Arista                |
| 2。     | 10 GbE| 封包-SFP-SFP-2 M        | 被動式 copper 纜線 SFP + 10 Gb/s 2 m                   | Arista                |
| 3。     | 10 GbE| 封包-SFP-SFP-3 M        | 被動式 copper 纜線 SFP + 10 Gb/s 3 m                   | Arista                |
| 4。     | 10 GbE| 封包-SFP-SFP-5 M        | 被動式 copper 纜線 SFP + 10 Gb/s 5 m                   | Arista                |
| 5。     | 10 GbE| Cisco SFP H10GBCU1M   | Cisco SFP + 纜線                                       | Cisco                 |
| 6。     | 10 GbE| Cisco SFP H10GBCU3M   | Cisco SFP + 纜線                                       | Cisco                 |
| 7。     |10 GbE | Cisco SFP H10GBCU5M   | Cisco SFP + 纜線                                       | Cisco                 |
| 8。     | 10 GbE| J9281B HP X242 10 G    | + SFP SFP + 1m 直接附加銅纜線             | HP                    |
| 9。     | 10 GbE| 455883 b21 中 HP BLc     | 10 Gb SR SFP + 選擇                                       | HP                    |
| 10。    | 10 GbE| 455886 b21 中 HP BLc     | 10 Gb 長程 SFP + 選擇                                       | HP                    |
| 11。    |10 GbE | 487649 b21 中 HP BLc     | SFP + 0.5 m 10GbE 銅纜線                           | HP                    |
| 12。    |10 GbE | 487652 b21 中 HP BLc     | SFP + 1m 10GbE 銅纜線                             | HP                    |
| 13。    |10 GbE | 487655 b21 中 HP BLc     | SFP + 3 m 10GbE 銅纜線                             | HP                    |
| 14。    |10 GbE | 487658 b21 中 HP BLc     | SFP + 7 m 10GbE 銅纜線                             | HP                    |
| 15。    |10 GbE | 537963 b21 中 HP BLc     | SFP + 5 m 10GbE 銅纜線                             | HP                    |
| 16。    |10 GbE | AP784A HP             | 3 m C 數列被動式銅 SFP + 纜線                  | HP                    |
| 17。    |10 GbE | AP785A HP             | 5 m C 數列被動式銅 SFP + 纜線                  | HP                    |
| 18。    |10 GbE | AP818A HP             | 1m B 數列作用中的銅 SFP + 纜線                   | HP                    |
| 19。    |10 GbE | AP819A HP             | 3 m B 數列作用中的銅 SFP + 纜線                   | HP                    |
| 20。    |10 GbE | J9150A HP             | X132 10 G SFP + LC SR 收發                        | HP                    |
| 21。    |10 GbE | J9151A HP             | X132 10 G SFP + LC 長程收發                        | HP                    |
| 22。    |10 GbE | J9283B HP             | X242 10 G SFP + SFP + 3 m DAC 纜線                        | HP                    |
| 23。    |10 GbE | J9285B HP             | X242 10 G SFP + SFP + 7 m DAC 纜線                        | HP                    |
| 24。    | 10 GbE| JD095B HP             | X240 10 G SFP + SFP + 0.65 m DAC 纜線                     | HP                    |
| 25。    | 10 GbE| JD096B HP             | X240 10 G SFP + SFP + 1.2 m DAC 纜線                      | HP                    |
| 26。    | 10 GbE| JD097B HP             | X240 10 G SFP + SFP + 3 m 爸纜線                        | HP                    |
| 27。    | 10 GbE| MAM1Q00A QSA Mellanox | QSFP SFP + 介面卡                                   | Mellanox 技術 |
| 28。    | 10 GbE| MC2309124 006 細明體      | 以 QSFP 10 Gb/s 24awg 被動式銅纜線 1 x SFP+ 7 m   | Mellanox 技術 |
| 29。    | 10 GbE| MC2309124 007 細明體      | 以 QSFP 10 Gb/s 24awg 被動式銅纜線 1 x SFP+ 7 m   | Mellanox 技術 |
| 30。    | 10 GbE| MC2309130 003 細明體      | 以 QSFP 10 Gb/s 30awg 被動式銅纜線 1 x SFP+ 3 m   | Mellanox 技術 |
| 31。    | 10 GbE| MC2309130 00A 細明體      | 以 QSFP 10 Gb/s 30awg 被動式銅纜線 1 x SFP+ 0.5 m | Mellanox 技術 |
| 32。    | 10 GbE| MC3309124 005 細明體      | 被動式 Copper 纜線 1 x SFP+ 10 Gb/s 24awg 5 m           | Mellanox 技術 |
| 33。    | 10 GbE| MC3309124 007 細明體      | 被動式 Copper 纜線 1 x SFP+ 10 Gb/s 24awg 7 m           | Mellanox 技術 |
| 34。    | 10 GbE| MC3309130 003 細明體      | 被動式 Copper 纜線 1 x SFP+ 10 Gb/s 30awg 3 m           | Mellanox 技術 |
| 35。    | 10 GbE| MC3309130 00A 細明體      | 被動式 Copper 纜線 1 x SFP+ 10 Gb/s 30awg 0.5 m         | Mellanox 技術 |

### <a name="switches-supported-by-mellanox"></a>支援 Mellanox 的切換參數 

下表列出支援 Mellanox 參數。 這些尚未經過測試 microsoft，但可能會使用您 StorSimple 的裝置。

| S 鍵。 [否]。 | 速度 | 模型 | 描述                                                         | 進行              |
|--------|-------|-------------|---------------------------------------------------------------------|-------------|
| 1。     | 10GbE | 516733 B21 中  | HP ProCurve 6120XG 10GbE 乙太網路刀切換                      | HP    |
| 2。     | 10GbE | 538113 B21 中  | HP 10GbE 通過]、 模組 (PTM)                                  | HP    |
| 3。     | 10GbE | EN4093      | IBM PureFlex 系統布料的轉印圖樣 EN4093 10 Gb 調整切換模組 | IBM   |
| 4。     | 1GbE  | 3020        | Cisco Catalyst 3020 1GbE 切換刀                               | Cisco |
| 5。     | 1GbE  | 3020 X       | Cisco Catalyst 3020 X 1GbE 切換刀                              | Cisco |
| 6。     | 1GbE  | 438030 B21 中  | HP 1GbE 切換模組-GbE2c 階層 2/3 乙太網路刀切換       | HP    |
| 7。     | 1GbE  | 6120 G       | HP ProCurve 6120 G/XG 1GbE 切換刀                              | HP    |

## <a name="next-steps"></a>後續步驟

[瞭解更多 StorSimple 硬體元件和狀態](storsimple-monitor-hardware-status.md)。
