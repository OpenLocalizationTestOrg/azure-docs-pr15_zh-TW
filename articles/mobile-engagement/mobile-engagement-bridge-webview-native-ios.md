<properties 
    pageTitle="使用原生行動互動 iOS SDK 橋 iOS 網頁檢視" 
    description="說明如何建立橋樑執行 Javascript 及原生行動互動 iOS SDK 的網頁檢視"      
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-ios" 
    ms.devlang="objective-c" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="bridge-ios-webview-with-native-mobile-engagement-ios-sdk"></a>使用原生行動互動 iOS SDK 橋 iOS 網頁檢視

> [AZURE.SELECTOR]
- [Android 橋](mobile-engagement-bridge-webview-native-android.md)
- [iOS 橋](mobile-engagement-bridge-webview-native-ios.md)

某些行動應用程式被設計為混合式部署的應用程式，使用原生 iOS 目標 C 開發開發應用程式本身，但部分或甚至全部的畫面上會呈現內 iOS 網頁檢視。 您仍然可以使用這類應用程式中的行動互動 iOS SDK，並在此教學課程說明如何執行此動作。 

有兩種方法都記載雖然達到這個目的︰

- 第一次一個說明這個[連結](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip)這需要註冊`UIWebViewDelegate`web 檢視和攔截及-立即-取消完成 Javascript 位置變更。 
- 第二個其中一個依據此[WWDC 2013 工作階段](https://developer.apple.com/videos/play/wwdc2013/615)，這是更簡潔比第一個和我們將依照本指南的一種方法。 請注意，此方法只適用於 ios7 重新設計和上方。 

針對 iOS 搭範例，請遵循下列步驟︰

1. 首先，您需要確認您已經透過整合混合式應用程式中的行動互動 iOS SDK 我們[開始教學課程](mobile-engagement-ios-get-started.md)。 或者，您也可以啟用測試記錄，如下所示，讓您可以看到 SDK 方法，如我們觸發從網頁檢視。 
    
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           ....
            [EngagementAgent setTestLogEnabled:YES];
           ....
        }

2. 現在請確定您的混合式應用程式上有網頁檢視畫面。 您可以將它新增`Main.storyboard`的應用程式。 

3. 建立此網頁檢視與您**ViewController**關聯按一下並拖曳網頁檢視來自檢視控制器場景至`ViewController.h`編輯畫面中，將其只要以下`@interface`線條。 

4. 一旦您這麼做，就會出現對話方塊詢問名稱。 提供的名稱為**網頁檢視**。 您`ViewController.h`檔案應該如下所示︰

        #import <UIKit/UIKit.h>
        #import "EngagementViewController.h"
        
        @interface ViewController : EngagementViewController
        @property (strong, nonatomic) IBOutlet UIWebView *webView;
        
        @end

5. 我們將會更新`ViewController.m`稍後檔案，但我們會建立在一些常用的行動互動 iOS SDK 方法建立包裝紙橋檔案的第一次。 建立新的標頭檔案稱為**EngagementJsExports.h**使用`JSExport`機制上述[工作階段](https://developer.apple.com/videos/play/wwdc2013/615)，公開的原生 iOS 方法所述。 

        #import <Foundation/Foundation.h>
        #import <JavaScriptCore/JavascriptCore.h>
        
        @protocol EngagementJsExports <JSExport>
        
        + (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras;
        + (void) endEngagementJob:(NSString*) name;
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras;
        + (void) sendEngagementAppInfo:(NSString*) appInfo;
        
        @end

        @interface EngagementJs : NSObject <EngagementJsExports>

        @end

6. 接下來，我們會建立第二部分橋檔案。 建立名**EngagementJsExports.m**包含實作呼叫行動互動 iOS SDK 方法來建立的實際包裝函式的檔案。 也請注意，我們會剖析`extras`從網頁檢視 javascript 傳遞，並將的到`NSMutableDictionary`所傳送的互動 SDK 方法呼叫的物件。  

        #import <UIKit/UIKit.h>
        #import "EngagementAgent.h"
        #import "EngagementJsExports.h"
        
        @implementation EngagementJs
        
        +(void) sendEngagementEvent:(NSString*)name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendEvent:name extras:extrasInput];
        }
        
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] startJob:name extras:extrasInput];
        }
        
        + (void) endEngagementJob:(NSString*) name {
           [[EngagementAgent shared] endJob:name];
        }
        
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendError:name extras:extrasInput];
        }
        
        + (void) sendEngagementAppInfo:(NSString*) appInfo {
           NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
           [[EngagementAgent shared] sendAppInfo:appInfoInput];
        }
        
        + (NSMutableDictionary*) ParseExtras:(NSString*) input {
           NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
           NSError* error = nil;
           NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
           
           return extras;
        }
        
        @end

