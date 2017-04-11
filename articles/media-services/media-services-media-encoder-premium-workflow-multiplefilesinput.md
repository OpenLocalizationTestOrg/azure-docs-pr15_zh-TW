<properties
    pageTitle="進階版 Encoder 中使用多個輸入的檔案和元件屬性 |Microsoft Azure"
    description="本主題說明如何使用多個輸入的檔案，並將自訂資料傳遞到 Media Encoder 進階版工作流程媒體處理器使用 setRuntimeProperties。"
    services="media-services"
    documentationCenter=""
    authors="xpouyat"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"  
    ms.author="xpouyat;anilmur;juliako"/>

# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>進階版 Encoder 中使用多個輸入的檔案和元件屬性

## <a name="overview"></a>概觀

有可讓您可能需要自訂元件屬性指定多媒體項目清單中的 XML 內容，或傳送輸入的多個檔案，當您送出**Media Encoder 進階版工作流程**媒體處理器工作的案例。 以下是一些範例︰

- 在視訊以及設定文字值 （例如，目前日期） 在執行階段每個輸入視訊的重疊文字。
- 自訂多媒體項目清單 XML （以指定一或多個來源檔案，包含或不含修整等等。）。
- 視訊編碼時，請在輸入視訊重疊標誌圖像。

若要讓知道您要變更工作流程中的某些屬性，當您建立的工作，或傳送輸入的多個檔案，您必須使用設定字串的**媒體 Encoder 進階版工作流程**包含**setRuntimeProperties**及/或**transcodeSource**。 本主題說明如何使用它們。


## <a name="configuration-string-syntax"></a>設定字串的語法

若要設定編碼工作中的設定字串使用 XML 文件看起來像這樣︰

    <?xml version="1.0" encoding="utf-8"?>
    <transcodeRequest>
      <transcodeSource>
      </transcodeSource>
      <setRuntimeProperties>
        <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      </setRuntimeProperties>
    </transcodeRequest>


以下是 C# 程式碼，從檔案讀取 XML 設定，並將其傳遞至中工作的工作︰

    XDocument configurationXml = XDocument.Load(xmlFileName);
    IJob job = _context.Jobs.CreateWithSingleTask(
                                                  "Media Encoder Premium Workflow",
                                                  configurationXml.ToString(),
                                                  myAsset,
                                                  "Output asset",
                                                  AssetCreationOptions.None);


## <a name="customizing-component-properties"></a>自訂元件屬性  

### <a name="property-with-a-simple-value"></a>使用簡單的值] 屬性
在某些情況下，是很有用自訂元件屬性與前往 Media Encoder 進階版工作流程中執行的工作流程檔案。

假設您在您的視訊，設計工作流程的重疊文字應該會在執行階段設定的文字 （例如，目前日期）。 您可以傳送編碼工作設定為 [text] 屬性的覆疊元件的新值的文字。 若要變更的元件 （例如位置或重疊的色彩、 位元速率 AVC encoder 等等。） 工作流程中的其他屬性，您可以使用這個機制。

**setRuntimeProperties**用來覆寫中的工作流程元件的屬性。

範例︰

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Optional Overlay/Overlay/filename" value="MyLogo.png"/>
          <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
      </setRuntimeProperties>
    </transcodeRequest>


### <a name="property-with-an-xml-value"></a>使用 XML 值屬性

若要設定的預期 XML 值的屬性，封裝使用`<![CDATA[ and ]]>`。

範例︰

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

>[AZURE.NOTE]請確定您不只之後將輸入鍵符號傳回`<![CDATA[`。


### <a name="propertypath-value"></a>propertyPath 值

在上一個範例中，已 propertyPath 」 / 媒體檔案輸入/檔案名稱 」 或 「 / inactiveTimeout 」 或 「 clipListXml 」。
一般而言，這是元件的名稱，然後屬性的名稱。 路徑可以想擁有更多或較少的層級，「 / primarySourceFile 」 （因為屬性是工作流程的根） 或 「 / 視訊處理/圖形覆疊不透明 」 （因為覆疊] 群組中）。    

若要檢查的路徑和屬性名稱，請使用會立即出現在每個屬性旁邊的 [動作] 按鈕。 您可以按一下此動作] 按鈕，並選取 [**編輯**]。 這會顯示實際名稱的屬性，並立即其命名空間上方。

