<properties
    pageTitle="管理儲存空間檔案總管 （預覽版本） 的 Azure Blob 儲存體資源 |Microsoft Azure"
    description="管理 Azure Blob 容器和 Blob 儲存體檔案總管 （預覽版本）"
    services="storage"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />

 <tags
    ms.service="storage"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/17/2016"
    ms.author="tarcher" />

# <a name="manage-azure-blob-storage-resources-with-storage-explorer-preview"></a>管理 Azure Blob 儲存體資源儲存檔案總管 （預覽版本）

## <a name="overview"></a>概觀

[Azure Blob 儲存體](./storage/storage-dotnet-how-to-use-blobs.md)] 是儲存大量的非結構化資料，例如文字或二進位資料，可透過 HTTP 或 HTTPS 的世界從任何地方存取的服務。
以公開公開至世界各地，或私下儲存應用程式的資料，您可以使用 Blob 儲存體。 在本文中，您將學習如何使用 [儲存檔案總管] （預覽版本），使用 blob 容器及二進位大型物件。

## <a name="prerequisites"></a>必要條件

若要完成此文件中的步驟進行，您會需要下列項目︰

- [下載並安裝存放裝置總管 （預覽版本）](http://www.storageexplorer.com)
- [連線至 Azure 儲存體帳戶或服務](./vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>建立 blob 容器

所有的二進位大型物件必須位於 blob 容器，也就是只要 blob 的邏輯群組。 帳戶可包含數量容器，以及每個容器可以儲存 blob 數量。

下列步驟說明如何建立 blob 容器中儲存檔案總管 （預覽版本）。

1.  開啟儲存檔案總管] （預覽版本）。
1.  在左窗格中，展開 [您想要建立 blob 容器內的儲存空間帳戶]。
1.  以滑鼠右鍵按一下**Blob 容器**，以及-從操作功能表-選取 [**建立 Blob 容器**。

    ![建立 blob 容器操作功能表][0]

1.  文字會出現一個方塊**Blob 容器**資料夾下方。 輸入您的 blob 容器的名稱。 請參閱[容器命名規則](./storage/storage-dotnet-how-to-use-blobs.md#create-a-container)清單的規則和限制命名 blob 容器。

    ![建立 Blob 容器的文字方塊][1]

1.  按下**Enter**完成建立 blob 容器或**Esc**可取消。 Blob 容器已成功建立之後，它將會顯示選取的儲存帳戶**Blob 容器**資料夾下。

    ![建立 blob 容器][2]

## <a name="view-a-blob-containers-contents"></a>檢視 blob 容器的內容

Blob 容器包含二進位大型物件的和資料夾 （也可以包含二進位大型物件）。

下列步驟說明如何檢視 blob 容器中儲存檔案總管 （預覽版本） 的內容︰

1.  開啟儲存檔案總管] （預覽版本）。
1.  在左窗格中，展開您想要檢視的儲存空間帳戶包含 blob 容器。
1.  展開儲存帳戶的**Blob 容器**。
1.  以滑鼠右鍵按一下您想要檢視的 blob 容器和-從操作功能表-選取 [**開啟 Blob 容器編輯器]**。
您也可以按兩下您想要檢視的 blob 容器。

    ![開啟 blob 容器編輯器] 內容功能表][19]

1.  [主要] 窗格會顯示 blob 容器的內容。

    ![Blob 容器編輯器][3]

## <a name="delete-a-blob-container"></a>刪除 blob 容器

Blob 容器可以輕鬆地建立及刪除視。 （若要查看如何刪除個別的二進位大型物件，請參閱] 區段中， [blob 容器中的管理 blob](./#managing-blobs-in-a-blob-container)。）

下列步驟說明如何刪除 blob 容器中儲存檔案總管 （預覽版本）︰

1.  開啟儲存檔案總管] （預覽版本）。
1.  在左窗格中，展開您想要檢視的儲存空間帳戶包含 blob 容器。
1.  展開儲存帳戶的**Blob 容器**。
1.  以滑鼠右鍵按一下您想要刪除的 blob 容器和-從操作功能表-選取 [**刪除**]。
您也可以按 [**刪除**] 以刪除目前選取的 blob 容器。

    ![刪除 blob 容器操作功能表][4]

