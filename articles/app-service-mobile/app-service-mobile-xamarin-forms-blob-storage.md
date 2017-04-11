<properties
    pageTitle="連線到 Xamarin.Forms 應用程式中的 Azure 儲存體"
    description="將影像加入至 todo 清單 Xamarin.Forms 行動應用程式中，只要連接至 Azure blob 儲存體"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="connect-to-azure-storage-in-your-xamarinforms-app"></a>連線到 Xamarin.Forms 應用程式中的 Azure 儲存體

## <a name="overview"></a>概觀

Azure Mobile 應用程式的用戶端和伺服器 SDK 支援具有 /tables 端點 CRUD 作業的結構化資料的離線同步處理。 通常此資料會儲存在資料庫或類似儲存、，通常資料儲存區無法大型二進位有效率地儲存資料。 此外，部分應用程式有相關的資料儲存在其他地方 （例如 blob 儲存體，檔案共用），及很有用，才能建立 /tables 端點中的記錄和其他資料之間的關聯。

本主題說明如何新增圖像的支援 Mobile 應用程式 todo 清單快速入門。 您必須先完成教學課程中[建立 Xamarin.Forms 應用程式]。

在本教學課程中，您會建立儲存帳戶，並新增至您的行動應用程式後端的連線字串。 然後您要加入新的行動應用程式類型新繼承`StorageController<T>`至伺服器專案。