![巨集指令或編輯](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![屬性](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>輸入的多個檔案

提交給**Media Encoder 進階版工作流程**工作之每項工作需要兩個資產︰

- 第一個是包含的工作流程檔案的*工作流程資產*。 您可以使用[工作流程設計工具](media-services-workflow-designer.md)設計工作流程的檔案。
- 第二個是包含您想要編碼的媒體檔案的*媒體資產*。

當您要傳送多個媒體檔案以**Media Encoder 進階版工作流程**encoder 時，下列條件約束套用︰

- 所有的媒體檔案必須在同一個*媒體資產*。 不支援使用多個媒體資產。
- 您必須在此媒體資產設定主要的檔案 （理想的情況下，這是主編碼器要求處理程序的視訊檔案）。
- 就必須傳遞設定資料，其中包含處理器**setRuntimeProperties**及/或**transcodeSource**項目。
  - **setRuntimeProperties**用來覆寫檔案名稱] 屬性或另一個屬性中的工作流程元件。
  - **transcodeSource**用來指定的多媒體項目清單中的 XML 內容。

工作流程中的連線︰

 - 如果您使用一或多個媒體檔案輸入元件和計劃使用**setRuntimeProperties**指定的檔案名稱，然後不主要檔案元件 pin 進行連線。 請確定沒有主要檔案物件和媒體檔案 Input(s) 之間的連線。
 - 如果您想要使用多媒體項目清單 XML 和一個媒體來源元件，然後您可以連線兩者都放在一起。

![從主要的來源檔案沒有連線媒體檔案的輸入](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*如果您是使用 setRuntimeProperties 設定檔名] 屬性，沒有從主要的檔案連線至媒體檔案輸入元件。*

![從多媒體項目清單 XML 剪輯清單來源的連線](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*您可以連線至媒體來源的多媒體項目清單 XML，並使用 transcodeSource。*


### <a name="clip-list-xml-customization"></a>剪輯清單 XML 自訂
您可以指定在執行階段工作流程中的多媒體項目清單 XML 設定字串 XML 中使用**transcodeSource** 。 需要連線至工作流程中的媒體來源元件的多媒體項目清單 XML pin。

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <transcodeSource>
          <clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
          </clipList>
        </transcodeSource>
        <setRuntimeProperties>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

如果您想要指定名稱的輸出檔案，使用 [運算式] 使用此屬性的 /primarySourceFile，我們建議傳送多媒體項目清單 XML，做為屬性*之後*/primarySourceFile 屬性，若要避免覆寫 /primarySourceFile 設定的 [美工圖案] 清單。

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

與其他的圖文框精確調整︰

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
               <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


## <a name="example"></a>範例

請考慮您要同時視訊編碼重疊上輸入視訊的標誌圖像的範例。 在此範例中，輸入的視訊名稱為 「 MyInputVideo.mp4 」，標誌名稱為 「 MyLogo.png 」。 您應該執行下列步驟︰

- 使用工作流程檔案建立工作流程資產 （請參閱下列範例）。
- 建立多媒體資產，其中包含兩個檔案︰ 為主要的檔案和 MyLogo.png MyInputVideo.mp4。
- 將工作傳送給上述輸入資產 Media Encoder 進階版工作流程媒體處理器，並指定下列設定字串。

設定︰

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="MyLogo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


在上述範例中，視訊檔案的名稱會傳送至媒體檔案輸入元件和 primarySourceFile 屬性。 標誌檔案的名稱會傳送至已連線至圖形的覆疊元件的其他媒體檔案輸入。

>[AZURE.NOTE]視訊檔案名稱會傳送至 primarySourceFile 屬性。 原因是建置正確的輸出檔案的名稱，例如使用運算式，請使用此屬性中的工作流程。


### <a name="step-by-step-workflow-creation-that-overlays-a-logo-on-top-of-the-video"></a>建立逐步工作流程的覆疊的標誌上方視訊     

若要建立使用兩個檔案做為輸入工作流程的步驟如下︰ 視訊和圖像。 它會覆疊在視訊的圖像。

開啟**工作流程設計工具**，然後選取**檔案** > **新的工作區** > **轉碼藍圖**。

新的工作流程顯示三個項目︰

- 主要的來源檔案
- XML 的多媒體項目清單
- 輸出檔案/資產  

![新編碼的工作流程](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*新編碼的工作流程*


若要接受輸入的媒體檔案，請開始新增媒體檔案輸入元件。 若要新增工作流程元件，存放庫的 [搜尋] 方塊中，尋找並拖曳所要的項目拖曳到設計工具窗格。

接下來，新增要用於設計您的工作流程的視訊檔案。 若要這麼做，請按一下 [工作流程設計工具中的 [背景] 窗格並尋找右側的屬性窗格的主要的來源檔案屬性。 按一下資料夾圖示，然後選取適當的視訊檔案。

![主要的檔案來源](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*主要的檔案來源*


接下來，指定視訊檔案中的媒體檔案輸入元件。   

![媒體檔案輸入的來源](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*媒體檔案輸入的來源*


一旦完成後請媒體檔案輸入元件會檢查檔案，並填入其輸出 pin，以反映該檢查的檔案。

下一步是新增 「 視訊資料類型更新器 」 來指定 Rec.709 色彩空間。 新增 「 視訊格式轉換程式 」 的設定為 [版面配置的資料類型 = 哪個平面。 這會將視訊資料流轉換的格式，可以做為覆疊元件的來源。

![視訊的資料類型的更新程式和格式轉換程式](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*視訊的資料類型的更新程式和格式轉換程式*

![版面配置類型 = 哪個平面](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*版面配置類型是哪個平面*

接下來，新增視訊覆疊元件，並將 （壓縮） 的視訊 pin 連線至 （壓縮） 的視訊釘選的媒體檔案輸入。

新增其他媒體檔案輸入 （載入標誌檔案），這個元件上按一下 [重新命名為 「 媒體檔案輸入標誌 」，並選取 [檔案] 屬性中的圖像 （例如.png 檔案）。 連接壓縮的圖像釘選到壓縮的圖像 pin 的覆疊。

![重疊元件和圖像的檔案來源](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*重疊元件和圖像的檔案來源*


如果您想要修改標誌出現在視訊的位置 （例如，您可能會想，將它放置在人員視訊的左上角的 10%），清除 [手動輸入 」] 核取方塊。 因為您使用的媒體檔案輸入提供標誌檔案的覆疊元件，您可以執行此動作。

![重疊的位置](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*重疊的位置*


若要編碼 H.264 的視訊資料流，新增 AVC 視訊 Encoder 和 AAC encoder 元件設計工具介面。 連線的 pin。
設定 AAC encoder，然後選取 [音訊格式轉換/預設︰ 2.0 （L、 R）。

![音訊和視訊編碼器](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*音訊和視訊編碼器*


現在新增**ISO mpeg-4 多工器**與**檔案輸出**元件，並連接的 pin，如下圖所示。

![MP4 多工器與檔案輸出](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 多工器與檔案輸出*


您需要設定輸出檔案的名稱。 按一下 [**檔案輸出**元件和編輯檔案的運算式︰

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![輸出的檔案名稱](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*輸出的檔案名稱*

您可以執行本機若要查看正在順利執行工作流程。

完成後，您可以在 Azure 媒體服務中執行。

首先，準備資產 Azure 媒體服務中的使用了兩個檔案︰ 視訊檔案和標誌。 您可以使用.NET 或 REST API 來執行這個動作。 您也可以執行此使用 Azure 入口網站或[Azure 媒體服務總管](https://github.com/Azure/Azure-Media-Services-Explorer)(AMSE)。

本教學課程教您如何管理資產與 AMSE。 有兩種方式將檔案新增到資產︰

- 建立資料夾、 複製兩個檔案，和拖放到 [**資產**] 索引標籤的資料夾。
- 上傳的視訊檔案，為資產，顯示 [資產] 資訊、 移至 [檔案] 索引標籤，然後上傳額外檔案 （標誌）。

>[AZURE.NOTE]請確認主要的檔案集資產 （主要的視訊檔案）。

![資產 AMSE 檔案](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*資產 AMSE 檔案*


選取 [資產，並選擇使用進階版 Encoder 編碼，因此。 上傳工作流程並加以選取。

按一下按鈕，將資料傳遞到處理器，並新增下列 XML 來設定執行階段屬性︰

![在 [AMSE 進階版 Encoder](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*在 [AMSE 進階版 Encoder*


然後，貼上下列的 XML 資料。 您需要的媒體檔案輸入和 primarySourceFile 指定視訊檔案的名稱。 太指定的檔案名稱標誌的名稱。

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*


如果您使用.NET SDK 來建立與執行工作時，必須設定字串形式傳送此 XML 資料。

    public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);

完成工作之後，輸出資產的 MP4 檔案會顯示重疊 ！

![在視訊上的重疊](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*在視訊上的重疊*


您可以從[GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/)下載的範例工作流程。


## <a name="see-also"></a>另請參閱

- [介紹編碼 Azure 媒體服務的進階版](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

- [如何使用 Azure 媒體服務中的 [進階版編碼](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

- [Azure 媒體服務編碼視內容](media-services-encode-asset.md#media_encoder_premium_workflow)

- [媒體 Encoder 進階版工作流程格式及轉碼器](media-services-premium-workflow-encoder-formats.md)

- [範例工作流程檔案](https://github.com/AzureMediaServicesSamples/Encoding-Presets/tree/master/VoD/MediaEncoderPremiumWorkfows)

- [Azure 媒體服務總管] 工具](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
