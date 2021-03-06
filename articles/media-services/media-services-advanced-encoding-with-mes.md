<properties 
    pageTitle="進階編碼標準 Media Encoder" 
    description="本主題說明如何執行進階編碼自訂 Media Encoder 標準工作預設格式]。 主題說明如何使用媒體服務.NET SDK 建立編碼工作和工作。 也會顯示如何提供自訂的預設設定，以便編碼的工作。" 
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
    ms.date="09/25/2016"   
    ms.author="juliako"/>


#<a name="advanced-encoding-with-media-encoder-standard"></a>進階編碼標準 Media Encoder

##<a name="overview"></a>概觀

本主題說明如何執行進階編碼工作 Media Encoder 標準。 主題說明[如何使用.NET 建立編碼的工作和工作︰ 執行這項工作](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet)。 也會顯示如何提供自訂預設格式編碼的工作。 使用預設值的項目描述，請參閱[這份文件](https://msdn.microsoft.com/library/mt269962.aspx)。 

執行下列編碼工作自訂預設格式示範如下︰

- [產生的縮圖](media-services-custom-mes-presets-with-dotnet.md#thumbnails)
- [修剪視訊 （剪輯）](media-services-custom-mes-presets-with-dotnet.md#trim_video)
- [建立重疊](media-services-custom-mes-presets-with-dotnet.md#overlay)
- [插入無音訊追蹤時輸入具有沒有音訊](media-services-custom-mes-presets-with-dotnet.md#silent_audio)
- [停用自動去交錯](media-services-custom-mes-presets-with-dotnet.md#deinterlacing)
- [純音訊的預設格式](media-services-custom-mes-presets-with-dotnet.md#audio_only)
- [串連兩個或多個視訊檔案](media-services-custom-mes-presets-with-dotnet.md#concatenate)
- [裁剪視訊與媒體 Encoder 標準](media-services-custom-mes-presets-with-dotnet.md#crop)
- [輸入有視訊時，請插入視訊的追蹤](media-services-custom-mes-presets-with-dotnet.md#no_video)
- [旋轉視訊](media-services-custom-mes-presets-with-dotnet.md#rotate_video)

##<a id="encoding_with_dotnet"></a>與媒體服務.NET SDK 編碼

下列範例會使用媒體服務.NET SDK 來執行下列工作︰

- 建立編碼的工作。
- 取得媒體 Encoder 標準 encoder 的參考。
- 載入自訂 XML 或 JSON 預設格式]。 您可以儲存 XML 或 JSON （例如[XML](media-services-custom-mes-presets-with-dotnet.md#xml)或[JSON](media-services-custom-mes-presets-with-dotnet.md#json)檔案並使用下列程式碼中載入的檔案。

        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
- 新增 「 編碼工作的工作。 
- 指定要編碼輸入的資產。
- 建立輸出資產包含編碼的資產。
- 新增事件處理常式來檢查工作進度。
- 送出工作。
    
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Net;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Newtonsoft.Json.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using System.Web;
        using System.Globalization;
        
        namespace CustomizeMESPresests
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
        
                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");
        
                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
        
                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the chached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    // Get an uploaded asset.
                    var asset = _context.Assets.FirstOrDefault();
        
                    // Encode and generate the output using custom presets.
                    EncodeToAdaptiveBitrateMP4Set(asset);
        
                    Console.ReadLine();
                }
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
                
        
                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText("CustomPreset_JSON.json");
                
                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
                        TaskOptions.None);
                
                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);
                
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
                
                    return job.OutputMediaAssets[0];
                }
        
                static public IAsset UploadMediaFilesFromFolder(string folderPath)
                {
                    IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);
        
                    foreach (var af in asset.AssetFiles)
                    {
                        // The following code assumes 
                        // you have an input folder with one MP4 and one overlay image file.
                        if (af.Name.Contains(".mp4"))
                            af.IsPrimary = true;
                        else
                            af.IsPrimary = false;
        
                        af.Update();
                    }
        
                    return asset;
                }
        
        
                static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        
                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText(customPresetFileName);
        
                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input assets to be encoded.
                    // This asset contains a source file and an overlay file.
                    task.InputAssets.Add(assetSource);
        
                    // Add an output asset to contain the results of the job. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);
        
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
                    return job.OutputMediaAssets[0];
                }
        

                private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                            break;
                        case JobState.Canceling:
                        case JobState.Queued:
                        case JobState.Scheduled:
                        case JobState.Processing:
                            Console.WriteLine("Please wait...\n");
                            break;
                        case JobState.Canceled:
                        case JobState.Error:
        
                            // Cast sender as a job.
                            IJob job = (IJob)sender;
        
                            // Display or log error details as needed.
                            break;
                        default:
                            break;
                    }
                }
        
        
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
                    return processor;
                }
        
            }
        }


