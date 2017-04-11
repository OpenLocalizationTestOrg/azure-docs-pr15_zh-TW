<properties
    pageTitle="內部部署與 blob 儲存體 (Java) 的應用程式 |Microsoft Azure"
    description="瞭解如何建立主控台應用程式上傳至 Azure 的圖像，然後在瀏覽器中顯示的影像。 Java 的程式碼範例。"
    services="storage"
    documentationCenter="java"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="on-premises-application-with-blob-storage"></a>內部部署的應用程式與 blob 儲存體

## <a name="overview"></a>概觀

下列範例會顯示如何使用 Azure 儲存空間，將圖像儲存 Azure。 本文中的程式碼是主控台應用程式上傳至 Azure 的圖像，然後建立 HTML 檔案的在瀏覽器中顯示的影像。

## <a name="prerequisites"></a>必要條件

- Java 開發人員套件 (JDK)，1.6 或更新版本，版本會安裝。
- Azure SDK 已安裝。
- Java，Azure 文件庫的 JAR 任何適用的相依性 （每瓶），已安裝，並在您 Java 編譯器所用的建立路徑。 安裝 java Azure 文件庫的相關資訊，請參閱[下載 java Azure SDK](java-download-azure-sdk.md)。
- Azure 儲存體帳戶已設定。 [帳戶名稱] 和 [帳戶金鑰]，儲存帳戶會使用這份文件中的程式碼。 如需擷取帳戶金鑰的資訊建立的儲存空間帳戶和[檢視和複製的儲存空間便捷鍵](storage-create-storage-account.md#view-and-copy-storage-access-keys)相關資訊，請參閱[如何建立儲存的帳戶](storage-create-storage-account.md#create-a-storage-account)。

- 您已建立一個名為本機圖像檔案儲存在路徑 c:\\myimages\\image1.jpg。 或者，修改  **FileInputStream**建構函式中使用不同的圖像路徑和檔名的範例。

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="to-use-azure-blob-storage-to-upload-a-file"></a>若要將檔案上傳使用 Azure blob 儲存體

此處所提供的逐步程序。 如果您想要跳，就會在本文稍後列出整個的程式碼。

藉由匯入 Azure 核心儲存類別、 Azure blob 用戶端類別、 Java IO 課程]，及**URISyntaxException**課程開始程式碼。

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

