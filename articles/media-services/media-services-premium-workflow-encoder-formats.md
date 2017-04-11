<properties 
    pageTitle="媒體 Encoder 進階版工作流程格式及轉碼器 |Microsoft Azure" 
    description="本主題提供媒體 Encoder 進階版工作流程格式格式及轉碼器的概觀" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erik43" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"    
    ms.author="juliako;anilmur"/>

#<a name="media-encoder-premium-workflow-formats-and-codecs"></a>媒體 Encoder 進階版工作流程格式及轉碼器


>[AZURE.NOTE]進階版 encoder 問題的電子郵件在 Microsoft.com mepd。
>
>本主題中所討論的媒體 Encoder 進階版工作流程媒體處理器不適用於 china （中國）。 

這份文件包含輸入與輸出檔案格式及公用預覽版本的**媒體 Encoder 進階版工作流程**編碼器支援的轉碼器的清單。

[媒體 Encoder 進階版 Worflow 輸入格式及轉碼器](#input_formats)

[媒體 Encoder 進階版 Worflow 輸出格式及轉碼器](#output_formats)

**媒體 Encoder 進階版工作流程**支援字幕[本節](#closed_captioning)所述。 


##<a id="input_formats"></a>媒體 Encoder 進階版工作流程輸入格式及轉碼器

下一節列出此媒體處理器支援做為輸入的轉碼器與檔案格式。

###<a name="input-containerfile-formats"></a>輸入容器/檔案格式

- Adobe® Flash® F4V
- MXF SMPTE 377 M
- GXF
- Mpeg-2 傳輸串流
- Mpeg-2 程式串流
- MPEG-4/MP4
- Windows Media/ASF
- AVI （未壓縮 8 位元/10 位元）

###<a name="input-video-codecs"></a>輸入視訊轉碼器

- AVC 8 位元/10-元，最多 4:2:2，包括 AVCIntra
- 勤 DNxHD （在 MXF)
- DVCPro/DVCProHD （在 MXF)
- JPEG2000
- Mpeg-2 （最多 422 的設定檔與高層級; 包括變化，例如 XDCAM、 XDCAM HD、 XDCAM /IMX、 CableLabs® 和 D10）
- MPEG-1
- Windows Media 視訊/vc-1

###<a name="input-audio-codecs"></a>輸入的音訊轉碼器

- AES ([AES3-2003年中的 [SMPTE 331 M 和 302 M）
- Dolby® E
- Dolby® 數位 (AC3)
- AAC (AAC-LC、 AAC 他和 AAC HEv2; 最 5.1)
- MPEG 階層 2
- MP3 （mpeg-1 音訊層 3）
- Windows Media 音訊
- WAV/PCM
 
##<a id="output_format"></a>媒體 Encoder 進階版的工作流程輸出格式及轉碼器

下一節列出此媒體處理器輸出為支援的轉碼器和檔案格式。

###<a name="output-containerfile-formats"></a>輸出容器/檔案格式

- Adobe® Flash® F4V
- MXF （OP1a、 XDCAM 和 AS02）
- DPP （包括 AS11）
- GXF
- MPEG-4/MP4
- Windows Media/ASF
- AVI （未壓縮 8 位元/10 位元）
- Smooth Streaming 檔案格式 (PIFF 1.3)
- MPEG TS 


###<a name="output-video-codecs"></a>輸出的視訊轉碼器

- AVC (H.264; 8 位元; 最高設定檔，層級 5.2; 4 K 強力 HD;AVC 內部）
- 勤 DNxHD （在 MXF)
- DVCPro/DVCProHD （在 MXF)
- Mpeg-2 （最多 422 的設定檔與高層級; 包括變化，例如 XDCAM、 XDCAM HD、 XDCAM /IMX、 CableLabs® 和 D10）
- MPEG-1
- Windows Media 視訊/vc-1
- JPEG 縮圖建立

###<a name="output-audio-codecs"></a>輸出音訊轉碼器

- AES ([AES3-2003年中的 [SMPTE 331 M 和 302 M）
- Dolby® 數位 (AC3)
- Dolby® 數位加上 (E-AC3) 最 7.1
- AAC (AAC-LC、 AAC 他和 AAC HEv2; 最 5.1)
- MPEG 階層 2
- MP3 （mpeg-1 音訊層 3）
- Windows Media 音訊

##<a id="closed_captioning"></a>支援字幕

在內嵌， **Media Encoder 進階版工作流程**支援︰

1. SCC 檔案
1. SMPTE TT 檔案
1. CEA-608/CEA-708 – 為使用者資料 （SEI 郵件 H.264 基本資料流，ATSC/53，SCTE20） 執行，或執行作為輔助 MXF/GXF 檔案中的資料
1. STL 副標題檔案

在輸出時，可以使用下列的選項︰

1. CEA-608 CEA 708 翻譯
1. CEA 608/CEA 708 傳遞 （內嵌於 SEI 郵件 H.264 基本資料流，或執行為輔助 MXF 檔案中的資料）
1. SCC
1. SMPTE 逾時文字 （從來源 CEA 608 每 SMPTE RP2052; 包括 DFXP 檔案建立）
1. SRT 副標題檔案
1. DVB 副標題串流

注意︰ 並非所有上述的輸出格式的支援透過 Azure 媒體服務串流傳遞。

##<a name="known-issues"></a>已知的問題

如果您輸入的視訊中不包含隱藏式輔助字幕輸出資產仍然會包含空白的 TTML 檔案。 


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
