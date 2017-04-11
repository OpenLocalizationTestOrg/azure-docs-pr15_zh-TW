<properties
    pageTitle="快速入門的 Cordova/Phonegap Azure 行動互動"
    description="瞭解如何使用 Azure 行動互動分析及推入通知 Cordova/Phonegap 應用程式。"
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-phonegap"
    ms.devlang="js"
    ms.topic="hero-article" 
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>快速入門的 Cordova/Phonegap Azure 行動互動

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主題說明如何使用 Azure 行動互動，以瞭解您的應用程式使用狀況，並傳送區段的使用者，以 Cordova 開發行動應用程式的推播通知。

在本教學課程中，我們會建立空白的 Cordova 應用程式，使用 Mac，然後整合行動互動 SDK。 它會收集基本分析資料，並會接收推入通知 iOS 和 Google 雲端訊息 (GCM) 版的 Apple 推入通知系統 (APN) 使用 Android 版。 我們會將它部署至 iOS 或 Android 裝置以進行測試。 

> [AZURE.NOTE] 若要完成此教學課程中，您必須使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started)。

本教學課程的需求如下︰

+ XCode，可以從 Mac App Store 安裝 （適用於 iOS 部署）
+ [Android SDK 與模擬器](http://developer.android.com/sdk/installing/index.html)（適用於 Android 的部署）
+ 您可以從 Apple 開發人員中心的 APN 取得的推入通知憑證 (.p12)
+ 您可以取得從您的 Google 開發人員主控台 GCM GCM 專案數字
+ [行動裝置的互動 Cordova 外掛程式](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [AZURE.NOTE] 您可以找到上[Github](https://github.com/Azure/azure-mobile-engagement-cordova) Cordova 外掛程式原始程式碼和讀我檔案

##<a id="setup-azme"></a>設定行動裝置 Cordova 應用程式的互動

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>連線到行動互動後端的 [應用程式

本教學課程中會顯示 「 基本整合 」 這是最小設定才能收集資料，並傳送推入通知。 

我們會示範整合 Cordova 與建立基本的應用程式︰

###<a name="create-a-new-cordova-project"></a>建立新的 Cordova 專案

1. 啟動 Mac 電腦上的*終端機*視窗，並輸入以下這將會從預設範本建立新的 Cordova 專案。 請確認發佈您最後使用部署 iOS 應用程式的設定檔使用 「 com.mycompany.myapp 」 為應用程式識別碼 

        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova

2. 執行設定**iOS**版專案，並在 iOS 模擬器中執行下列動作︰

        $ cordova platform add ios 
        $ cordova run ios

3. 執行**android**設定您的專案，並在 Android 模擬器中執行下列動作。 請確定您的 Android SDK 模擬器設定有其目標為 Google Api (Google Inc.) CPU 使用 / 為 Google Api ARM ABI。  

        $ cordova platform add android
        $ cordova run android

4.  新增 Cordova 主控台外掛程式。 

        $ cordova plugin add cordova-plugin-console 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>將您的應用程式連線到行動互動後端

1. 同時提供變數的值，以設定外掛程式安裝 Azure 行動互動 Cordova 外掛程式︰

        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
            --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Android 達到圖示*︰ 必須是不含任何分機] 或 drawable 首碼資源的名稱 (365&ex: mynotificationicon)，及圖示檔案必須複製到您的 android 專案 (平台/android/解析度/drawable)

*iOS 達到圖示*︰ 必須副檔名與資源的名稱 (e x: mynotificationicon.png)，並將圖示檔案必須與 XCode （使用 [新增檔案] 功能表） 新增到 iOS 專案

##<a id="monitor"></a>啟用即時監視

1. 在 Cordova 專案-編輯**www/js/index.js**新增收到通話行動互動宣告一次*deviceReady*事件至新的活動。

         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }

2. 執行應用程式︰
        
    - **IOS 版**
    
        在 [`Terminal`視窗啟動您的應用程式，在新的模擬器執行個體中藉由執行下列動作︰

            cordova run ios

    - **Android 版**
        
        在 [`Terminal`視窗啟動您的應用程式，在新的模擬器執行個體中藉由執行下列動作︰

            cordova run android

3. 您可以看到主控台記錄中︰

        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

##<a id="monitor"></a>使用即時監視連線應用程式

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>啟用推入通知和訊息的應用程式

行動互動可讓您與使用推入通知並在應用程式行銷活動的內容中的訊息使用者進行互動。 本單元稱為行動互動入口網站中的範圍。
下列各節會設定您的應用程式會收到通知。

###<a name="configure-push-credentials-for-mobile-engagement"></a>設定推入認證的行動電話的互動

若要允許傳送推入通知，代表您的行動互動，您需要授與存取權給您的 Apple iOS 憑證或 GCM 伺服器 API 金鑰。 
    
1. 瀏覽至您的行動互動入口網站。 請確定您是我們為此專案使用的，然後按一下 [ **Engage** ] 按鈕，底部的應用程式中︰
    
    ![][1]
    
2. 您會在您的互動入口網站開啟 [設定] 頁面中。 從 [**原生推入**] 區段中有顯示按一下︰
    
    ![][2]

3. 設定 iOS 憑證/GCM 伺服器 API 金鑰

    **[iOS]**

    。 選取您.p12、 上傳並輸入您的密碼︰
    
    ![][3]

    **[Android]**

    。 按一下 [編輯圖示前面**API 金鑰**GCM 設定] 區段中，然後在快顯功能表，顯示貼上 GCM 伺服器機碼，然後按一下**[確定**]。 
        
    ![][4]

###<a name="enable-push-notifications-in-the-cordova-app"></a>啟用 Cordova 應用程式中的推播通知

編輯**www/js/index.js**若要將電話加入要求推入通知，及宣告處理常式行動互動︰

     onDeviceReady: function() {
           Engagement.initializeReach(  
                // on OpenUrl  
                function(_url) {   
                alert(_url);   
                });  
            Engagement.startActivity("myPage",{});  
        }

###<a name="run-the-app"></a>執行應用程式

**[iOS]**

1. 若要建立並部署測試推入通知，iOS 只允許在實際裝置推入通知的裝置上的應用程式，我們將使用 XCode。 移至建立 Cordova 專案的位置的位置，然後瀏覽至**...\platforms\ios**位置。 原生.xcodeproj 檔案 XCode 中開啟。 
    
2. 建立並部署 Cordova 應用程式在 iOS 裝置使用的帳戶有佈建的設定檔包含您剛才的憑證上傳至行動互動入口網站與應用程式識別碼您建立的 Cordova 應用程式時所提供的相符項目。 您可以查看*套件識別碼*您**資源\*-info.plist** XCode 可將該檔案。 

3. 您會看到標準 iOS 快顯拒絕應用程式要求權限可傳送通知您裝置上。 授與權限。 

**[Android]**

您只可以使用模擬器 GCM 通知支援在 Android 模擬器上執行 Android 應用程式。 

    cordova run android

##<a id="send"></a>傳送通知給您的應用程式

現在，我們將會建立簡單的推入通知行銷活動的推入傳送到您裝置上執行的應用程式︰

1. 瀏覽至您的行動互動入口網站中，[**連絡**] 索引標籤

2. 按一下 [建立您的推入行銷活動的**新的通知**

    ![][6]

3. 提供建立**[Android]**您行銷活動的輸入
    
    - 提供您的行銷活動的**名稱**。 
    - 選取 [**傳遞類型**作為*系統通知**簡單*
    - 選取為*[任何時間]*的**傳送時間**
    - 提供您通知將會在推入第一行的**標題**。
    - 提供其做為郵件本文在通知**訊息**。 

    ![][11]

4. 提供建立您的活動**[iOS]**的輸入

    - 提供您的行銷活動的**名稱**。 
    - 為*「 不在只應用程式]*中選取 [**傳送時間**
    - 提供您通知將會在推入第一行的**標題**。
    - 提供其做為郵件本文在通知**訊息**。 
 
    ![][12]

5. 捲動，並在 [內容] 區段中選取 [**只顯示通知**

    ![][8]

6. [選擇性]您也可以提供動作 URL。 請確定該使用時設定的外掛程式提供一個 URL 配置**AZME\_重新導向\_URL**變數例如*myapp://test*。  

7. 完成設定可能最基本的活動。 現在向下捲動，然後按一下 [**建立**] 按鈕，以儲存您的活動。

8. 最後**啟動**您的活動
    
    ![][10]

9. 現在，您應該會看到您的裝置或模擬器上推入通知做為此行銷活動的一部分。 

##<a id="next-steps"></a>後續步驟
[用於 Cordova 行動互動 SDK 的所有方法的概觀](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png

