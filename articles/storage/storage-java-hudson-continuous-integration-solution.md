<properties
    pageTitle="如何使用 Blob 儲存體 Hudson |Microsoft Azure"
    description="說明如何使用 Hudson Azure Blob 儲存體為儲存機制建立成品。"
    services="storage"
    documentationCenter="java"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/18/2016" 
    ms.author="dinesh"/>

# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>使用 Hudson 連續整合解決方案 Azure 儲存體

## <a name="overview"></a>概觀

下列資訊會顯示如何使用用於建立程序中 Blob 儲存體建立成品 Hudson 連續整合 (SONT) 解決方案，所建立的存放庫或做為來源的可下載的檔案。 您可以找到這實用的案例的其中一個是您正在撰寫程式碼 （使用 Java 或其他語言） 靈活的開發環境時建置執行的根據連續整合，與您需要存放庫您建立的成品，讓您無法、，例如，與其他組織的成員，您的客戶，共用或維護封存。  另一種情況時，您建立的工作本身需要其他的檔案，例如，若要下載屬於組建輸入的相依性。

在本教學課程您將會使用 Azure 儲存體外掛程式用 Hudson SONT 由 Microsoft 提供。

## <a name="introduction-to-hudson"></a>Hudson 簡介 ##

Hudson 軟體專案的連續整合藉由使用開發人員可以輕鬆地整合程式碼所做的變更，並建置製作自動和常見問題，藉此增加的開發人員生產力。 建置版本，而建立的成品可以上傳至各種不同的存放庫。 本文將說明如何建立成品存放庫做 Azure Blob 儲存體。 它也會顯示如何從 Azure Blob 儲存體下載相依性。

深入瞭解 Hudson 位於[符合 Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson)。

## <a name="benefits-of-using-the-blob-service"></a>使用 Blob 服務的優點 ##

使用 Blob 服務裝載您的敏捷式開發建立成品的優點包括︰

- 您建立的成品及/或可下載的相依性的可用性。
- 當您 Hudson SONT 解決方案上傳您建立的成品時的效能。
- 客戶及合作夥伴下載您建立的成品時的效能。
- Access 等控制使用者存取原則，搭配匿名存取、 到期日型共用的存取簽章存取私人之間的選擇。

## <a name="prerequisites"></a>必要條件 ##

您將需要下列 Blob 服務使用 Hudson SONT 方案︰

