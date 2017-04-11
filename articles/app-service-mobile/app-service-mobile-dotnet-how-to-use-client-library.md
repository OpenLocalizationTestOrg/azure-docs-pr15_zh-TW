<properties
    pageTitle="使用應用程式服務行動應用程式的受管理的用戶端文件庫 (Windows |Xamarin) |Microsoft Azure"
    description="瞭解如何使用.NET 用戶端 Azure 應用程式服務行動應用程式視窗和 Xamarin 應用程式。"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>如何使用受管理的用戶端 Azure Mobile 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

##<a name="overview"></a>概觀

本指南為您示範如何執行 Azure Windows 應用程式服務行動應用程式和 Xamarin 應用程式中使用受管理的用戶端文件庫的常見案例。 如果您不熟悉 Mobile 應用程式，您應該先完成[Azure Mobile 應用程式快速入門][1]教學課程。 本指南中，我們著重於用戶端管理 SDK。 若要進一步瞭解伺服器端 Sdk 的行動應用程式，請參閱[.NET 伺服器 SDK]的文件[2]或[Node.js 伺服器 SDK][3]。

## <a name="reference-documentation"></a>參照文件

用戶端 SDK 的參考文件的位置︰ [Azure 行動應用程式.NET 用戶端參照][4]。
您也可以在[Azure 範例 GitHub 存放庫]中尋找有幾個用戶端範例[5]。

## <a name="supported-platforms"></a>支援的平台

.NET 平台支援下列平台︰

* Xamarin Android 的 API 19 發行至 24 (透過 Nougat KitKat)
* Xamarin iOS 版本 iOS 8.0 及更新版本
* 通用 Windows 平台
* 在 Windows Phone 8.1
* Windows Phone 8.0 除了 Silverlight 應用程式

「 伺服器流程 「 驗證用於呈現的使用者介面的網頁檢視。  如果裝置無法呈現網頁檢視使用者介面，所需其他的驗證方法。  此 SDK 並因此不適用於監看型態或同樣在受限制的裝置。

##<a name="setup"></a>設定與先決條件

我們假設您有已建立及發佈 Mobile 應用程式後端專案，其中包含至少一個資料表。  在本主題中使用的程式碼，名為表格`TodoItem`都有下列資料行︰ `Id`， `Text`，及`Complete`。 此資料表是相同的資料表建立完成後，[Azure Mobile 應用程式快速入門]。

C# 中的相對應輸入用戶端類型是下列類別︰

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

[JsonPropertyAttribute] [6]用來定義*PropertyName*之間的對應的用戶端類型，以及。

