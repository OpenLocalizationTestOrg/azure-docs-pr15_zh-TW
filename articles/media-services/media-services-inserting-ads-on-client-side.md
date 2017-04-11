<properties 
    pageTitle="插入在用戶端的廣告 |Microsoft Azure" 
    description="本主題說明如何插入廣告在用戶端。" 
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
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="inserting-ads-on-the-client-side"></a>插入廣告在用戶端

本主題包含如何插入各種類型的廣告在用戶端的詳細資訊。

關閉式輔助字幕和 ad 支援 Live 串流影片的相關資訊，請參閱[字幕支援與 Ad 插入標準](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads)。

>[AZURE.NOTE] Azure Media Player 目前不支援廣告。

##<a id="insert_ads_into_media"></a>廣告插入媒體

Azure 媒體服務提供支援透過 Windows Media 平台插入廣告︰ 播放程式架構。 Ad 支援的播放程式架構，可用於 Windows 8、 Silverlight、 Windows Phone 8、 和 iOS 裝置。 每個播放程式架構包含範例將示範如何實作播放程式的應用程式。有三種不同的廣告您可以插入媒體︰ 清單。

- **等差級數**– 暫停主視訊的完整的圖文框廣告。
- **Nonlinear** – 為主要的視訊播放時所顯示的覆疊廣告、 通常標誌或其他靜態圖像會放在播放程式。
- **小幫手**– 會顯示播放程式以外的廣告。

廣告可以放在主要視訊的時間列上的任一點。 您必須在何時播放 ad，若要播放的廣告告訴播放程式。 這是使用一組標準的 xml 檔案︰ 視訊 Ad 服務範本 (VAST)、 數位影片多個 Ad 播放清單 (VMAP)、 媒體抽象排序範本 （第） 及數位視訊播放程式 Ad 介面定義 (VPAID)。 大量檔案指定要顯示哪些廣告。 VMAP 檔案，指定何時播放各種廣告及包含大量的 XML。 第檔案會在另一種方法其中也可以包含大量 XML 順序廣告。 VPAID 檔案定義的視訊播放程式和 ad 或 ad 伺服器之間介面。

每個播放程式架構的運作方式不同，並將它自己的主題中涵蓋每。 本主題將說明用來插入廣告基本機制。視訊播放程式應用程式要求廣告從 ad 伺服器。 Ad 伺服器的回應數種方式︰

- 傳回大量檔案
- 傳回 VMAP 使用的檔案 （內嵌 VAST)
- 傳回第使用的檔案 （內嵌 VAST)
- 傳回 VPAID 廣告大量檔案

 
###<a name="using-a-video-ad-service-template-vast-file"></a>使用視訊 Ad 服務範本 (VAST) 檔案

大量檔案指定哪些 ad 或廣告以顯示。 下列 XML 是線性的廣告大量檔案的範例︰
    
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
    
所述的線性 ad 是**<Linear>**項目。 它會指定廣告的工期、 追蹤事件，按一下 [透過按一下 [追蹤] 和數字的**<MediaFile>**項目。 追蹤事件指定在**<TrackingEvents>**項目，並允許追蹤各種檢視 ad 時所發生的事件 ad 伺服器。 在這種情況下開始]，[中間點]，完成，然後展開追蹤事件。 開始事件發生時，廣告顯示。 中間點事件發生至少已檢視 50%的廣告的時間表。 完成的事件發生時 ad 執行結尾。 展開事件發生於使用者至全螢幕擴充的視訊播放程式。 會指定 Clickthroughs**<ClickThrough>**內的項目**<VideoClicks>**項目，並指定給資源以顯示當使用者在 ad URI。 中指定 ClickTracking**<ClickTracking>**項目，也中**<VideoClicks>**項目，並指定追蹤資源以要求使用者在 ad 時播放程式。**<MediaFile>**項目指定特定的廣告編碼的相關資訊。 有超過一個時**<MediaFile>**項目，視訊播放程式可以選擇的平台的最佳編碼方式。 

