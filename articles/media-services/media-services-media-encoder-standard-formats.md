<properties 
    pageTitle="媒體 Encoder 標準格式及轉碼器" 
    description="本主題提供 Media Encoder 標準格式及轉碼器的概觀。" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016"
    ms.author="juliako;anilmur"/>

#<a name="media-encoder-standard-formats-and-codecs"></a>媒體 Encoder 標準格式及轉碼器


這份文件包含最常見的匯入及匯出檔案格式，您可以使用媒體 Encoder 標準的清單。


##<a name="input-containerfile-formats"></a>輸入容器/檔案格式

檔案格式 （副檔名）|支援
---|---|---|---
FLV （含 H.264 和 AAC 轉碼器） (.flv)          |[是] 
MXF (.mxf)                  |[是] 
GXF (.gxf)                  |[是] 
MPEG2 PS，MPEG2 TS，3GP （.ts.ps、.3gp、.3gpp、.mpg）   |[是] 
Windows Media 視訊 (WMV) / ASF （.wmv、.asf） |[是] 
AVI （未壓縮 8 位元/10 位元） (avi)|[是] 
MP4 （.mp4、.m4a、.m4v） / ISMV （.isma、.ismv）|[是] 
[Microsoft 數位視訊 Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)(為.dvr-ms) |[是] 
Matroska WebM (.mkv)        |[是] 
音效 WAV (.wav) |[是] 
QuickTime (.mov) |[是]

>[AZURE.NOTE] 上方是更常遇到的副檔名清單。 媒體 Encoder 標準是否支援多個其他人 (例如︰.m2ts、.mpeg2video、.qt)。 如果您嘗試編碼檔案，您會收到關於不支援的格式的錯誤訊息，請提供意見反應[以下](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/)。

###<a name="audio-formats-in-input-containers"></a>輸入容器中的音訊格式 

標準媒體編碼器支援攜帶輸入容器中的下列音訊格式︰

- MXF 與 GXF QuickTime 檔案沒有與交錯立體聲曲目或 5.1 範例

或

- MXF、 GXF 及 QuickTime 檔案，為個別 PCM 追蹤執行音訊，但可以推算頻道對應 （立體聲音訊或 5.1），從檔案中繼資料

會在不久的將來提供明確/使用者提供頻道對應，請注意支援。


##<a name="input-video-codecs"></a>輸入視訊轉碼器

輸入視訊轉碼器|支援
---|---|---|---
AVC 8 位元/10-元，最多 4:2:2，包括 AVCIntra   |8 位元 4:2:0 和 4:2:2 
勤 DNxHD （在 MXF)                                 |[是] 
DVCPro/DVCProHD （在 MXF)                            |[是] 
數位視訊 (DV) （AVI 檔案）                   |[是]
JPEG 2000                                           |[是] 
Mpeg-2 （最多 422 的設定檔與高層級; 包括變化，例如 XDCAM、 XDCAM HD、 XDCAM /IMX、 CableLabs® 和 D10）|最多 422 的設定檔 
MPEG-1                                              |[是] 
VC-1/WMV9                                           |[是] 
組織總部/HQX                                      |無 
Mpeg-4 第 2 部分                                       |[是] 
[Theora](https://en.wikipedia.org/wiki/Theora)      |[是] 
壓縮前，YUV420 或 mezzanine                   |[是]
Apple ProRes 422                                    |[是]
Apple ProRes 422 LT |[是]
Apple ProRes 422 總部 |[是]
Apple ProRes Proxy|[是]
Apple ProRes 4444 |[是]
Apple ProRes 4444 XQ |[是]



##<a name="input-audio-codecs"></a>輸入的音訊轉碼器

輸入的音訊轉碼器|支援
---|---|---|---
AAC (AAC-LC、 AAC 他和 AAC HEv2; 最 5.1)|[是] 
MPEG 階層 2|[是] 
MP3 （mpeg-1 音訊層 3）|[是] 
Windows Media 音訊|[是] 
WAV/PCM|[是] 
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|[是] 
[這種門外漢只](http://go.microsoft.com/fwlink/?LinkId=822667) |[是] 
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|[是] 
AMR （自動調整多重工資率）|[是]
AES ([AES3-2003年中的 [SMPTE 331 M 和 302 M）        |無 
Dolby® E                                    |無 
Dolby® 數位 (AC3)                        |無 
Dolby® 數位加上 (E AC3)                 |無 


##<a name="output-formats-and-codecs"></a>輸出格式及轉碼器

下表列出匯出支援的轉碼器與檔案格式。


檔案格式|視訊轉碼器|音訊轉碼器
---|---|---
MP4 <br/><br/>（包括多重位元率 MP4 容器） |H.264 （[高]、 [主要和 [比較基準設定檔）|AAC-LC、 HE-AAC v1、 HE-AAC v2 
MPEG2 TS |H.264 （[高]、 [主要和 [比較基準設定檔）|AAC-LC、 HE-AAC v1、 HE-AAC v2 



##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>另請參閱

[Azure 媒體服務編碼視內容](media-services-encode-asset.md)

[如何使用標準 Media Encoder](media-services-dotnet-encode-with-media-encoder-standard.md)
