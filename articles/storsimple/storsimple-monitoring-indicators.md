<properties 
    pageTitle="監控指標 StorSimple |Microsoft Azure" 
    description="將說明 light-emitting 二極體 (Led) 及音效用來監視 StorSimple 裝置的狀態的警示。"
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
    ms.date="08/18/2016"
    ms.author="alkohli" />

# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>若要管理您的裝置使用 StorSimple 監控指標   

## <a name="overview"></a>概觀

警示，您可以使用監視模組和 StorSimple 裝置的整體狀態和包含 light-emitting 二極體 (Led) StorSimple 裝置。 監控標記位於裝置的主索引的圍繞符號和 EBOD 圍繞符號的硬體元件。 監控標記可以 Led 或音效警示。

有三種 LED 狀態用來表示模組的狀態︰ 綠色閃爍紅色琥珀，或紅色琥珀綠色。  

- 綠色 Led 代表良好的作業系統狀態。  
- 閃爍綠色紅色琥珀 Led 代表，可能會要求使用者操作的非要徑條件的目前狀態。  
- 紅色琥珀 Led 表示簡報的模組中的要徑錯誤。  

這篇文章的其餘部分說明各種監控指標 Led StorSimple 在裝置上，裝置狀態，根據 LED 狀態，以及任何相關聯的音效鬧鐘其位置。

## <a name="front-panel-indicator-leds"></a>面板指標 Led

面板，也稱為*作業面板]*或 [*選項] 面板*中，顯示系統中的彙總的所有模組狀態。 面板相同主要 StorSimple 和 EBOD 圍繞符號]，如下所示。  

   ![裝置面板][1]
 
面板包含下列指標︰  

1. [靜音] 按鈕
2. Power 指標 LED （綠色/紅色-琥珀）
3. 模組錯誤指標主導 （在紅色-琥珀/關閉）
4. （在紅色-琥珀/關閉主導的邏輯錯誤指標
5. 單位識別碼顯示  

面板的主要差異裝置的 Led 於 EBOD 圍繞符號，請顯示顯示**系統單位識別碼**。 在裝置上顯示的預設單位識別碼是**00**，而 EBOD 圍繞符號上顯示的預設單位識別碼是**01**。 這個選項可讓您快速區分裝置和 EBOD 圍繞符號時使用的裝置已開啟。 如果您的裝置已關閉，可用於中[開啟新的裝置上](storsimple-turn-device-on-or-off.md#turn-on-a-new-device)的資訊區別 EBOD 圍繞符號的裝置。  

## <a name="front-panel-led-status"></a>面板 LED 狀態  

請使用下表識別裝置或 EBOD 圍繞符號面板上 Led 所指出的狀態。  

|系統電源 | 模組錯誤 | 邏輯錯誤 | 警示 | 狀態|
|-------------|---------------|-----------------|-------|-------|
|紅色琥珀 | 關閉     | 關閉 | N/A | 電源遺失，在備份電源或電源作業系統上和控制站模組已移除。|
|綠色 | 開啟 | 開啟 | N/A | (5s) 上的選項] 面板 power 測試狀態|
|綠色 | 關閉 | 關閉 | N/A | Power 上所有的建議函數|
|綠色 | 開啟 |N/A | PCM 故障 Led 風扇故障 Led | 任何 PCM 錯誤，風扇錯誤，或在溫度|
| 綠色 | 開啟 | N/A | I/O 模組 Led  | 任何控制器模組錯誤|
| 綠色 | 開啟 | N/A | N/A | 圍繞符號邏輯錯誤|
| 綠色 | Flash | N/A | 模組狀態主導控制器模組。 PCM 故障 Led 風扇故障 Led | 安裝未知的控制器模組類型，I2C 匯流排失敗，控制器模組重要的產品資料 (VPD) 設定錯誤 |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Power 製冷模組 (PCM) 指標 Led   

Power 冷卻模組 (PCM) 指標 Led 位於主要的圍繞符號或 EBOD 圍繞符號後，在每個 PCM 模組。 本主題說明如何使用下列的 Led 監視 StorSimple 裝置的狀態。  

- PCM Led 的主要的圍繞符號
- PCM Led 的 EBOD 圍繞符號

