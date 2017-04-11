<properties 
    pageTitle="ASP.NET MVC 教學課程的 DocumentDB: Web 應用程式開發 |Microsoft Azure" 
    description="若要建立使用 DocumentDB MVC web 應用程式的 ASP.NET MVC 教學課程。 將儲存 JSON 和存取裝載於 Azure 網站-ASP 網路 MVC 教學課程逐步 todo 應用程式的資料。" 
    keywords="asp.net mvc 教學課程，web 應用程式開發、 mvc web 應用程式，asp 淨 mvc 教學課程逐步解說"
    services="documentdb" 
    documentationCenter=".net" 
    authors="syamkmsft" 
    manager="jhubbard" 
    editor="cgronlun"/>


<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="hero-article" 
    ms.date="08/25/2016" 
    ms.author="syamk"/>

# <a name="_Toc395809351"></a>使用 DocumentDB ASP.NET MVC 教學課程︰ Web 應用程式開發

> [AZURE.SELECTOR]
- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md)
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md) 

醒目提示如何能有效運用 Azure DocumentDB 儲存和查詢 JSON 文件，這篇文章提供如何使用 Azure DocumentDB todo 應用程式的端對端逐步解說。 工作會儲存為 Azure DocumentDB JSON 文件。

![待辦清單建立此教學課程-ASP 網路 MVC 教學課程逐步 MVC web 應用程式的螢幕擷取畫面](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image1.png)