- Hudson 連續整合解決方案。

    如果您目前沒有 Hudson SONT 解決方案，您可以執行 Hudson SONT 解決方案使用下列技巧︰

    1. Java 啟用在電腦上，請從<http://hudson-ci.org/>下載 Hudson 馬。
    2. 在含有 Hudson 馬的資料夾，以開啟命令提示字元中，執行 Hudson 馬。 例如，如果您已下載 3.1.2 版本︰

        `java -jar hudson-3.1.2.war`

    3. 在瀏覽器中開啟`http://localhost:8080/`。 這會開啟 [Hudson 儀表板。

    4. 在第一次使用 Hudson，完成的初始設定， `http://localhost:8080/`。

    5. 在您完成初始設定、 取消 Hudson 馬執行個體、 Hudson 馬重新啟動，並重新開啟 Hudson 儀表板之後, `http://localhost:8080/`，可用來安裝和設定 Azure 儲存體外掛程式。

        一般 Hudson SONT 解決方案會設定為服務，在命令列執行 Hudson 馬執行時，足以在本教學課程。

- Azure 帳戶。 您可以註冊<http://www.azure.com>的 Azure 帳戶。

- Azure 儲存體帳戶。 如果您還沒有儲存帳戶，您可以建立一個述的步驟，[建立儲存的帳戶](storage-create-storage-account.md#create-a-storage-account)。

- 熟悉 Hudson SONT 解決方案是建議，但並非必要，如下列內容會以顯示您使用 Hudson SONT 為儲存機制 Blob 服務時所需的步驟建立成品使用基本的範例。

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>如何使用 Hudson SONT Blob 服務 ##

若要使用 Hudson Blob 服務，您需要安裝 Azure 儲存體外掛程式、 設定以使用您儲存的帳戶，外掛程式，然後建立的上傳您儲存的帳戶的您建立的成品建置後動作。 下列各節說明這些步驟。

## <a name="how-to-install-the-azure-storage-plugin"></a>如何安裝 Azure 儲存體外掛程式 ##

1. 按一下 [Hudson 儀表板，**管理 Hudson**。
2. 在 [**管理 Hudson**頁面上，按一下 [**管理增益集**]。
3. 按一下 [**線上**] 索引標籤。
4. 按一下 [**其他人**。
5. 在 [**成品 Uploaders** ] 區段中，選取 [ **Microsoft Azure 儲存體外掛程式**]。
6. 按一下 [**安裝**]。
7. 安裝完成之後，重新啟動 Hudson。

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>如何設定為使用您儲存帳戶 Azure 儲存體外掛程式 ##

1. 按一下 [Hudson 儀表板，**管理 Hudson**。
2. 在 [**管理 Hudson**頁面上，按一下 [**設定系統**]。
3. 在**Microsoft Azure 儲存體帳戶設定**] 區段中︰

    。 輸入您儲存體帳戶名稱，您可以從[Azure 入口網站](https://portal.azure.com)取得。

    b。 輸入您儲存帳戶金鑰]，也可行，或是從[Azure 入口網站](https://portal.azure.com)。

    c。 如果您使用公用 Azure 雲端，則您可以用於**Blob 服務端點 URL**的預設值。 如果您使用的不同的 Azure 雲端，用於端點[Azure 入口網站](https://portal.azure.com)中的指定您儲存的帳戶。

    d。 按一下以驗證您的儲存空間帳戶**驗證儲存認證**]。

    e。 [選擇性]如果您有您想要提供您 Hudson SONT 的額外儲存空間帳戶，請按一下 [**新增更多儲存空間帳戶**]。

    f。 按一下 [**儲存**] 儲存設定。

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>如何建立建置後動作，以便將您建立的成品上載至您儲存的帳戶 ##

第一次僅供指示，我們會需要建立工作，將會建立多個檔案，然後在 [上傳檔案至您的儲存空間帳戶建置後動作中新增。

1. 內 Hudson 儀表板中，按一下 [**新工作**]。
2. 命名工作**MyJob****建立的免費樣式軟體工作**，，然後按一下然後按一下**[確定]**。
3. 在工作設定的 [**建立**] 區段中，按一下 [**新增建置步驟**並選擇**執行 Windows 批次] 命令**。
4. 在**命令**中，使用下列命令︰

        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt

5. 在 [工作設定的 [**建置後動作**] 區段中，按一下 [**上傳至 Microsoft Azure Blob 儲存體成品**]。
6. **儲存體帳戶名稱**] 下，選取要使用儲存的帳戶。
7. **容器的名稱**，請指定容器名稱。 （容器會建立不已經存在時建立成品上傳。）您可以使用環境變數，因此這個範例中為容器名稱請輸入**${JOB_NAME}** 。

    **秘訣**

    [**命令**] 下方區段中，您所輸入的指令碼**執行 Windows 批次] 命令**會是辨識 Hudson 環境變數的連結。 按一下該連結，以瞭解環境變數名稱和描述。 請注意，包含特殊字元，例如**BUILD_URL**環境變數，環境變數不允許容器名稱或一般虛擬路徑。

8. 此範例中，按一下 [**預設公開新容器**]。 （如果您想要使用的私人容器，必須建立共用的 access 簽名允許存取。 這是這份文件的範圍之內。 您可以進一步瞭解[使用共用 Access 簽章 (SA)](storage-dotnet-shared-access-signature-part-1.md)在共用的 access 簽章。）
9. [選擇性]如果您想要清除的內容，建立成品上傳前容器，按一下 [**清理容器之前上傳**（不要將其選取如果您不想要清理的容器中的內容）。
10. **若要上傳--成品**，請輸入**文字 /*.txt**。
11. **上傳的成品一般虛擬路徑**中，輸入**${建立\_識別碼} / ${建立\_數字}**。
12. 按一下 [**儲存**] 儲存設定。
13. 在 Hudson 儀表板中，按一下 [**立即建立**執行**MyJob**。 檢查狀態主控台輸出效果。 建置後動作開始上傳建立成品時，會主控台輸出中包含 Azure 儲存體的狀態訊息。
14. 在工作的成功完成時，您可以開啟公用 blob 檢查建立成品。

    。 [Azure 入口網站](https://portal.azure.com)登入。

    b。 按一下 [**儲存**]。

    c。 按一下您要用於 Hudson 儲存體帳戶名稱。

    d。 按一下 [**容器**]。

    e。 按一下 [容器名稱**myjob**，這是您在建立 Hudson 工作時，您指派的工作名稱的小寫版本]。 容器的名稱及 blob 名稱的大小寫 （和區分大小寫） 中 Azure 儲存體。 二進位大型物件的容器名稱**myjob**的清單中，您應該會看到**hello.txt**和**date.txt**。 複製 URL 的這些項目，並在瀏覽器中開啟]。 您會看到的文字檔案上傳的時間，以建立成品。

您可以建立只有一個建置後巨集指令的上傳至 Azure Blob 儲存體的成品每個作業。 請注意，不同的檔案 （包括萬用字元） 和路徑中使用以分號區隔**清單的成品上傳**的檔案，可以指定單一建置後動作成品其上傳到 Azure Blob 儲存體。 比方說，如果 Hudson 建置產生 JAR 檔案和 TXT 檔案，在您的工作區**建立**資料夾中，您要上傳視訊至 Azure Blob 儲存體請使用下列針對**要上傳--成品**值︰**建立 /\*.jar; 建立 /\*.txt**。 您也可以使用雙冒號語法，若要指定 blob 名稱中使用的路徑。 例如，如果您想要取得上傳至取得 blob 路徑中使用**的通知**上傳使用 blob 路徑和 TXT 檔案中的**二進位檔案**（每瓶），請使用下列**要上傳--成品**值︰**建立 /\*。 jar::binaries; 建立 /\*。 txt::notices**。

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>如何建立建置步驟下載從 Azure Blob 儲存體 ##

下列步驟說明如何設定建立的步驟，從 Azure Blob 儲存體下載項目。 這是如果您想要包含在您建立，例如，將 Azure Blob 儲存體 （每瓶） 的項目。

1. 在工作設定的 [**建立**] 區段中，按一下 [**新增建置步驟**，選擇 [**從 Azure Blob 儲存體下載**。
2. **儲存體帳戶名稱**] 下，選取要使用儲存的帳戶。
3. **容器的名稱**，請指定容器具有您要下載二進位大型物件的名稱。 您可以使用環境變數。
4. **Blob 的名稱**，請指定 blob 名稱。 您可以使用環境變數。 此外，您可以使用星號之後您指定 blob 名稱的初始 letter(s), 萬用字元。 例如，**專案\***指定所有的二進位大型物件開頭**專案**的名稱。
5. [選擇性]**下載路徑**，請在您要下載的檔案從 Azure Blob 儲存體 Hudson 電腦上指定的路徑。 也可以用環境變數。 （如果您不提供的**下載路徑**的值，從 Azure Blob 儲存體檔案會下載至工作的工作區。）

如果您有其他您想要從 Azure Blob 儲存體下載的項目，您可以建立其他建置步驟。

執行組建之後，您可以核取 [建立記錄主控台輸出] 清單，或查看您的下載位置，以查看是否已成功下載您預期的二進位大型物件。

## <a name="components-used-by-the-blob-service"></a>Blob 服務所使用的元件 ##

以下提供 Blob 服務元件的概觀。

- **儲存帳戶**︰ 透過儲存帳戶完成所有存取 Azure 儲存體。 這是最高層級的存取二進位大型物件的命名空間。 帳戶的總大小為底下 100 TB 時，可以包含容器，數量。
- **容器**︰ 容器會提供一組二進位大型物件的群組。 所有的二進位大型物件必須容器中。 帳戶可以包含容器數量。 容器可以儲存 blob 數量。
- **Blob**︰ 任何類型與大小的檔案。 有兩種類型的可儲存於 Azure 儲存體的二進位大型物件︰ 封鎖和頁面的二進位大型物件。 大部分的檔案會封鎖二進位大型物件。 單一區塊 blob 可以是最多 200 GB 大小。 本教學課程中使用區塊二進位大型物件。 經常修改的位元組檔案中的範圍時，頁面 blob，另一個 blob 類型，可以是 1 TB 的大小，以及會更有效率。 如需有關二進位大型物件的詳細資訊，請參閱[瞭解區塊 Blob、 附加二進位大型物件和頁面二進位大型物件](http://msdn.microsoft.com/library/azure/ee691964.aspx)。
- **URL 格式**︰ Blob 會定址使用下列的 URL 格式︰

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`

    （上述格式套用到公用 Azure 雲端。 如果您使用的不同的 Azure 雲端，使用[Azure 入口網站](https://portal.azure.com)中的端點來判斷您的 URL 結束點）。

    上述格式`storageaccount`表示您的儲存體帳戶名稱，`container_name`表示您的容器的名稱和`blob_name`分別代表您 blob 的名稱。 容器名稱，您可以有多個路徑，以斜線， **/**。 在本教學課程的範例容器名稱是**MyJob**，及**${建置\_識別碼} / ${建置\_數字}**所用的一般虛擬路徑，有下列形式的 URL blob 結果︰

    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>後續步驟

- [符合 Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
- [Azure 儲存體 java SDK](https://github.com/azure/azure-storage-java)
- [Azure 儲存用戶端 SDK 參考](http://dl.windowsazure.com/storage/javadoc/)
- [Azure 儲存服務 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx )
- [Azure 儲存體小組部落格](http://blogs.msdn.com/b/windowsazurestorage/)

如需詳細資訊，請參閱[Java 開發人員中心](https://azure.microsoft.com/develop/java/)。