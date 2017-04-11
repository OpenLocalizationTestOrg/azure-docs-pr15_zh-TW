<properties 
    pageTitle="如何使用 Windows 標準互動 API" 
    description="如何使用 Windows 標準互動 API"            
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-store" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="how-to-use-the-engagement-api-on-windows-universal"></a>如何使用 Windows 標準互動 API

這份文件是文件[如何在 Windows 通用的整合互動](mobile-engagement-windows-store-integrate-engagement.md)的增益集︰ 會提供深度詳細資料，瞭解如何使用互動 API 報告您的應用程式統計資料。

請記住，如果您只想參與報告您的應用程式工作階段、 活動、 當機和技術資訊，請再最簡單的方法是所有您`Page`子類別繼承自`EngagementPage`類別。

如果您想要執行其他工作，例如，如果您需要報告應用程式特定事件與錯誤的工作，或如果您使用不同的方式中的應用程式的活動報表超過一個實作於`EngagementPage`類別，然後您需要使用互動 API。

互動 API 提供的`EngagementAgent`類別。 您可以透過這些方法存取`EngagementAgent.Instance`。

即使未初始化的代理程式模組，對 API 的每個呼叫延後，和代理程式可用時再次執行。

##<a name="engagement-concepts"></a>互動的概念

下列組件來調整常見[行動互動概念](mobile-engagement-concepts.md)通用 Windows 平台。

### <a name="session-and-activity"></a>`Session`與`Activity`

*活動*是通常與相關聯一頁的應用程式，也就是說*活動*啟動時頁面會顯示，並關閉頁面時停駐點︰ 這是大小寫時使用整合互動 SDK`EngagementPage`類別。

但*活動*可以也手動控制使用互動 API。 這個選項可讓您分割指定的頁面，以取得更多詳細資料 （例如知道頻率和多久對話方塊內使用，則此頁面） 此頁面的使用狀況的幾個 sub 部分。

##<a name="reporting-activities"></a>報表的活動

### <a name="user-starts-a-new-activity"></a>使用者啟動新的活動

#### <a name="reference"></a>參照

            void StartActivity(string name, Dictionary<object, object> extras = null)

您需要呼叫`StartActivity()`每次使用者活動的變更。 此函數的第一個呼叫開始新的使用者工作階段。