此逐步解說示範如何使用所提供 Azure DocumentDB 服務來儲存和存取裝載於 Azure ASP.NET MVC web 應用程式的資料。 如果您正在尋找的只解釋 DocumentDB，並不 ASP.NET MVC 組成的教學課程，請參閱[建立 DocumentDB C# 主控台應用程式](documentdb-get-started.md)。

> [AZURE.TIP] 本教學課程假設您有先前曾使用 ASP.NET MVC 和 Azure 網站。 如果您是新 ASP.NET 或[必要的工具](#_Toc395637760)，我們建議您從[GitHub][]及 [追蹤此範例中的指示下載完整的範例專案。 一旦您沒有內建，您可以檢視此文章，以了解專案的內容中的程式碼。

## <a name="_Toc395637760"></a>在此資料庫教學課程的先決條件

您應該之前遵循本文中的指示，請確定您有下列︰

- 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。
- [Visual Studio 2015](http://www.visualstudio.com/)或 Visual Studio 2013 更新 4 或更新版本。 如果使用 Visual Studio 2013，您必須安裝[Microsoft.Net.Compilers nuget 套件](https://www.nuget.org/packages/Microsoft.Net.Compilers/)，以加入 C# 6.0 的支援。 
- Azure SDK.NET 2.5.1 版本或更新版本，可透過[Microsoft Web 平台安裝程式][]。

使用 Visual Studio 2013 套用的更新 4 和 Azure SDK.net 版本 2.5.1 移本文中的所有螢幕擷取畫面。 如果您的系統設定有可能是您的畫面與選項並不完全，相符，但如果您符合上述的先決條件應該使用此方案的不同版本。

## <a name="_Toc395637761"></a>步驟 1︰ 建立 DocumentDB 資料庫帳戶

現在就讓我們開始建立 DocumentDB 帳戶。 如果您已經有帳戶，您可以跳至[建立新的 ASP.NET MVC 應用程式](#_Toc395637762)。

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

<br/>
現在，我們會逐步查看如何從基本上建立新的 ASP.NET MVC 應用程式。 

## <a name="_Toc395637762"></a>步驟 2︰ 建立新的 ASP.NET MVC 應用程式

您已經有帳戶，讓我們來建立新 ASP.NET 專案。

1. 在 Visual Studio 中，按一下 [**檔案**] 功能表指向 [**新增**]，然後再按一下 [**專案**。

    [**新專案**] 對話方塊隨即出現。
2. 在 [**專案類型**] 窗格中，展開**範本**、 **Visual C#**、**網頁**，然後選取**ASP.NET Web 應用程式**。

    ![ASP.NET Web 應用程式專案已醒目提示類型的 [新的專案資訊] 對話方塊的螢幕擷取畫面](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image10.png)

3. 在 [**名稱**] 方塊中，輸入專案的名稱。 本教學課程中使用名稱 「 todo 」。 如果您選擇使用此以外，然後無論本教學課程說話相關 todo 命名空間，您需要調整提供的程式碼範例，使用您為您的應用程式項目名稱。 

4. 按一下 [**瀏覽]** ，瀏覽至資料夾位置您想要建立專案，然後按一下**[確定**]。

    **ASP.NET 專案**] 對話方塊隨即出現。

    ![醒目提示 [MVC 應用程式範本與 host （主機） 核取 [雲端] 方塊中的 [新 ASP.NET 專案資訊] 對話方塊的螢幕擷取畫面](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image11.png)

5. 在 [範本] 窗格中，選取 [ **MVC**]。

6. 如果您打算裝載您的應用程式中 Azure 然後選取**主機在雲端**上右下方有 Azure 裝載應用程式。 我們已選取裝載在雲端，並執行裝載於 Azure 網站的應用程式。 選取這個選項，將您 preprovision Azure 網站，並讓生活更加時，若要部署最後一個可用的應用程式。 如果您想要主控位置，或不想要設定 Azure 事前，只要清除**在雲端的主機**。

7. 按一下**[確定]** ，並讓 Visual Studio 執行其周圍 scaffolding 空白 ASP.NET MVC 範本的動作。 

8. 如果您選擇此裝載在雲端，您會看到要求您登入您的 Azure 帳戶，並提供您的新網站的一些值的至少一個其他畫面。 提供所有其他的值，然後繼續。 

    我還沒有選擇 「 資料庫伺服器 」 以下因為我們不在這裡使用 Azure SQL 資料庫伺服器，我們要建立新的 Azure DocumentDB 帳戶稍後 Azure 入口網站中。

    如需關於選擇**應用程式服務計劃**和**資源群組**的詳細資訊，請參閱[Azure 應用程式服務方案深入概觀](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。

    ![設定 Microsoft Azure 網站] 對話方塊的螢幕擷取畫面](./media/documentdb-dotnet-application/image11_1.png)

9. 一旦完成 Visual Studio 建立樣本 MVC 應用程式的空白的 ASP.NET 應用程式，您可以在本機上執行。

    我們會略過執行專案的本機相信過 ASP.NET"Hello World 」 應用程式。 我們前往直接將 DocumentDB 新增至 [此專案，並建立應用程式。

## <a name="_Toc395637767"></a>步驟 3︰ 新增 DocumentDB 至 MVC web 應用程式專案

現在，我們已大部分的我們需要此方案 ASP.NET MVC 配管，讓我們來取得此教學課程中，我們 MVC web 應用程式中加入 Azure DocumentDB 的實際用途。

1. DocumentDB.NET SDK 封裝並分佈為 NuGet 套件。 若要在 Visual Studio 中取得 NuGet 套件，可使用 Visual Studio 中 NuGet 封裝管理員，以滑鼠右鍵按一下 [專案**總管**] 中，然後按一下 [**管理 NuGet 套件**。

    ![在方案總管中，以管理 NuGet 套件的反白顯示的 web 應用程式專案以滑鼠右鍵按一下選項的螢幕擷取畫面。](./media/documentdb-dotnet-application/image21.png)

    [**管理 NuGet 套件**] 對話方塊隨即出現。

2. 在 [NuGet**瀏覽**] 方塊中，輸入***Azure DocumentDB***。
    
    從結果]，安裝**Microsoft Azure DocumentDB 用戶端文件庫**套件。 將下載並安裝 DocumentDB 套件，以及所有的相依性，例如 Newtonsoft.Json。 按一下 [**預覽**] 視窗中的**[確定]**和**我接受]** **接受授權**視窗中以完成安裝。

    ![管理 NuGet 套件] 視窗中，與 Microsoft Azure DocumentDB 用戶端文件庫醒目提示的螢幕擷取畫面](./media/documentdb-dotnet-application/nuget.png)

    或者您可以使用封裝管理員主控台安裝套件。 若要這麼做，請在 [**工具**] 功能表上，按一下 [ **NuGet 封裝管理員**]，再按一下 [**封裝管理員主控台**。 出現提示時，輸入下列項目。

        Install-Package Microsoft.Azure.DocumentDB

3. 安裝套件之後，您的 Visual Studio 解決方案看起來應該像以下兩個新參照新增，Microsoft.Azure.Documents.Client 和 Newtonsoft.Json。

    ![新增到 JSON 資料專案總管] 中的兩個參照的螢幕擷取畫面](./media/documentdb-dotnet-application/image22.png)


##<a name="_Toc395637763"></a>步驟 4︰ 設定 ASP.NET MVC 應用程式
 
現在讓我們來加入此 MVC 應用程式模型、 檢視和控制站︰

- [新增模型](#_Toc395637764)。
- [新增控制器](#_Toc395637765)。
- [新增檢視](#_Toc395637766)]。


### <a name="_Toc395637764"></a>新增 JSON 資料模型

現在就讓我們開始建立**M** MVC，模型中。 

1. 在**方案總管]**中，以滑鼠右鍵按一下 [**模型**] 資料夾，按一下 [**新增**]，然後按一下**類別**。

    [**新增項目**] 對話方塊隨即出現。

2. 命名新課程**Item.cs** ，然後按一下 [**新增**]。 

3. 在新**Item.cs**檔案，新增下列*使用陳述式*的最後一個之後。
        
        using Newtonsoft.Json;
    
4. 現在取代 [將此程式碼 
        
        public class Item
        {
        }

    下列程式碼。

        public class Item
        {
            [JsonProperty(PropertyName = "id")]
            public string Id { get; set; }
             
            [JsonProperty(PropertyName = "name")]
            public string Name { get; set; }

            [JsonProperty(PropertyName = "description")]
            public string Description { get; set; }

            [JsonProperty(PropertyName = "isComplete")]
            public bool Completed { get; set; }
        }

    DocumentDB 中的所有資料會透過網路傳遞，而儲存為 JSON。 若要控制的方式的物件是序列化/由還原序列化的 JSON.NET 我們剛剛建立的**項目**類別中所示，您可以使用**JsonProperty**屬性。 您不**具有**執行此但我想要確保我屬性遵循 JSON camelCase 命名慣例。 
    
    不只可以您控制屬性名稱的格式時，它會進入 JSON，但可以像我一樣的**Description**屬性完全重新命名您的.NET 屬性。 
    

### <a name="_Toc395637765"></a>新增控制器

負責**M**，現在讓我們來建立**C** MVC，控制器類別中。

1. 在**方案總管]**中，**控制站**資料夾按一下滑鼠右鍵按一下 [**新增**]，再按一下**控制器**。

    [**新增 Scaffold** ] 對話方塊隨即出現。

2. 選取**MVC 5 控制器空的**然後再按一下 [**新增**]。

    ![與 MVC 5 控制器-空白選項的 [新增 Scaffold] 對話方塊的螢幕擷取畫面](./media/documentdb-dotnet-application/image14.png)

3. 命名新的控制站**ItemController。**

    ![[新增控制器] 對話方塊的螢幕擷取畫面](./media/documentdb-dotnet-application/image15.png)

    一旦建立檔案之後，您的 Visual Studio 解決方案應該類似下面的新 ItemController.cs 檔案**總管**] 中。 也會顯示先前建立的新 Item.cs 檔案。

    ![Visual Studio 方案的新 ItemController.cs 檔案與反白顯示的 Item.cs 檔案總管的螢幕擷取畫面](./media/documentdb-dotnet-application/image16.png)

    您可以關閉 ItemController.cs，我們會回到它更新版本。 

### <a name="_Toc395637766"></a>新增檢視

現在讓我們來建立 MVC，檢視中的 [ **V** :

- [新增項目索引檢視](#AddItemIndexView)。
- [新增新的項目檢視](#AddNewIndexView)。
- [新增編輯項目檢視](#_Toc395888515)。


#### <a name="AddItemIndexView"></a>新增項目索引檢視

1. 在**方案總管]**中，展開 [**檢視**] 資料夾，以滑鼠右鍵按一下您先前新增**ItemController**時，Visual Studio 建立您的空白**項目**資料夾，按一下 [**新增**]，然後按一下**檢視**。

    ![方案總管] 中顯示項目所建立的資料夾 Visual Studio 與反白顯示的 [新增檢視畫面] 命令的螢幕擷取畫面](./media/documentdb-dotnet-application/image17.png)

2. 在 [**新增檢視**] 對話方塊中，執行下列動作︰
    - 在 [**檢視名稱**] 方塊中，輸入***索引***。
    - 在 [**範本**] 方塊中，選取 [***清單***]。
    - 在**模型類別**] 方塊中，選取 [***項目 (todo。模型）***。
    - 將**資料內容類別**] 方塊保留為空白。 
    - 在 [版面配置頁面] 方塊中，輸入***~/Views/Shared/_Layout.cshtml***。
    
    ![螢幕擷取畫面顯示 [新增檢視] 對話方塊](./media/documentdb-dotnet-application/image18.png)

3. 所有的值會設定之後，請按一下 [**新增**]，並讓 Visual Studio 建立新的範本檢視。 完成時，隨即會開啟 cshtml 檔案所建立。 我們會回到它稍後時，我們可以關閉 Visual Studio 中的該檔案。

#### <a name="AddNewIndexView"></a>新增新的項目檢視

類似我們所建立的**項目索引**檢視的方式，我們會立即建立新的檢視以建立新的**項目**。

1. 在**方案總管**] 中的**項目**資料夾上按一下滑鼠右鍵，按一下 [**新增**]，然後按一下**檢視**。

2. 在 [**新增檢視**] 對話方塊中，執行下列動作︰
    - 在 [**檢視名稱**] 方塊中，輸入***建立***。
    - 在 [**範本**] 方塊中，選取 [***建立***。
    - 在**模型類別**] 方塊中，選取 [***項目 (todo。模型）***。
    - 將**資料內容類別**] 方塊保留為空白。
    - 在 [版面配置頁面] 方塊中，輸入***~/Views/Shared/_Layout.cshtml***。
    - 按一下 [**新增**]。

#### <a name="_Toc395888515"></a>新增編輯項目檢視

然後，最後，新增一個的最後一個檢視以供編輯**項目**中與之前相同的方式。

1. 在**方案總管**] 中的**項目**資料夾上按一下滑鼠右鍵，按一下 [**新增**]，然後按一下**檢視**。

2. 在 [**新增檢視**] 對話方塊中，執行下列動作︰
    - 在 [**檢視名稱**] 方塊中，輸入***編輯***。
    - 在 [**範本**] 方塊中，選取 [***編輯***]。
    - 在**模型類別**] 方塊中，選取 [***項目 (todo。模型）***。
    - 將**資料內容類別**] 方塊保留為空白。 
    - 在 [版面配置頁面] 方塊中，輸入***~/Views/Shared/_Layout.cshtml***。
    - 按一下 [**新增**]。

完成之後，關閉我們會稍後返回這些檢視表 Visual Studio 中的所有 cshtml 文件。

## <a name="_Toc395637769"></a>步驟 5: DocumentDB 連接

現在，是處理標準 MVC 內容，現在就讓我們開啟 for DocumentDB 新增程式碼。 

在此區段中，我們將會新增程式碼來處理下列動作︰

- [列出未完成的項目](#_Toc395637770)。
- [新增項目](#_Toc395637771)。
- [編輯項目](#_Toc395637772)。

### <a name="_Toc395637770"></a>列出 MVC web 應用程式中未完成的項目

首先請執行以下是新增包含連線至並使用 DocumentDB 所有邏輯的類別。 在此教學課程我們會封裝所有此邏輯中的稱為 DocumentDBRepository 存放庫課程。 

1. 在**方案總管]**中，以滑鼠右鍵按一下專案，按一下 [**新增**]，然後按一下**類別**。 命名新課程**DocumentDBRepository** ，然後按一下 [**新增**]。
 
2. 在新建立的**DocumentDBRepository**類別，並新增下列*使用陳述式*上方的*命名空間*宣告
        
        using Microsoft.Azure.Documents; 
        using Microsoft.Azure.Documents.Client; 
        using Microsoft.Azure.Documents.Linq; 
        using System.Configuration;
        using System.Linq.Expressions;
        using System.Threading.Tasks;

    現在取代 [將此程式碼 

        public class DocumentDBRepository
        {
        }

    下列程式碼。

        public static class DocumentDBRepository<T> where T : class
        {
            private static readonly string DatabaseId = ConfigurationManager.AppSettings["database"];
            private static readonly string CollectionId = ConfigurationManager.AppSettings["collection"];
            private static DocumentClient client;
    
            public static void Initialize()
            {
                client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
                CreateDatabaseIfNotExistsAsync().Wait();
                CreateCollectionIfNotExistsAsync().Wait();
            }
    
            private static async Task CreateDatabaseIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
    
            private static async Task CreateCollectionIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDocumentCollectionAsync(
                            UriFactory.CreateDatabaseUri(DatabaseId),
                            new DocumentCollection { Id = CollectionId },
                            new RequestOptions { OfferThroughput = 1000 });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
        }

    > [AZURE.TIP] 建立新的 DocumentCollection 時，您可以提供 OfferType，可讓您指定的新集合的效能層級的選擇性 RequestOptions 參數。 如果不會傳送此參數會使用預設優惠類型。 如需 DocumentDB 優惠類型，請參閱[DocumentDB 效能層級](documentdb-performance-levels.md)

3. 我們正在讀取的設定，有些值，因此開啟應用程式的**Web.config**檔案，並將在下的面幾行`<AppSettings>`一節。
    
        <add key="endpoint" value="enter the URI from the Keys blade of the Azure Portal"/>
        <add key="authKey" value="enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal"/>
        <add key="database" value="ToDoList"/>
        <add key="collection" value="Items"/>
    
4. 現在，更新*結束點*] 及 [ *authKey*使用金鑰刀 Azure 入口網站的值。 使用按鍵刀從**URI**為端點設定的值並作為**主索引鍵**或**第二個機碼**從鍵刀 authKey 設定的值。


    會負責線路圖 DocumentDB 存放庫，現在讓我們來加入我們的應用程式邏輯。

5. 我們想要能夠做 todo 清單應用程式的第一個項目會顯示未完成的項目。  複製並貼上下列的程式碼片段**DocumentDBRepository**類別內的任何位置。

        public static async Task<IEnumerable<T>> GetItemsAsync(Expression<Func<T, bool>> predicate)
        {
            IDocumentQuery<T> query = client.CreateDocumentQuery<T>(
                UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId))
                .Where(predicate)
                .AsDocumentQuery();

            List<T> results = new List<T>();
            while (query.HasMoreResults)
            {
                results.AddRange(await query.ExecuteNextAsync<T>());
            }

            return results;
        }


6. 開啟**ItemController**我們先前新增，並新增下列*使用陳述式*的命名空間宣告上方。

        using System.Net;
        using System.Threading.Tasks;
        using todo.Models;

    如果您的專案不是 「 todo 」，然後您需要更新使用 「 todo。模型 」;以反映您的專案名稱。

    現在取代 [將此程式碼

        //GET: Item
        public ActionResult Index()
        {
            return View();
        }

    下列程式碼。

        [ActionName("Index")]
        public async Task<ActionResult> IndexAsync()
        {
            var items = await DocumentDBRepository<Item>.GetItemsAsync(d => !d.Completed);
            return View(items);
        }
    
7. 開啟**Global.asax.cs** ，並將下列行新增至**Application_Start**方法 
 
        DocumentDBRepository<todo.Models.Item>.Initialize();
    
現在您的方案應會建立沒有任何錯誤。

如果您執行的應用程式現在，您會移至**HomeController**和 [**索引**] 檢視的控制站。 這是我們之所以選擇這個開頭 MVC 範本專案的預設行為，但我們不想要的 ！ 讓我們來變更這個 MVC 應用程式，來變更這個行為上路由。

開啟***應用程式\_Start\RouteConfig.cs***並找出行開頭 「 預設值: 「 並將其變更為如下所示。

        defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

這就是告訴如果您尚未指定值的 url，以控制路由，請改為 [**常用**]，使用**項目**作為控制器和使用者**索引**的檢視設為的 ASP.NET MVC。

如果您執行應用程式時，它會撥入您**ItemController**撥入存放庫類別，並使用 GetItems 方法若要返回 [**檢視**] 中的所有未完成的項目現在\\**項目**\\**索引**檢視。 

如果您建立並執行此專案現在，您現在應該會看到這看起來的項目。    

![此資料庫教學課程中所建立的 todo 清單 web 應用程式的螢幕擷取畫面](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771"></a>新增項目

現在就讓我們將某些項目放入資料庫，因此我們多個空白格線看起來。

讓我們來新增一些程式碼至 DocumentDBRepository 和 ItemController 保存在 DocumentDB 中的記錄。

1.  新增下列方法**DocumentDBRepository**課程。

        public static async Task<Document> CreateItemAsync(T item)
        {
            return await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId), item);
        }

    這個方法只要接受傳入的物件，然後將其保留在 DocumentDB。

2. 開啟 ItemController.cs 檔案，並新增下列程式碼片段的類別內。 這是 ASP.NET MVC 如何知道如何**建立**動作。 在此情況下只會呈現相關先前建立的 Create.cshtml 檢視。

        [ActionName("Create")]
        public async Task<ActionResult> CreateAsync()
        {
            return View();
        }

    現在，我們會需要此控制站，就會接受送出，從 [**建立**] 檢視中的部分其他程式碼。

2. 新增下一個區塊的程式碼會告訴 ASP.NET MVC 如何處理此控制站文章表單 ItemController.cs 類別。
    
        [HttpPost]
        [ActionName("Create")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> CreateAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.CreateItemAsync(item);
                return RedirectToAction("Index");
            }

            return View(item);
        }

    將此程式碼至 DocumentDBRepository 通話，並使用 CreateItemAsync 方法固定至資料庫的新 todo 項目。 
 
    **安全性附註**︰ **ValidateAntiForgeryToken**屬性以下可用來協助保護跨網站要求偽造攻擊此應用程式。 除了還有更多，只要新增此屬性，您的檢視必須使用這個反偽造權杖。 如需主旨及如何能正確實作的範例，請參閱[防止跨網站要求偽造][]。 [GitHub][]上提供的原始碼具有完整實作中的位置。

    **安全性附註**︰ 我們也使用**繫結**上的屬性的方法參數以協助防範過度張貼攻擊。 如需詳細資訊，請參閱[在 ASP.NET MVC 基本的 CRUD 作業][]。

這會結束將新項目新增至資料庫所需的程式碼。


### <a name="_Toc395637772"></a>編輯項目

有一個待辦事項]，我們的最後一個項目，也就是將功能新增至資料庫中編輯**項目**，並將其標示為完成。 檢視以供編輯已加入至專案，因此我們只需要再次將部分程式碼新增至 [我們控制器和**DocumentDBRepository**類別。

1. 將下列文字新增至**DocumentDBRepository**課程。

        public static async Task<Document> UpdateItemAsync(string id, T item)
        {
            return await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id), item);
        }

        public static async Task<T> GetItemAsync(string id)
        {
            try
            {
                Document document = await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id));
                return (T)(dynamic)document;
            }
            catch (DocumentClientException e)
            {
                if (e.StatusCode == HttpStatusCode.NotFound)
                {
                    return null;
                }
                else
                {
                    throw;
                }
            }
        }
    
    這些方法， **GetItem**擷取項目會傳遞 DocumentDB 回到**ItemController** ，然後執行 [**編輯**] 檢視中的第一個。
    
    第二個方法剛才新增**文件**中的**文件**的版本與 DocumentDB 傳入從**ItemController**取代。

2. 將下列文字新增至**ItemController**課程。

        [HttpPost]
        [ActionName("Edit")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> EditAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
                return RedirectToAction("Index");
            }

            return View(item);
        }

        [ActionName("Edit")]
        public async Task<ActionResult> EditAsync(string id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }

            Item item = await DocumentDBRepository<Item>.GetItemAsync(id);
            if (item == null)
            {
                return HttpNotFound();
            }

            return View(item);
        }
    
    第一種方法控點的 Http GET，當使用者按一下 [**索引**] 檢視中的 [**編輯**] 連結。 這個方法從 DocumentDB 擷取[**文件**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx)，然後傳送到 [**編輯**] 檢視。

    [**編輯**檢視然後會執行 Http 張貼至**IndexController**。 
    
    我們新增第二個方法可處理傳遞給 DocumentDB 保存在資料庫中的 [更新的物件。

就是它，就必須執行我們的應用程式、 列出未完成的**項目**、 新增新的**項目**，以及編輯**項目**。

## <a name="_Toc395637773"></a>步驟 6︰ 在本機上執行應用程式

若要測試您的本機電腦上的應用程式，請執行下列動作︰

1. Visual Studio 建立偵錯模式中的應用程式的 f5。 它應該建置應用程式，並啟動我們先前所看到的空白格線網頁瀏覽器︰

    ![此資料庫教學課程中所建立的 todo 清單 web 應用程式的螢幕擷取畫面](./media/documentdb-dotnet-application/image24.png)

    如果您使用 Visual Studio 2013，並收到錯誤訊息 「 無法等攔截子句的內文中。 」 您必須安裝[Microsoft.Net.Compilers nuget 套件](https://www.nuget.org/packages/Microsoft.Net.Compilers/)。 您也可以比較您的程式碼範例專案[GitHub][]上。 

2. 按一下 [**建立新**的連結，並將值新增至 [**名稱**與**描述**] 欄位。 **完成**的核取方塊取消選取 [離開否則新**項目**將會新增為已完成的狀態並不會出現在初始的清單。

    ![[建立] 檢視的螢幕擷取畫面](./media/documentdb-dotnet-application/image25.png)

3. 按一下 [**建立**您要重新導向到 [**索引**] 檢視，並出現在清單中**項目**。

    ![[索引] 檢視的螢幕擷取畫面](./media/documentdb-dotnet-application/image26.png)

    可以自由加入您的待辦清單中的幾個**項目**。

3. 按一下 [**編輯**] 旁的清單上的**項目**，您會移至 [**編輯**檢視您可以在此更新您的物件，包括**完成已**標幟的任何屬性。 如果您將標示為**完成**標幟，按一下 [**儲存**]，從清單中未完成的工作會移除**項目**。

    ![[索引] 檢視，並核取已完成] 方塊的螢幕擷取畫面](./media/documentdb-dotnet-application/image27.png)

4. 一次測試過應用程式，請按 Ctrl + F5 停止偵錯應用程式。 您準備好要部署 ！

## <a name="_Toc395637774"></a>步驟 7︰ 部署 Azure 網站的應用程式

您已經有完整的應用程式正確地使用 DocumentDB 我們要將此 web 應用程式部署至 Azure 網站。 如果您已選取**主機在雲端**，當您建立空白的 ASP.NET MVC 專案 Visual Studio 使這能夠輕鬆地與會為您的大部分的工作。 

1. 若要發佈此應用程式，您只需要是以滑鼠右鍵按一下 [專案**總管**] 中，按一下 [**發佈**。

    ![在方案總管中的 [發佈] 選項的螢幕擷取畫面](./media/documentdb-dotnet-application/image28.png)

2. 所有項目應該已設定根據您的認證。就要網站已經建立 Azure 中為您在**目的地 URL**所示，您需要執行的是按一下 [**發佈**]。

    ![[發佈網站] 對話方塊的螢幕擷取畫面 step by step 方塊在 Visual Studio-ASP 網路 MVC 教學課程](./media/documentdb-dotnet-application/image29.png)

幾秒數，Visual Studio 會完成發佈 web 應用程式，並啟動瀏覽器，您可以在這裡看見您方便 Azure 中執行的工作 ！

## <a name="_Toc395637775"></a>後續步驟

恭喜您 ！ 您只是建立您的第一個 ASP.NET MVC 使用 Azure DocumentDB web 應用程式，並將其發佈至 Azure 網站。 可以下載或從[GitHub][]複製完整的應用程式，包括未包含在此教學課程中詳細資料，並刪除功能的程式碼。 因此，如果您想要新增的應用程式，吸引程式碼，並將其新增此應用程式中。

若要新增其他功能至您的應用程式，請檢閱[DocumentDB.NET 文件庫](https://msdn.microsoft.com/library/azure/dn948556.aspx)中提供的 Api，隨意參與[GitHub][]DocumentDB.NET 文件庫。 


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web 平台安裝程式]: http://www.microsoft.com/web/downloads/platform.aspx
[防止跨網站要求偽造]: http://go.microsoft.com/fwlink/?LinkID=517254
[ASP.NET MVC 中的基本的 CRUD 作業]: http://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/documentdb-net-todo-app