##<a name="support-for-relative-sizes"></a>支援相對的大小

產生的縮圖，當您不需要永遠指定像素為單位的輸出寬度和高度。 您可以在百分比，[1%、 100%] 範圍中加以指定。

###<a name="json-preset"></a>JSON 預設格式] 
    
    "Width": "100%",
    "Height": "100%"

###<a name="xml-preset"></a>XML 預設格式]
    
    <Width>100%</Width>
    <Height>100%</Height>
    
##<a id="thumbnails"></a>產生的縮圖

本節說明如何自訂預設會產生的縮圖。 定義如下的預設會包含您想要編碼您檔案，以及產生的縮圖所需的資訊的方式的詳細資訊。 您可以採取下列任一 MES 預設記錄[以下](https://msdn.microsoft.com/library/mt269960.aspx)，將會產生的縮圖的程式碼。  

>[AZURE.NOTE]在下列預設可以只**SceneChangeDetection**設定設為 true，如果您以單一位元率視訊編碼。 如果您多重位元率視訊編碼， **SceneChangeDetection**設為 true，則編碼器會傳回錯誤。  


結構描述的相關資訊，請參閱[本](https://msdn.microsoft.com/library/mt269962.aspx)主題。

請確定檢閱[考量](media-services-custom-mes-presets-with-dotnet.md#considerations)一節。

###<a id="json"></a>JSON 預設格式]


    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
       
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": 640,
              "Height": 360,
            }
          ],
          "Start": "00:00:01",
          "Step": "00:00:10",
          "Range": "00:00:58",
          "Type": "PngImage"
        },
        {
          "BmpLayers": [
            {
              "Type": "BmpLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "10%",
          "Step": "10%",
          "Range": "90%",
          "Type": "BmpImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "BmpFormat"
          }
        },
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


###<a id="xml"></a>XML 預設格式]


    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>640</Width>
              <Height>360</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
        <BmpImage Start="10%" Step="10%" Range="90%">
          <BmpLayers>
            <BmpLayer>
              <Width>640</Width>
              <Height>360</Height>
            </BmpLayer>
          </BmpLayers>
        </BmpImage>
        <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
          <PngLayers>
            <PngLayer>
              <Width>640</Width>
              <Height>360</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <BmpFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

###<a name="considerations"></a>考量

下列事項︰

- 使用明確的時間戳記的開始/步驟/範圍假設至少 1 分鐘長輸入的來源。
- 已啟動] 步驟中，Jpg/Png/BmpImage 項目，而範圍字串屬性 – 這些可能會解讀為︰

    - 如果有非負整數外框數字，例如 [開始]: 「 120 」
    - 相對如果表示為的 %加來源持續時間，例如 [開始]:"15%"，或
    - 如果以 ss 時間戳記... 設定格式，例如 [開始]: 「 00: 01:00 」

    您可以混合，並請符合您的標記法。
    
    此外，啟動也支援特殊的巨集: {最佳}，其會判斷內容的附註的第一個 「 有趣 」 畫面: （步驟範圍會忽略和啟動設定為 [{最佳} 時）
    
    - 預設值︰ 開始: {最佳}
- 輸出格式需要明確提供的每個圖像格式︰ Jpg/Png/BmpFormat。 時，MES 符合 JpgVideo 至 JpgFormat 等等。 OutputFormat 會介紹新圖像轉碼器特定巨集: {Index}、 哪些需要展示 （一次與一次） 圖像輸出格式。

##<a id="trim_video"></a>修剪視訊 （剪輯）

本節說話修改 encoder 預設格式] 來裁剪或修剪輸入是所謂的 mezzanine 檔案或指定檔案的位置輸入的視訊的相關。 編碼器也會用於裁剪或修剪資產，擷取或封存的即時資料流 – 詳細資料可在[此部落格](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)。