線性廣告可以顯示指定的順序。 若要這麼做，新增其他<Ad>元素以 VAST 檔案，並指定順序使用循序屬性。 下列範例說明這點︰
    
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
    
指定非線性廣告<Creative>項目。 下列範例所示<Creative>說明非線性 ad 的項目。

    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>

 
**<NonLinearAds>**項目可以包含下列一或多**<NonLinear>**元素，每一種可用來描述非線性 ad。 **<NonLinear>**項目會指定非線性 ad 的資源。 資源可**<StaticResouce>**、 **<IFrameResource>**，或是**<HTMLResouce>**。**<StaticResource>** 說明非 HTML 資源，並定義 creativeType 屬性來指定顯示資源的方式︰

圖像 gif、 jpeg 圖像/圖像/png – 資源會顯示在 HTML**<img>**標籤。

應用程式/x-javascript – 資源會顯示在 HTML <**指令碼**> 標籤。

應用程式/x-shockwave-flash – 資源會顯示在 Flash player。

**<IFrameResource>**將 HTML 資源的 IFrame 中所顯示的說明。 **<HTMLResource>**說明一段的網頁中插入 HTML 程式碼。 **<TrackingEvents>**指定追蹤活動和事件發生時要求 URI。 在這個範例中追蹤的 acceptInvitation 及摺疊事件。 如需有關**<NonLinearAds>**項目和子項，請參閱 IAB.NET/VAST。 請注意，**<TrackingEvents>**項目是位於**<NonLinearAds>**項目，而非**<NonLinear>**項目。