1.  [確認] 對話方塊中選取**[是]** 。

    ![刪除 blob 容器確認][5]

## <a name="copy-a-blob-container"></a>複製 blob 容器

儲存檔案總管 （預覽版本） 可讓您將 blob 容器複製到剪貼簿]，然後將該 blob 容器貼到其他儲存帳戶。 （若要查看如何複製個別的二進位大型物件，請參閱] 區段中， [blob 容器中的管理 blob](./#managing-blobs-in-a-blob-container)。）

下列步驟說明如何將 blob 容器複製到另一儲存帳戶。

1.  開啟儲存檔案總管] （預覽版本）。
1.  在左窗格中，展開您想要複製的儲存空間帳戶包含 blob 容器。
1.  展開儲存帳戶的**Blob 容器**。
1.  以滑鼠右鍵按一下您想要複製的 blob 容器和-從操作功能表-選取 [**複製 Blob 容器**。

    ![複製 blob 容器操作功能表][6]

1.  以滑鼠右鍵按一下您要貼上 blob 容器的所需的 「 目標 」 儲存帳戶和-從操作功能表-選取 [**貼上 Blob 容器**。

    ![貼上 blob 容器操作功能表][7]

## <a name="get-the-sas-for-a-blob-container"></a>SA 取得 blob 容器

[共用的 access 簽章 (SA)](./storage/storage-dotnet-shared-access-signature-part-1.md)提供委派的存取您儲存的帳戶中的資源。
這表示您可以授與用戶端在指定期間內的時間，以指定的權限，您儲存的帳戶中的物件權限限制而不需要共用您的帳戶便捷鍵。

下列步驟說明如何建立 blob 容器的 SA:

1.  開啟儲存檔案總管] （預覽版本）。
1.  在左窗格中，展開 [包含您要取得 SA blob 容器的儲存空間帳戶]。
1.  展開儲存帳戶的**Blob 容器**。
1.  以滑鼠右鍵按一下您要的 blob 容器，以及-從操作功能表-選取 [**取得共用的 Access 簽名**。

    ![取得 SA 操作功能表][8]

1.  在**共用的 Access 簽章**] 對話方塊中，指定原則、 開始日期和到期日期、 時間區域，並存取層級您想要用於資源。

    ![取得 SA 選項][9]

1.  當您完成指定 SA 選項時，請選取 [**建立**。

1.  第二個的**共用 Access 簽章**] 對話方塊就會顯示會列出 URL 以及您可以用來存取儲存資源 QueryStrings blob 容器。
選取您想要複製到剪貼簿的 URL] 旁的**複本**]。

    ![複製 SA Url][10]

1.  完成時，請選取 [**關閉**]。

## <a name="manage-access-policies-for-a-blob-container"></a>管理 blob 容器的存取原則

下列步驟說明如何管理 （新增和移除） 存取 blob 容器的原則︰

1.  開啟儲存檔案總管] （預覽版本）。
1.  在左窗格中，展開 [包含 blob 容器您想要管理其存取原則的儲存空間帳戶]。
1.  展開儲存帳戶的**Blob 容器**。
1.  選取您要的 blob 容器，以及-從操作功能表-選取 [**管理存取原則**。

    ![管理存取原則操作功能表][11]

1.  **存取原則**] 對話方塊會列出已經選取的 blob 容器建立任何存取原則。

    ![存取原則選項][12]        

1.  遵循下列步驟視原則管理工作的存取權而定︰

    - **新增新的存取原則**-選取 [**新增]**。 一旦產生，**存取原則**] 對話方塊會顯示最近新增的存取原則 （預設設定）。
    - **編輯存取原則**-進行任何所需的編輯，然後選取 [**儲存**]。
    - **移除存取原則**-選取**移除**您想要移除的存取原則旁。

## <a name="set-the-public-access-level-for-a-blob-container"></a>設定公用存取層級 blob 容器

根據預設，每個 blob 容器會設定為 「 沒有公用存取 」。

下列步驟說明如何指定 blob 容器公用存取層級。