>[AZURE.TIP] 本教學課程中會有[小幫手] 範例](https://azure.microsoft.com/documentation/samples/app-service-mobile-dotnet-todo-list-files/)，可以部署 Azure 帳戶。 

## <a name="prerequisites"></a>必要條件

* 完成[建立 Xamarin.Forms 應用程式]教學課程，列出其他的先決條件。 本文會使用該教學課程中的已完成應用程式。

>[AZURE.NOTE] 如果您想要快速入門 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](https://tryappservice.azure.com/?appServiceName=mobile)。 那里，您可以立即建立短暫入門行動應用程式的應用程式服務，必要時，沒有信用卡和沒有承諾。

## <a name="create-a-storage-account"></a>建立儲存的帳戶

1. 依照[建立 Azure 儲存體帳戶]教學課程中建立儲存帳戶。 

2. 在 Azure 入口網站中，瀏覽至您的新建立的儲存空間帳戶然後按一下 [**索引鍵**] 圖示。 複製**主索引的連線字串**。

3. 瀏覽至您的行動應用程式後端。 在 [**所有設定** -> **應用程式設定** -> **連接字串**，建立新的金鑰名為`MS_AzureStorageAccountConnectionString`並使用您儲存帳戶中複製的值。 使用**自訂**做為索引鍵的類型。

## <a name="add-a-storage-controller-to-the-server"></a>新增儲存空間控制器至伺服器

您需要新增新的控制站至伺服器專案會回應要求 SA 權杖 Azure 儲存空間，以及返回記錄中的對應檔案的清單︰

- [新增儲存空間控制器至伺服器專案](#add-controller-code)
- [登錄存放控制器路由](#routes-registered)
- [用戶端與伺服器通訊](#client-communication)

###<a name="add-controller-code"></a>新增儲存空間控制器至伺服器專案

1. 在 Visual Studio 中，開啟.NET 伺服器專案。 新增 Nuget 封裝[Microsoft.Azure.Mobile.Server.Files]。 請務必選取 [**包含搶鮮版**。

2. 在 Visual Studio 中，開啟.NET 伺服器專案。 **控制站**資料夾按一下滑鼠右鍵，然後選取 [**新增** -> **控制器** -> **Web API 2 控制器空的**。 命名控制器`TodoItemStorageController`。

3. 新增下列使用陳述式︰

        using Microsoft.Azure.Mobile.Server.Files;
        using Microsoft.Azure.Mobile.Server.Files.Controllers;

4. 變更基底類別`StorageController`:
    
        public class TodoItemStorageController : StorageController<TodoItem>

5. 在類別中新增下列方法︰

        [HttpPost]
        [Route("tables/TodoItem/{id}/StorageToken")]
        public async Task<HttpResponseMessage> PostStorageTokenRequest(string id, StorageTokenRequest value)
        {
            StorageToken token = await GetStorageTokenAsync(id, value);

            return Request.CreateResponse(token);
        }

        // Get the files associated with this record
        [HttpGet]
        [Route("tables/TodoItem/{id}/MobileServiceFiles")]
        public async Task<HttpResponseMessage> GetFiles(string id)
        {
            IEnumerable<MobileServiceFile> files = await GetRecordFilesAsync(id);

            return Request.CreateResponse(files);
        }

        [HttpDelete]
        [Route("tables/TodoItem/{id}/MobileServiceFiles/{name}")]
        public Task Delete(string id, string name)
        {
            return base.DeleteFileAsync(id, name);
        }

6. 更新網頁 API 設定來設定屬性路由。 在**Startup.MobileApp.cs**，新增下列列`ConfigureMobileApp()`方法之後的定義,`config`變數︰

        config.MapHttpAttributeRoutes();

7. [發佈至您的行動應用程式後端伺服器專案。

###<a name="routes-registered"></a>登錄存放控制器路由

新`TodoItemStorageController`公開兩個子資源管理記錄︰

- StorageToken

    + HTTP 文章︰ 建立的儲存空間權杖
    
        `/tables/TodoItem/{id}/MobileServiceFiles`
    
- MobileServiceFiles

    + HTTP GET︰ 擷取的檔案清單與相關聯記錄
    
        `/tables/TodoItem/{id}/MobileServiceFiles`

    + HTTP 刪除︰ 刪除檔案資源識別項中指定的檔案
    
        `/tables/TodoItem/{id}/MobileServiceFiles/{fileid}`

###<a name="client-communication"></a>用戶端與伺服器通訊

請注意，`TodoItemStorageController`並*不*已上傳或下載 blob 的路由。 這是因為行動用戶端與 blob 儲存體*直接*互動，才能執行這些作業後第一份 SA 權杖 （共用 Access 簽章） 安全地存取的特定 blob 或容器。 這是很重要的架構設計，否則存取儲存空間會受到延展性和行動裝置的後端的可用性。 不過，直接連接到 Azure 儲存體，行動用戶端可以利用其功能，例如自動分割地理通訊群組。

共用的 access 簽名提供委派的存取您儲存的帳戶中的資源。 這表示您可以授與用戶端在指定期間內的時間，以指定的權限，您儲存的帳戶中的物件權限限制而不需要共用您的帳戶便捷鍵。 若要深入瞭解，請參閱[瞭解共用 Access 簽章]。

下圖顯示的用戶端與伺服器的互動。 上傳檔案，再用戶端，請從服務要求 SA 權杖。 服務會用來產生新的 SA，然後傳回用戶端的儲存空間連線字串。 SA 時間限制，而只是特定的檔案或容器限制權限。 行動用戶端然後使用此 SA 和 Azure 儲存體用戶端 SDK 檔案上傳至 blob 儲存體。

![要求的 SA 權杖](./media/app-service-mobile-xamarin-forms-blob-storage/storage-token-diagram.png)

## <a name="update-your-client-app-to-add-image-support"></a>更新您的用戶端應用程式新增圖像的支援

在 Visual Studio 或 Xamarin Studio 中開啟 Xamarin.Forms 快速入門專案。 您將會安裝 Nuget 套件及更新可攜式文件庫專案 iOS、 Android 和 Windows 用戶端專案︰

- [新增 Nuget 套件](#add-nuget)
- [新增 IPlatform 介面](#add-iplatform)
- [新增 FileHelper 類別](#add-filehelper)
- [將檔案同步處理常式](#file-sync-handler)
- [更新 TodoItemManager](#update-todoitemmanager)
- [新增詳細資料檢視](#add-details-view)
- [更新主要檢視](#update-main-view)
- [更新 Android 專案](#update-android)， [iOS 專案](#update-ios)， [Windows 專案](#update-windows)

>[AZURE.NOTE] 本教學課程中僅包含 Android iOS，與 Windows 市集平台，而不是在 Windows Phone 的相關指示。

###<a name="add-nuget"></a>新增 Nuget 套件

以滑鼠右鍵按一下方案，然後選取 [**管理 Nuget 套件解決方案**。 新增下列 Nuget 封裝方案中的**所有**專案。 請務必核取 [**包含搶鮮版**。

  - [Microsoft.Azure.Mobile.Client.Files]

  - [Microsoft.Azure.Mobile.Client.SQLiteStore]

  - [PCLStorage]

為了方便，此範例使用[PCLStorage]文件庫，但 Azure Mobile 應用程式用戶端 SDK 不需要。

[PCLStorage]: https://www.nuget.org/packages/PCLStorage/

###<a name="add-iplatform"></a>新增 IPlatform 介面

建立新的介面`IPlatform`主可攜式文件庫專案中。 這將依照載入在執行階段的權限的平台特定類別[Xamarin.Forms DependencyService]模式。 您稍後將加入的實作特定中每個用戶端的專案。

1. 新增下列使用陳述式︰

        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Sync;

2. 實作取代下列動作︰

        public interface IPlatform
        {
            Task <string> GetTodoFilesPathAsync();

            Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata);

            Task<string> TakePhotoAsync(object context);

            Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename);
        }

###<a name="add-filehelper"></a>新增 FileHelper 類別

1. 建立新類別`FileHelper`主可攜式文件庫專案中。 新增下列使用陳述式︰

        using System.IO;
        using PCLStorage;
        using System.Threading.Tasks;
        using Xamarin.Forms;

2. 新增類別定義︰

        public class FileHelper
        {
            public static async Task<string> CopyTodoItemFileAsync(string itemId, string filePath)
            {
                IFolder localStorage = FileSystem.Current.LocalStorage;

                string fileName = Path.GetFileName(filePath);
                string targetPath = await GetLocalFilePathAsync(itemId, fileName);

                var sourceFile = await localStorage.GetFileAsync(filePath);
                var sourceStream = await sourceFile.OpenAsync(FileAccess.Read);

                var targetFile = await localStorage.CreateFileAsync(targetPath, CreationCollisionOption.ReplaceExisting);

                using (var targetStream = await targetFile.OpenAsync(FileAccess.ReadAndWrite)) {
                    await sourceStream.CopyToAsync(targetStream);
                }

                return targetPath;
            }

            public static async Task<string> GetLocalFilePathAsync(string itemId, string fileName)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();

                string recordFilesPath = Path.Combine(await platform.GetTodoFilesPathAsync(), itemId);

                    var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(recordFilesPath);
                    if (checkExists == ExistenceCheckResult.NotFound) {
                        await FileSystem.Current.LocalStorage.CreateFolderAsync(recordFilesPath, CreationCollisionOption.ReplaceExisting);
                    }

                return Path.Combine(recordFilesPath, fileName);
            }

            public static async Task DeleteLocalFileAsync(Microsoft.WindowsAzure.MobileServices.Files.MobileServiceFile fileName)
            {
                string localPath = await GetLocalFilePathAsync(fileName.ParentId, fileName.Name);
                var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(localPath);

                if (checkExists == ExistenceCheckResult.FileExists) {
                    var file = await FileSystem.Current.LocalStorage.GetFileAsync(localPath);
                    await file.DeleteAsync();
                }
            }
        }

###<a name="file-sync-handler"></a>將檔案同步處理常式

建立新類別`TodoItemFileSyncHandler`主可攜式文件庫專案中。 這個類別包含從 Azure SDK 時新增或移除檔案時，通知您的程式碼的回撥。

Azure 行動用戶端 SDK 實際也不會儲存檔案的任何資料︰ 在用戶端 SDK 叫用的實作`IFileSyncHandler`在決定是否以及如何將檔案儲存在本機的裝置上。

1. 新增下列使用陳述式︰

        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Xamarin.Forms;

2. 類別定義取代下列動作︰ 

        public class TodoItemFileSyncHandler : IFileSyncHandler
        {
            private readonly TodoItemManager todoItemManager;

            public TodoItemFileSyncHandler(TodoItemManager itemManager)
            {
                this.todoItemManager = itemManager;
            }

            public Task<IMobileServiceFileDataSource> GetDataSource(MobileServiceFileMetadata metadata)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();
                return platform.GetFileDataSource(metadata);
            }

            public async Task ProcessFileSynchronizationAction(MobileServiceFile file, FileSynchronizationAction action)
            {
                if (action == FileSynchronizationAction.Delete) {
                    await FileHelper.DeleteLocalFileAsync(file);
                }
                else { // Create or update. We're aggressively downloading all files.
                    await this.todoItemManager.DownloadFileAsync(file);
                }
            }
        }

###<a name="update-todoitemmanager"></a>更新 TodoItemManager

1. 在**TodoItemManager.cs**，取消註解行`#define OFFLINE_SYNC_ENABLED`。

2. 在**TodoItemManager.cs**，新增下列使用陳述式︰

        using System.IO;
        using Xamarin.Forms;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Eventing;

3. 建構函式中`TodoItemManager`，在通話`DefineTable()`:

        // Initialize file sync
        this.client.InitializeFileSyncContext(new TodoItemFileSyncHandler(this), store);

4. 建構函式，取代通話`InitializeAsync`以下列。 這樣就能確定沒有回撥本機存放區中修改記錄時。 檔案同步處理功能會使用這些回撥来觸發在您的檔案同步處理常式。

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

5. 在 [ `SyncAsync()`，在通話`PushAsync()`:

        await this.todoTable.PushFileChangesAsync();

6. 新增下列方法來`TodoItemManager`:

        internal async Task DownloadFileAsync(MobileServiceFile file)
        {
            var todoItem = await todoTable.LookupAsync(file.ParentId);
            IPlatform platform = DependencyService.Get<IPlatform>();

            string filePath = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name); 
            await platform.DownloadFileAsync(this.todoTable, file, filePath);
        }

        internal async Task<MobileServiceFile> AddImage(TodoItem todoItem, string imagePath)
        {
            string targetPath = await FileHelper.CopyTodoItemFileAsync(todoItem.Id, imagePath);
            return await this.todoTable.AddFileAsync(todoItem, Path.GetFileName(targetPath));
        }

        internal async Task DeleteImage(TodoItem todoItem, MobileServiceFile file)
        {
            await this.todoTable.DeleteFileAsync(file);
        }

        internal async Task<IEnumerable<MobileServiceFile>> GetImageFilesAsync(TodoItem todoItem)
        {
            return await this.todoTable.GetFilesAsync(todoItem);
        }

###<a name="add-details-view"></a>新增詳細資料檢視

在此區段中，您將加入新的 [詳細資料] 檢視 todo 項目。 使用者選取 todo 項目，並可讓新的圖像，以新增至項目時，會建立檢視。

1. 使用下列實作可攜式文件庫專案中新增新的類別**TodoItemImage** :

        public class TodoItemImage : INotifyPropertyChanged
        {
            private string name;
            private string uri;

            public MobileServiceFile File { get; private set; }

            public string Name
            {
                get { return name; }
                set
                {
                    name = value;
                    OnPropertyChanged(nameof(Name));
                }
            }

            public string Uri
            {
                get { return uri; }      
                set
                {
                    uri = value;
                    OnPropertyChanged(nameof(Uri));
                }
            }

            public TodoItemImage(MobileServiceFile file, TodoItem todoItem)
            {
                Name = file.Name;
                File = file;

                FileHelper.GetLocalFilePathAsync(todoItem.Id, file.Name).ContinueWith(x => this.Uri = x.Result);
            }

            public event PropertyChangedEventHandler PropertyChanged;

            private void OnPropertyChanged(string propertyName)
            {
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
            }
        }

2. 編輯**App.cs**。 取代初始化`MainPage`以下列︰
    
        MainPage = new NavigationPage(new TodoList());

3. 在**App.cs**，新增下列屬性︰

        public static object UIContext { get; set; }

4. 以滑鼠右鍵按一下 [可攜式文件庫專案，然後選取 [**新增** -> **新項目** -> **跨平台** -> **表單 Xaml 頁面**。 命名檢視`TodoItemDetailsView`。

5. 開啟**TodoItemDetailsView.xaml** ，並以下列取代 ContentPage 本文內容︰

          <Grid>
            <Grid.RowDefinitions>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
            <Button Clicked="OnAdd" Text="Add image"></Button>
            <ListView x:Name="imagesList"
                      ItemsSource="{Binding Images}"
                      IsPullToRefreshEnabled="false"
                      Grid.Row="2">
              <ListView.ItemTemplate>
                <DataTemplate>
                  <ImageCell ImageSource="{Binding Uri}"
                             Text="{Binding Name}">
                  </ImageCell>
                </DataTemplate>
              </ListView.ItemTemplate>
            </ListView>
          </Grid>

6. 編輯**TodoItemDetailsView.xaml.cs**及新增下列使用陳述式︰

        using System.Collections.ObjectModel;
        using Microsoft.WindowsAzure.MobileServices.Files;

7. 取代的實作`TodoItemDetailsView`以下列︰

        public partial class TodoItemDetailsView : ContentPage
        {
            private TodoItemManager manager;

            public TodoItem TodoItem { get; set; }        
            public ObservableCollection<TodoItemImage> Images { get; set; }

            public TodoItemDetailsView(TodoItem todoItem, TodoItemManager manager)
            {
                InitializeComponent();
                this.Title = todoItem.Name;

                this.TodoItem = todoItem;
                this.manager = manager;

                this.Images = new ObservableCollection<TodoItemImage>();
                this.BindingContext = this;
            }

            public async Task LoadImagesAsync()
            {
                IEnumerable<MobileServiceFile> files = await this.manager.GetImageFilesAsync(TodoItem);
                this.Images.Clear();

                foreach (var f in files) {
                    var todoImage = new TodoItemImage(f, this.TodoItem);
                    this.Images.Add(todoImage);
                }
            }

            public async void OnAdd(object sender, EventArgs e)
            {
                IPlatform mediaProvider = DependencyService.Get<IPlatform>();
                string sourceImagePath = await mediaProvider.TakePhotoAsync(App.UIContext);

                if (sourceImagePath != null) {
                    MobileServiceFile file = await this.manager.AddImage(this.TodoItem, sourceImagePath);

                    var image = new TodoItemImage(file, this.TodoItem);
                    this.Images.Add(image);
                }
            }
        }

###<a name="update-main-view"></a>更新主要檢視 

更新選取 todo 項目時，開啟詳細資料] 檢視的主檢視。

在**TodoList.xaml.cs**，取代實作`OnSelected`以下列︰

    public async void OnSelected(object sender, SelectedItemChangedEventArgs e)
    {
        var todo = e.SelectedItem as TodoItem;

        if (todo != null) {
            var detailsView = new TodoItemDetailsView(todo, manager);
            await detailsView.LoadImagesAsync();
            await Navigation.PushAsync(detailsView);
        }

        todoList.SelectedItem = null;
    }

###<a name="update-android"></a>更新 Android 的專案

將特定程式碼新增到 Android 的專案，包括程式碼下載檔案，並使用該攝影機擷取新的圖像。 

此程式碼會使用 Xamarin.Forms [DependencyService](https://developer.xamarin.com/guides/xamarin-forms/dependency-service/)載入右特定類別在執行階段。

1. Android 的專案中新增元件**Xamarin.Mobile** 。

2. 加入新的類別`DroidPlatform`具有下列實作。 「 YourNamespace 」 換成您專案的主要命名空間。

        using System;
        using System.IO;
        using System.Threading.Tasks;
        using Android.Content;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.Droid.DroidPlatform))]
        namespace YourNamespace.Droid
        {
            public class DroidPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var uiContext = context as Context;
                        if (uiContext != null) {
                            var mediaPicker = new MediaPicker(uiContext);
                            var photo = await mediaPicker.TakePhotoAsync(new StoreCameraMediaOptions());

                            return photo.Path;
                        }
                    }
                    catch (TaskCanceledException) {
                    }

                    return null;
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string appData = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
                    string filesPath = Path.Combine(appData, "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. 編輯**MainActivity.cs**。 在 [ `OnCreate`，新增下列通話之前`LoadApplication()`:

        App.UIContext = this;

###<a name="update-ios"></a>更新 iOS 專案

將特定程式碼新增到 iOS 專案。

1. 新增到 iOS 專案的元件**Xamarin.Mobile** 。

2. 加入新的類別`TouchPlatform`具有下列實作。 「 YourNamespace 」 換成您專案的主要命名空間。

        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.iOS.TouchPlatform))]
        namespace YourNamespace.iOS
        {
            class TouchPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var mediaPicker = new MediaPicker();
                        var mediaFile = await mediaPicker.PickPhotoAsync();
                        return mediaFile.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string filesPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments), "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. 編輯**AppDelegate.cs** ，並取消註解通話`SQLitePCL.CurrentPlatform.Init()`。

###<a name="update-windows"></a>更新 Windows 專案

1. 安裝[SQLite Windows 8.1](http://go.microsoft.com/fwlink/?LinkID=716919)Visual Studio 副檔名。 如需詳細資訊，請參閱[啟用離線同步處理您的 Windows 應用程式](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)教學課程。 

2. 編輯**Package.appxmanifest** ，並查看**網路攝影機**功能。

3. 加入新的類別`WindowsStorePlatform`具有下列實作。 「 YourNamespace 」 換成您專案的主要命名空間。

        using System;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Windows.Foundation;
        using Windows.Media.Capture;
        using Windows.Storage;
        using YourNamespace;

        [assembly: Xamarin.Forms.Dependency(typeof(WinApp.WindowsStorePlatform))]
        namespace WinApp
        {
            public class WindowsStorePlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> GetTodoFilesPathAsync()
                {
                    var storageFolder = ApplicationData.Current.LocalFolder;
                    var filePath = "TodoItemFiles";

                    var result = await storageFolder.TryGetItemAsync(filePath);

                    if (result == null) {
                        result = await storageFolder.CreateFolderAsync(filePath);
                    }

                    return result.Name; // later operations will use relative paths
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        CameraCaptureUI dialog = new CameraCaptureUI();
                        Size aspectRatio = new Size(16, 9);
                        dialog.PhotoSettings.CroppedAspectRatio = aspectRatio;

                        StorageFile file = await dialog.CaptureFileAsync(CameraCaptureUIMode.Photo);
                        return file.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }
            }
        }

##<a name="summary"></a>摘要

本文說明如何使用新的檔案支援 Azure 行動用戶端和伺服器 SDK 中使用 Azure 儲存體。 

- 建立儲存帳戶並新增至您的行動應用程式後端的連接字串。 只在後端 Azure 儲存體的關鍵︰ 行動用戶端要求的 SA 權杖 （共用 Access 簽章），只要需要存取 Azure 儲存體。 若要進一步瞭解 SA 權杖 Azure 儲存體中，請參閱[瞭解共用 Access 簽章]。

- 建立控制站的子類別`StorageController`才能處理 SA token 要求，並取得與記錄相關聯的檔案。 根據預設，檔案會與記錄相關聯屬於容器名稱; 使用記錄識別碼可自訂行為，請指定的實作`IContainerNameResolver`。 您也可以自訂 SA 權杖原則。

- Azure 行動用戶端 SDK 不會儲存實際儲存檔案的任何資料。 而用戶端 SDK 叫用您`IFileSyncHandler`，然後決定如何 （以及 if） 檔案會儲存在本機的裝置上。 同步處理常式註冊，如下所示︰

        client.InitializeFileSync(new MyFileSyncHandler(), store);

      + `IFileSyncHandler.GetDataSource`稱為 Azure 行動用戶端 SDK 需要檔案資料 （例如，上傳程序的一部分）。 這可讓您能夠管理如何 （以及 if） 檔案會儲存在本機裝置，並傳回時所需的資訊。

      + `IFileSyncHandler.ProcessFileSynchronizationAction`叫用檔案同步處理流程的一部分。 檔案參考和 FileSynchronizationAction 列舉值會提供讓您決定您的應用程式應該如何處理該事件 （例如檔案自動下載，建立或更新時，在伺服器上刪除該檔案時，從本機裝置刪除檔案）。

- A`MobileServiceFile`可使用 [在線上或離線模式中，使用`IMobileServiceTable`或`IMobileServiceSyncTable`分別。 在離線案例中，當應用程式的通話時，會發生上傳`PushFileChangesAsync`。 這會使離線作業佇列中處理;每個檔案作業，Azure 行動用戶端 SDK 將叫用`GetDataSource`上的方法`IFileSyncHandler`擷取上傳的檔案內容的執行個體。

- 若要擷取的項目檔案，請撥打 ' GetFilesAsync` method on the  `IMobileServiceTable<T> ` or IMobileServiceSyncTable<T>`執行個體。 這個方法會傳回提供資料的項目關聯的檔案清單。 (請注意︰ 這是*本機*的作業，而且會傳回根據物件的狀態，最後一個已同步處理的檔案。 若要從伺服器取得更新的檔案清單，您應該啟動同步處理作業第一次。）

        IEnumerable<MobileServiceFile> files = await myTable.GetFilesAsync(myItem);

- 檔案同步處理功能會使用本機存放區上記錄變更通知，才能擷取的用戶端收到發送或提取作業的記錄。 這以開啟 [同步處理內容使用本機和伺服器通知達成`StoreTrackingOptions`參數。 

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

      + 存放區追蹤可取得其他選項，例如僅本機或僅在伺服器上執行的通知。 您可以新增或擁有自訂的回撥使用`EventManager`屬性`IMobileServiceClient`:

            jobService.MobileService.EventManager.Subscribe<StoreOperationCompletedEvent>(StoreOperationEventHandler);

- 若要新增或移除記錄中的檔案，因為關聯的方法是透過的命名慣例，直接修改 blob 儲存體可能是。 不過，在此情況下應該一律**更新記錄時的時間戳記相關聯的二進位大型物件進行修改**。 Azure 行動用戶端 SDK 隨時更新記錄時新增或移除檔案。 

    這項需求的原因是，某些行動用戶端會記錄在中已經有本機的儲存空間。 當這些用戶端執行累加擷取時，不會傳回此記錄，然後用戶端不會在新的相關檔案的查詢。 若要避免此問題，建議您更新記錄的時間戳記，在執行 Azure 行動用戶端 SDK 項不使用任何 blob 儲存變更。

- 用戶端專案使用的[Xamarin.Forms DependencyService]模式執行階段載入右特定類別。 在此範例中，我們已定義之介面`IPlatform`實作中每個特定的專案。

<!-- URLs. -->

[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
[建立 Xamarin.Forms 應用程式]: app-service-mobile-xamarin-forms-get-started.md
[Xamarin.Forms DependencyService]: https://developer.xamarin.com/guides/xamarin-forms/dependency-service/
[Microsoft.Azure.Mobile.Client.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.Files/
[Microsoft.Azure.Mobile.Client.SQLiteStore]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[Microsoft.Azure.Mobile.Server.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Files/
[瞭解共用 Access 簽章]: ../storage/storage-dotnet-shared-access-signature-part-1.md
[建立 Azure 儲存體帳戶]:  ../storage/storage-create-storage-account.md#create-a-storage-account
