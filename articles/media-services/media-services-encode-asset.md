<properties 
    pageTitle="概觀與比較 Azure 上視需要媒體編碼器 |Microsoft Azure" 
    description="本主題提供概觀，以及上視需要媒體編碼器 Azure 的比較。" 
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
    ms.date="09/19/2016" 
    ms.author="juliako"/>

#<a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>概觀與上視需要媒體編碼器 Azure 的比較

##<a name="encoding-overview"></a>編碼的概觀

Azure 媒體服務會提供多個選項的雲端中的媒體編碼方式。

當開始與媒體服務，務必瞭解轉碼器與檔案格式之間的差異。
轉碼器是而檔案格式是按住壓縮的視訊的容器實作壓縮/解壓縮演算法的軟體。

媒體服務會提供可讓您進行資料流支援的媒體服務 （MPEG 虛線 HLS、 平滑串流、 HDS） 格式中調整位元率 MP4 或平滑串流編碼內容的動態包裝，您不必重新封裝成這些串流格式。

若要充分利用[動態包裝](media-services-dynamic-packaging-overview.md)，您需要執行下列動作︰

- 將一組調整位元率 MP4 檔案或平滑串流 （示範稍後在本教學課程編碼步驟） 之檔案的自動調整位元率編碼 mezzanine （來源） 檔案。
- 至少有一個視串流單位取得串流端點從您打算傳送您的內容。 如需詳細資訊，請參閱[如何視需要串流保留刻度](media-services-portal-manage-streaming-endpoints.md)。

媒體服務上本文所述的需求編碼器支援下列動作︰

