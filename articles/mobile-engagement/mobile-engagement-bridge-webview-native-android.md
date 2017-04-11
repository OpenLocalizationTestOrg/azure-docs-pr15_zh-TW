<properties 
    pageTitle="使用原生行動互動 Android SDK 橋 Android 的網頁檢視" 
    description="說明如何建立橋樑執行 Javascript 及原生的行動互動 Android SDK 的網頁檢視"      
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-android" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="bridge-android-webview-with-native-mobile-engagement-android-sdk"></a>使用原生行動互動 Android SDK 橋 Android 的網頁檢視

> [AZURE.SELECTOR]
- [Android 橋](mobile-engagement-bridge-webview-native-android.md)
- [iOS 橋](mobile-engagement-bridge-webview-native-ios.md)

某些行動應用程式被設計為混合式部署的應用程式，使用原生 Android 開發開發應用程式本身，但部分或甚至全部的畫面上會呈現在 Android 的網頁檢視。 您仍然可以使用行動互動 Android SDK 這類應用程式，並在此教學課程說明如何執行此動作。 以下範例為基礎的文件，在 Android[以下](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript)。 說明如何此記錄的方法可用於執行相同的行動互動 Android SDK 的常用的方法，例如從混合式應用程式網頁檢視也可以透過我們 Android SDK 線路它們時起始要求追蹤事件、 工作、 錯誤、 應用程式資訊。 

1. 首先，您需要確認您已經透過整合行動互動 Android SDK，您混合式部署的應用程式中我們[開始教學課程](mobile-engagement-android-get-started.md)。 如此一來，您`OnCreate`方法將如下所示。  
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
            EngagementAgent.getInstance(this).init(engagementConfiguration);
        }

2. 現在請確定您的混合式應用程式上有網頁檢視畫面。 程式碼會類似下列我們會在此載入本機的 HTML 檔案**Sample.html**中的網頁檢視`onCreate`畫面的方法。 

        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
        }

        protected void onCreate(Bundle savedInstanceState) {
            ...
            ...
            SetWebView();
        }

3. 現在建立橋檔案稱為**WebAppInterface**一些常用的行動互動 Android SDK 方法，使用在建立包裝紙`@JavascriptInterface` [Android 的文件](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript)中所述的方法︰

        import android.content.Context;
        import android.os.Bundle;
        import android.util.Log;
        import android.webkit.JavascriptInterface;
        
        import com.microsoft.azure.engagement.EngagementAgent;
        
        import org.json.JSONArray;
        import org.json.JSONObject;
        
        public class WebAppInterface {
            Context mContext;
        
            /** Instantiate the interface and set the context */
            WebAppInterface(Context c) {
                mContext = c;
            }
        
            @JavascriptInterface
            public void sendEngagementEvent(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void startEngagementJob(String name, String extras ){
                EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void endEngagementJob(String name){
                EngagementAgent.getInstance(mContext).endJob(name);
            }
        
            @JavascriptInterface
            public void sendEngagementError(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void sendEngagementAppInfo(String appInfo){
                EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
            }
        
            public Bundle ParseExtras(String input) {
                Bundle extras = new Bundle();
        
                try {
                    JSONObject jObject = new JSONObject(input);
                    extras.putString(jObject.names().getString(0),
                            jObject.get(jObject.names().getString(0)).toString());
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return extras;
            }
        }  

4. 一旦我們建立上述橋檔案後，我們需要確定它是我們的網頁檢視與相關聯。 此選項，您需要編輯您`SetWebview`方法，讓它看起來如下所示︰

        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
            WebSettings webSettings = myWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
        }

5. 上述的程式碼片段，稱為`addJavascriptInterface`橋類別與我們的網頁檢視，也會建立稱為**EngagementJs**呼叫方法，從橋檔案控點。 

6. 現在建立下列稱為**Sample.html**稱為**資產**的載入網頁檢視和，我們會呼叫方法從橋檔案的資料夾中您專案中的檔案。

        <!doctype html>
        <html>
            <head>
                <style type='text/css'>
                    html { font-family:Helvetica; color:#222; }
                    h1 { color:steelblue; font-size:22px; margin-top:16px; }
                    h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
                </style>
        
                <script type="text/javascript">
        
                    window.onerror = function(err)
                    {
                      log('window.onerror: ' + err);
                    }
        
                    send = function(inputId)
                    {
                        var input = document.getElementById(inputId);
                        if(input)
                        {
                            var value = input.value;
                            // Example of how extras info can be passed with the Engagement logs
                            var extras = '{"CustomerId":"MS290011"}';
        
                            if(value && value.length > 0)
                            {
                                switch(inputId)
                                {
                                    case "event":
                                    EngagementJs.sendEngagementEvent(value, extras);
                                    break;
        
                                    case "job":
                                    EngagementJs.startEngagementJob(value, extras);
                                    window.setTimeout( function()
                                    {
                                      EngagementJs.endEngagementJob(value);
                                    }, 10000 );
                                    break;
        
                                    case "error":
                                    EngagementJs.sendEngagementError(value, extras);
                                    break;
        
                                    case "appInfo":
                                    EngagementJs.sendEngagementAppInfo({"customer_name":value});
                                    break;
                                }
                            }
                        }
                    }
                </script>
            </head>
            <body>
                <h1>Bridge Tester</h1>
                <div id='engagement'>
                    <h2>Event</h2>
                    <input type="text" id="event" size="35">
                    <button onclick="send('event')">Send</button>
        
                    <h2>Job</h2>
                    <input type="text" id="job" size="35">
                    <button onclick="send('job')">Send</button>
        
                    <h2>Error</h2>
                    <input type="text" id="error" size="35">
                    <button onclick="send('error')">Send</button>
        
                    <h2>AppInfo</h2>
                    <input type="text" id="appInfo" size="35">
                    <button onclick="send('appInfo')">Send</button>
                </div>
            </body>
        </html>

8. 請注意下列相關的 HTML 檔案上述點︰

    -   它所含輸入方塊，您可以在此提供資料的事件、 工作、 錯誤、 應用名稱作為一的組。 當您按一下按鈕時，並呼叫 javascript 的最終呼叫從橋檔案傳遞行動互動 Android sdk 的變更這個通話的方法。 
    -   我們會在一些額外的靜態資訊傳送給事件、 工作和偶數示範如何進行此錯誤標記。 這項額外資訊會傳送 JSON 字串，如果您在中尋找`WebAppInterface`檔案、 剖析和放在 Android`Bundle`並傳遞以及傳送事件、 工作、 錯誤。 
    -   您在輸入方塊中，指定執行 10 秒，關閉名稱會開始行動互動工作。 
    -   行動互動應用或標籤傳遞與 'customer_name' 為靜態鍵與您所輸入中輸入值為標記的值。 
 
9. 執行應用程式，您會看到以下訊息。 現在提供的測試事件，如下所示的一些名稱，然後按一下其下的 [**傳送**。 

    ![][1]

10. 現在，如果您移至您的應用程式和下**事件]-> [詳細資料**的外觀的 [**監視器**] 索引標籤，您會看到顯示以及靜態應用程式的資訊，我們會傳送此事件。 

    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png
