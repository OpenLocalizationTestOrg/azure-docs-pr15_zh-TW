### <a name="prerequisites"></a>必要條件
- Azure 帳戶。您可以建立的[免費的帳戶](https://azure.microsoft.com/free)
- [Azure Blob 儲存體帳戶](../articles/storage/storage-create-storage-account.md)包括儲存體帳戶名稱，以及其便捷鍵。 這項資訊會列在 [帳戶內容的儲存空間 Azure 入口網站中。 進一步瞭解[Azure 儲存體](../articles/storage/storage-introduction.md)。

使用之前 Azure Blob 儲存體帳戶中的邏輯應用程式，連線至 Azure Blob 儲存體帳戶。 您可以輕鬆地在 Azure 入口網站上邏輯應用程式中。  

連線至 Azure Blob 儲存體帳戶使用下列步驟︰  

1. 建立邏輯應用程式。 在邏輯應用程式的設計工具中，將觸發程序，然後再新增動作。 在下拉式清單中，選取 [**顯示 Microsoft 受管理的 Api** ，然後在 [搜尋] 方塊中輸入 「 blob 」。 選取其中一個動作︰  

    ![Azure Blob 儲存體連線建立步驟](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  

2. 如果您還沒有先前建立的任何連線至 Azure 儲存體，系統提示您連線詳細資料︰   

    ![Azure Blob 儲存體連線建立步驟](./media/connectors-create-api-azureblobstorage/connection-details.png)  

3. 輸入的儲存空間帳戶詳細資料。 星號屬性所需。

    | 屬性 | 詳細資料 |
|---|---|
| 連線名稱 * | 輸入您的連線任何名稱。 |
| Azure 儲存體帳戶名稱 * | 輸入儲存體帳戶名稱。 儲存體帳戶名稱會顯示在 Azure 入口網站中的儲存空間屬性。 |
| Azure 儲存體帳戶便捷鍵 * | 輸入的儲存空間帳戶金鑰。 Azure 入口網站中的儲存空間屬性會顯示便捷鍵。 |

    若要授權您邏輯的應用程式連線，並存取您的資料會使用這些認證。 

4. 選取 [**建立**]。

5. 請注意，在建立連線。 現在，繼續邏輯應用程式中的其他步驟︰ 

    ![Azure Blob 儲存體連線建立步驟](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  
