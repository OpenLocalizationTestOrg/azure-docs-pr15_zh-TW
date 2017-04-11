<properties
   pageTitle="使用資料湖市集 Java SDK 開發應用程式 |Microsoft Azure"
   description="使用 Azure 資料湖市集 Java SDK 開發應用程式"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/17/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-java"></a>Azure 資料湖存放區使用 Java 快速入門

> [AZURE.SELECTOR]
- [入口網站](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

瞭解如何使用 Azure 資料湖市集 Java SDK 執行基本作業，例如建立資料夾上, 傳和下載等資料檔案。如需有關資料湖的詳細資訊，請參閱[Azure 資料湖存放區](data-lake-store-overview.md)。

您可以存取 Java SDK API 的文件的 Azure 資料湖存放在[Azure 資料湖市集 Java API 文件](https://azure.github.io/azure-data-lake-store-java/javadoc/)。

## <a name="prerequisites"></a>必要條件

* Java 開發套件 （JDK 7 或更新版本、 使用 Java 1.7 或更新版本）
* Azure 資料湖存放帳戶。 請遵循在[開始使用 Azure 資料湖存放區使用 Azure 入口網站](data-lake-store-get-started-portal.md)的指示進行。
* [Maven](https://maven.apache.org/install.html)。 本教學課程中使用 Maven 建立與專案的相依性。 雖然您可以建立，而不使用建置系統 Maven 或 Gradle 等，這些系統請是更容易管理相依性。
* （選用）與[IntelliJ 想法](https://www.jetbrains.com/idea/download/)或[蝕](https://www.eclipse.org/downloads/)等 IDE 或類似。

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>如何驗證使用 Azure Active Directory？

在本教學課程中，我們會使用 Azure AD 應用程式的用戶端密碼來擷取的 Azure Active Directory 權杖 （服務-服務驗證）。 我們會使用此 token 加建立資料湖存放用戶端物件，以執行作業的檔案] 和 [目錄] 作業。 如需如何使用用戶端機密 Azure 資料湖存放驗證方法的指示，我們可以執行下列高層級的步驟︰

1. 建立 Azure AD web 應用程式
2. 擷取用戶端識別碼、 用戶端私人和 Azure AD web 應用程式的權杖結束點。
3. 資料湖存放檔案/資料夾您想要從您正在建立 Java 應用程式存取的設定 Azure AD web 應用程式的存取權。

如需如何執行這些步驟的指示，請參閱[建立 Active Directory 應用程式](data-lake-store-authenticate-using-active-directory.md#create-an-active-directory-application)。

Azure Active Directory 提供其他選項，以及要擷取的權杖。 您可以挑選從多個不同的驗證機制，以符合您的狀況，例如執行或 Azure 虛擬機器中的瀏覽器、 應用程式散發為桌面應用程式或執行內部部署的伺服器應用程式的應用程式。 您也可以挑選不同類型的認證，例如密碼、 憑證、 2 因素驗證等。此外，Azure Active Directory 可讓您與雲端同步處理您的內部部署 Active Directory 使用者。 如需詳細資訊，請參閱[Azure Active Directory 驗證案例](../active-directory/active-directory-authentication-scenarios.md)。 

## <a name="create-a-java-application"></a>建立 Java 應用程式

程式碼範例使用[上 GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/)查核行程串連檔案、 下載檔案，和刪除某些檔案存放區中的市集]，在您完成程序建立的檔案。 文件的此區段會引導您主要的組件的程式碼。

1. 建立使用[mvn 原型](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html)從命令列，或使用 IDE Maven 專案。 如需如何建立使用 IntelliJ Java 專案的相關指示，請參閱[以下](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html)。 如需如何建立使用蝕在專案上的指示，請參閱[以下](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm)。 

2. 新增下列相依性至 Maven **pom.xml**檔案。 新增下列之間的文字片段**\</version >**標籤以及**\</專案 >**標記︰

        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.0.4-SNAPSHOT</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>

    第一個相依性是使用資料湖市集 SDK (`azure-datalake-store`) 從 maven 存放庫。 第二個相依性 (`slf4j-nop`) 可指定哪些記錄架構時，若要使用此應用程式。 資料湖市集 SDK 使用[slf4j](http://www.slf4j.org/)記錄外觀，可讓您選擇從數個常用的記錄架構，例如 log4j，Java 記錄等 logback，或沒有記錄。 例如，我們將會停用記錄，因此我們使用**slf4j nop**繫結。 若要在您的應用程式中使用其他的記錄選項，請參閱[以下](http://www.slf4j.org/manual.html#projectDep)。

### <a name="add-the-application-code"></a>新增應用程式碼

有三個主要部分程式碼。

1. 取得 Azure Active Directory 權杖

2. 若要建立的資料湖存放用戶端使用權杖。

3. 使用資料湖存放用戶端來執行作業。

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>步驟 1︰ 取得 Azure Active Directory 權杖。

資料湖市集 SDK 提供方便的方法可讓您取得所需的資料湖存放帳戶與交談的安全性權杖。 SDK 但不要求僅這些方法，使用。 您可以使用取得權杖，例如使用[Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java)或您自己的自訂程式碼的任何其他方法。

若要使用資料湖市集 SDK 取得您先前建立的 Active Directory Web 應用程式的權杖，使用中的靜態方法`AzureADAuthenticator`類別。 Azure Active Directory Web 應用程式的實際值取代**填滿的步驟**。

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AzureADToken token = AzureADAuthenticator.getTokenUsingClientCreds(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>步驟 2︰ 建立 Azure 資料湖存放用戶端 (ADLStoreClient) 物件

建立[ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/)物件需要您指定資料湖存放客戶名稱] 和 [您的最後一個步驟所產生的 Azure Active Directory token。 請注意資料湖存放帳戶名稱必須是完整的網域名稱。 例如，換成**填滿的以下** **mydatalakestore.azuredatalakestore.net**類似。

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, token);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>步驟 3︰ 使用 ADLStoreClient 執行檔案] 和 [目錄] 作業

下列程式碼包含範例程式碼片段的一些常見的作業。 您可以查看完整[資料湖市集 Java SDK API 文件](https://azure.github.io/azure-data-lake-store-java/javadoc/)的**ADLStoreClient**物件以查看其他作業。
 
請注意檔案是從讀取和寫入使用標準 Java 資料流。 這表示您可以層任何的 Java 資料流上方資料湖存放資料流時，可受益標準 Java 功能 （例如列印資料流格式化的輸出，或任何在頂端等其他功能的壓縮或加密資料流。）。

    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>步驟 4︰ 建立和執行應用程式

1. 若要從執行 IDE 中，找出並按下 [**執行**] 按鈕。 若要從 Maven 執行，請使用[接著︰ 執行](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html)。

2. 若要產生您可以從命令列執行獨立 jar 會建立糖與包含，使用[Maven 組件外掛程式](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html)的所有相依性。 [範例上的原始程式碼 github](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) pom.xml 有如何進行此動作的範例。


## <a name="next-steps"></a>後續步驟

- [保護資料湖存放區中的資料](data-lake-store-secure-data.md)
- [使用資料湖存放 Azure 資料湖狀況分析](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [使用資料湖存放 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)