若要修剪視訊，您可以採取下列任一 MES 預設記錄[以下](https://msdn.microsoft.com/library/mt269960.aspx)，修改**來源**元素 （如下圖所示）。 開始時間的值必須以符合絕對輸入視訊的時間戳記。 例如，如果第一個畫面，輸入視訊的時間戳記的 12:00:10.000，然後開始時間必須至少 12:00:10.000 和大於。 在下面的範例中，我們假設輸入的視訊的零開始的時間戳記。 **來源**必須放在預設的開頭。 
 
###<a id="json"></a>JSON 預設格式]
    
    {
      "Version": 1.0,
      "Sources": [
        {
          "StartTime": "00:00:04",
          "Duration": "00:00:16"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1280,
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
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1500,
              "MaxBitrate": 1500,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1000,
              "MaxBitrate": 1000,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 650,
              "MaxBitrate": 650,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 400,
              "MaxBitrate": 400,
              "BufferWindow": "00:00:05",
              "Width": 320,
              "Height": 180,
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

###<a name="xml-preset"></a>XML 預設格式]
    
若要修剪視訊，您可以採取下列任一 MES 預設記錄[以下](https://msdn.microsoft.com/library/mt269960.aspx)，修改**來源**元素 （如下圖所示）。

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source StartTime="PT4S" Duration="PT14S"/>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>3400</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>3400</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>2250</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>2250</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1500</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1500</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1000</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1000</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>650</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>650</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>400</Bitrate>
              <Width>320</Width>
              <Height>180</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>400</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>

##<a id="overlay"></a>建立重疊

媒體 Encoder 標準可讓您覆疊圖像到現有的視訊。 目前支援下列格式︰ gif、，jpg、 png 及 bmp。 下面定義的預設格式是視訊覆疊的基本範例。

除了定義預設的檔案，您也可以讓知道您要覆疊圖像檔案的資產覆疊影像，而檔案會視訊來源媒體服務。 視訊檔案必須是**主要**的檔案。 

.NET 上述範例中定義兩個功能︰ **UploadMediaFilesFromFolder**和**EncodeWithOverlay**。 UploadMediaFilesFromFolder 函數上傳檔案的資料夾 （例如，BigBuckBunny.mp4 和 Image001.png），且設定為主要的檔案中的資產的 mp4 檔案。 **EncodeWithOverlay**函數會使用自訂的預設的檔案已傳遞至該 （例如，下列預設） 若要建立編碼的工作。 

>[AZURE.NOTE]目前的限制︰
>
>不支援重疊透明度設定。
>
>來源視訊檔案和覆疊圖像檔案位於相同的資產，而且必須與主要的檔案，這項資產中設定視訊檔案。

###<a name="json-preset"></a>JSON 預設格式]
    
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "VideoOverlay": {
              "Position": {
                "X": 100,
                "Y": 100,
                "Width": 100,
                "Height": 50
              },
              "AudioGainLevel": 0.0,
              "MediaParams": [
                {
                  "OverlayLoopCount": 1
                },
                {
                  "IsOverlay": true,
                  "OverlayLoopCount": 1,
                  "InputLoop": true
                }
              ],
              "Source": "Image001.png",
              "Clip": {
                "Duration": "00:00:05"
              },
              "FadeInDuration": {
                "Duration": "00:00:01"
              },
              "FadeOutDuration": {
                "StartTime": "00:00:03",
                "Duration": "00:00:04"
              }
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
              "Bitrate": 1045,
              "MaxBitrate": 1045,
              "BufferWindow": "00:00:05",
              "ReferenceFrames": 3,
              "EntropyMode": "Cavlc",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Type": "CopyAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}{Extension}",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


###<a name="xml-preset"></a>XML 預設格式]
    
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source>
          <Streams />
          <Filters>
            <VideoOverlay>
              <Source>Image001.png</Source>
              <Clip Duration="PT5S" />
              <FadeInDuration Duration="PT1S" />
              <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
              <Position X="100" Y="100" Width="100" Height="50" />
              <Opacity>0</Opacity>
              <AudioGainLevel>0</AudioGainLevel>
              <MediaParams>
                <MediaParam>
                  <IsOverlay>false</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>false</InputLoop>
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>true</InputLoop>
                </MediaParam>
              </MediaParams>
            </VideoOverlay>
          </Filters>
          <Pad>true</Pad>
        </Source>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>1045</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>0</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cavlc</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1045</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <CopyAudio />
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}{Extension}">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>


##<a id="silent_audio"></a>插入無音訊追蹤時輸入具有沒有音訊

根據預設，如果您傳送至編碼器只包含視訊和沒有音訊輸入然後輸出資產包含檔案包含唯一視訊的資料。 某些播放程式可能無法處理這種輸出資料流。 若要強制編碼器新增無音訊到輸出在這個案例中，您可以使用這項設定。

若要強制編碼器產生時輸入具有沒有音訊包含無音訊追蹤資產，指定 「 InsertSilenceIfNoAudio 」 值。

您可以採取下列任一 MES 預設記錄[以下](https://msdn.microsoft.com/library/mt269960.aspx)，並進行下列修改︰

###<a name="json-preset"></a>JSON 預設格式]

    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

###<a name="xml-preset"></a>XML 預設格式]

    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

##<a id="deinterlacing"></a>停用自動去交錯

客戶，不需要執行任何動作，如果要自動就能取消交錯 interlace 內容。 自動去交錯] 時 （預設） MES 交錯的圖文框的自動偵測並僅就能取消 interlaces 標示為交錯的外框。

您可以關閉自動去交錯。 不建議使用此選項。

###<a name="json-preset"></a>JSON 預設格式]
    
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

###<a name="xml-preset"></a>XML 預設格式]
    
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


##<a id="audio_only"></a>純音訊的預設格式

本節說明兩個純音訊 MES 預設格式︰ AAC 音訊和 AAC 好品質音訊。

###<a name="aac-audio"></a>AAC 音訊 

    {
      "Version": 1.0,
      "Codecs": [
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
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

###<a name="aac-good-quality-audio"></a>AAC 高品質音訊

    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

##<a id="concatenate"></a>串連兩個或多個視訊檔案

下列範例會說明如何產生預設格式] 來串連兩個或多個視訊檔案。 您想要將頁首或結尾新增至主要的視訊時最常見的案例。 想要的使用時，共同編輯的視訊檔案共用的內容 （視訊的解析度、 框架工資率、 音訊追蹤計數等）。 您應該注意不要混合不同框架費率或曲目的數目不同的影片。

###<a name="requirements-and-considerations"></a>需求和考量

- 輸入的視訊只應有一個音訊的追蹤。
- 輸入的視訊應該有相同的圖文框工資率。
- 您必須將視訊上傳到另一個資產，並將視訊設為每個資產的主檔案。
- 您必須知道您的視訊的持續時間。
- 預設的下面的範例假設所有輸入的視訊開頭為零的時間戳記。 您需要修改的開始時間值，如果視訊有不同的開始時間戳記，通常是即時封存的大小寫。
- JSON 預設可讓您明確的輸入資產 AssetID 值的參照。
- 在 [程式碼範例假設 JSON 預設的已儲存至本機的檔案，例如 「 C:\supportFiles\preset.json 」。 也假設兩個資產都由上傳視訊的兩個檔案，並且您知道結果 AssetID 值。
- 程式碼片段並 JSON 預設顯示範例串連兩個視訊檔案。 您可以將它延伸由兩個以上的視訊︰

    1. 電話工作。InputAssets.Add() 鍵，順序來新增更多影片。
    2. 進行對應 「 來源 」 中的項目 JSON，編輯同一訂單中新增更多項目]。 


###<a name="net-code"></a>.NET 程式碼

    
    IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
    IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();
    
    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
    // Get a media processor reference, and pass to it the name of the 
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
    
    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");
    
    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);
    
    // Specify the input videos to be concatenated (in order).
    task.InputAssets.Add(asset1);
    task.InputAssets.Add(asset2);
    // Add an output asset to contain the results of the job. 
    // This output is specified as AssetCreationOptions.None, which 
    // means the output asset is not encrypted. 
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);
    
    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

###<a name="json-preset"></a>JSON 預設格式]

更新您的自訂預設與您想要串連的資產的識別碼，以及與每個視訊適當的時間區段。

    {
      "Version": 1.0,
      "Sources": [
        {
          "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
          "StartTime": "00:00:01",
          "Duration": "00:00:15"
        },
        {
          "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
          "StartTime": "00:00:02",
          "Duration": "00:00:05"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [
            {
              "Level": "auto",
              "Bitrate": 1800,
              "MaxBitrate": 1800,
              "BufferWindow": "00:00:05",
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
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

##<a id="crop"></a>裁剪視訊與媒體 Encoder 標準

請參閱[裁剪視訊與媒體 Encoder 標準](media-services-crop-video.md)主題。

##<a id="no_video"></a>輸入有視訊時，請插入視訊的追蹤

根據預設，如果您傳送至編碼器只包含音訊和沒有影片中，輸入然後輸出資產包含檔案包含只音訊的資料。 某些播放機，包括 Azure Media Player （請參閱[此](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)） 可能無法處理這種資料流。 若要強制編碼器新增單色視訊到輸出在這個案例中，您可以使用這項設定。 

>[AZURE.NOTE]強制以插入輸出視訊追蹤 encoder 增加的輸出大小資產，，藉此成本所產生的編碼的工作。 您應該執行測試，以驗證此結果增加有只太大的影響，在您每月費用。

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>在 [僅最低的位元率插入影片

假設您使用多個位元率編碼例如預設[」 H264 多個位元率 720p 」](https://msdn.microsoft.com/library/mt269960.aspx)編碼整個輸入的類別目錄串流，其中包含的視訊檔案及純音訊檔案。 在此案例中時輸入有視訊，, 您可能要強制編碼器單色視訊追蹤插入只最低位元率，而不是在每個輸出位元率插入視訊。 若要達到此目標，必須指定 「 InsertBlackIfNoVideoBottomLayerOnly 」 標幟。

您可以採取下列任一 MES 預設記錄[以下](https://msdn.microsoft.com/library/mt269960.aspx)，並進行下列修改︰

#### <a name="json-preset"></a>JSON 預設格式]

    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML 預設格式]

    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <StretchMode>AutoSize</StretchMode>
    <Condition>InsertBlackIfNoVideoBottomLayerOnly</Condition>

### <a name="inserting-video-at-all-output-bitrates"></a>在插入影片輸出 bitrates

假設您使用多個位元率編碼例如預設[」 H264 多個位元率 720p](https://msdn.microsoft.com/library/mt269960.aspx)編碼整個輸入的類別目錄串流，其中包含的視訊檔案及純音訊檔案。 在此案例中時輸入有視訊，, 您可能要強制編碼器插入完全輸出 bitrates 單色視訊追蹤。 這可確保您的輸出資產的所有同質性提供視訊的追蹤及音軌數目解答。 若要達到此目標，必須指定 「 InsertBlackIfNoVideo 」 標幟。

您可以採取下列任一 MES 預設記錄[以下](https://msdn.microsoft.com/library/mt269960.aspx)，並進行下列修改︰

#### <a name="json-preset"></a>JSON 預設格式]

    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML 預設格式]
    
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <StretchMode>AutoSize</StretchMode>
    <Condition>InsertBlackIfNoVideo</Condition>

##<a id="rotate_video"></a>旋轉視訊

[標準媒體編碼器](media-services-dotnet-encode-with-media-encoder-standard.md)支援 0/90/180/270 的旋轉角度。 預設行為是 「 自動 」，它嘗試偵測傳入的視訊檔案中的 [旋轉中繼資料和其賠償。 包含下列**來源**元素的 [預設格式定義[以下](http://msdn.microsoft.com/library/azure/mt269960.aspx)其中一個︰

### <a name="json-preset"></a>JSON 預設格式]

    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [
    
    ...
### <a name="xml-preset"></a>XML 預設格式]

    <Sources>
        <Source>
          <Streams />
          <Filters>
            <Rotation>90</Rotation>
          </Filters>
        </Source>
    </Sources>

請參閱[本](https://msdn.microsoft.com/library/azure/mt269962.aspx#PreserveResolutionAfterRotation)主題，如需詳細資訊上觸發旋轉補償時編碼器解譯預設格式] 的寬度和高度設定的方式。

您可以使用 「 0 」 的值可忽略旋轉中繼資料，如果有的話，輸入視訊中 encoder。 


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>另請參閱 

[媒體服務編碼的概觀](media-services-encode-asset.md)
