既然您已新增觸發程序，其時間進行操作有趣的資料所產生的觸發程序。 請遵循下列步驟，將**SharePoint Online-建立檔案**的動作。 這個動作會檔案在 SharePoint Online 中每次建立新的項目觸發程序，則會啟動。 

若要設定此動作，您需要提供下列資訊。 您會發現很容易使用做為新檔案的摘要資訊的部分輸入產生觸發程序的資料︰

|建立檔案屬性|描述|
|---|---|
|網站 URL|這是您要建立新檔案的 SharePoint Online 網站的 URL。 從清單中選取的網站。|
|資料夾的路徑|這是新的檔案所在的資料夾 （在網站的 URL)。 瀏覽並選取的資料夾。|
|檔案名稱|這是建立檔案的名稱。|
|檔案內容|檔案會寫入內容。|

1. 選取 [ **+ 新步驟**以新增動作]。  
![](./media/connectors-create-api-sharepointonline/action-1.png)  
- 選取 [**新增動作**] 連結。 [搜尋] 方塊，您可以搜尋的任何動作您想要採取此隨即會開啟。 此範例中，SharePoint 動作會感興趣。    
![](./media/connectors-create-api-sharepointonline/action-2.png)    
- 輸入*sharepoint*搜尋 SharePoint 與相關的動作。
- 選取 [ **SharePoint Online-建立檔案**以採取的動作。   **注意**︰ 您會提示您授權邏輯應用程式來存取您的 SharePoint 帳戶，如果您還不這麼做之前。    
![](./media/connectors-create-api-sharepointonline/action-3.png)    
- **建立檔案**控制項隨即會開啟。   
![](./media/connectors-create-api-sharepointonline/action-4.png)     
- 選取**網站的 URL** ，然後瀏覽以尋找您想要建立檔案的網站。     
![](./media/connectors-create-api-sharepointonline/action-5.png)  
- 選取**資料夾路徑**，然後瀏覽以尋找新的檔案所在的資料夾。  
![](./media/connectors-create-api-sharepointonline/action-6.png)  
- 選取 [**檔案名稱**] 控制項，然後輸入您想要建立檔案的名稱。 檔案名稱，請注意，您可以使用從觸發程序建立先前只從所呈現的清單中選取屬性。     
![](./media/connectors-create-api-sharepointonline/action-7.png)  
- 選取**檔案內容**控制項，然後輸入會建立的檔案會寫入的內容。 對於檔案的內容，請注意，您可以使用的任何內容，從您先前建立的觸發程序。 只從所呈現的清單中選取的內容。 或者，您也可以直接將控制項輸入**檔案內容**的文字。 在此範例中，我可以選取某些屬性，並新增空格及連字號之間的每個屬性。        
![](./media/connectors-create-api-sharepointonline/action-8.png)  
- 將所做的變更儲存到您的工作流程  