## <a name="pcm-leds-for-the-primary-enclosure"></a>PCM Led 的主要的圍繞符號  

StorSimple 裝置有其他電池 764W PCM 模組。 下圖顯示裝置的 LED 面板。  

   ![在主要的圍繞符號 PCM Led][2]

LED 圖例︰

1. AC 電源中斷
2. 風扇失敗
3. 電池錯誤
4. PCM [確定]
5. DC 失敗
6. 電池建議  

PCM 的狀態是 [LED 面板表示。 裝置 PCM LED 面板有六個 Led。 四個這些 Led 顯示的電源風扇的狀態。 剩餘的兩個 Led 指出電池模組 PCM 中的狀態。 您可以使用下列表格，來判斷 PCM 狀態。  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>電源和風扇 PCM 指標 Led
| 狀態 | PCM 確定 （綠色） | AC 會失敗 （琥珀） | 風扇會失敗 （琥珀） | DC 會失敗 （琥珀） |
|--------|----------------|-----------------------|------------------|----------------------|
| 沒有電源 （移至 [圍繞符號） | 關閉 | 關閉 | 關閉 | 關閉|
| 沒有電源 (僅限此 PCM) | 關閉 | 開啟 | 關閉 | 開啟 |
| AC 呈現 PCM 上的 [確定]     | 開啟 | 關閉 | 關閉 | 關閉 |
| PCM 會失敗 （風扇會失敗。） | 關閉 | 關閉 | 開啟 | N/A |
| PCM 錯誤 （在透過伏特，在目前的 amp) | 關閉 | 開啟 | 開啟 | 開啟 |
| PCM （扇出了） | 開啟 | 關閉 | 關閉 | 開啟 |
| 備用模式 | 閃爍 | 關閉 | 關閉 | 關閉 |
| PCM 韌體下載 | 關閉 | 閃爍 | 閃爍 | 閃爍 |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>備用的 PCM 指標 Led  

| 狀態 | 電池建議 （綠色） | 電池錯誤 （琥珀） |
|--------|----------------------|-----------------------|
| 不存在的電池 | 關閉 | 關閉 |
| 電池簡報和充電 | 開啟 | 關閉 |
| 電池充電或進行的維修作業排放 | 閃爍 | 關閉 |
| 電池 「 軟體 」 錯誤 （可還原） | 關閉 | 閃爍 |
| 電池 「 硬體 」 錯誤 （無法復原） | 關閉 | 開啟 |
| 電池 disarmed | 閃爍 | 關閉 |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>PCM Led 的 EBOD 圍繞符號  

EBOD 圍繞符號有 580W PCM 並沒有其他電池。 EBOD 圍繞符號 PCM 面板具有指標 Led 僅適用於 power 耗材和風扇。 下圖顯示這些 Led。

   ![PCM Led 上 EBOD 圍繞符號][3] 
 
您可以使用下表，來判斷 PCM 的狀態。  

| 狀態 | PCM 確定 （綠色） | AC 會失敗 （琥珀） | 風扇會失敗 （琥珀） | DC 會失敗 （琥珀） |
|--------|---------------|------------------------|------------------|----------------------|
| 沒有電源 （移至 [圍繞符號） | 關閉 | 關閉 | 關閉 | 關閉 |
| 沒有電源 (僅限此 PCM) | 關閉 | 開啟 | 關閉 | 開啟 |
| AC 呈現 PCM 開啟 – [確定] | 開啟 | 關閉 | 關閉 | 關閉 |
| PCM 會失敗 （風扇會失敗。） | 關閉 | 關閉 | 開啟 | X |
| （在 amp，透過伏特，在目前的 PCM 錯誤 | 關閉 | 開啟 | 開啟 | 開啟 |
| PCM （扇出了） | 開啟 | 關閉 | 關閉 | 開啟 |
| 備用模型 | 閃爍 | 關閉 | 關閉 | 關閉 |
| PCM 韌體下載 | 關閉 | 閃爍 | 閃爍 | 閃爍 |

## <a name="controller-module-indicator-leds"></a>控制站模組指標 Led  

StorSimple 裝置包含 Led 主控站和 EBOD 控制器模組。   

### <a name="monitoring-leds-for-the-primary-controller"></a>監控主控制站的 Led
下圖中，可協助您識別主要控制器上的 Led。 （所有元件會列出協助方向）。  

   ![監控 Led-主控站][4]
 
請使用下表來決定控制器模組是否正常運作。  

### <a name="controller-indicator-leds"></a>控制站指標 Led  

| LED | 描述                                                                            
|---- | ----------- |
| 識別碼 LED （藍色） | 指出識別的時的模組。 如果藍色 LED 閃爍執行控制器上，然後控制器作用中的控制站，而另一個是備用網域控制站。 如需詳細資訊，請參閱[識別您的裝置上使用中控制器](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device)。 |
| 故障 LED （琥珀） | 指出控制器中的錯誤。        
| 確定 LED （綠色） | 穩定增加綠色表示控制器是 [確定]。 閃爍綠色指出控制器 VPD 設定錯誤。 |
| SA 活動 Led （綠色） | 穩定增加綠色指出目前沒有活動的連線。 閃爍綠色表示連線進行的活動。 |
| 乙太網路狀態 Led | 右側表示連結或網路活動: （穩定增加綠色） 連結使用中] （閃爍綠色） 網路活動。 左側表示的網路速度: （黃色） 1000 Mb/s、 （綠色） 100 Mb/s 和 （關閉） 10 Mb/s。 根據元件模型，即使不會啟用的網路介面，可能會閃爍指示燈。 |
| 文章 Led | 控制站開啟時，會指出開機進度。 如果 StorSimple 裝置無法啟動，則此 LED 可協助 Microsoft 支援服務識別發生失敗的啟動程序中的點。 |

>[AZURE.IMPORTANT] 
如果亮 LED 的錯誤，則表示控制器模組，可重新啟動控制器可能可以解決問題。 請如果重新啟動控制器無法解決此問題，連絡 Microsoft 支援服務。  


### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>監視 Led EBOD （EBOD 圍繞符號）  

每個 6 Gb/s SA EBOD 控制站都有標示其狀態，如下圖所示的 Led。  

  ![監控 Led-EBOD 圍繞符號][5]

請使用下表來決定 EBOD 控制器模組是否正常運作。  

### <a name="ebod-controller-module-indicator-leds"></a>EBOD 控制器模組指標 Led  

|狀態 | I/O 模組確定 （綠色） | I/O 模組錯誤 （琥珀） | Host （主機） 連接埠活動 （綠色） |
|-------|----------------------|-------------------------------|----------------------------|
| 控制站模組 [確定] | 開啟 | 關閉 | - |
| 控制站模組錯誤 | 關閉 | 開啟 | - |
| 沒有外部主機連接埠連線 | - | - | 關閉 |
| 外部主機連接埠連線 – 沒有活動 | - | - | 開啟 |
| 外部主機連接埠連線活動 | - | - | 閃爍 |
| 控制站模組中繼資料錯誤 | 閃爍 | - | - |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>磁碟機指標 Led 主要的圍繞符號和 EBOD 圍繞符號

StorSimple 裝置有位於主要的圍繞符號] 與 [EBOD 圍繞符號的磁碟機。 每一個磁碟機包含監控指標 Led 本節所述。 

磁碟機的磁碟機狀態會以綠色 LED 和紅色琥珀 LED 裝載在每一個磁碟機航空公司模組的最上層。 下圖顯示這些 Led。

  ![磁碟機 Led][6]
 
您可以使用下表來判定每個磁碟機，依序會影響整體面板 LED 狀態的狀態。  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>磁碟機指標 Led EBOD 圍繞符號  

| 狀態 | 活動確定 LED （綠色） | 故障 LED （紅色琥珀） | 相關聯的選項] 面板 LED |
|-------|--------------------------|----------------------|-------------------------|
| 沒有安裝的磁碟機 | 關閉 | 關閉 | 無 |
| 磁碟機安裝與操作 | 開啟或關閉閃爍活動 | X | 無 |
| SCSI 圍繞符號服務 (SES) 裝置身分識別設定 | 開啟 | 閃爍 1 第二個上/1 第二個關閉 | 無 |
| 設定 SES 裝置故障位元 | 開啟 | 開啟 | 邏輯錯誤 （紅色） |
| 停電控制項電路 | 關閉 | 開啟 | 模組錯誤 （紅色） |

