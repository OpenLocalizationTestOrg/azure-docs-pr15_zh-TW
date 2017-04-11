<properties
    pageTitle="如何在 iOS 上使用互動 API"
    description="最新的 iOS SDK-iOS 上使用互動 API"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />


#<a name="how-to-use-the-engagement-api-on-ios"></a>如何在 iOS 上使用互動 API

這份文件是文件的增益集在 iOS 上的 [整合互動的方式︰ 會提供深度詳細資料，瞭解如何使用互動 API 報告您的應用程式統計資料。

請記住，如果您只想參與報告您的應用程式工作階段、 活動、 當機和技術資訊，然後最簡單的方法是，讓您的自訂`UIViewController`物件繼承對應`EngagementViewController`類別。

如果您想要執行其他工作，例如，如果您需要報告應用程式特定事件與錯誤的工作，或如果您使用不同的方式中的應用程式的活動報表超過一個實作於`EngagementViewController`類別，然後您需要使用互動 API。

互動 API 提供的`EngagementAgent`類別。 這個類別的執行個體，則可電話擷取`[EngagementAgent shared]`靜態方法 (請注意，`EngagementAgent`傳回物件是單一)。

API 呼叫前,`EngagementAgent`物件必須初始化呼叫的方法`[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];`

##<a name="engagement-concepts"></a>互動的概念

下列組件來調整常見[行動互動概念](mobile-engagement-concepts.md)iOS 平台。

### <a name="session-and-activity"></a>`Session`與`Activity`

*活動*是通常相關聯的應用程式，一個畫面也就是說*活動*的開始畫面會顯示，並停駐點螢幕關閉時︰ 這是大小寫時使用整合互動 SDK`EngagementViewController`類別。

但*活動*可以也手動控制使用互動 API。 這個選項可讓分割指定的螢幕幾個 sub 部分，以取得更多詳細資料 （例如以已知頻率，並多久對話方塊內使用，則此畫面） 這個畫面的使用方式。

##<a name="reporting-activities"></a>報表的活動

### <a name="user-starts-a-new-activity"></a>使用者啟動新的活動

            [[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

您需要呼叫`startActivity()`每次使用者活動的變更。 此函數的第一個呼叫開始新的使用者工作階段。

### <a name="user-ends-his-current-activity"></a>使用者結束他目前活動

            [[EngagementAgent shared] endActivity];

> [AZURE.WARNING] 您**永遠不**應該自行呼叫此函數，除非您想要分割成多個工作階段的應用程式的使用︰ 此函數呼叫想結束目前的工作階段，因此，在後續通話`startActivity()`會開始新的工作階段。 關閉您的應用程式時，此函數會自動呼叫 SDK 的。

##<a name="reporting-events"></a>報告的事件

### <a name="session-events"></a>工作階段事件

工作階段事件通常用來報告使用者在他們的工作階段期間執行的動作。

**不含額外資料的範例︰**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
            ...
       }
       [...]
    }

**額外資料的範例︰**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        NSMutableDictionary* extras = [NSMutableDictionary dictionary];
        [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
        [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
            ...
       }
       [...]
    }

### <a name="standalone-events"></a>獨立的事件

相反工作階段事件獨立事件可供以外的工作階段的內容。

**範例︰**

    [[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

##<a name="reporting-errors"></a>報表錯誤

### <a name="session-errors"></a>工作階段錯誤

工作階段錯誤通常用來報告他的工作階段期間影響使用者的錯誤。

**範例︰**

    /** The user has entered invalid data in a form */
    @implementation MyViewController {
      [...]
      -(void)onMyFormSubmitted:(MyForm*)form {
        [...]
        /* The user has entered an invalid email address */
        [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
        [...]
      }
      [...]
    }

### <a name="standalone-errors"></a>獨立的錯誤

相反工作階段的錯誤，可以使用獨立錯誤以外的工作階段的內容。

**範例︰**

    [[EngagementAgent shared] sendError:@"something_failed" extras:nil];

##<a name="reporting-jobs"></a>報告工作

**範例︰**

假設您要報告您的登入程序的工期︰

    [...]
    -(void)signIn
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      [... sign in ...]

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    }
    [...]

### <a name="report-errors-during-a-job"></a>工作期間的報表錯誤

若要執行的工作，而不是在目前的使用者工作階段的相關關聯錯誤。

**範例︰**

假設您想要在您的登入程序期間報告錯誤︰

    [...]
    -(void)signin
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      BOOL success = NO;
      while (!success) {
        /* Try to sign in */
        NSError* error = nil;
        [self trySigin:&error];
        success = error == nil;

        /* If an error occured report it */
        if(!success)
        {
          [[EngagementAgent shared] sendJobError:@"sign_in_error"
                         jobName:@"sign_in"
                          extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];

          /* Retry after a moment */
          [NSThread sleepForTimeInterval:20];
        }
      }

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    };
    [...]

### <a name="events-during-a-job"></a>工作期間的事件

執行中的工作，而不是在目前的使用者工作階段的相關可以相關聯的事件。

**範例︰**

假設我們有社交網路，我們使用報告工作的使用者連線至伺服器的總時間。 使用者可以從其朋友接收郵件，這是工作的事件。

    [...]
    - (void) signin
    {
      [...Sign in code...]
      [[EngagementAgent shared] startJob:@"connection" extras:nil];
    }
    [...]
    - (void) signout
    {
      [...Sign out code...]
      [[EngagementAgent shared] endJob:@"connection"];
    }
    [...]
    - (void) onMessageReceived
    {
      [...Notify user...]
      [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
    }
    [...]

##<a name="extra-parameters"></a>額外的參數

可附加任意資料事件、 錯誤、 活動和工作。

可以結構化資料，它會使用 iOS 的 NSDictionary 類別。

請注意，可以包含 extras `arrays(NSArray, NSMutableArray)`， `numbers(NSNumber class)`， `strings(NSString, NSMutableString)`， `urls(NSURL)`，`data(NSData, NSMutableData)`或其他`NSDictionary`執行個體。

> [AZURE.NOTE] 額外的參數被序列化 JSON。 如果您想要將不同的物件，比上述的項目，您必須在課堂中實作下列方法︰
>
             -(NSString*)JSONRepresentation;
>
> 方法會傳回您物件的 JSON 表示。

### <a name="example"></a>範例

    NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
    [extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
    [extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
    [[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### <a name="limits"></a>限制

#### <a name="keys"></a>索引鍵

在每個機碼`NSDictionary`必須符合下列規則運算式︰

`^[a-zA-Z][a-zA-Z_0-9]*`

這表示開始鍵時，必須至少有一個字母，後面接著字母、 數字或底線 (\_)。

#### <a name="size"></a>大小

Extras 限於**1024年**個字元，每個呼叫 （一次以編碼 JSON 互動代理程式）。

在上一個範例中，傳送至伺服器 JSON 是 58 字元︰

    {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##<a name="reporting-application-information"></a>報告應用程式的資訊

您可以手動在報告追蹤資訊 （或任何其他應用程式特定資訊） 使用`sendAppInfo:`函數。

請注意，這些資訊可以傳送從屬參照︰ 只指定索引鍵的最新值將會保留為指定的裝置。

等事件 extras`NSDictionary`類別用來擷取應用程式的資訊，請注意，陣列或子字典會被視為一般的字串 （使用 JSON 序列化）。

**範例︰**

    NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
    [appInfo setObject:@"female" forKey:@"gender"];
    [appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
    [[EngagementAgent shared] sendAppInfo:appInfo];

### <a name="limits"></a>限制

#### <a name="keys"></a>索引鍵

在每個機碼`NSDictionary`必須符合下列規則運算式︰

`^[a-zA-Z][a-zA-Z_0-9]*`

這表示開始鍵時，必須至少有一個字母，後面接著字母、 數字或底線 (\_)。

#### <a name="size"></a>大小

應用程式的資訊僅限於**1024年**個字元，每個呼叫 （一次以編碼 JSON 互動代理程式）。

在上一個範例中，傳送至伺服器 JSON 是 44 字元︰

    {"birthdate":"1983-12-07","gender":"female"}
