### <a name="prerequisites"></a>必要條件

- [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol)帳戶  


您可以使用 SFTP 帳戶中的邏輯應用程式之前，您必須授權邏輯應用程式連線至 SFTP 帳戶。 所幸，您可以輕鬆從 Azure 入口網站上邏輯應用程式中。  

以下是授權邏輯應用程式連線到您 SFTP 帳戶步驟︰  
1. 若要建立的連線，SFTP，邏輯應用程式設計工具] 中，選取下拉式清單中的 [**顯示 Microsoft 受管理的 Api** ，然後在 [搜尋] 方塊中輸入*SFTP* 。 選取**SFTP-新增或修改檔案時**觸發程序︰  
![SFTP 連線圖像 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. 如果您還沒有建立任何連線到 SFTP 之前，您會提供您 SFTP 的認證提示。 這些認證會用於授權邏輯應用程式連線至，並存取 SFTP 帳戶的資料︰  
![SFTP 連線圖像 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. 請注意已經建立連線，您現在可以自由邏輯應用程式中的步驟進行︰   
 ![SFTP 連線圖像 3](./media/connectors-create-api-sftp/sftp-3.png) 
