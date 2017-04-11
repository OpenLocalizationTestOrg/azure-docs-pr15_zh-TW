<properties
    pageTitle="如何裁剪視訊 |Microsoft Azure"
    description="本文將示範如何裁剪與媒體 Encoder 標準的影片。"
    services="media-services"
    documentationCenter=""
    authors="anilmur"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/26/2016"  
    ms.author="anilmur;juliako;"/>

# <a name="crop-videos-with-media-encoder-standard"></a>裁剪視訊與媒體 Encoder 標準

如果要裁剪您輸入的視訊，您可以使用媒體 Encoder 標準 (MES)。 裁剪是選取矩形視窗中的視訊畫面，以及編碼方式就像素為單位的視窗中的程序。 下圖中，可協助說明程序。

![裁剪視訊](./media/media-services-crop-video/media-services-crop-video01.png)

假設您有做為輸入視訊的 1920 x 1080 像素 （16:9 長寬比） 的解析度，但有黑色列 （圓柱方塊），在左側和右側，以便僅 4:3 視窗或 1440 x 1080 像素包含作用中的視訊。 裁剪或編輯出黑色的列，使用 MES 與編碼為 1440 x 1080 區域。

裁剪 MES 中是前置處理的階段，裁剪參數中編碼的預設格式套用至原始輸入視訊。 編碼後續步驟中，而/寬度和高度設定套用至*預先處理*視訊，而非原始的視訊。 設計您的預設格式時，您需要執行下列動作: （a） 選取 [根據原始輸入視訊，請裁剪參數，（b） 選取您編碼根據裁剪視訊設定]。 如果您不符合您進行設定以裁剪視訊編碼，輸出不會如預期般。

[下列](media-services-advanced-encoding-with-mes.md#encoding_with_dotnet)主題會顯示如何建立 MES 編碼的工作，以及如何指定預設編碼工作的自訂。 

## <a name="creating-a-custom-preset"></a>建立自訂的預設格式

在圖表中顯示的範例︰

1. 原始的輸入是 1920 x 1080
1. 若要裁剪成 1440 x 1080，其會顯示在輸入的圖文框中央的輸出需要
1. 這表示 (1920年 – 1440) X 位移 / 2 = 240 和 Y 位移為零
1. 裁剪矩形的高度與寬度為 1440年和 1080，分別是
1. 詢問是產生三個層級，分別是解決方案 1440 x 1080，960 x 720 和 480 x 360，編碼] 階段中

###<a name="json-preset"></a>JSON 預設格式]


    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "Crop": {
                "X": 240,
                "Y": 0,
                "Width": 1440,
                "Height": 1080
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1440,
              "Height": 1080,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1250,
              "MaxBitrate": 1250,
              "BufferWindow": "00:00:05",
              "Width": 480,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


##<a name="restrictions-on-cropping"></a>裁剪的限制

[裁剪] 功能會是用來手動。 您必須輸入的視訊載入適當的編輯工具，可讓您選取感興趣的圖文框]，將游標置於決定位移裁剪矩形，來判斷編碼的預設調整為該特定的視訊等等。此功能不是啟用項目，例如︰ 自動偵測和移除的黑色的信箱 pillarbox 您輸入視訊的框線。

下列限制會套用至 [裁剪] 功能。 如果不符合這些項目，會編碼任務可以失敗，或產生非預期的輸出。

1. 共同 ordinates 及裁剪矩形的大小必須符合輸入視訊
1. 寬度與高度，在編碼設定如上所述，一定要對應至裁剪的視訊
1. 裁剪適用於擷取橫向模式中的視訊 (亦即不適用於使用智慧型手機錄製視訊保留垂直或直向模式)
1. 最適合用於漸進擷取方形像素的視訊

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-step"></a>下一步
 
請參閱 Azure 媒體服務學習路徑來協助您瞭解好反飛彈系統所提供的功能。  

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]