1.  開啟儲存檔案總管] （預覽版本）。
1.  在左窗格中，展開 [包含 blob 容器您想要管理其存取原則的儲存空間帳戶]。
1.  展開儲存帳戶的**Blob 容器**。
1.  選取您要的 blob 容器，以及-從操作功能表-選取 [**設定公用存取層級**。

    ![設定公用存取層級的操作功能表][13]

1.  在 [**設定容器公用存取層級**] 對話方塊中，指定所要的存取權等級。

    ![設定公用存取層級的選項][14]

1.  選取 [**套用**]。

## <a name="managing-blobs-in-a-blob-container"></a>管理 blob 容器中的二進位大型物件

建立 blob 容器之後，您可以上傳至該 blob 容器的 blob blob 下載至您的本機電腦、 blob 開啟您的本機電腦，以及執行其他功能。

下列步驟說明如何管理的二進位大型物件 （與資料夾） blob 容器內。

1.  開啟儲存檔案總管] （預覽版本）。
1.  在左窗格中，展開您想要管理的儲存空間帳戶包含 blob 容器。
1.  展開儲存帳戶的**Blob 容器**。
1.  按兩下您想要檢視 blob 容器。
1.  [主要] 窗格會顯示 blob 容器的內容。

    ![檢視 blob 容器][3]

1.  [主要] 窗格會顯示 blob 容器的內容。

1.  請遵循下列步驟，取決於您想要執行的工作︰

    - **上傳檔案到 blob 容器**

        1.  [主要] 窗格的工具列上，選取 [**上傳**，然後**上傳的檔案**從下拉式功能表。

            ![上傳檔案] 功能表][15]

        1.  在 [**上傳檔案**] 對話方塊中，選取 [省略符號 （**...**）] 按鈕，選取您要上傳的檔案的 [文字**檔案**] 方塊的右側。

            ![上傳檔案的選項][16]

        1.  指定**Blob 類型**的類型。 [使用.NET Azure Blob 儲存體快速入門](./storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts)文章說明各種 blob 類型之間的差異。

        1.  或者，您可以指定您選擇的檔案會將其上傳的目標資料夾。 如果不存在的目標資料夾，將會建立。

        1.  選取 [**上傳**]。

    - **上傳至 blob 容器的資料夾**

        1.  [主要] 窗格的工具列上，選取 [**上傳**，然後**上傳資料夾**從下拉式功能表。

            ![上傳資料夾] 功能表][17]

        1.  在 [**上傳資料夾**] 對話方塊中，選取省略符號 （**...**） 按鈕右側的 [**資料夾**] 文字方塊中，選取您要上傳的內容的資料夾。

            ![上傳資料夾選項][18]

        1.  指定**Blob 類型**的類型。 [使用.NET Azure Blob 儲存體快速入門](./storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts)文章說明各種 blob 類型之間的差異。

        1.  或者，您可以指定所選的資料夾內容會將其上傳的目標資料夾。 如果不存在的目標資料夾，將會建立。

        1.  選取 [**上傳**]。

    - **Blob 下載至您的本機電腦**

        1.  選取您想要下載 blob。

        1.  在 [主要] 窗格的工具列上選取 [**下載**]。

        1.  在 [**指定下載的 blob 儲存位置**] 對話方塊中，指定您要下載，blob 的位置，以及您想要讓它的名稱。  

        1.  選取 [**儲存**]。

    - **開啟您的本機電腦上的 blob**

        1.  選取您想要開啟的 blob。

        1.  在主要的窗格工具列中，選取 [**開啟**]。

        1.  Blob 會下載並使用 blob 的基礎的檔案類型相關聯的應用程式開啟。

    - **Blob 複製到剪貼簿**

        1.  選取您想要複製的 blob。

        1.  在 [主要] 窗格的工具列上選取 [**複製**]。

        1.  在左窗格中，瀏覽到另一個 blob 容器，然後按兩下以在 [主要] 窗格中檢視。

        1.  主要的窗格工具列上，選取 [**貼上**建立 blob 的複本。

    - **刪除 blob**

        1.  選取您想要刪除的 blob。

        1.  在 [主要] 窗格的工具列上選取 [**刪除**]。

        1.  [確認] 對話方塊中選取**[是]** 。

## <a name="next-steps"></a>後續步驟

- 檢視的[最新的儲存空間檔案總管 （預覽版本） 放開備忘稿和視訊](http://www.storageexplorer.com)。
- 瞭解如何[建立使用 Azure blob、 表格、 佇列和檔案的應用程式](https://azure.microsoft.com/documentation/services/storage/)。

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png