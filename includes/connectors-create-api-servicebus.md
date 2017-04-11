### <a name="prerequisites"></a>必要條件

您必須[服務匯流排](https://azure.microsoft.com/services/service-bus/)帳戶。  

您可以使用您的 Azure 服務匯流排帳戶中的邏輯應用程式之前，您必須授權邏輯應用程式連線至您的服務匯流排帳戶。 所幸，您可以輕鬆從 Azure 入口網站上邏輯應用程式中。  

以下是授權邏輯應用程式連線至您的服務匯流排帳戶步驟︰  

1. 若要建立連線到服務匯流排邏輯應用程式設計工具] 中，選取 [**顯示 Microsoft 受管理的 Api**下拉式清單中。 然後在 [搜尋] 方塊中輸入**服務匯流排**。 選取您想要使用的巨集指令的觸發程序。  
    ![服務匯流排連接圖像 1](./media/connectors-create-api-servicebus/servicebus-1.png)  

2. 如果您還沒有建立任何連線到服務匯流排之前，系統會提示您提供服務匯流排認證。 授權存取您的服務匯流排帳戶資料的連線到您邏輯應用程式會使用這些認證。 服務匯流排連接器必須連接字串服務匯流排命名空間。 也需要**管理**權限。 若要知道是否您的連線字串的命名空間或特定的實體其包含的好方法`EntityPath`參數。 如果是這樣，它不正確的連接字串邏輯應用程式。  
    ![服務匯流排連線字串](./media/connectors-create-api-servicebus/connectionstring.png)

1. 您收到命名空間的連接字串之後，您可以 API 連線邏輯應用程式中使用它。  
    ![服務匯流排連接圖像 2](./media/connectors-create-api-servicebus/servicebus-2.png)  

3. 請注意建立連線之後，您現在可以自由邏輯應用程式中的步驟進行。  
    ![服務匯流排連接圖像 3](./media/connectors-create-api-servicebus/servicebus-3.png)   
