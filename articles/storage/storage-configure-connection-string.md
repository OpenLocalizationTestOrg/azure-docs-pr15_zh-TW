<properties 
    pageTitle="設定連線字串至 Azure 儲存體 |Microsoft Azure"
    description="設定 Azure 儲存體帳戶的連線字串。 連線字串包含驗證儲存帳戶的存取，從您的應用程式在執行階段所需的資訊。"
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="configure-azure-storage-connection-strings"></a>設定 Azure 儲存空間的連接字串

## <a name="overview"></a>概觀

連線字串包含驗證所需的資訊存取您的應用程式在執行階段 Azure 儲存體帳戶中的資料。 您可以設定的連接字串︰

- 連接到 Azure 儲存模擬器。
- 存取 Azure 中儲存的帳戶。
- 存取共用的 access 簽章 (SA) 透過 Azure 中的指定的資源。

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>儲存您的連線字串

您的應用程式必須驗證 Azure 儲存體所做的要求，才能存取在執行階段中的連接字串。 您有幾個不同的選項，以儲存您的連線字串︰

- 在桌面上，或在裝置上的應用程式執行，您可以儲存中的連接字串`app.config `檔案或`web.config`檔案。 加入**和 AppSettings** ] 區段中的連接字串。
- Azure 雲端服務中執行的應用程式，您可以儲存您的連線字串中[Azure 服務設定結構描述 (.cscfg) 檔案](https://msdn.microsoft.com/library/ee758710.aspx)。 新增連接字串的**ConfigurationSettings**區段的 [服務設定檔。
- 您也可以直接在您的程式碼中使用您的連線字串。 在大部分情況下，不過，我們建議您在設定檔中儲存您設定的字串。

儲存您的設定檔中的連接字串讓您更容易更新連線字串的儲存空間模擬器和雲端中的 Azure 儲存體帳戶之間切換。 您只需要編輯指向您的目標環境中的連接字串。

您可以使用[Microsoft Azure 組態管理員](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)類別來存取您在執行階段，無論正在應用程式的連接字串。

## <a name="create-a-connection-string-to-the-storage-emulator"></a>建立儲存模擬器的連接字串

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

如需儲存空間模擬器相關資訊，請參閱[使用 Azure 儲存模擬器開發和測試](storage-use-emulator.md)。

## <a name="create-a-connection-string-to-an-azure-storage-account"></a>建立 Azure 儲存體帳戶的連線字串

若要建立連線字串 Azure 儲存體帳戶，請使用下列的連線字串格式。 指出您是否要連線至儲存帳戶，透過 HTTPS （建議使用） 或 HTTP、 取代`myAccountName`名稱的儲存空間帳戶和取代`myAccountKey`與您的帳戶便捷鍵︰

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

例如，您的連線字串看起來類似下列範例連接字串︰

    DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>

> [AZURE.NOTE] Azure 儲存支援 HTTP 和 HTTPS 的連線字串。不過，強烈建議使用 HTTPS。

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>建立使用共用的 access 簽章的連接字串

[AZURE.INCLUDE [storage-use-sas-in-connection-string-include](../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="creating-a-connection-string-to-an-explicit-storage-endpoint"></a>建立明確的儲存空間端點的連接字串

您可以在您的連接字串，而不是使用預設端點明確指定服務端點。 若要建立連線字串，指定明確端點，指定完成服務端點的每個服務，包括通訊協定規格 （HTTPS （建議使用） 或 HTTP），以下列格式︰

    DefaultEndpointsProtocol=[http|https];
    BlobEndpoint=myBlobEndpoint;
    QueueEndpoint=myQueueEndpoint;
    TableEndpoint=myTableEndpoint;
    FileEndpoint=myFileEndpoint;
    AccountName=myAccountName;
    AccountKey=myAccountKey

您可能會想要指定明確的結束點的其中一種情況是如果您有對應您 Blob 儲存體端點的自訂網域。 在此情況下，您可以指定在您的連接字串中的 Blob 儲存體您自訂的端點，並您也可以指定其他服務的預設端點，如果您的應用程式使用。

以下是有效的連接字串的指定明確的端點 Blob 服務的範例︰

    # Blob endpoint only
    DefaultEndpointsProtocol=https;
    BlobEndpoint=www.mydomain.com;
    AccountName=storagesample;
    AccountKey=account-key

    # All service endpoints
    DefaultEndpointsProtocol=https;
    BlobEndpoint=www.mydomain.com;
    FileEndpoint=myaccount.file.core.windows.net;
    QueueEndpoint=myaccount.queue.core.windows.net;
    TableEndpoint=myaccount;
    AccountName=storagesample;
    AccountKey=account-key

會列在 [連線字串的結束點值用來建構 Blob 服務的要求 Uri，它表示您的程式碼會傳回任何 Uri 的表單。

請注意，是否您選擇省略服務端點連接字串，然後您將無法存取您的程式碼中該服務的資料使用的連線字串。

### <a name="creating-a-connection-string-with-an-endpoint-suffix"></a>含端點後稱謂建立連線字串

若要建立區域或不同的端點尾碼執行個體中的儲存空間服務的連線字串例如 Azure china （中國） 或 Azure 管理使用下列的連接字串格式。 指出您是否要連線至透過 HTTP 或 HTTPS 儲存帳戶、 取代`myAccountName`使用您儲存的帳戶名稱，來取代`myAccountKey`與您的帳戶便捷鍵，取代`mySuffix`與 URI 後置字元︰


    DefaultEndpointsProtocol=[http|https];
    AccountName=myAccountName;
    AccountKey=myAccountKey;
    EndpointSuffix=mySuffix;


例如，您的連線字串看起來應該類似以下的連線字串︰

    DefaultEndpointsProtocol=https;
    AccountName=storagesample;
    AccountKey=<account-key>;
    EndpointSuffix=core.chinacloudapi.cn;

## <a name="parsing-a-connection-string"></a>剖析連線字串

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]


## <a name="next-steps"></a>後續步驟

- [Azure 儲存模擬器用於開發和測試](storage-use-emulator.md)
- [Azure 儲存瀏覽器](storage-explorers.md)
- [使用共用的 Access 簽章 (SA)](storage-dotnet-shared-access-signature-part-1.md)