若要瞭解如何建立您的行動應用程式後端的資料表，請參閱[.NET 伺服器 SDK 主題]中的資訊[7]或[Node.js 伺服器 SDK 主題][8]。 如果您使用 [快速入門 Azure 入口網站中建立您的行動應用程式後端，您也可以使用**簡單的表格**設定[Azure 入口網站]中。

###<a name="how-to-install-the-managed-client-sdk-package"></a>如何︰ 安裝受管理的用戶端 SDK 套件

使用以下兩種方法的其中一個[NuGet]的行動應用程式的安裝受管理的用戶端 SDK 套件[9]:

+ **Visual Studio**以滑鼠右鍵按一下您的專案，請按一下 [**管理 NuGet 封包**，搜尋`Microsoft.Azure.Mobile.Client`封裝，然後按一下 [**安裝**]。

+ **Xamarin Studio**以滑鼠右鍵按一下您的專案，按一下 [**新增** > **新增 NuGet 套件**，搜尋`Microsoft.Azure.Mobile.Client `封裝，，然後按一下 [**新增套件**。

在您主要的活動的檔案，請記得加入下列**使用**陳述式︰

    using Microsoft.WindowsAzure.MobileServices;

###<a name="symbolsource"></a>如何︰ 使用 Visual Studio 中偵錯符號

符號 Microsoft.Azure.Mobile 命名空間都提供[SymbolSource][10]。  請參閱[SymbolSource 指示][11]與 Visual Studio 整合 SymbolSource。

##<a name="create-client"></a>建立行動應用程式用戶端

下列程式碼會建立[MobileServiceClient] [12]會用來存取您的行動應用程式後端的物件。

    var client = new MobileServiceClient("MOBILE_APP_URL");

在前面的程式碼，取代`MOBILE_APP_URL`Mobile 應用程式後端的 url，其中中找不到您的行動應用程式後端[Azure 入口網站]中的刀。 MobileServiceClient 物件應該單一。

## <a name="work-with-tables"></a>使用表格

下一節詳細說明如何搜尋並擷取記錄並修改在資料表中的資料。  包含下列主題︰

* [建立表格參照](#instantiating)
* [查詢資料](#querying)
* [傳回資料的篩選](#filtering)
* [傳回資料的排序](#sorting)
* [在頁面中傳回的資料](#paging)
* [選取特定的資料行](#selecting)
* [查閱識別碼記錄](#lookingup)
* [處理不具型別查詢](#untypedqueries)
* [插入資料](#inserting)
* [更新資料](#updating)
* [刪除資料](#deleting)
* [解決衝突和樂觀](#optimisticconcurrency)
* [繫結至 Windows 使用者介面](#binding)
* [變更頁面大小](#pagesize)

###<a name="instantiating"></a>如何︰ 建立表格參照

所有的程式碼存取或修改後端表格中的資料上通話函數`MobileServiceTable`物件。 取得表格參照，則可電話[GetTable]方法，如下所示︰

    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();

傳回的物件會使用輸入的序列化模型。 同時支援不具型別的序列化模型。 下列範例[會建立不具型別表格的參照]︰

    // Get an untyped table reference
    IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

在不具型別查詢中，您必須指定基礎 OData 查詢字串。

###<a name="querying"></a>如何︰ 查詢您的行動應用程式的資料

本節說明如何查詢發給 Mobile 應用程式後端，其中還包含下列功能︰

- [傳回資料的篩選](#filtering)
- [傳回資料的排序](#sorting)
- [在頁面中傳回的資料](#paging)
- [選取特定的資料行](#selecting)
- [查閱識別碼的資料](#lookingup)

>[AZURE.NOTE]若要防止傳回的所有資料列，會強制執行伺服器導向頁面大小。  分頁可防止大型資料集的預設要求產生負面影響服務。  若要傳回超過 50 個資料列，請使用`Skip`和`Take`方法，在 [頁面中傳回的資料] 中所述。

###<a name="filtering"></a>如何︰ 篩選傳回的資料

下列程式碼會說明如何篩選資料，包括`Where`子句的查詢。 會傳回的所有項目`todoTable`其`Complete`屬性是等於`false`。 將[位置]函數套用篩選述詞，對資料表查詢的資料列。

    // This query filters out completed TodoItems and items without a timestamp.
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .ToListAsync();

您可以檢視傳送到後端的訊息檢查軟體，例如瀏覽器開發人員工具] 或 [ [Fiddler]要求 URI。 如果您查看要求 URI 時，請注意，修改查詢字串︰

    GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

此 OData 要求的伺服器 SDK 翻譯成的 SQL 查詢︰

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0

傳遞給函數`Where`方法的任意數目的條件。

    // This query filters out completed TodoItems where Text isn't null
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
       .ToListAsync();

此範例會成數伺服器 SDK 的 SQL 查詢︰

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0

此查詢也可以分成多個子句︰

    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .Where(todoItem => todoItem.Text != null)
       .ToListAsync();

兩種方法不區分大小，可能會交替使用。  離職選項&mdash;的串連在查詢中的多個述詞&mdash;更精簡，建議。

`Where`子句支援會之作業的翻譯成的 OData 子集合。 操作包括︰

* 關聯式運算子 (= =、 ！ =，<、 < =、 >，> =)，
* 算術運算子 (+、-，/、 *，%)，
* 數字 （Math.Floor、 Math.Ceiling） 的精確度
* 字串函數 （長度、 子字串、 取代、 IndexOf、 StartsWith、 EndsWith）
* 日期屬性 （年、 月、 天、 小時、 分鐘、 第二個），
* 存取物件的屬性，和
* 結合任何一項作業的運算式。

考慮伺服器 SDK 的支援，您可以考慮[OData v3 若文件]。

###<a name="sorting"></a>如何︰ 排序傳回的資料

下列程式碼會說明如何藉由在查詢中加入的[排序方式]] 或 [ [OrderByDescending]函數來排序資料。 會傳回的項目`todoTable`以以遞增順序排列`Text`欄位。

    // Sort items in ascending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderBy(todoItem => todoItem.Text)
    List<TodoItem> items = await query.ToListAsync();

    // Sort items in descending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderByDescending(todoItem => todoItem.Text)
    List<TodoItem> items = await query.ToListAsync();

###<a name="paging"></a>如何︰ 在頁面中傳回的資料

根據預設後, 端會傳回的前 50 個資料列。 您可以增加傳回的列數呼叫[採取]的方法。 使用`Take`以及要求的特定 「 頁 」 總的資料集的查詢所傳回的[略過]方法。 在執行時，下列查詢會傳回資料表中的前三個項目。

    // Define a filtered query that returns the top 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Take(3);
    List<TodoItem> items = await query.ToListAsync();

下列修訂的查詢略過的前三個結果，並傳回三個結果。 此查詢會產生的第二 」 頁 」 的頁面大小與三個項目所在的資料。

    // Define a filtered query that skips the top 3 items and returns the next 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Skip(3)
                    .Take(3);
    List<TodoItem> items = await query.ToListAsync();

[IncludeTotalCount]方法要求總計的_所有_傳回，略過指定任何分頁/限制子句的記錄︰

    query = query.IncludeTotalCount();

在真實世界應用程式中，您可以使用類似上述範例中的查詢和呼叫器控制項或相容的 UI，頁面之間瀏覽。

>[AZURE.NOTE]要覆寫 Mobile 應用程式後端 50 個資料列限制，您也必須將[EnableQueryAttribute]套用至公用取得方法，並指定分頁行為。 套用至方法時，下列設定資料列的形式傳回至 1000年的最大值︰
>
>    [EnableQuery(MaxTop=1000)]

### <a name="selecting"></a>如何︰ 選取特定的資料行

您可以指定要包含在結果中[選取]的子句新增至您的查詢的屬性設定。 例如，下列程式碼會顯示如何選取只有一個欄位，同時選取並設定多個欄位的格式︰

    // Select one field -- just the Text
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => todoItem.Text);
    List<string> items = await query.ToListAsync();

    // Select multiple fields -- both Complete and Text info
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => string.Format("{0} -- {1}",
                        todoItem.Text.PadRight(30), todoItem.Complete ?
                        "Now complete!" : "Incomplete!"));
    List<string> items = await query.ToListAsync();

目前所描述的所有函數都會加總值，因此我們可以保留鏈結它們。 每個鏈結的通話會影響多個查詢。 一個範例︰

    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Where(todoItem => todoItem.Complete == false)
                    .Select(todoItem => todoItem.Text)
                    .Skip(3).
                    .Take(3);
    List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>如何︰ 查閱識別碼的資料

[LookupAsync]函數可以用來查詢從資料庫與特定的識別碼。

    // This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
    TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="untypedqueries"></a>如何︰ 執行不具型別的查詢

當執行查詢，使用不具型別的表格物件時，您必須明確指定 OData 查詢字串呼叫[ReadAsync]，如下列範例所示︰

    // Lookup untyped data using OData
    JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

您取得您可以使用 like 屬性袋的 JSON 值。 如需有關 JToken 和 Newtonsoft Json.NET 的詳細資訊，請參閱[Json.NET]網站。

### <a name="inserting"></a>如何︰ 插入 Mobile 應用程式後端的資料

所有的用戶端類型必須包含名稱為**識別碼**，這是預設字串成員。 此**識別碼**，才能執行 CRUD 作業以及離線同步處理。 下列程式碼會說明如何使用[InsertAsync]方法，將表格插入新的資料列。 參數包含要插入.NET 物件的資料。

    await todoTable.InsertAsync(todoItem);

如果自訂的唯一識別碼值不會包含在`todoItem`伺服器時，插入，產生 GUID。
您可以在呼叫傳回之後，透過檢查物件來擷取所產生的編號。

若要插入不具型的資料，您可能會利用 Json.NET:

    JObject jo = new JObject();
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

使用電子郵件地址為唯一字串 id 範例如下︰

    JObject jo = new JObject();
    jo.Add("id", "myemail@emaildomain.com");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

### <a name="working-with-id-values"></a>使用 [識別碼值

行動應用程式支援專屬的自訂字串值，以表格的**識別碼**] 資料行。 字串值，可讓應用程式，以使用自訂的值，例如電子郵件地址] 或 [使用者名稱的識別碼。  字串識別碼為您提供下列優點︰

* 識別碼會產生不會建立資料庫的來回。
* 記錄是合併來自不同資料表或資料庫的工作變得更容易。
* 使用應用程式的邏輯更有效地整合識別碼值。

行動應用程式後端時的插入記錄未設定字串的識別碼值，則會產生的唯一值 id。 您可以使用[Guid.NewGuid]方法來產生在用戶端或後端自己識別碼值。

    JObject jo = new JObject();
    jo.Add("id", Guid.NewGuid().ToString("N"));

###<a name="modifying"></a>如何︰ 在行動應用程式後端修改資料

下列程式碼會說明如何使用[UpdateAsync]方法來更新現有記錄的相同的識別碼，以新的資訊。 參數包含要更新為.NET 物件的資料。

    await todoTable.UpdateAsync(todoItem);

若要更新不具型的資料，您可能會利用[Json.NET] ，如下所示︰

    JObject jo = new JObject();
    jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.UpdateAsync(jo);

`id`進行更新時，必須指定欄位。 後端使用`id`欄位以識別要更新的資料列。 `id` ] 欄位可以取自的結果`InsertAsync`來電。 `ArgumentException`如果您嘗試更新項目，但不供會引發`id`值。

###<a name="deleting"></a>如何︰ 刪除在行動應用程式後端的資料

下列程式碼會說明如何使用[DeleteAsync]方法若要刪除現有的執行個體。 執行個體由`id`的欄位組`todoItem`。

    await todoTable.DeleteAsync(todoItem);

若要刪除不具型的資料，您可能會利用 Json.NET，如下所示︰

    JObject jo = new JObject();
    jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    await table.DeleteAsync(jo);

當您刪除要求時，必須指定識別碼。 其他屬性不會傳送到服務，或在服務會略過。 結果`DeleteAsync`通話通常是`null`。 您可以從的結果取得傳入的識別碼`InsertAsync`來電。 A`MobileServiceInvalidOperationException`就會擲回當您嘗試刪除項目，而不用指定`id`欄位。

###<a name="optimisticconcurrency"></a>如何︰ 使用樂觀的衝突解決程序

兩個或多個用戶端可能撰寫同時變更相同的項目。 不衝突偵測上次寫入會覆寫任何先前的更新。 **樂觀控制項**假設每次交易認可，因此不會使用任何資源鎖定。  之前先測試交易，樂觀控制項確認沒有其他交易已修改資料。 如果已經修改的資料，認可的交易被復原。

行動應用程式支援樂觀控制項以追蹤變更每個項目使用`version`定義您的行動應用程式後端的每個資料表之系統屬性欄。 每次更新記錄時，行動應用程式設定`version`為新值的記錄的屬性。 每個更新要求期間`version`隨附要求記錄的屬性與伺服器上的記錄的相同屬性進行比較。 如果版本傳遞要求不相符的後端，然後用戶端文件庫會引發`MobileServicePreconditionFailedException<T>`例外狀況。 包含的例外狀況類型是包含記錄的伺服器版本後端的記錄。 應用程式可以再使用這項資訊，以決定是否要執行一次使用正確的更新要求`version`認可變更後端 」 的值。

定義資料行的資料表類別上`version`啟用樂觀系統屬性。 例如︰

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

        // *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "version")]
        public string Version { set; get; }
    }


使用不具型別的表格的應用程式設定來啟用樂觀`Version`加上標幟`SystemProperties`的資料表，如下所示。

    //Enable optimistic concurrency by retrieving version
    todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

除了啟用樂觀，您必須也掌握`MobileServicePreconditionFailedException<T>`呼叫[UpdateAsync]時，程式碼中的例外狀況。  套用正確的一項來解決衝突`version`更新的記錄與通話[UpdateAsync]解決的記錄。 下列程式碼會顯示如何解決一次偵測到寫入衝突︰

    private async void UpdateToDoItem(TodoItem item)
    {
        MobileServicePreconditionFailedException<TodoItem> exception = null;

        try
        {
            //update at the remote table
            await todoTable.UpdateAsync(item);
        }
        catch (MobileServicePreconditionFailedException<TodoItem> writeException)
        {
            exception = writeException;
        }

        if (exception != null)
        {
            // Conflict detected, the item has changed since the last query
            // Resolve the conflict between the local and server item
            await ResolveConflict(item, exception.Item);
        }
    }


    private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
    {
        //Ask user to choose the resoltion between versions
        MessageDialog msgDialog = new MessageDialog(
            String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
            serverItem.Text, localItem.Text),
            "CONFLICT DETECTED - Select a resolution:");

        UICommand localBtn = new UICommand("Commit Local Text");
        UICommand ServerBtn = new UICommand("Leave Server Text");
        msgDialog.Commands.Add(localBtn);
        msgDialog.Commands.Add(ServerBtn);

        localBtn.Invoked = async (IUICommand command) =>
        {
            // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
            // catching a MobileServicePreConditionFailedException.
            localItem.Version = serverItem.Version;

            // Updating recursively here just in case another change happened while the user was making a decision
            UpdateToDoItem(localItem);
        };

        ServerBtn.Invoked = async (IUICommand command) =>
        {
            RefreshTodoItems();
        };

        await msgDialog.ShowAsync();
    }

如需詳細資訊，請參閱[離線 Azure 行動應用程式中的資料同步處理]主題。

###<a name="binding"></a>如何︰ Windows 使用者介面的資料繫結 Mobile 應用程式

本節說明如何顯示傳回的資料使用 Windows 應用程式中的使用者介面元素的物件。  在下列程式碼繫結至查詢的未完成的項目與清單的來源。 [MobileServiceCollection]建立注意行動應用程式的繫結集合。

    // This query filters out completed TodoItems.
    MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
        .Where(todoItem => todoItem.Complete == false)
        .ToCollectionAsync();

    // itemsControl is an IEnumerable that could be bound to a UI list control
    IEnumerable itemsControl  = items;

    // Bind this to a ListBox
    ListBox lb = new ListBox();
    lb.ItemsSource = items;

受管理的執行階段中的某些控制項支援稱為[ISupportIncrementalLoading]介面。 這個介面 」 可讓使用者捲動時，要求額外的資料控制項。 沒有內建支援透過[MobileServiceIncrementalLoadingCollection]，其會自動處理來電的控制項通用的 Windows 應用程式介面。 使用`MobileServiceIncrementalLoadingCollection`Windows 應用程式，如下所示︰

    MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
    items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

    ListBox lb = new ListBox();
    lb.ItemsSource = items;

若要在 Windows Phone 8 和 「 Silverlight 」 應用程式中使用新的集合，請使用`ToCollection`延伸方法上的`IMobileServiceTableQuery<T>`和`IMobileServiceTable<T>`。 若要載入資料，請連絡`LoadMoreItemsAsync()`。

    MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
    await items.LoadMoreItemsAsync();

當您使用的集合，則可電話建立`ToCollectionAsync`或`ToCollection`，就可以使用者介面控制項繫結的集合。  這個集合是分頁注意。  從網路集合載入資料，因為載入有時候會失敗。 若要處理這類失敗，覆寫`OnException`上的方法`MobileServiceIncrementalLoadingCollection`處理所產生的來電至] 的例外狀況`LoadMoreItemsAsync`。

如果您的表格有許多欄位，但只想要在控制項中顯示將其中一些考量。 您可以使用 「[選取特定的資料行](#selecting)[前一節提供的指導方針，若要選取特定的資料行，顯示在 ui 上。

###<a name="pagesize"></a>變更頁面大小

Azure Mobile 應用程式會傳回最大值的每個要求 50 項目預設。  您可以變更分頁大小增加用戶端與伺服器上的最大的頁面大小。  若要增加要求的頁面大小，請指定`PullOptions`時使用`PullAsync()`:

    PullOptions pullOptions = new PullOptions
        {
            MaxPageSize = 100
        };

假設您已經`PageSize`要求等於或大於 100 伺服器內，傳回最多 100 個項目。

##<a name="#offlinesync"></a>使用離線的表格

離線的資料表，使用離線時使用本機 SQLite 商店資料。  針對本機完成作業的所有資料表 SQLite 都儲存，而不是遠端伺服器存放都區。  若要建立離線的資料表，請先準備您的專案︰

1. 在 Visual Studio 中，以滑鼠右鍵按一下方案 >**管理 NuGet 套件解決方案...**]，然後搜尋並安裝方案中的所有專案的**Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet 套件。

2. （選用）若要支援 Windows 裝置，請安裝下列 SQLite runtime 封裝其中一項︰

    * **Windows 8.1 執行階段︰**安裝[windows 8.1 SQLite][3]。
    * **Windows Phone 8.1:**安裝[Windows Phone 8.1 版的 SQLite][4]。
    * **通用 Windows 平台**安裝[的通用 Windows 標準 SQLite][5]。

3. （選擇性）。 適用於 Windows 裝置上，按一下 [**參考資料** > **加入參考**，展開 [ **Windows** ] 資料夾 > [**延伸**]，然後啟用適當**SQLite for Windows** SDK 以及**Visual c + + 2013 Runtime Windows** SDK。
    SQLite SDK 名稱略有不同的每個 Windows 平台。

您可以建立表格參照之前，必須準備本機存放區︰

    var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
    store.DefineTable<TodoItem>();

    //Initializes the SyncContext using the default IMobileServiceSyncHandler.
    await this.client.SyncContext.InitializeAsync(store);

存放區初始化通常是在建立用戶端之後。  **OfflineDbPath**應該適用於所有您所支援的平台上的檔案名稱。  如果路徑的完整的路徑 （也就是開始有一條斜線），然後就會使用該路徑。  如果不完整的路徑，檔案會在特定位置中。

* IOS 和 Android 裝置，預設路徑是 「 個人檔案 」 資料夾。
* 若為 Windows 裝置，預設的路徑是特定應用程式的 [AppData] 資料夾。

表格參照可使用`GetSyncTable<>`方法︰

    var table = client.GetSyncTable<TodoItem>();

您不需要驗證使用離線的表格。  您只需要驗證您通訊的後端服務。

###<a name="syncoffline"></a>同步處理離線的表格

與後端預設不同步離線的資料表。  同步處理分割成兩個部分。  您可以變更推分別下載新項目。  以下是一般的同步處理的方法︰

    public async Task SyncAsync()
    {
        ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

        try
        {
            await this.client.SyncContext.PushAsync();

            await this.todoTable.PullAsync(
                //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
                //Use a different query name for each unique query in your program
                "allTodoItems",
                this.todoTable.CreateQuery());
        }
        catch (MobileServicePushFailedException exc)
        {
            if (exc.PushResult != null)
            {
                syncErrors = exc.PushResult.Errors;
            }
        }

        // Simple error/conflict handling. A real application would handle the various errors like network conditions,
        // server conflicts and others via the IMobileServiceSyncHandler.
        if (syncErrors != null)
        {
            foreach (var error in syncErrors)
            {
                if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                {
                    //Update failed, reverting to server's copy.
                    await error.CancelAndUpdateItemAsync(error.Result);
                }
                else
                {
                    // Discard local change.
                    await error.CancelAndDiscardItemAsync();
                }

                Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
            }
        }
    }

如果第一個引數`PullAsync`是空值，則不會使用累加同步處理。  每次同步處理作業擷取的所有記錄。

SDK 執行隱含`PushAsync()`之前抽取記錄。

衝突處理發生在`PullAsync()`方法。  您可以為線上的資料表相同的方式處理衝突。  會產生衝突時`PullAsync()`稱為而不是期間插入、 更新或刪除。 如果有多個衝突發生時，它們都結合在一起到單一 MobileServicePushFailedException。  分別處理每次失敗。

##<a name="#customapi"></a>使用自訂的 API

自訂的 API 可讓您定義自訂的端點，公開伺服器功能無法對應至 [插入、 更新、 刪除，或讀取作業。 您可以藉由使用自訂的 API，讓更多的控制訊息，包括讀取和設定 HTTP 郵件標題以及定義 JSON 以外的郵件本文格式。

您在用戶端呼叫[InvokeApiAsync]兩種方法之一來呼叫自訂的 API。 例如，下列程式碼會文章要求傳送給**completeAll** API 後端上︰

    var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);

此表單是輸入的方法通話，並要求定義**MarkAllResult**傳回類型。 支援的輸入及不具型的方法。

##<a name="authentication"></a>驗證的使用者

行動應用程式支援使用各種不同的外部的身分識別提供者的應用程式使用者驗證與授權︰ Facebook、 Google、 Microsoft 帳戶、 Twitter 和 Azure Active Directory。 您可以設定權限限制為特定作業已驗證的使用者存取的表格。 您也可以使用已驗證的使用者身分的識別實作伺服器指令碼中的授權規則。 如需詳細資訊，請參閱教學課程[新增驗證您的應用程式]。

兩個驗證流程支援︰_用戶端管理_與_伺服器管理_流量。 伺服器管理流程提供最簡單的驗證體驗，必須提供者的 web 驗證介面。 用戶端管理流程時必須提供者特定裝置的特定 Sdk 允許更深入的整合與裝置的特定功能。

>[AZURE.NOTE] 我們建議您生產應用程式中使用的用戶端管理流程。

若要設定驗證，您必須使用一或多個身分識別提供者註冊您的應用程式。  用戶端識別碼] 和 [用戶端密碼應用程式，則會產生身分識別提供者。  在您的後端啟用 Azure 應用程式服務驗證和授權然後設定這些值。  如需詳細資訊，請遵循在教學課程中[新增驗證您的應用程式]的詳細的指示。

本節中涵蓋下列主題︰

+ [用戶端管理驗證](#clientflow)
+ [伺服器管理驗證](#serverflow)
+ [快取的 「 驗證 」 token](#caching)

###<a name="clientflow"></a>用戶端管理驗證

您的應用程式可以獨立連絡 [身分識別提供者，然後提供傳回的權杖期間登入您的後端。 此用戶端流程可讓您為使用者提供單一登入體驗，或從身分識別提供者取得其他使用者資料。 用戶端流程驗證時，偏好使用伺服器流程的身分識別提供者 SDK 提供更多原生的 UX 風格，並讓其他自訂作業。

範例所提供的下列的用戶端流程驗證模式︰

+ [Active Directory 驗證文件庫](#adal)
+ [Facebook 或 Google](#client-facebook)
+ [即時 SDK](#client-livesdk)

#### <a name="adal"></a>驗證 Active Directory 驗證庫的使用者

您可以使用啟動使用者驗證的 Active Directory 驗證文件庫 (ADAL) 從用戶端使用 Azure Active Directory 驗證。

1. 依照[如何設定 Active Directory 登入應用程式服務]教學課程中設定您的行動應用程式後端 AAD 登入。 請確定完成註冊的原生的用戶端應用程式的選用步驟。
2. 在 Visual Studio 或 Xamarin Studio 中，開啟您的專案，並新增參照`Microsoft.IdentityModel.CLients.ActiveDirectory`NuGet 套件。 在搜尋時，包含預先發行版本。
3. 將下列程式碼新增至 [應用程式中，根據您所使用的平台。 在每個，進行下列取代項目︰

    * **插入授權這裡**取代為您佈建應用程式的租用戶的名稱。 格式應該是 https://login.windows.net/contoso.onmicrosoft.com。 此值可以複製您在[Azure 傳統入口網站]的 Azure Active Directory 中的 [網域] 索引標籤。
    * **插入資源識別碼-以下**換成您的行動應用程式後端的用戶端識別碼。 您可以從 [**進階]**索引標籤底下**Azure Active Directory 設定**入口網站中，以取得的用戶端識別碼。
    * **插入用戶端識別碼-以下**換成您複製的原生的用戶端應用程式的用戶端識別碼。
    * 取代您的網站_/.auth/login/done_端點，請使用 HTTPS 配置**插入重新導向-URI 這裡**。 此值應該類似_https://contoso.azurewebsites.net/.auth/login/done_。

    每個平台所需的程式碼所示︰

    **在 Windows 中︰**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            while (user == null)
            {
                string message;
                try
                {
                    AuthenticationContext ac = new AuthenticationContext(authority);
                    AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                        new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                    JObject payload = new JObject();
                    payload["access_token"] = ar.AccessToken;
                    user = await App.MobileService.LoginAsync(
                        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    **Xamarin.iOS**

        private MobileServiceUser user;
        private async Task AuthenticateAsync(UIViewController view)
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(view));
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await client.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    **Xamarin.Android**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(this));
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await client.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
            }
            catch (Exception ex)
            {
                AlertDialog.Builder builder = new AlertDialog.Builder(this);
                builder.SetMessage(ex.Message);
                builder.SetTitle("You must log in. Login Required");
                builder.Create().Show();
            }
        }
        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);
            AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
        }

####<a name="client-facebook"></a>單一登入使用從 Facebook 或 Google 的權杖

您可以使用用戶端流程，如下所示的 Facebook 或 Google 此程式碼片段。

    var token = new JObject();
    // Replace access_token_value with actual value of your access token obtained
    // using the Facebook or Google SDK.
    token.Add("access_token", "access_token_value");

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {
        while (user == null)
        {
            string message;
            try
            {
                // Change MobileServiceAuthenticationProvider.Facebook
                // to MobileServiceAuthenticationProvider.Google if using Google auth.
                user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

####<a name="client-livesdk"></a>單一登入 Microsoft 帳戶使用 Live SDK

若要驗證的使用者，您必須註冊您的應用程式，在 [開發人員中心的 Microsoft 帳戶。 設定您的行動應用程式後端註冊詳細資料。 若要建立 Microsoft 帳戶註冊，並將其連線至您的行動應用程式後端，完成[註冊使用 Microsoft 帳戶登入您的應用程式]中的步驟進行。 如果您有 Windows Store 和 Windows Phone 8/Silverlight 版本，您的應用程式的請先註冊 Windows 市集的版本。

下列程式碼會驗證使用 Live SDK，並使用傳回的權杖登入您的行動應用程式後端。

    private LiveConnectSession session;
    //private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the Mobile App backend.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the service URL.
        LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
        //// Create the authentication client for Windows Phone using the client ID of the registration.
        //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

        while (session == null)
        {
            // Request the authentication token from the Live authentication service.
            // The wl.basic scope should always be requested.  Other scopes can be added
            LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
            if (result.Status == LiveConnectSessionStatus.Connected)
            {
                session = result.Session;

                // Get information about the logged-in user.
                LiveConnectClient client = new LiveConnectClient(session);
                LiveOperationResult meResult = await client.GetAsync("me");

                // Use the Microsoft account auth token to sign in to App Service.
                MobileServiceUser loginResult = await App.MobileService
                    .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                // Display a personalized sign-in greeting.
                string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                var dialog = new MessageDialog(message, title);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
            else
            {
                session = null;
                var dialog = new MessageDialog("You must log in.", "Login Required");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
    }

如需詳細資訊，請參閱[Windows Live SDK]的文件。

###<a name="serverflow"></a>伺服器管理驗證

一旦您已註冊您的身分識別提供者，請呼叫[LoginAsync]方法 [MobileServiceClient] 在您的提供者[MobileServiceAuthenticationProvider]值。 例如，下列程式碼會啟動伺服器流程登入使用 Facebook。

    private MobileServiceUser user;
    private async System.Threading.Tasks.Task Authenticate()
    {
        while (user == null)
        {
            string message;
            try
            {
                user = await client
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

如果您使用的 Facebook 以外的身分識別提供者，變更的值[MobileServiceAuthenticationProvider]的值，您的提供者。

伺服器的流程，Azure 應用程式服務會顯示 [登入] 頁面的 [選取的提供者管理 OAuth 驗證流程。  一旦身分識別提供者會傳回，Azure 應用程式服務會產生應用程式服務驗證權杖。 [LoginAsync]方法會傳回[MobileServiceUser]，提供兩個已驗證的使用者的[使用者識別碼]和[MobileServiceAuthenticationToken]，作為 JSON web 權杖 (JWT)。 此 token 加可以快取，並重複使用，直到到期。 如需詳細資訊，請參閱[快取的 「 驗證 」 token](#caching)。

###<a name="caching"></a>快取的 「 驗證 」 token

在某些情況下，呼叫登入方法可避免第一個驗證成功後所儲存的提供者驗證權杖。  Windows 市集和 UWP 應用程式可以使用[PasswordVault]快目前 「 驗證 」 token 取順利登入之後，如下所示︰

    await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

    PasswordVault vault = new PasswordVault();
    vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
        client.currentUser.MobileServiceAuthenticationToken));

使用者識別碼值會儲存為認證的使用者名稱和權杖已儲存的密碼。 在後續的啟動程式，您可以檢查**PasswordVault**快取認證。 找不到，並嘗試再次通過驗證後端，否則時，下列範例會使用快取的認證︰

    // Try to retrieve stored credentials.
    var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
    if (creds != null)
    {
        // Create the current user from the stored credentials.
        client.currentUser = new MobileServiceUser(creds.UserName);
        client.currentUser.MobileServiceAuthenticationToken =
            vault.Retrieve("Facebook", creds.UserName).Password;
    }
    else
    {
        // Regular login flow and cache the token as shown above.
    }

當您登出使用者時，您也必須移除已儲存的認證，如下所示︰

    client.Logout();
    vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));

Xamarin 應用程式使用[Xamarin.Auth] Api 安全地**帳戶**物件中儲存的認證。 如需使用這些 Api 的範例，請參閱[ContosoMoments 相片分享範例]中的[AuthStore.cs]檔。

當您使用用戶端管理驗證時，您也可以快取存取權杖，從您的提供者，例如 Facebook 或 Twitter 取得。 可以提供此 token 加後端，要求新的驗證憑證，，如下所示︰

    var token = new JObject();
    // Replace <your_access_token_value> with actual value of your access token
    token.Add("access_token", "<your_access_token_value>");

    // Authenticate using the access token.
    await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);

##<a name="pushnotifications"></a>推播通知

下列主題涵蓋推播通知︰

* [註冊推入通知](#register-for-push)
* [取得 Windows 市集套件 SID](#package-sid)
* [註冊使用跨平台範本](#register-xplat)

###<a name="register-for-push"></a>如何︰ 註冊推入通知

在行動應用程式的用戶端可讓您註冊 Azure 通知集線器與推入通知。 當註冊，取得您取得從特定推入通知服務 (PNS) 控點。 當您建立註冊，然後提供此值，以及任何標籤。 下列程式碼，註冊您的 Windows 應用程式推播通知與 Windows 通知服務 (WNS):

    private async void InitNotificationsAsync()
    {
        // Request a push notification channel.
        var channel =await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // Register for notifications using the new channel.
        await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
    }

如果您發送至 WNS，您必須[取得 Windows 市集套件 SID](#package-sid)。  如需詳細資訊，在 Windows 應用程式，包括如何註冊範本註冊，請參閱[新增至您的應用程式的推入通知]。

不支援從用戶端要求標籤。  從註冊時，會自動卸除標籤要求。
如果您想要標記以註冊您的裝置，建立自訂的 API，代表您執行註冊使用通知集線器 API。  [呼叫自訂 API](#customapi)而不是`RegisterNativeAsync()`方法。

###<a name="package-sid"></a>如何︰ 取得 Windows 市集套件 SID

封裝 SID 所需的啟用 Windows 市集應用程式中的推播通知。  若要接收套件 SID，註冊 Windows 市集應用程式。

若要取得此值︰

1. Visual Studio 方案總管] 中以滑鼠右鍵按一下 Windows 市集應用程式專案，請按一下 [**市集** > **建立關聯的應用程式存放區...**。
2. 在精靈中，按一下 [**下一步**、 使用您的 Microsoft 帳戶登入、 輸入您的應用程式的名稱中**保留新的應用程式名稱**，然後按一下 [**保留**。
3. 應用程式註冊已成功建立之後，請選取 [應用程式的名稱，按 [**下一步**，，然後按一下**建立關聯**。
4. [Windows 開發人員中心]使用您的 Microsoft 帳戶登入。 按一下 [**我的應用程式**，您建立的應用程式註冊。
5. 按一下 [**應用程式管理** > **應用程式的身分識別**，然後再捲動以尋找您的**套件 SID**。

封裝 SID 的許多使用它視為 URI，您需要在此情況下使用_ms 應用程式: / /_為配置。 請記下您套件 SID 格式來造句此值做為前置詞的版本。

Xamarin 應用程式需要能登錄應用程式在 iOS 或 Android 的平台上執行的一些其他程式碼。 如需詳細資訊，請參閱針對您的平台的主題︰

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push)

###<a name="register-xplat"></a>如何︰ 傳送跨平台通知 Register 推入範本

若要註冊範本，請使用`RegisterAsync()`的範本，如下所示的方法︰

        JObject templates = myTemplates();
        MobileService.GetPush().RegisterAsync(channel.Uri, templates);

您的範本，應該`JObject`類型，而且可以包含多個範本 JSON 格式如下︰

        public JObject myTemplates()
        {
            // single template for Windows Notification Service toast
            var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

            var templates = new JObject
            {
                ["generic-message"] = new JObject
                {
                    ["body"] = template,
                    ["headers"] = new JObject
                    {
                        ["X-WNS-Type"] = "wns/toast"
                    },
                    ["tags"] = new JArray()
                },
                ["more-templates"] = new JObject {...}
            };
            return templates;
        }

方法**RegisterAsync()**也會接受第二個方塊︰

        MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);

安全性註冊期間立即移除所有標籤。 若要新增標籤至安裝或安裝中的範本，請參閱 [使用.NET 後端伺服器 SDK Azure Mobile 應用程式]。

若要傳送通知，利用這些已註冊的範本，請參閱[通知集線器 Api]。

##<a name="misc"></a>其他主題

###<a name="errors"></a>如何︰ 處理錯誤

在後端發生錯誤時，用戶端 SDK 引發`MobileServiceInvalidOperationException`。  下列範例會示範如何處理傳回後端的例外狀況︰

    private async void InsertTodoItem(TodoItem todoItem)
    {
        // This code inserts a new TodoItem into the database. When the operation completes
        // and App Service has assigned an Id, the item is added to the CollectionView
        try
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);
        }
        catch (MobileServiceInvalidOperationException e)
        {
            // Handle error
        }
    }

在[行動應用程式檔案範例]找處理錯誤狀況的另一個範例。 在[LoggingHandler]提供登入後端進行要求的記錄代理人處理常式 （動作）。

###<a name="headers"></a>如何︰ 自訂要求標頭

若要支援您特定應用程式的情況，您可能需要自訂行動應用程式後端與通訊。 例如，您可能要新增自訂頁首至每封外寄的邀請，或甚至變更回覆狀態碼。 您可以使用自訂[DelegatingHandler]，如下列範例所示︰

    public async Task CallClientWithHandler()
    {
        HttpResponseMessage[]
        MobileServiceClient client = new MobileServiceClient("AppUrl",
            new MyHandler()
            );
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await todoTable.InsertAsync(newItem);
    }

    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage>
            SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Change the request-side here based on the HttpRequestMessage
            request.Headers.Add("x-my-header", "my value");

            // Do the request
            var response = await base.SendAsync(request, cancellationToken);

            // Change the response-side here based on the HttpResponseMessage

            // Return the modified response
            return response;
        }
    }


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/en-us/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: http://www.symbolsource.org/
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[將驗證新增至您的應用程式]: app-service-mobile-windows-store-dotnet-get-started-users.md
[離線資料 Azure 行動應用程式中的同步處理]: app-service-mobile-offline-data-sync.md
[新增至您的應用程式的 [推入通知]: app-service-mobile-windows-store-dotnet-get-started-push.md
[註冊使用 Microsoft 帳戶登入您的應用程式]: app-service-mobile-how-to-configure-microsoft-authentication.md
[如何設定 Active Directory 登入應用程式服務]: app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/en-us/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/en-us/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[不]: https://msdn.microsoft.com/en-us/library/azure/jj554275(v=azure.10).aspx
[建立不具型的資料表參照]: https://msdn.microsoft.com/en-us/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/en-us/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/en-us/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/en-us/library/azure/jj871654(v=azure.10).aspx
[排序方式]: https://msdn.microsoft.com/en-us/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/en-us/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/en-us/library/azure/mt691741(v=azure.10).aspx
[記錄]: https://msdn.microsoft.com/en-us/library/azure/dn250574(v=azure.10).aspx
[選取]: https://msdn.microsoft.com/en-us/library/azure/dn250569(v=azure.10).aspx
[略過]: https://msdn.microsoft.com/en-us/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/en-us/library/azure/dn250536.(v=azure.10)aspx
[使用者識別碼]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[位置]: https://msdn.microsoft.com/en-us/library/azure/dn250579(v=azure.10).aspx
[Azure 入口網站]: https://portal.azure.com/
[Azure 傳統入口網站]: https://manage.windowsazure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/en-us/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows 開發人員中心]: https://dev.windows.com/en-us/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[Windows Live SDK]: https://msdn.microsoft.com/en-us/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[通知集線器 Api]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[行動應用程式檔案範例]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3 若文件]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: (https://github.com/azure-appservice-samples/ContosoMoments/blob/dev/src/Mobile/ContosoMoments/Helpers/AuthStore.cs)
[ContosoMoments 相片共用的範例]: https://github.com/azure-appservice-samples/ContosoMoments