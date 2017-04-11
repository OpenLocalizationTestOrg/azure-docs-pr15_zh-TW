## <a name="azure-storage-linked-service"></a>Azure 儲存連結服務

**Azure 儲存體連結服務**可讓您連結至 Azure 資料工廠的 Azure 儲存體帳戶，使用**[帳戶金鑰]**。 這提供資料 factory 通用存取至 Azure 儲存體。 下表提供 JSON 項目的特定 Azure 儲存體連結服務描述。

| 屬性 | 描述 | 所需 |
| :-------- | :----------- | :-------- |
| 類型 | [類型] 屬性必須設定為︰ **AzureStorage** | [是] |
| 連接字串 | 指定連線至 Azure 儲存體連接字串屬性所需的資訊。 | [是] |

Azure 儲存空間，請參閱下列文章︰ 檢視或複製的 [帳戶金鑰的步驟︰[檢視、 複製與重新產生的儲存空間便捷鍵](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)。

**範例︰**  
  
    {  
        "name": "StorageLinkedService",  
        "properties": {  
            "type": "AzureStorage",  
            "typeProperties": {  
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
            }  
        }  
    }  


## <a name="azure-storage-sas-linked-service"></a>Azure 儲存體 Sa 連結服務  
共用的 access 簽章 (SA) 提供委派的存取您儲存的帳戶中的資源。 這表示您可以授與用戶端有限在指定期間內的時間，以指定的權限，您儲存的帳戶中的物件權限，而不必共用您的帳戶便捷鍵。 SA 是 URI 其查詢參數中所包含的所有的所需的資訊已驗證的儲存空間資源的存取權。 若要存取與 SA 儲存資源，用戶端只需要 SA 中傳送到適當的建構函式或方法。 如需 SA 詳細資訊，請參閱[共用存取簽章︰ 了解 SA 模型](../articles/storage/storage-dotnet-shared-access-signature-part-1.md)
  
Azure 連結的儲存空間 SA 服務可讓您 Azure 儲存體帳戶使用連結至 Azure 資料工廠共用 Access 簽章 (SA)。 讓資料 factory 有限制/時間繫結存取存放區中的所有/特定資源 （blob/容器）。 下表提供 JSON 元素特定 Azure 連結的儲存空間 SA 服務描述。 

| 屬性 | 描述 | 所需 |
| :-------- | :----------- | :-------- |
| 類型 | [類型] 屬性必須設定為︰ **AzureStorageSas**  | [是] |
| sasUri | 指定共用 Access 簽章 URI Azure 儲存體資源，例如 blob、 容器或表格。 請參閱下方注意事項以取得詳細資料。 | [是] | 


**範例︰**
  
    {  
        "name": "StorageSasLinkedService",  
        "properties": {  
            "type": "AzureStorageSas",  
            "typeProperties": {  
                "sasUri": "<storageUri>?<sasToken>"   
            }  
        }  
    }  

建立時**SA URI**，請考慮下列動作︰  

- Azure 資料 Factory 支援只**服務 SA**，不帳戶 SA。 這兩種類型的詳細資料，請參閱[共用 Access 簽章的類型](../articles/storage/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures)。
- 根據如何連結的服務 （讀取、 寫入讀/寫） 將用於您的資料工廠的物件上設定的適當的讀/寫**權限**需求。
- **到期時間**必須正確設定。 請確定使用中的管線期間內不會過期的存取權 Azure 儲存體物件。
- 在右側的容器 blob 或根據需要的表格層級中建立 Uri。 Azure blob SA Uri 可讓您存取該特定 blob 的資料工廠服務。 Azure blob 容器 SA Uri 可讓逐一二進位大型物件的容器中的資料工廠服務。 如果您需要更新版本中，提供存取更多/減少物件或更新 SA URI，請記得要以新的 URI 更新連結的服務。   