5. 現在我們回頭**ViewController.m** ，然後更新下列程式碼︰ 
        
        #import <JavaScriptCore/JavaScriptCore.h>
        #import "ViewController.h"
        #import "EngagementJsExports.h"
        
        @interface ViewController ()
        
        @end
        
        @implementation ViewController
        
        - (void)viewDidLoad
        {
           self.webView.delegate = self;
           [super viewDidLoad];
           [self loadWebView];
        }
        
        - (void)loadWebView {
           NSBundle* mainBundle = [NSBundle mainBundle];
           NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
           
           NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
           [self.webView loadRequest:urlReq];
        }
        
        - (void)webViewDidFinishLoad:(UIWebView*)wv
        {
           JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
           
           context[@"EngagementJs"] = [EngagementJs class];
        }
        
        - (void)webView:(UIWebView*)wv didFailLoadWithError:(NSError*)error
        {
           NSLog(@"Error for WEBVIEW: %@", [error description]);
        }
        
        - (void)didReceiveMemoryWarning {
           [super didReceiveMemoryWarning];
           // Dispose of any resources that can be recreated.
        }
        
        @end

6. 請注意下列**ViewController.m**檔案的相關要點︰

    - 在 [`loadWebView`方法，我們會載入稱為**LocalPage.html**接下來，我們會檢閱其程式碼的本機 HTML 檔案。 
    - 在 [`webViewDidFinishLoad`方法，我們會由抓取介於`JsContext`和包裝紙類別關聯。 這可讓呼叫我們包裝紙 SDK 方法使用控點**EngagementJs**從網頁檢視。 

7. 建立名**LocalPage.html**下列程式碼的檔案︰

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
                   alert('window.onerror: ' + err);
               }
               
               function send(inputId)
               {
                   var input = document.getElementById(inputId);
                   if(input)
                   {
                       var value = input.value;
                       // Example of how extras info can be passed with the Engagement logs
                       var extras = '{"CustomerId":"MS290011"}';
                   }
                   
                   if(value && value.length > 0)
                   {
                       switch(inputId)
                       {
                           case "event":
                           EngagementJs.sendEngagementEvent(value, extras);
                           break;
                           
                           case "job":
                           EngagementJs.startEngagementJob(value, extras);
                           window.setTimeout(
                           function(){
                               EngagementJs.endEngagementJob(value);
                           }, 10000 );
                           break;
        
                           case "error":
                           EngagementJs.sendEngagementError(value, extras);
                           break;
                           
                           case "appInfo":
                           var appInfo = '{"customer_name":"' + value + '"}';
                           EngagementJs.sendEngagementAppInfo(appInfo);
                           break;
                       }
                   }
               }
               </script>
               
           </head>
           <body>
               <h1>Bridge Tester</h1>
               
               <div id='engagement'>
                   
                   <br/>
                   <h2>Event</h2>
                   <input type="text" id="event" size="35">
                   <button onclick="send('event')">Send</button>
                   
                   <br/>
                   <h2>Job</h2>
                   <input type="text" id="job" size="35">
                   <button onclick="send('job')">Send</button>
                   
                   <br/>
                   <h2>Error</h2>
                   <input type="text" id="error" size="35">
                   <button onclick="send('error')">Send</button
                   
                   <br/>
                   <h2>AppInfo</h2>
                   <input type="text" id="appInfo" size="35">
                   <button onclick="send('appInfo')">Send</button>
               
               </div>
           </body>
        </html>

8. 請注意下列相關的 HTML 檔案上述點︰

    -   它所含輸入方塊，您可以在此提供資料的事件、 工作、 錯誤、 應用名稱作為一的組。 當您按一下按鈕時，並呼叫 javascript 的最終呼叫從傳遞行動互動 iOS SDK 呼叫橋檔案的方法。 
    -   我們會在一些額外的靜態資訊傳送給事件、 工作和偶數示範如何進行此錯誤標記。 這項額外資訊會傳送 JSON 字串，如果您在中尋找`EngagementJsExports.m`檔案、 剖析和傳遞以及傳送事件、 工作、 錯誤。 
    -   您在輸入方塊中，指定執行 10 秒，關閉名稱會開始行動互動工作。 
    -   行動互動應用或標籤傳遞與 'customer_name' 為靜態鍵與您所輸入中輸入值為標記的值。 
 
9. 執行應用程式，您會看到以下訊息。 現在提供的測試事件，如下所示的一些名稱，然後按一下旁邊的 [**傳送**。 

    ![][1]

10. 現在，如果您移至您的應用程式和下**事件]-> [詳細資料**的外觀的 [**監視器**] 索引標籤，您會看到顯示以及靜態應用程式的資訊，我們會傳送此事件。 

    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png
