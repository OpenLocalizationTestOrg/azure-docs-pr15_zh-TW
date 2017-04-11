讓我們來新增觸發程序。

1. 在 [搜尋] 方塊中輸入*sftp*邏輯應用程式設計工具，然後選取 [ **SFTP-新增或修改檔案時**觸發程序   
![SFTP 觸發程序圖像 1](./media/connectors-create-api-sftp/trigger-1.png)  
- 開啟**新增或修改檔案時**的控制項  
![SFTP 觸發程序圖像 2](./media/connectors-create-api-sftp/trigger-2.png)  
- 選取**...**位於右側的控制項。 這會開啟資料夾選擇器控制項  
![SFTP 觸發程序圖像 3](./media/connectors-create-api-sftp/action-1.png)  
- 選取 [ **SFTP**選取監控新增或修改檔案的資料夾的根資料夾]。 請注意的根資料夾現在會顯示在 [**資料夾**] 控制項。  
![SFTP 觸發程序圖像 4](./media/connectors-create-api-sftp/action-2.png)   

此時，您的邏輯應用程式已使用會開始執行的其他引動程序和工作流程中的動作，當檔案是修改，或是在特定的 SFTP 資料夾中建立的觸發程序。 

>[AZURE.NOTE]功能邏輯應用程式，它必須包含至少一個觸發程序和一個動作。 請依照下一節新增巨集指令。  