- [Media Encoder 標準](media-services-encode-asset.md#media-encoder-standard)
- [媒體 Encoder 進階版工作流程](media-services-encode-asset.md#media-encoder-premium-workflow)

本文提供概觀視媒體編碼器，並提供文章連結，其中提供更詳細的資訊。 主題也會提供編碼器的比較。

請注意，根據預設每個媒體服務帳戶可以進行中的一個編碼工作，一次。 您可以保留編碼單位，可讓您有多個編碼的工作，同時執行，針對每個編碼的保留單位，您購買一個。 如需的資訊，請參閱[縮放比例編碼單位](media-services-scale-media-processing-overview.md)。

##<a name="media-encoder-standard"></a>Media Encoder 標準

###<a name="how-to-use"></a>如何使用

[如何使用標準 Media Encoder](media-services-dotnet-encode-with-media-encoder-standard.md)

###<a name="formats"></a>格式

[格式及轉碼器](media-services-media-encoder-standard-formats.md)

###<a name="presets"></a>預設格式

媒體 Encoder 標準設定使用其中一個 encoder 預設格式所述[以下](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)。

###<a name="input-and-output-metadata"></a>輸入與輸出的中繼資料

說明編碼器輸入中繼資料[以下](http://msdn.microsoft.com/library/azure/dn783120.aspx)。

說明編碼器輸出中繼資料[以下](http://msdn.microsoft.com/library/azure/dn783217.aspx)。

###<a name="generate-thumbnails"></a>產生的縮圖

詳細資訊，請參閱[如何產生使用 Media Encoder 標準的縮圖](media-services-custom-mes-presets-with-dotnet.md#thumbnails)。

###<a name="trim-videos-clipping"></a>修剪視訊 （剪輯）

詳細資訊，請參閱[如何修剪視訊使用 Media Encoder 標準](media-services-custom-mes-presets-with-dotnet.md#trim_video)。

###<a name="create-overlays"></a>建立覆疊

資訊，請參閱[如何建立使用 Media Encoder 標準的覆疊](media-services-custom-mes-presets-with-dotnet.md#overlay)。

###<a name="see-also"></a>另請參閱

[媒體服務部落格](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##<a name="media-encoder-premium-workflow"></a>媒體 Encoder 進階版工作流程

###<a name="overview"></a>概觀

[介紹編碼 Azure 媒體服務的進階版](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

###<a name="how-to-use"></a>如何使用

使用複雜的工作流程設定 Media Encoder 進階版工作流程。 工作流程檔案無法建立和使用[工作流程設計](media-services-workflow-designer.md)工具來更新。

[如何使用編碼方式 Azure 媒體服務的進階版](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

###<a name="known-issues"></a>已知的問題

如果您輸入的視訊中不包含隱藏式輔助字幕輸出資產仍然會包含空白的 TTML 檔案。 


##<a id="compare_encoders"></a>比較編碼器

###<a id="billing"></a>使用每個 encoder 計費量表

媒體處理器名稱|適用的價格|備忘稿
---|---|---
**Media Encoder 標準** |ENCODER|編碼工作需要付費的輸出大小根據資產在指定的速度 GBytes，[以下][1]，[ENCODER] 欄。
**媒體 Encoder 進階版工作流程** |進階版 ENCODER|編碼工作需要付費的輸出大小根據資產在指定的速度 GBytes，[以下][1]，[進階版 ENCODER] 欄。


本節會比較編碼**Media Encoder 標準**和**媒體 Encoder 進階版工作流程**的功能。


###<a name="input-containerfile-formats"></a>輸入容器/檔案格式

輸入容器/檔案格式|Media Encoder 標準|媒體 Encoder 進階版工作流程
---|---|---
Adobe® Flash® F4V           |[是]|[是]
MXF SMPTE 377 M              |[是]|[是]
GXF                         |[是]|[是]
Mpeg-2 傳輸串流    |[是]|[是]
Mpeg-2 程式串流      |[是]|[是]
MPEG-4/MP4                  |[是]|[是]
Windows Media/ASF           |[是]|[是]
AVI （未壓縮 8 位元/10 位元）|[是]|[是]
3GPP/3GPP2                  |[是]|無
Smooth Streaming 檔案格式 (PIFF 1.3)|[是]|無
[Microsoft 數位視訊 Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|[是]|無
Matroska/WebM               |[是]|無
QuickTime (.mov) |[是]|無

###<a name="input-video-codecs"></a>輸入視訊轉碼器

輸入視訊轉碼器|Media Encoder 標準|媒體 Encoder 進階版工作流程
---|---|---
AVC 8 位元/10-元，最多 4:2:2，包括 AVCIntra   |8 位元 4:2:0 和 4:2:2|[是]
勤 DNxHD （在 MXF)                                 |[是]|[是]
DVCPro/DVCProHD （在 MXF)                            |[是]|[是]
JPEG2000                                            |[是]|[是]
Mpeg-2 （最多 422 的設定檔與高層級; 包括變化，例如 XDCAM、 XDCAM HD、 XDCAM /IMX、 CableLabs® 和 D10）|最多 422 的設定檔|[是]
MPEG-1                                              |[是]|[是]
Windows Media 視訊/vc-1                            |[是]|[是]
組織總部/HQX                                      |無|無
Mpeg-4 第 2 部分                                       |[是]|無
[Theora](https://en.wikipedia.org/wiki/Theora)      |[是]|無
Apple ProRes 422    |[是]|無
Apple ProRes 422 LT |[是]|無
Apple ProRes 422 總部 |[是]|無
Apple ProRes Proxy|[是]|無
Apple ProRes 4444 |[是]|無
Apple ProRes 4444 XQ |[是]|無

###<a name="input-audio-codecs"></a>輸入的音訊轉碼器

輸入的音訊轉碼器|Media Encoder 標準|媒體 Encoder 進階版工作流程
---|---|---
AES ([AES3-2003年中的 [SMPTE 331 M 和 302 M）        |無|[是]
Dolby® E                                    |無|[是]
Dolby® 數位 (AC3)                        |無|[是]
Dolby® 數位加上 (E AC3)                 |無|[是]
AAC (AAC-LC、 AAC 他和 AAC HEv2; 最 5.1)|[是]|[是]
MPEG 階層 2|[是]|[是]
MP3 （mpeg-1 音訊層 3）|[是]|[是]
Windows Media 音訊|[是]|[是]
WAV/PCM|[是]|[是]
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|[是]|無
[這種門外漢只](https://en.wikipedia.org/wiki/Opus_(audio_format)) |[是]|無
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|[是]|無


###<a name="output-containerfile-formats"></a>輸出容器/檔案格式

輸出容器/檔案格式|Media Encoder 標準|媒體 Encoder 進階版工作流程
---|---|---
Adobe® Flash® F4V|無|[是]
MXF （OP1a、 XDCAM 和 AS02）|無|[是]
DPP （包括 AS11）|無|[是]
GXF|無|[是]
MPEG-4/MP4|[是]|[是]
MPEG TS|[是]|[是]
Windows Media/ASF|無|[是]
AVI （未壓縮 8 位元/10 位元）|無|[是]
Smooth Streaming 檔案格式 (PIFF 1.3)|無|[是]

###<a name="output-video-codecs"></a>輸出的視訊轉碼器

輸出的視訊轉碼器|Media Encoder 標準|媒體 Encoder 進階版工作流程
---|---|---
AVC (H.264; 8 位元; 最高設定檔，層級 5.2; 4 K 強力 HD;AVC 內部）|只有 8 位元 4:2:0|[是]
勤 DNxHD （在 MXF)|無|[是]
Mpeg-2 （最多 422 的設定檔與高層級; 包括變化，例如 XDCAM、 XDCAM HD、 XDCAM /IMX、 CableLabs® 和 D10）|無|[是]
MPEG-1|無|[是]
Windows Media 視訊/vc-1|無|[是]
JPEG 縮圖建立|無|[是]

###<a name="output-audio-codecs"></a>輸出音訊轉碼器

輸出音訊轉碼器|Media Encoder 標準|媒體 Encoder 進階版工作流程
---|---|---
AES ([AES3-2003年中的 [SMPTE 331 M 和 302 M）|無|[是]
Dolby® 數位 (AC3)|無|[是]
Dolby® 數位加上 (E-AC3) 最 7.1|無|[是]
AAC (AAC-LC、 AAC 他和 AAC HEv2; 最 5.1)|[是]|[是]
MPEG 階層 2|無|[是]
MP3 （mpeg-1 音訊層 3）|無|[是]
Windows Media 音訊|無|[是]


##<a name="error-codes"></a>錯誤碼  

下表列出錯誤碼，以防產生錯誤編碼執行的工作期間無法傳回。  若要取得您.NET 程式碼中的錯誤詳細資料，請使用[ErrorDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx)類別。 若要取得其他程式碼中的錯誤詳細資料，使用[ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) REST API。

ErrorDetail.Code|錯誤的可能原因
-----|-----------------------
未知| 在執行工作時未知的錯誤
ErrorDownloadingInputAssetMalformedContent|類別的錯誤，包括中下載輸入的資產，例如不正確的檔案名稱，零長度檔案不正確的錯誤等格式。
ErrorDownloadingInputAssetServiceFailure|錯誤的涵蓋在服務端-下載時的範例網路或儲存空間錯誤的問題的類別。
ErrorParsingConfiguration|類別錯誤的位置工作<see cref="MediaTask.PrivateData"/>（設定） 不正確，例如設定不正確的系統預設或包含無效的 XML。
ErrorExecutingTaskMalformedContent|期間內的輸入的媒體檔案的問題會造成失敗工作的執行錯誤的類別。
ErrorExecutingTaskUnsupportedFormat|類別的錯誤位置媒體處理器無法處理程序所提供的檔案，-的媒體格式不支援，或不相符的設定。 例如，嘗試純音訊從產生資產具有唯一的視訊
ErrorProcessingTask|其他的錯誤處理工作的期間的媒體處理器遇到不相關的內容分類。
ErrorUploadingOutputAsset|上傳輸出資產的錯誤的類別
ErrorCancelingTask|類別的涵蓋失敗，當您嘗試取消工作時的錯誤
TransientError|類別的錯誤涵蓋的暫時性問題 （例如。 Azure 儲存體暫時網路問題）


從**媒體服務**小組相關說明，請開啟[支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。



##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="related-articles"></a>相關的文章

- [執行進階編碼工作的自訂 Media Encoder 標準的預設格式](media-services-custom-mes-presets-with-dotnet.md)
- [配額和限制](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