> [AZURE.IMPORTANT] 關閉應用程式時，SDK 自動呼叫 EndActivity 方法。 因此，建議使用者的變更，然後為 [無活動呼叫 EndActivity 方法，因為呼叫這個方法強制結束工作流程目前的工作階段時，呼叫 StartActivity 方法。

#### <a name="example"></a>範例

            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>使用者結束他目前活動

#### <a name="reference"></a>參照

            void EndActivity()

這會結束的活動和工作階段。 您不應該呼叫此方法，除非您知道您正在執行的動作。

#### <a name="example"></a>範例

            EngagementAgent.Instance.EndActivity();

##<a name="reporting-jobs"></a>報告工作

### <a name="start-a-job"></a>啟動工作

#### <a name="reference"></a>參照

            void StartJob(string name, Dictionary<object, object> extras = null)

您可以在一段時間追蹤 certains 工作使用工作。

#### <a name="example"></a>範例

            // An upload begins...
            
            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");
            
            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>結束工作

#### <a name="reference"></a>參照

            void EndJob(string name)

一旦已終止工作所追蹤的任務，您應致電這項作業，EndJob 方法所提供的工作名稱。

#### <a name="example"></a>範例

            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends
            
            EngagementAgent.Instance.EndJob("uploadData");

##<a name="reporting-events"></a>報告的事件

有三種類型的事件︰

-   獨立的事件
-   工作階段事件
-   工作事件

### <a name="standalone-events"></a>獨立的事件

#### <a name="reference"></a>參照

            void SendEvent(string name, Dictionary<object, object> extras = null)

獨立事件發生以外的工作階段的內容。

#### <a name="example"></a>範例

            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>工作階段事件

#### <a name="reference"></a>參照

            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

工作階段事件通常用來報告使用者在他們的工作階段期間執行的動作。

#### <a name="example"></a>範例

**不含資料︰**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");
            
            // or
            
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**使用資料︰**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>工作事件

#### <a name="reference"></a>參照

            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

工作事件通常用來報告工作期間由使用者執行的動作。

#### <a name="example"></a>範例

            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

##<a name="reporting-errors"></a>報表錯誤

有三種類型的錯誤︰

-   獨立的錯誤
-   工作階段錯誤
-   工作錯誤

### <a name="standalone-errors"></a>獨立的錯誤

#### <a name="reference"></a>參照

            void SendError(string name, Dictionary<object, object> extras = null)

相反工作階段的錯誤，獨立錯誤可能會發生以外的工作階段的內容。

#### <a name="example"></a>範例

            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>工作階段錯誤

#### <a name="reference"></a>參照

            void SendSessionError(string name, Dictionary<object, object> extras = null)

工作階段錯誤通常用來報告他的工作階段期間影響使用者的錯誤。

#### <a name="example"></a>範例

            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>工作錯誤

#### <a name="reference"></a>參照

            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

若要執行的工作，而不是在目前的使用者工作階段的相關關聯錯誤。

#### <a name="example"></a>範例

            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##<a name="reporting-crashes"></a>報告當機

代理程式提供兩種方法可以處理當機。

### <a name="send-an-exception"></a>傳送例外狀況

#### <a name="reference"></a>參照

            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>範例

您可以藉由呼叫傳送隨時例外狀況︰

            EngagementAgent.Instance.SendCrash(aCatchedException);

您也可以使用 [選擇性參數終止互動工作階段，同時比傳送當機。 若要這麼做，請連絡︰

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

如果您執行這項作業時，傳送當機之後，只要將關閉工作階段與工作。

### <a name="send-an-unhandled-exception"></a>傳送處理的例外狀況

#### <a name="reference"></a>參照

            void SendCrash(Exception e)

互動也會提供將傳送處理的例外狀況，如果您**已停用**的互動自動**當機**報告的方法。 這是使用應用程式 UnhandledException 事件處理常式內時尤其有用。

此方法會**永遠**會終止之後呼叫的互動工作階段及工作。

#### <a name="example"></a>範例

您可以使用其實作 UnhandledExceptionEventArgs 處理常式。 例如，新增`Current_UnhandledException`的方法`App.xaml.cs`檔案︰

            // In your App.xaml.cs file
            
            // Code to execute on Unhandled Exceptions
            void Current_UnhandledException(object sender, UnhandledExceptionEventArgs e)
            {
               EngagementAgent.Instance.SendCrash(e.Exception,false);
            }

在 「 公用 App() {」 中 App.xaml.cs 加上︰

            Application.Current.UnhandledException += Current_UnhandledException;

##<a name="device-id"></a>裝置識別碼

            String EngagementAgent.Instance.GetDeviceId()

您可以取得的行動裝置識別碼呼叫這個方法。

##<a name="extras-parameters"></a>額外的參數

可附加任意資料事件、 錯誤、 活動或工作。 這些資料可以使用字典結構化。 索引鍵和值可以是任何類型。

額外資料被序列化，因此如果您想要插入自己的類型 extras] 中您必須新增此類型的資料合約。

### <a name="example"></a>範例

我們建立新類別，「 人員 」。

            using System.Runtime.Serialization;
            
            namespace Microsoft.Azure.Engagement
            {
              [DataContract]
              public class Person
              {
                public Person(string name, int age)
                {
                  Age = age;
                  Name = name;
                }
            
                // Properties
            
                [DataMember]
                public int Age
                {
                  get;
                  set;
                }
            
                [DataMember]
                public string Name
                {
                  get;
                  set; 
                }
              }
            }

然後，我們將會新增`Person`額外的執行個體。

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);
            
            EngagementAgent.Instance.SendEvent("Event", extras);

> [AZURE.WARNING] 如果您將其他類型的物件，請確定其 tostring （） 方法實作傳回人力可讀取的字串。

### <a name="limits"></a>限制

#### <a name="keys"></a>索引鍵

物件中的每個機碼，必須符合下列規則運算式︰

`^[a-zA-Z][a-zA-Z_0-9]*$`

這表示開始鍵時，必須至少有一個字母，後面接著字母、 數字或底線 (\_)。

#### <a name="size"></a>大小

Extras 限於每個呼叫**1024年**個字元。

##<a name="reporting-application-information"></a>報告應用程式的資訊

### <a name="reference"></a>參照

            void SendAppInfo(Dictionary<object, object> appInfos)

您可以手動在報告追蹤資訊 （或任何其他應用程式特定資訊） 使用 SendAppInfo() 函數。

請注意，此資料可以不斷︰ 只指定索引鍵的最新值將會保留為指定的裝置。 事件 extras]，例如使用的字典\<物件，物件\>要附加的資料。

### <a name="example"></a>範例

            Dictionary<object, object> appInfo = new Dictionary<object, object>()
              {
                {"birthdate", "1983-12-07"},
                {"gender", "female"}
              };
            
            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>限制

#### <a name="keys"></a>索引鍵

物件中的每個機碼，必須符合下列規則運算式︰

`^[a-zA-Z][a-zA-Z_0-9]*$`

這表示開始鍵時，必須至少有一個字母，後面接著字母、 數字或底線 (\_)。

#### <a name="size"></a>大小

應用程式的資訊僅限於每個呼叫**1024年**個字元。

在上一個範例中，傳送至伺服器 JSON 是 44 字元︰

            {"birthdate":"1983-12-07","gender":"female"}

##<a name="logging"></a>記錄
###<a name="enable-logging"></a>啟用記錄功能

您可以設定 SDK 來產生測試 IDE 主控台中檢視的記錄。
根據預設，將無法啟用這些記錄。 若要自訂此，更新屬性`EngagementAgent.Instance.TestLogEnabled`其中一個可用的值`EngagementTestLogLevel`列舉，例如︰

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();
 