將類別命名為**StorageSample**，且包含左括弧， **{**。

    public class StorageSample {

**StorageSample**在類別內，將會包含預設的端點通訊協定，您儲存體帳戶名稱和您的儲存空間便捷鍵，為 Azure 儲存體帳戶中指定的字串變數。 取代預留位置值**您\_帳戶\_名稱**和**您\_帳戶\_鍵**與您的帳戶名稱和帳戶金鑰]，分別。

    public static final String storageConnectionString =
           "DefaultEndpointsProtocol=http;" +
               "AccountName=your_account_name;" +
               "AccountKey=your_account_name";

新增在您的**主要**的宣告，包括**嘗試**區塊，包括必要開啟括弧括住， **{**。

    public static void main(String[] args)
    {
        try
        {

宣告變數的下列類型 （在此範例中的使用方式是描述）︰

-   **CloudStorageAccount**︰ 用來初始化帳戶物件 Azure 儲存體帳戶名稱與索引鍵，並建立 blob 用戶端物件。
-   **CloudBlobClient**︰ 用來存取 blob 服務。
-   **CloudBlobContainer**︰ 用來建立 blob 容器，清單容器中的二進位大型物件，然後刪除容器。
-   **CloudBlockBlob**︰ 用來將本機的圖像檔案上傳至容器。

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

指派給**帳戶**變數的值。

    account = CloudStorageAccount.parse(storageConnectionString);

指派給**serviceClient**變數的值。

    serviceClient = account.createCloudBlobClient();

指派給**容器**變數的值。 我們將會收到名為**gettingstarted**容器的參考。

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

建立容器。 這個方法會建立容器，如果它不存在於 （並傳回**true**）。 如果容器不存在，則會傳回**false**。 另一個**createIfNotExists**是**建立**方法 （如果容器已經存在，則會傳回錯誤）。

    container.createIfNotExists();

設定容器匿名存取。

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

取得區塊 blob，即代表 Azure 儲存體中的 blob 的參考。

    blob = container.getBlockBlobReference("image1.jpg");

使用**檔案**建構函式取得的本機建立的檔案，您將上傳的參考。 請確定您已建立此檔案之前執行程式碼。

    File fileReference = new File ("c:\\myimages\\image1.jpg");

透過**CloudBlockBlob.upload**方法呼叫本機的檔案上傳。 第一個參數**CloudBlockBlob.upload**方法是**FileInputStream**物件代表會將它上傳至 Azure 儲存體本機檔案。 第二個參數是位元組，檔案的大小。

    blob.upload(new FileInputStream(fileReference), fileReference.length());

Call 名為**MakeHTMLPage**，進行基本的 HTML 頁面包含協助函數**&lt;圖像&gt;**與來源的項目設定為現在位於 Azure 儲存體帳戶 blob。 將本文稍後的討論**MakeHTMLPage**的程式碼。

    MakeHTMLPage(container);

列印出來的狀態訊息和建立 HTML 網頁的相關資訊。

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

插入的右括弧，關閉**區塊**︰ **}**

處理下列例外狀況︰

-   **FileNotFoundException**︰ 可能會擲回**FileInputStream**或**FileOutputStream**建構函式。
-   **StorageException**︰ 可能會擲回 Azure 用戶端儲存文件庫。
-   **URISyntaxException**: **ListBlobItem.getUri**方法擲回。
-   **例外狀況**︰ 一般例外處理。

<!-- -->

    catch (FileNotFoundException fileNotFoundException)
    {
        System.out.print("FileNotFoundException encountered: ");
        System.out.println(fileNotFoundException.getMessage());
        System.exit(-1);
    }
    catch (StorageException storageException)
    {
        System.out.print("StorageException encountered: ");
        System.out.println(storageException.getMessage());
        System.exit(-1);
    }
    catch (URISyntaxException uriSyntaxException)
    {
        System.out.print("URISyntaxException encountered: ");
        System.out.println(uriSyntaxException.getMessage());
        System.exit(-1);
    }
    catch (Exception e)
    {
        System.out.print("Exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

關閉**主**插入右括弧︰ **}**

建立名為**MakeHTMLPage**建立基本的 HTML 頁面的方法。 這個方法有類型**CloudBlobContainer**，會用來上傳的二進位大型物件的清單中重複的參數。 這個方法會引發例外狀況類型**FileNotFoundException**，這可能會擲回的**FileOutputStream**建構函式，並**URISyntaxException**，這可能會擲回**ListBlobItem.getUri**方法。 包含的左括弧， **{**。

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

建立一個名為**index.html**的本機檔案。

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

本機的檔案，新增中撰寫**&lt;html&gt;**，**&lt;頁首&gt;**，及**&lt;本文&gt;**項目。

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

逐一查看上傳的二進位大型物件的清單。 針對每個 blob，在 [HTML] 頁面建立**&lt;影像&gt;**已存在於 Azure 儲存體帳戶中傳送至 blob 的 URI 其**src**屬性的項目。
雖然您只有一個圖像中新增這個範例中，如果您新增更多，將此程式碼會持續所有。

為求，假設每個上傳的項目不圖像。 如果您已經更新圖像，以外的二進位大型物件或頁面，而不是區塊 blob 的二進位大型物件，請視需要調整程式碼。

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

關閉**&lt;本文&gt;**項目和**&lt;html&gt;**項目。

    stream.println("</body>");
    stream.println("</html>");

關閉本機的檔案。

    stream.close();

關閉**MakeHTMLPage**插入右括弧︰ **}**

關閉**StorageSample**插入右括弧︰ **}**

以下是完整的程式碼，此範例。 修改版面配置區的值，請記得**您\_帳戶\_名稱**和**您\_帳戶\_鍵**以使用您的帳戶名稱和帳戶金鑰]，分別。

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

    // Create an image, c:\myimages\image1.jpg, prior to running this sample.
    // Alternatively, change the value used by the FileInputStream constructor
    // to use a different image path and file that you have already created.
    public class StorageSample {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                       "AccountName=your_account_name;" +
                       "AccountKey=your_account_name";

        public static void main(String[] args) {
            try {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExists();

                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");

                File fileReference = new File("c:\\myimages\\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            } catch (FileNotFoundException fileNotFoundException) {
                System.out.print("FileNotFoundException encountered: ");
                System.out.println(fileNotFoundException.getMessage());
                System.exit(-1);
            } catch (StorageException storageException) {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            } catch (URISyntaxException uriSyntaxException) {
                System.out.print("URISyntaxException encountered: ");
                System.out.println(uriSyntaxException.getMessage());
                System.exit(-1);
            } catch (Exception e) {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }

        // Create an HTML page that can be used to display the uploaded images.
        // This example assumes all of the blobs are for images.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
        {
            PrintStream stream;
            stream = new PrintStream(new FileOutputStream("index.html"));

            // Create the opening <html>, <header>, and <body> elements.
            stream.println("<html>");
            stream.println("<header/>");
            stream.println("<body>");

            // Enumerate the uploaded blobs.
            for (ListBlobItem blobItem : container.listBlobs()) {
                // List each blob as an <img> element in the HTML body.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
            }

            stream.println("</body>");

            // Complete the <html> element and close the file.
            stream.println("</html>");
            stream.close();
        }
    }

除了將您的本機的影像檔案上傳至 Azure 儲存體，程式碼範例會建立本機檔案 namedindex.html，您可在若要查看上傳的圖像瀏覽器中開啟。

因為您的帳戶名稱和帳戶金鑰]，將包含的程式碼，請確定您的程式碼是安全。

## <a name="to-delete-a-container"></a>若要刪除容器

由於您會儲存空間，您可能需要您完成之後，刪除**gettingstarted**容器嘗試此範例使用。 若要刪除容器，使用 [ **CloudBlobContainer.delete**方法。

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

初始化**CloudStorageAccount**、 **ClodBlobClient**和**CloudBlobContainer**物件的程序呼叫**CloudBlobContainer.delete**方法，是相同**createIfNotExist**方法所示。 以下是完整的範例，刪除容器名稱**gettingstarted**。

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

    public class DeleteContainer {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                   "AccountName=your_account_name;" +
                   "AccountKey=your_account_key";

        public static void main(String[] args)
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.delete();

                System.out.println("Container deleted.");

            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }
    }

如需其他 blob 儲存體類別和方法的概觀，瞭解[如何使用從 Java Blob 儲存體。](storage-java-how-to-use-blob-storage.md)

## <a name="next-steps"></a>後續步驟

請遵循這些連結，深入了解更複雜的儲存空間工作。

- [Azure 儲存體 java SDK](https://github.com/azure/azure-storage-java)
- [Azure 儲存用戶端 SDK 參考](http://dl.windowsazure.com/storage/javadoc/)
- [Azure 儲存服務 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Azure 儲存體小組部落格](http://blogs.msdn.com/b/windowsazurestorage/)