內所定義的小幫手廣告<CompanionAds>項目。 <CompanionAds>項目可以包含下列一或多<Companion>項目。 每個<Companion>說明小幫手 ad 項目，並可以包含<StaticResource>， <IFrameResource>，或<HTMLResource>其指定非線性 ad 方式相同。 大量檔案最多可以包含多個小幫手廣告和播放程式應用程式，可以選擇最適合 ad 以顯示。 如需有關 VAST 的詳細資訊，請參閱[大量 3.0](http://www.iab.net/media/file/VASTv3.0.pdf)。

###<a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>使用多個 Ad 播放清單 (VMAP) 檔案的數位視訊

VMAP 檔案可讓您指定當 ad 符號、 每個符號是多久、 多少廣告可以顯示在 [分隔設定]，以及類型的廣告可能會中斷時顯示。 定義單一 ad 符號的範例 VMAP 檔案中的下列︰
    
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[http://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scaleable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
     
VMAP 檔案的開頭<VMAP>包含一或多項目<AdBreak>元素，每個定義 ad 符號。 每個 ad 符號指定符號類型符號識別碼與時間位移。 BreakType 屬性指定 ad 可播放期間符號的類型︰ 線性、 非線性，或顯示。 顯示大量的小幫手廣告廣告地圖。 逗點 （不加空格） 分隔清單中，就可以指定一個以上的 ad 類型。 BreakID 是選擇性的廣告識別碼。 TimeOffset 指定時顯示 ad。 它可以指定下列方法之一︰

1. 時間-ss 或 hh:mm:ss.mmm.mmm 哪裡毫秒為單位的格式。 此屬性的值指定視訊的時間表的開頭移至 ad 符號開頭的時間。
1. 百分比-n %格式其中 n 是視訊時間表的百分比之前播放 ad 播放
1. 開始/結束 – 指定之前或之後已顯示視訊，來顯示廣告
1. 將-ad 分頁符號的時間不明，例如即時資料流時，指定 ad 分頁符號的順序。 其中 n 是整數，大於或等於 1 #n 格式中指定的每個 ad 符號順序。 1 代表應該播放 ad 第一次的機會，在 2 代表 ad 應該播放在第二個機會等等。

在 <**AdBreak**> 項目可以是一個 <**AdSource**> 項目。 <**AdSource**> 項目會包含下列屬性︰

1. 識別碼 – 指定 ad 來源識別項
1. allowMultipleAds – 一個 Boolean 值，指定是否可顯示多個廣告期間 ad 符號
1. followRedirects – 選用的布林值，指定視訊的播放程式是否應該接受重新導向內 ad 回應

內嵌 ad 回應或 ad 回應的參照，<**AdSource**> 項目會提供播放程式。 它可以包含下列元素的其中一個︰

- <VASTAdData>指出大量 ad 回應內嵌於 VMAP 檔案
- <AdTagURI>從另一個系統參照 ad 回應 URI
- <CustomAdData>-任意字串，代表非大量的回應

在此範例中使用指定的內嵌 ad 回應<VASTAdData>包含大量 ad 回應項目。 如需其他元素的詳細資訊，請參閱[VMAP](http://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap)。

<**AdBreak**> 項目也可以包含一個 <**TrackingEvents**> 項目。 <**TrackingEvents**> 元素可讓您追蹤的開始或結束 ad 符號，或是否 ad 符號時，發生錯誤。 <**TrackingEvents**> 項目包含一或多個 <**追蹤**> 元素，每個指定追蹤事件，以及追蹤 URI。 可能的追蹤事件為︰

1. breakStart – 追蹤 ad 符號的開頭
1. breakEnd – 追蹤完成的 ad 符號
1. 錯誤-追蹤 ad 符號時，發生錯誤

下列範例顯示的指定追蹤事件 VMAP 檔案

    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>

如需有關 <**TrackingEvents**> 項目，且及其下層的詳細資訊，請參閱 http://iab.org/VMAP.pdf

###<a name="using-a-media-abstract-sequencing-template-mast-file"></a>使用媒體抽象排序範本 （第） 檔案

第檔案可讓您指定的定義廣告時顯示觸發程序。 以下是包含引動程序前的相簿 ad、 更換相簿 ad，及後的相簿 ad 範例第檔案。

    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
    
        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
    
        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>

 

第檔案的開頭**<MAST>**包含一個項目**<triggers>**項目。 <triggers>項目包含一或多**<trigger>**定義 ad 應該播放時的項目。 

**<trigger>**項目包含**<startConditions>**指定當 ad 應該會開始播放的項目。 **<startConditions>**項目包含一或多<condition>項目。 當每個<condition>評估為 true 觸發程序會啟動，或撤銷根據是否<condition>包含在**< startConditions**> 或**<endConditions>**元素分別。 當多個<condition>項目會出現，它們會被視為隱含或、 評估為 true 的任何條件會觸發程序進行。 <condition>可以巢狀項目。 當子<condition>預設的項目，它們會被視為隱含與，所有條件必須都評估為 true，若要啟動觸發程序。 <condition>項目會包含下列屬性的定義條件︰ 

1. **輸入**– 指定條件、 事件或內容的類型
1. **名稱**– 屬性或事件評估時要使用的名稱
1. **值**-會評估屬性的值
1. **運算子**-評估期間使用的作業︰ EQ （等）、 NEQ （不等於）、 GTR （大於）、 GEQ （大於或等於）、 LT （小於），LEQ （小於或等於），MOD （模數）

**<endConditions>**也包含<condition>項目。 重設時的條件結果為 true，則觸發程序。<trigger>項目也包含<sources>包含一或多項目<source>項目。 <source>項目定義 URI ad 回應和 ad 回應的類型。 在此範例中 URI 是提供給大量的回應。 


    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
 

###<a name="using-video-player-ad-interface-definition-vpaid"></a>使用視訊播放程式 Ad 介面定義 (VPAID)

VPAID 為 API 啟用通訊與視訊播放程式的可執行 ad 單位。 這個選項可讓高度互動性 ad 體驗。 使用者可以與 ad 互動，及 ad 可以回覆檢視器所要採取的動作。 例如 ad 可能會顯示按鈕可讓使用者檢視詳細的資訊或 ad 完整版本。 視訊播放程式必須支援 VPAID API，並可執行 ad 必須實作 API。 當播放程式要求 ad 從 ad 伺服器伺服器可能會使用大量回應包含 VPAID ad 的回覆。

可執行的 ad 會建立在例如 Adobe Flash™ 或網頁瀏覽器中執行的 JavaScript runtime 環境中，必須執行的程式碼。 當 ad 伺服器傳回包含 VPAID ad 大量回應時，在屬性值 apiFramework<MediaFile>項目必須是 「 VPAID 」。 這個屬性會指定包含的 ad 是 VPAID 可執行 ad。 型別屬性必須設定 MIME 資料類型的可執行檔，例如 「 應用程式/x-shockwave-flash 」 或 「 應用程式/x-javascript 」。 下列 XML 程式碼片段顯示<MediaFile>大量回應包含 VPAID 可執行的廣告元素。 

    
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type=”application/x-shockwaveflash”
                  width=”640” height=”480” apiFramework=”VPAID”>
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
 

可以使用初始化可執行的 ad<AdParameters>內的項目<Linear>或<NonLinear>大量回覆中的項目。 如需有關<AdParameters>項目，請參閱[大量 3.0](http://www.iab.net/media/file/VASTv3.0.pdf)。 如需有關 VPAID API 的詳細資訊，請參閱[VPAID 2.0](http://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf)。

##<a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>執行 Windows 或 Windows Phone 8 Player 與 Ad 支援

Microsoft 媒體平台︰ Windows 8 播放程式架構版和 Windows Phone 8 包含範例示範如何實作使用架構的視訊播放程式應用程式的應用程式的集合。 您可以從[Windows 8 播放程式架構版和 Windows Phone 8](https://playerframework.codeplex.com)下載播放程式架構和範例。

當您開啟 Microsoft.PlayerFramework.Xaml.Samples 解決方案時，您會看到專案中的資料夾中的數字。 廣告資料夾包含建立 ad 支援的視訊播放程式相關的範例程式碼。 廣告內資料夾會是 XAML/cs 檔案的數字的顯示如何使用不同的方式插入廣告。 下列清單說明每一個︰

- AdPodPage.xaml 示範如何顯示 ad pod。
- AdSchedulingPage.xaml 會顯示如何排程廣告。
- FreeWheelPage.xaml 會顯示如何使用 FreeWheel 外掛程式排程廣告。
- MastPage.xaml 會顯示如何排程廣告第檔案。
- ProgrammaticAdPage.xaml 示範如何以程式設計方式成視訊排程廣告。
- ScheduleClipPage.xaml 會顯示如何排程 ad 不大的檔案。
- VastLinearCompanionPage.xaml 示範如何插入線性和 ad 小幫手]。
- VastNonLinearPage.xaml 示範如何插入非線性的廣告。
- VmapPage.xaml 會顯示如何指定廣告 VMAP 檔案。

每個這些範例使用 MediaPlayer 類別所定義的播放程式架構。 大部分的範例使用加入各種 ad 回應格式的支援的外掛程式。 ProgrammaticAdPage 範例以程式設計方式互動 MediaPlayer 執行個體。

###<a name="adpodpage-sample"></a>AdPodPage 範例

此範例使用 AdSchedulerPlugin 定義顯示 ad 的時機。 在此範例中更換相簿廣告排程 5 秒後播放。 傳回從 ad 伺服器大量檔案中指定 ad pod （廣告以顯示順序的群組）。 中指定的大量檔案 URI<RemoteAdSource>項目。

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
    
        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>
    
                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>
    
                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>

如需有關 AdSchedulerPlugin 的詳細資訊，請參閱[在 Windows 8 和 Windows Phone 8 上的播放程式架構的廣告](http://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

###<a name="adschedulingpage"></a>AdSchedulingPage

這個範例也使用 AdSchedulerPlugin。 它排程三個廣告、 前的相簿 ad、 更換相簿 ad 及後的相簿 ad。 每個廣告 VAST URI 中指定<RemoteAdSource>項目。
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>
    
                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>


###<a name="freewheelpage"></a>FreeWheelPage

此範例使用 FreeWheelPlugin 指定一個來源屬性來指定 URI，指向 [SmartXML 檔案，指定 ad 內容，以及 ad 排程資訊。
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="mastpage"></a>MastPage

此範例使用可讓您使用的第檔案 MastSchedulerPlugin。 來源屬性會指定第檔案的位置。
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="programmaticadpage"></a>ProgrammaticAdPage

此範例以程式設計方式互動 MediaPlayer。 ProgrammaticAdPage.xaml 檔案產生 MediaPlayer:

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>

ProgrammaticAdPage.xaml.cs 檔案時，建立 AdHandlerPlugin，新增要指定 TimelineMarker ad 應該顯示，並會載入 RemoteAdSource，指定 URI 大量檔案，並再播放 ad MarkerReached 事件處理常式。
    
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;
    
            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }
    
            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }

###<a name="scheduleclippage"></a>ScheduleClipPage

此範例使用 AdSchedulerPlugin 排程更換相簿 ad 指定包含 ad.wmv 檔案。
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vastlinearcompanionpage"></a>VastLinearCompanionPage

這個範例說明如何使用 AdSchedulerPlugin 排程小幫手 ad 更換相簿線性廣告。 <RemoteAdSource>項目會指定大量檔案的位置。
    
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage

此範例使用排程線性 AdSchedulerPlugin 和非線性 ad。 使用指定的大量檔案位置<RemoteAdSource>項目。
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
                            
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vmappage"></a>VMAPPage

此範例使用 VmapSchedulerPlugin 排程廣告使用 VMAP 檔案。 在來源屬性中指定 VMAP 檔案 URI<VmapSchedulerPlugin>項目。
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

##<a name="implementing-an-ios-video-player-with-ad-support"></a>實作 iOS Ad 支援的視訊播放程式


Microsoft 媒體平台︰ IOS 版的播放程式架構包含範例示範如何實作使用架構的視訊播放程式應用程式的應用程式的集合。 您可以從[Azure 媒體播放程式架構](https://github.com/Azure/azure-media-player-framework)下載播放程式架構與範例。 Github 頁面已包含的播放程式架構的其他相關資訊的 Wiki 連結和播放程式範例簡介︰ [Azure 媒體播放程式 Wiki](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework)。


###<a name="scheduling-ads-with-vmap"></a>排程與 VMAP 廣告

下列範例會示範如何排程廣告使用 VMAP 檔案。

    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest
    
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }          
            }

###<a name="scheduling-ads-with-vast"></a>排程與 VAST 廣告

下列範例會示範如何排程落後的繫結大量 ad。
    
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
         
   下列範例會示範如何排程最早的繫結大量 ad。
範例 4︰ 排程最早的繫結大量 ad //Download VAST 檔案 (！ [framework.adResolver downloadManifest︰ 與資訊清單 withURL: [NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]]) {[自我 logFrameworkError];} 還 {adLinearTime.startTime = 7; adLinearTime.duration = 0;
        
        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }

下列範例會示範如何插入廣告使用概略剪下編輯 （強制）

    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    
    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

下列範例會示範如何排程 ad pod。

    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;
    
    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

下列範例會示範如何排程非連更換相簿 ad。 非連 ad 只會播放後無論任何搜尋執行檢視器。
    
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    
    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

下列範例會示範如何排程自黏更換相簿 ad。 自黏 ad 將會每次達到指定視訊的時間表上的點。

    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }


下列範例會示範如何排程後的相簿 ad。

    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

下列範例會示範如何排程前的相簿 ad。
    
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

下列範例會示範如何排程更換相簿覆疊 ad。
    
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }



##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
##<a name="see-also"></a>另請參閱

[開發視訊播放程式的應用程式](media-services-develop-video-players.md)