## <a name="audible-alarms"></a>音效警示  

StorSimple 裝置包含與主要的圍繞符號] 與 [EBOD 圍繞符號相關聯的音效警示。 音效警示位於的兩個附件面板 （也稱為 [選項] 面板）。 音效警示，指出錯誤條件時出現。 在下列情況會啟動警示︰  

- 風扇故障或失敗
- 範圍伏特
- 透過或溫度條件下
- 熱溢位
- 系統故障
- 邏輯錯誤
- Power 供應錯誤
- 移除的乘冪製冷模組 (PCM)  

下表說明各種不同的警示狀態。  

### <a name="alarm-states"></a>警示狀態  

| 警示狀態 | 巨集指令 | 按下 [靜音] 按鈕的動作 |
|------------|---------|---------------------------------|
| S0 | 標準模式︰ 無訊息 | 按兩次發出嗶聲 |
| S1 | 故障模式︰ 1 第二個上/1 第二個關閉 | 切換到 S2 或 S3 （請參閱備忘稿） |
| S2 | 提醒模式︰ 間歇性嗶聲 | 無 |
| S3 | 靜音的模式︰ 無訊息 | 無 |
| S4 | 要徑故障模式︰ 連續的警示 | 無法使用︰ 無法作用中的音訊靜音 |

> [AZURE.NOTE] 

>  - 在警示狀態 S1，如果您不要按下設為靜音 2 分鐘內，狀態自動轉換為 S2 或 S3。  
>  - 清除 [錯誤] 條件後 S4 警示狀態 S1 回到 S0 中。  
>  - 要徑錯誤狀態 S4 可以輸入的任何其他狀態。  

您將靜音音效警示，請按下 [選項] 面板上的 [靜音] 按鈕。 自動靜音便會發生在兩分鐘後不手動營運設為靜音切換。 警示被設為靜音，它會繼續簡短間歇性嗶聲指出問題仍然存在的音效。 清除所有問題時，會無警示。  

下表說明各種警示狀況。  

### <a name="alarm-conditions"></a>警示條件  

| 狀態 | 重要性 | 警示 | 選項面板 LED |
|--------|---------|--------|----------------|
| PCM 通知 – 從單一 PCM DC power 的影響 | 故障 – 不會遺失重複 | S1 | 模組錯誤|
|PCM 通知 – 從單一 PCM DC power 的影響 | 故障 – 重複性的影響 | S1 | 模組錯誤 |
| PCM 風扇會失敗。 | 故障 – 重複性的影響 | S1 | 模組錯誤 |
| SBB 偵測到的模組 PCM 錯誤 | 錯誤 | S1 | 模組錯誤 |
| 移除 PCM | 設定錯誤 | 無 | 模組錯誤 |
| 圍繞符號設定錯誤 | 故障 – 要徑 | S1 | 模組錯誤 |
| 低警告溫度提醒 | 警告 | S1 | 模組錯誤 |
| 高警告溫度提醒 | 警告 | S1 | 模組錯誤 |
| 透過溫度警示 | 故障 – 要徑 | S1 | 模組錯誤 |
| I2C 匯流排失敗 | 故障 – 重複性的影響 | S1 | 模組錯誤 |
| 選項面板通訊錯誤 (I2C) | 故障 – 要徑     | S1 | 模組錯誤 |
| 控制站錯誤 | 故障 – 要徑 | S1 | 模組錯誤 |
| SBB 介面模組錯誤 | 故障 – 要徑 | S1 | 模組錯誤 |
| SBB 介面模組故障沒有剩餘的正常運作模組 | 故障 – 要徑 | S4 | 模組錯誤 |
| 移除 SBB 介面模組 | 警告 | 無 | 模組錯誤 |
| 磁碟機 power 控制項錯誤 | 警告 – 磁碟機 power 不會遺失 | S1 | 模組錯誤 |
| 磁碟機 power 控制項錯誤 | 故障 – 要徑;磁碟機 power 的遺失。 | S1 | 模組錯誤 |
| 磁碟機中移除 | 警告 | 無 | 模組錯誤 |
| 可用的不足 power | 警告 | 無 | 模組錯誤 |

## <a name="next-steps"></a>後續步驟

進一步瞭解[StorSimple 硬體元件和狀態](storsimple-monitor-hardware-status.md)。

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png

 
