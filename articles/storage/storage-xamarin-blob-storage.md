<properties
    pageTitle="如何使用從 Xamarin Blob 儲存體 |Microsoft Azure"
    description="Azure 儲存用戶端文件庫的 Xamarin 可讓開發人員使用他們的原生使用者介面建立 iOS、 Android 和 Windows 市集應用程式。 本教學課程中會顯示如何使用 Xamarin 建立的應用程式使用 Azure Blob 儲存體。"
    services="storage"
    documentationCenter="xamarin"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2016"
    ms.author="micurd"/>

# <a name="how-to-use-blob-storage-from-xamarin"></a>如何使用從 Xamarin Blob 儲存體

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## <a name="overview"></a>概觀

Xamarin 讓開發人員使用共用 C# 程式碼基底使用它們的原生使用者介面建立 iOS、 Android 和 Windows 市集應用程式。 本教學課程教您如何使用 Azure Blob 儲存體 Xamarin 應用程式。 如果您想要進一步瞭解 Azure 儲存在探究程式碼之前，請參閱[Microsoft Azure 儲存體簡介](storage-introduction.md)。

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>建立新的 Xamarin 應用程式

為此開始，我們會建立 Android 與 Windows iOS，為目標應用程式。 此應用程式只會建立容器，然後上 blob 傳此容器。 我們將使用 Visual Studio Windows 上的這個快速入門，但建立 Mac OS 上使用 Xamarin Studio 應用程式時，可以套用相同檢討。

請遵循這些步驟來建立您的應用程式︰

1. 如果您尚未，下載及安裝[的 Visual Studio Xamarin](https://www.xamarin.com/download)。
2. 開啟 Visual Studio 中，並建立空白的應用程式 （原生共用）︰**檔案 > 新增 > 專案 > 跨平台 > 空白 App(Native Shared)**。
3. 以滑鼠右鍵按一下您在方案總管] 窗格中的方案，然後選取 [**管理 NuGet 套件的解決方案**。 搜尋**WindowsAzure.Storage** ，並在您的方案中的所有專案安裝最新的穩定版本。
4. 建立並執行您的專案。

您現在應該的應用程式，可讓您按一下的累加] 按鈕。

> [AZURE.NOTE] Azure 儲存用戶端文件庫的 Xamarin 目前支援下列專案類型︰ 原生共用、 Xamarin.Forms 共用、 Xamarin.Android 及 Xamarin.iOS。

## <a name="create-container-and-upload-blob"></a>建立容器，然後上傳 blob

接下來，您要新增的部分程式碼，來共用類別`MyClass.cs`的建立容器，然後上傳到此容器的 blob。 `MyClass.cs`應該如下所示︰

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;

    namespace XamarinApp
    {
        public class MyClass
        {
            public MyClass ()
            {
            }

            public static async Task createContainerAndUpload()
            {
                // Retrieve storage account from connection string.
                CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

                // Create the blob client.
                CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

                // Retrieve reference to a previously created container.
                CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

                // Create the container if it doesn't already exist.
                await container.CreateIfNotExistsAsync();

                // Retrieve reference to a blob named "myblob".
                CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

                // Create the "myblob" blob with the text "Hello, world!"
                await blockBlob.UploadTextAsync("Hello, world!");
            }
        }
    }

請確定您實際的客戶名稱] 和 [帳戶金鑰取代 「 your_account_name_here 」 和 「 your_account_key_here 」。 然後，您可以使用此共用的類別，在您的 iOS、 Android 和 Windows Phone 應用程式。 您可以直接新增`MyClass.createContainerAndUpload()`至每個專案。 例如︰

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

    using Android.App;
    using Android.Widget;
    using Android.OS;

    namespace XamarinApp.Droid
    {
        [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
        public class MainActivity : Activity
        {
            int count = 1;

            protected override async void OnCreate (Bundle bundle)
            {
                base.OnCreate (bundle);

                // Set our view from the "main" layout resource
                SetContentView (Resource.Layout.Main);

                // Get our button from the layout resource,
                // and attach an event to it
                Button button = FindViewById<Button> (Resource.Id.myButton);

                button.Click += delegate {
                    button.Text = string.Format ("{0} clicks!", count++);
                };

              await MyClass.createContainerAndUpload();
            }
        }
    }

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

    using System;
    using UIKit;

    namespace XamarinApp.iOS
    {
        public partial class ViewController : UIViewController
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.createContainerAndUpload();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc that aren't in use.
            }
        }
    }

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Navigation;

    // The Blank Page item template is documented at http://go.microsoft.com/fwlink/?LinkId=391641

    namespace XamarinApp.WinPhone
    {
        /// <summary>
        /// An empty page that can be used on its own or navigated to within a Frame.
        /// </summary>
        public sealed partial class MainPage : Page
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.createContainerAndUpload();
            }
        }
    }


## <a name="run-the-application"></a>執行應用程式

您現在可以執行這個應用程式在 Android 或 Windows Phone 模擬器中。 您也可以執行這個應用程式在 iOS 模擬器，但這需要 mac 如需如何執行此動作的特定指示，請參閱[連線 Visual Studio Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)的文件

當您執行您的應用程式時，它會建立容器`mycontainer`在您儲存的帳戶。 包含 blob， `myblob`，其中包含的文字， `Hello, world!`。 您可以使用[Microsoft Azure 儲存檔案總管](http://storageexplorer.com/)來驗證。

## <a name="next-steps"></a>後續步驟

在 [此開始使用，您學到如何建立跨平台應用程式中使用 Azure 儲存體 Xamarin。 此快速入門 」 特別著重於 Blob 儲存體中的一種情況。 不過，您可以執行更多使用不只 Blob 儲存體，但使用表格、 檔案及佇列中的儲存空間。 請查看下列文章，深入瞭解︰
- [使用.NET Azure Blob 儲存體快速入門](storage-dotnet-how-to-use-blobs.md)
- [使用.NET Azure 資料表儲存體快速入門](storage-dotnet-how-to-use-tables.md)
- [使用.NET Azure 佇列中儲存快速入門](storage-dotnet-how-to-use-queues.md)
- [開始使用 windows Azure 檔案儲存空間](storage-dotnet-how-to-use-files.md)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]
