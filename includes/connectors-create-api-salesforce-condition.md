此條件會評估每個新的 Salesforce 潛在客戶的電子郵件地址] 的欄位。 如果電子郵件地址包含*amazon.com*，條件則結果為*True*。

1. 選取 [ **+ 新的步驟**。  
![Salesforce 條件圖像 1](./media/connectors-create-api-salesforce/condition-1.png)   
- 選取 [**新增條件**。    
![Salesforce 條件圖像 2](./media/connectors-create-api-salesforce/condition-2.png)  
- 選取 [**選擇一個值**。    
![Salesforce 條件圖像 3](./media/connectors-create-api-salesforce/condition-3.png)  
- 從潛在客戶的觸發程序中選取的*電子郵件*的 token。    
![Salesforce 條件圖像 4](./media/connectors-create-api-salesforce/condition-4.png)  
- 選取 [*包含*]。      
![Salesforce 條件圖像 5](./media/connectors-create-api-salesforce/condition-5.png)  
- 選取 [**選擇一個值**，底部的控制項。     
![Salesforce 條件圖像 6](./media/connectors-create-api-salesforce/condition-6.png)  
- 輸入*amazon.com*做為您想要評估的新的潛在客戶的電子郵件地址的值。 如果電子郵件地址包含*amazon.com*，則條件會評估為*True* ，邏輯應用程式中的其他步驟進行。    
![Salesforce 條件圖像 7](./media/connectors-create-api-salesforce/condition-7.png)  
- 儲存您的邏輯應用程式。  

