<properties 
    pageTitle="平滑串流 Windows 市集應用程式教學課程 |Microsoft Azure" 
    description="瞭解如何使用 Azure 媒體服務建立內容與 XML MediaElement 控制項播放平滑串流 C# Windows 市集應用程式。" 
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



#<a name="how-to-build-a-smooth-streaming-windows-store-application"></a>如何建立平滑串流 Windows 市集應用程式

帶有平滑串流用戶端 SDK 的 Windows 8 可讓您可以播放視需要和即時平滑串流內容的 Windows 市集應用程式。 除了基本播放的平滑串流內容，SDK 也會提供豐富功能，例如 Microsoft PlayReady 保護、 串流切換、 狀態更新 （例如品質層級的變更） 和錯誤事件接聽音訊品質層級的限制，Live DVR，依此類推。 如需支援之功能的詳細資訊，請參閱[版本資訊](http://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes)。 如需詳細資訊，請參閱[為 Windows 8 播放程式架構](http://playerframework.codeplex.com/)。 

本教學課程中包含四個課程︰

1. 建立基本平滑串流市集應用程式
2. 新增滑桿來控制媒體進度
3. 選取帶有平滑串流資料流時
4. 選取帶有平滑串流追蹤

##<a name="prerequisites"></a>必要條件

- Windows 8 32 位元或 64 位元。 您可以從 MSDN 取得[Windows 8 企業評估](http://msdn.microsoft.com/evalcenter/jj554510.aspx)。
- Visual Studio 2012 或 Visual Studio Express 2012 （或更新版本）。 您可以從[這裡](http://www.microsoft.com/visualstudio/11/downloads)取得試用版。
- [Microsoft 平滑串流用戶端 SDK windows 8](http://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Homehttp://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home)。


您可以從 MSDN 開發人員程式碼範例 （程式碼庫） 下載的每個教學單元已完成的方案︰ 

- [課程 1](http://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f)串流 Media Player 簡單的 Windows 8 平滑 
- 控制[教學單元 2](http://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a)簡單的 Windows 8 平滑串流媒體播放使用滑桿列，請 
- [課程 3](http://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44)串流串流的選取範圍的媒體播放 Windows 8 平滑  
- [課程 4](http://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) -Windows 8 平滑串流追蹤的選取範圍的媒體播放。

##<a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>課程 1︰ 建立基本平滑串流市集應用程式

在本課程中，您將建立的 Windows 市集應用程式與播放平滑串流 MediaElement 控制項內容。  執行應用程式，看起來像︰

![平滑串流 Windows 市集應用程式的範例][PlayerApplication]
 
如需開發 Windows 市集應用程式的詳細資訊，請參閱[為 Windows 8 中開發很棒的應用程式](http://msdn.microsoft.com/windows/apps/br229512.aspx)。 本課程中會包含下列程序︰

1.  建立 Windows 市集專案
2.  設計的使用者介面 (XAML)
3.  修改檔案的程式碼
4.  編譯並測試應用程式

**若要建立 Windows 市集專案**

1.  執行 Visual Studio 2012 或更新版本。
2.  從 [**檔案**] 功能表中，按一下 [**新增**]，然後按一下**專案**。
3.  [新專案] 對話方塊中，輸入或選取下列值︰

名稱|值
---|---
範本] 群組|安裝/範本/Visual C# / Windows 市集
範本|空白的應用程式 (XAML)
名稱|SSPlayer
位置|C:\SSTutorials
方案名稱|SSPlayer
為方案建立目錄|（已選取）

4.  按一下**[確定]**。

**若要新增的參照平滑串流用戶端 sdk 的變更**

1.  從 [方案總管**SSPlayer**，以滑鼠右鍵按一下，然後按一下**新增參考。**
2.  輸入或選取下列值︰

名稱|值
---|---
參照群組|Windows/副檔名
參照|選取 [Microsoft 平滑的 Windows 8 及 Microsoft Visual c + + 執行階段套件串流用戶端 SDK
    
3.  按一下**[確定]**。 

新增後參照，您必須先選取目標平台 （x64 或 x86），新增參照不適用於任何 CPU 平台組態]。  在方案總管中，您會看到黃色的警告標示為這些新增參照。

**若要設計的播放程式使用者介面**

1.  從方案總管中，按兩下 [ **MainPage.xaml**在 [設計] 檢視中開啟它。
2.  找出**&lt;格線&gt;**和**&lt;/Grid&gt;**標籤 XAML 檔案，並貼上下列兩個標籤之間程式碼︰

        <Grid.RowDefinitions>
            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
        </Grid.RowDefinitions>
        
        <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="http://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
        </StackPanel>

        <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
        </StackPanel>

        <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
        </StackPanel>

    MediaElement 控制項用來播放媒體。 名為 sliderProgress 的滑桿控制項將會用於控制媒體進度的下一個教學單元。

3.  按下**CTRL + S**儲存檔案。

MediaElement 控制項不支援平滑串流內容的現成。 若要啟用平滑串流的支援，您必須註冊平滑串流位元組資料流處理常式的副檔名和 MIME 類型。  若要註冊，您可以使用 Windows.Media 命名空間的 MediaExtensionManager.RegisterByteStremHandler 方法。

在 XAML 檔案中一些事件處理常式是與控制項相關聯。  您必須定義那些事件處理常式。

**若要修改檔案的程式碼**

1.  從 [方案總管**MainPage.xaml**，以滑鼠右鍵按一下，然後再按一下 [**檢視程式碼**。
2.  在檔案頂端，新增下列使用陳述式︰

        using Windows.Media;

3.  在**MainPage**課程開始，新增下列資料成員︰

        private MediaExtensionManager extensions = new MediaExtensionManager();

4.  **MainPage**建構函式的結尾，新增下列兩行︰

        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
        
5.  結尾的**MainPage**類別，過去的下列程式碼︰

        #region UI Button Click Events
        private void btnPlay_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Play();
            txtStatus.Text = "MediaElement is playing ...";
        }
        
        private void btnPause_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Pause();
            txtStatus.Text = "MediaElement is paused";
        }
        
        private void btnSetSource_Click(object sender, RoutedEventArgs e)
        {
            sliderProgress.Value = 0;
            mediaElement.Source = new Uri(txtMediaSource.Text);
        
            if (chkAutoPlay.IsChecked == true)
            {
                txtStatus.Text = "MediaElement is playing ...";
            }
            else
            {
                txtStatus.Text = "Click the Play button to play the media source.";
            }
        }
        
        private void btnStop_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Stop();
            txtStatus.Text = "MediaElement is stopped";
        }
        
        private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
        {
            txtStatus.Text = "Seek to position " + sliderProgress.Value;
            mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
        }
        #endregion

    以下定義 sliderProgress_PointerPressed 事件處理常式。  還有更多運作方式若要使其運作，它會在此教學課程的下一個課程涵蓋。
6.  按下**CTRL + S**儲存檔案。

完成後檔案的程式碼應該看起來像這樣︰

![在 Visual Studio 的平滑串流 Windows 市集應用程式中的可檢視程式碼][CodeViewPic]

**若要編譯並測試應用程式**

1.  從 [**建置**] 功能表中，按一下 [**組態管理員**]。
2.  變更**作用中的方案平台**以符合您的開發平台。
3.  按**F6** ，編譯專案。 
4.  按**F5**執行應用程式。
5.  按一下頂端的應用程式，您可以使用預設的平滑串流的 URL，或輸入不同的項目。 
6.  按一下 [**設定來源**]。 依預設會啟用**自動播放**，因為應該自動播放媒體。  您可以控制使用 [**播放**]、 [**暫停**] 或 [**停止**] 按鈕的媒體。  您可以控制媒體音量使用 [垂直] 滑桿。  但是 [水平] 滑桿來控制媒體進度 」 完全尚未。 

您已完成 lesson1。  在本課程中，您可以使用 MediaElement 控制項來播放平滑串流內容。  在下一個課程中，將滑桿來控制平滑串流內容的進度。


##<a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>課程 2︰ 新增滑桿來控制媒體進度
在課程 1 中，您可以建立的 Windows 市集應用程式與 MediaElement XAML 控制項來播放平滑串流媒體內容。  其會出現啟動、 停止等暫停一些基本媒體的功能。  在本課程中，您會將滑桿列控制項新增至應用程式。

在本教學課程中，我們將使用計時器更新滑桿位置根據目前 MediaElement 控制項的位置。  滑桿開始及結束時間也需要即時內容若會更新。  這可以調整來源更新事件中更有效地處理。

媒體來源是產生多媒體資料的物件。  來源解析程式會 URL 或位元組資料流，而建立的適當的媒體來源的內容。  來源解析程式是標準的應用程式，以建立媒體來源。 

本課程中會包含下列程序︰

1.  註冊平滑串流處理常式 
2.  新增 [自動調整來源管理員層級的事件處理常式
3.  新增 [自動調整來源層級的事件處理常式
4.  新增 MediaElement 事件處理常式
5.  新增列相關的程式碼] 滑桿
6.  編譯並測試應用程式

**註冊平滑串流位元組資料流處理常式並傳遞 propertyset**

1.  從方案總管中，以滑鼠右鍵按一下**MainPage.xaml**，然後再按一下 [**檢視程式碼**。
2.  開頭的檔案時，新增下列使用陳述式︰

        using Microsoft.Media.AdaptiveStreaming;

3.  在 MainPage 課程開始，新增下列資料成員︰

        private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
        private IAdaptiveSourceManager adaptiveSourceManager;
    
4.  內**MainPage**建構函式中，新增下列程式碼之後**這。初始化 Components();**折線圖及撰寫的上一個課程註冊程式碼行︰
    
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
    
5.  **MainPage**建構函式內修改兩個 RegisterByteStreamHandler 方法若要新增左起第四個參數︰

        // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
        // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
        // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
        extensions.RegisterByteStreamHandler(
            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
        extensions.RegisterByteStreamHandler(
            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
        propertySet);

6.  按下**CTRL + S**儲存檔案。

**若要新增自動調整來源管理員層級的事件處理常式**

1.  從方案總管中，以滑鼠右鍵按一下**MainPage.xaml**，然後再按一下 [**檢視程式碼**。
2.  在**MainPage**類別中，新增下列資料成員︰

        private AdaptiveSource adaptiveSource = null;

3.  **MainPage**課程結尾新增下列事件處理常式︰
    
        #region Adaptive Source Manager Level Events
        private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
        {
            adaptiveSource = args.AdaptiveSource;
        }
        #endregion Adaptive Source Manager Level Events

4.  **MainPage**建構函式結尾新增訂閱的自動調整來源 open 事件的下列行︰
    
    adaptiveSourceManager.AdaptiveSourceOpenedEvent + = 新 AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);

5.  按下**CTRL + S**儲存檔案。

**若要新增自動調整來源層級的事件處理常式**

1.  從方案總管中，以滑鼠右鍵按一下**MainPage.xaml**，然後再按一下 [**檢視程式碼**。
2.  在**MainPage**類別中，新增下列資料成員︰
    
        private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
        private Manifest manifestObject;
    
3.  **MainPage**課程結尾新增下列事件處理常式︰

        #region Adaptive Source Level Events
        private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
        {
            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
        }
        
        private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
        {
            adaptiveSourceStatusUpdate = args;
        }
        
        private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
        {
            txtStatus.Text = "Error: " + args.HttpResponse;
        }
        #endregion Adaptive Source Level Events

4.  **MediaElement AdaptiveSourceOpened**方法結尾新增下列訂閱事件︰
    
        adaptiveSource.ManifestReadyEvent +=
                    mediaElement_ManifestReady;
        adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 
            mediaElement_AdaptiveSourceStatusUpdated;
        adaptiveSource.AdaptiveSourceFailedEvent += 
            mediaElement_AdaptiveSourceFailed;
    
5.  按下**CTRL + S**儲存檔案。

調整來源管理員層級，也可以用來處理常見的應用程式中的所有媒體項目功能，可供使用相同的事件。 每個 AdaptiveSource 包含自己的事件，會重疊顯示 AdaptiveSourceManager 底下的所有 AdaptiveSource 事件。

**若要新增媒體項目事件處理常式**

1.  從方案總管中，以滑鼠右鍵按一下**MainPage.xaml**，然後再按一下 [**檢視程式碼**。
2.  **MainPage**課程結尾新增下列事件處理常式︰
    
        #region Media Element Event Handlers 
        private void MediaOpened(object sender, RoutedEventArgs e)
        {
            txtStatus.Text = "MediaElement opened";
        }
        
        private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
        {
            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
        }
        
        private void MediaEnded(object sender, RoutedEventArgs e)
        {
            txtStatus.Text ="MediaElement ended.";
        }
        #endregion Media Element Event Handlers

3.  **MainPage**建構函式的結尾，新增下列程式碼至下標事件︰
    
        mediaElement.MediaOpened += MediaOpened;
        mediaElement.MediaEnded += MediaEnded;
        mediaElement.MediaFailed += MediaFailed;

4.  按下**CTRL + S**儲存檔案。

**若要新增滑桿列相關的程式碼**

1.  從方案總管中，以滑鼠右鍵按一下**MainPage.xaml**，然後再按一下 [**檢視程式碼**。
2.  開頭的檔案時，新增下列使用陳述式︰
    
        using Windows.UI.Core;

3.  在**MainPage**類別中，新增下列資料成員︰
    
        public static CoreDispatcher _dispatcher;
        private DispatcherTimer sliderPositionUpdateDispatcher;
    
4.  **MainPage**建構函式的結尾，新增下列程式碼︰

        _dispatcher = Window.Current.Dispatcher;
        PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
        sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
    
5.  **MainPage**課程結尾新增下列程式碼︰
    
        #region sliderMediaPlayer
        private double SliderFrequency(TimeSpan timevalue)
        {
            long absvalue = 0;
            double stepfrequency = -1;
        
            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }
        
            TimeSpan totalDVRDuration = new TimeSpan(absvalue);
        
            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }
        
            return stepfrequency;
        }
        
        void updateSliderPositionoNTicks(object sender, object e)
        {
            sliderProgress.Value = mediaElement.Position.TotalSeconds;
        }
        
        public void setupTimer()
        {
            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
        }

        public void startTimer()
        {
            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
        }
        
        // Slider start and end time must be updated in case of live content
        public async void setSliderStartTime(long startTime)
        {
            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
        }
        
        // Slider start and end time must be updated in case of live content
        public async void setSliderEndTime(long startTime)
        {
            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
        }
        #endregion sliderMediaPlayer

    **附註︰**CoreDispatcher 用來變更 UI 執行緒從非 UI 執行緒。 若發送器執行緒瓶頸，開發人員可以選擇使用提供的使用者介面元素他想要更新的發送器。  例如︰
    
        await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 
          timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
        double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 
          sliderProgress.Maximum = absvalue; }); 
        

6.  **MediaElement_AdaptiveSourceStatusUpdated**方法結尾新增下列程式碼︰
    
        setSliderStartTime(args.StartTime);
        setSliderEndTime(args.EndTime);

7.  **MediaOpened**方法結尾新增下列程式碼︰
    
    sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);sliderProgress.Width = mediaElement.Width;setupTimer();

8.  按下**CTRL + S**儲存檔案。

**若要編譯並測試應用程式**

1. 按**F6** ，編譯專案。 
2.  按**F5**執行應用程式。
3.  按一下頂端的應用程式，您可以使用預設的平滑串流的 URL，或輸入不同的項目。 
4.  按一下 [**設定來源**]。 
5.  測試滑桿。

您已經完成教學單元 2。  在本課程中，您可以新增滑桿至 [應用程式。 

##<a name="lesson-3-select-smooth-streaming-streams"></a>課程 3︰ 選取平滑串流資料流時
帶有平滑串流並使用多個語言的音軌所選取的檢視者串流內容。  在本課程中，您將啟用檢閱者，若要選取的資料流。 本課程中會包含下列程序︰

1. 修改 XAML 檔案
2. 修改的程式碼 behand 檔案
3. 編譯並測試應用程式


**若要修改 XAML 檔案**

1. 從方案總管**MainPage.xaml**，以滑鼠右鍵按一下，然後按一下**檢視設計工具**。
2. 找出&lt;Grid.RowDefinitions&gt;，及修改 RowDefinitions，使其看起來像︰

        <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
        </Grid.RowDefinitions>

3. 內&lt;格線&gt;&lt;/Grid&gt;標籤]，新增下列程式碼，定義清單方塊控制項，讓使用者可以看到的可用的資料流時，清單並選取資料流時︰

        <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
            <Grid.RowDefinitions>
                <RowDefinition Height="300"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="250*"/>
                <ColumnDefinition Width="250*"/>
            </Grid.ColumnDefinitions>
            <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                <StackPanel Orientation="Horizontal">
                    <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                    <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                </StackPanel>
                <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </StackPanel>
        </Grid>

4. 按下**CTRL + S**儲存變更。


**若要修改檔案的程式碼**

1. 從 [方案總管**MainPage.xaml**，以滑鼠右鍵按一下，然後再按一下 [**檢視程式碼**。
2. SSPlayer 命名空間加入新的類別︰ #region 課程串流
    
        public class Stream
        {
            private IManifestStream stream;
            public bool isCheckedValue;
            public string name;
    
            public string Name
            {
                get { return name; }
                set { name = value; }
            }
    
            public IManifestStream ManifestStream
            {
                get { return stream; }
                set { stream = value; }
            }
    
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    // mMke the video stream always checked.
                    if (stream.Type == MediaStreamType.Video)
                    {
                        isCheckedValue = true;
                    }
                    else
                    {
                        isCheckedValue = value;
                    }
                }
            }
    
            public Stream(IManifestStream streamIn)
            {
                stream = streamIn;
                name = stream.Name;
            }
        }
        #endregion class Stream

3. 在 MainPage 課程開始，新增變數定義如下︰

        private List<Stream> availableStreams;
        private List<Stream> availableAudioStreams;
        private List<Stream> availableTextStreams;
        private List<Stream> availableVideoStreams;

4. 在 MainPage 類別中，新增下列區域︰

        #region stream selection
        ///<summary>
        ///Functionality to select streams from IManifestStream available streams
        /// </summary>
        
        // This function is called from the mediaElement_ManifestReady event handler 
        // to retrieve the streams and populate them to the local data members.
        public void getStreams(Manifest manifestObject)
        {
            availableStreams = new List<Stream>();
            availableVideoStreams = new List<Stream>();
            availableAudioStreams = new List<Stream>();
            availableTextStreams = new List<Stream>();
        
            try
            {
                for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                {
                    Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                    newStream.isChecked = false;
        
                    //populate the stream lists based on the types
                    availableStreams.Add(newStream);
        
                    switch (newStream.ManifestStream.Type)
                    {
                        case MediaStreamType.Video:
                            availableVideoStreams.Add(newStream);
                            break;
                        case MediaStreamType.Audio:
                            availableAudioStreams.Add(newStream);
                            break;
                        case MediaStreamType.Text:
                            availableTextStreams.Add(newStream);
                            break;
                    }
        
                    // Select the default selected streams from the manifest.
                    for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                    {
                        string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                        if (selectedStreamName.Equals(newStream.Name))
                        {
                            newStream.isChecked = true;
                            break;
                        }
                    }
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        
        // This function set the list box ItemSource
        private async void refreshAvailableStreamsListBoxItemSource()
        {
            try
            {
                //update the stream check box list on the UI
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableStreams.ItemsSource = availableStreams; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        
        // This function creates a selected streams list
        private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
        {
            bool isOneVideoSelected = false;
            bool isOneAudioSelected = false;
        
            // Only one video stream can be selected
            for (int j = 0; j<availableVideoStreams.Count; j++)
            {
                if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                {
                    selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                    isOneVideoSelected = true;
                }
            }
        
            // Select the frist video stream from the list if no video stream is selected
            if (!isOneVideoSelected)
            {
                availableVideoStreams[0].isChecked = true;
                selectedStreams.Add(availableVideoStreams[0].ManifestStream);
            }
        
            // Only one audio stream can be selected
            for (int j = 0; j<availableAudioStreams.Count; j++)
            {
                if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                {
                    selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                    isOneAudioSelected = true;
                    txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                }
            }
        
            // Select the frist audio stream from the list if no audio steam is selected.
            if (!isOneAudioSelected)
            {
                availableAudioStreams[0].isChecked = true;
                selectedStreams.Add(availableAudioStreams[0].ManifestStream);
            }
        
            // Multiple text streams are supported.
            for (int j = 0; j < availableTextStreams.Count; j++)
            {
                if (availableTextStreams[j].isChecked)
                {
                    selectedStreams.Add(availableTextStreams[j].ManifestStream);
                }
            }
        }
        
        // Change streams on a smooth streaming presentation with multiple video streams.
        private async void changeStreams(List<IManifestStream> selectStreams)
        {
            try
            {
                IReadOnlyList<IStreamChangedResult> returnArgs =
                    await manifestObject.SelectStreamsAsync(selectStreams);
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        #endregion stream selection

5. 找出 mediaElement_ManifestReady 方法，新增下列程式碼結尾的函數︰
    
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();

    因此當 MediaElement 資訊清單時，程式碼取得的可用的資料流時，清單，並填入 UI 清單方塊的清單。

6. 在 MainPage 類別中，找出 UI 按鈕按一下事件區域]，然後新增 [下列函數定義︰

        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**若要編譯並測試應用程式**

1. 按**F6** ，編譯專案。 
2.  按**F5**執行應用程式。
3.  按一下頂端的應用程式，您可以使用預設的平滑串流的 URL，或輸入不同的項目。 
4.  按一下 [**設定來源**]。 
5.  預設語言是 audio_eng。 請嘗試 audio_eng 和 audio_es 之間切換。 表單中，選取新的資料流，您必須按一下 [送出] 按鈕。

您已完成課程 3。  在本課程中，您可以新增功能，以選擇資料流。

##<a name="lesson-4-select-smooth-streaming-tracks"></a>課程 4︰ 選取平滑串流追蹤
帶有平滑串流簡報可以包含多個視訊檔案，以不同的品質等級 （觀眾） 和解決方案編碼。 在本課程中，您會讓使用者選取追蹤。 本課程中會包含下列程序︰

1. 修改 XAML 檔案
2. 修改的程式碼 behand 檔案
3. 編譯並測試應用程式

**若要修改 XAML 檔案**

1. 從方案總管**MainPage.xaml**，以滑鼠右鍵按一下，然後按一下**檢視設計工具**。
2. 找出&lt;格線&gt;標記與名稱**gridStreamAndBitrateSelection**、 新增下列程式碼結尾的標記︰

        <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
         <StackPanel Orientation="Horizontal">
             <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
             <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
         </StackPanel>
         <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                  ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>
        </StackPanel>

3. 按下**CTRL + S**儲存他的變更


**若要修改檔案的程式碼**

1. 從 [方案總管**MainPage.xaml**，以滑鼠右鍵按一下，然後再按一下 [**檢視程式碼**。
2. SSPlayer 命名空間加入新的類別︰
    
        #region class Track
        public class Track
        {
            private IManifestTrack trackInfo;
            public string _bitrate;
            public bool isCheckedValue;
    
            public IManifestTrack TrackInfo
            {
                get { return trackInfo; }
                set { trackInfo = value; }
            }
    
            public string Bitrate
            {
                get { return _bitrate; }
                set { _bitrate = value; }
            }
    
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    isCheckedValue = value;
                }
            }
    
            public Track(IManifestTrack trackInfoIn)
            {
                trackInfo = trackInfoIn;
                _bitrate = trackInfoIn.Bitrate.ToString();
            }
            //public Track() { }
        }
        #endregion class Track

3. 在 MainPage 課程開始，新增變數定義如下︰
    
        private List<Track> availableTracks;

4. 在 MainPage 類別中，新增下列區域︰
    
        #region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>

        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();

            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;

            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;

                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }

        // This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }

        // This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }

        // This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }

        // This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection

5. 找出 mediaElement_ManifestReady 方法，新增下列程式碼結尾的函數︰

        getTracks(manifestObject);
        refreshAvailableTracksListBoxItemSource();

6. 在 MainPage 類別中，找出 UI 按鈕按一下事件區域]，然後新增 [下列函數定義︰

        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**若要編譯並測試應用程式**

1. 按**F6** ，編譯專案。 
2.  按**F5**執行應用程式。
3.  按一下頂端的應用程式，您可以使用預設的平滑串流的 URL，或輸入不同的項目。 
4.  按一下 [**設定來源**]。 
5.  根據預設，會選取所有的視訊資料流的追蹤。 若要嘗試元費率變更，您可以選取 [最低元工資率，，然後選取 [最高的位元率。 您必須按一下 [送出後每項變更。  您可以看到視訊品質變更。

您已完成課程 4。  在本課程中，您可以新增功能，以選擇追蹤。


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="other-resources"></a>其他資源︰
- [如何建置平滑串流 Windows 8 JavaScript 應用程式使用進階功能](http://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
- [帶有平滑串流技術概觀](http://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png
 
