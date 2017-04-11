
根據預設，在行動應用程式後端的 Api 可叫用以匿名方式。 接下來，您需要驗證的用戶端限制存取。  

+ **Node.js 後的端 （透過入口網站）** ︰  
    
    在您的行動應用程式**設定**] 中，按一下 [**簡單的表格**，選取您的資料表。 按一下 [**變更權限**，選取**只驗證的存取**所有的權限，然後按一下 [**儲存**]。 

+ **.NET 後端 (C#)**:  

    在伺服器專案中，瀏覽至**控制站** > **TodoItemController.cs**。 新增`[Authorize]`屬性**TodoItemController**類別，如下所示。 若要限制存取特定的方法，您也可以套用此屬性只這些方法，而非類別。 重新發佈伺服器專案。


        [Authorize]
        public class TodoItemController : TableController<TodoItem>

+ **Node.js 後的端 （透過 Node.js 程式碼）** ︰  
    
    若要要求存取資料表驗證，加入 Node.js 伺服器指令碼中的下列行︰


        table.access = 'authenticated';

    如需詳細資訊，請參閱[如何︰ 表格以存取需要驗證](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth)。 若要瞭解如何從您的網站下載快速入門程式碼專案，請參閱[如何︰ 下載 Node.js 後端快速入門程式碼專案使用給](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)。

