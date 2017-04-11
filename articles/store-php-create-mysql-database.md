<properties
    pageTitle="建立並連線到 MySQL 資料庫 Azure 中"
    description="瞭解如何使用建立 MySQL 資料庫，然後從 Azure 中 PHP web 應用程式連線至該 Azure 入口網站。"
    documentationCenter="php"
    services="app-service\web"
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="mysql"/>

<tags
    ms.service="multiple"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm;cephalin"/>

# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>建立並連線到 MySQL 資料庫 Azure 中

本教學課程教您如何建立 MySQL 資料庫中[Azure 入口網站](https://portal.azure.com)（提供者是[ClearDB](http://www.cleardb.com/)），以及如何從[Azure 應用程式服務](./app-service/app-service-value-prop-what-is.md)中執行的 PHP web 應用程式連線至它。 

> [AZURE.NOTE] 您也可以建立 MySQL 資料庫[服務商場應用程式範本](./app-service-web/app-service-web-create-web-app-from-marketplace.md)的一部分。

## <a name="create-a-mysql-database-in-azure-portal"></a>Azure 入口網站中建立 MySQL 資料庫

若要建立 MySQL 資料庫 Azure 入口網站中，執行下列動作︰

1. [Azure 入口網站](https://portal.azure.com)登入。

2. 從左側功能表中，按一下 [**新增** > **資料 + 的儲存空間** > **MySQL 資料庫**。

    ![Azure-開始中建立 MySQL 資料庫](./media/store-php-create-mysql-database/create-db-1-start.png)

2. 在新的 MySQL 資料庫[刀](azure-portal-overview.md)，設定您的新 MySQL 資料庫，如下所示 (*刀*︰ 入口網站的頁面會開啟 [水平):

    - **資料庫名稱**︰ 輸入可唯一識別的名稱
    - **訂閱**︰ 選擇要使用的訂閱
    - **資料庫類型**︰ 選取 [最低成本] 或 [免費層**共用**或**Dedicated**取得專用的資源。 
    - **資源群組**︰ 新增現有的[資源群組](../azure-resource-manager/resource-group-overview.md)MySQL 資料庫，或將它放在一個新。 [相同] 群組中的資源可以輕鬆地管理放在一起。
    - **位置**︰ 選取您自己的位置。 當新增至現有的資源群組，您被鎖定到 [資源] 群組的位置。
    - **價格層**︰ 按一下**價格層**]，然後選取 [價格] 選項 （**水星**層是免費），然後按一下 [**選取**。 
    - **法律條款**︰ 按一下 [**法律的字詞**，檢閱購買詳細資料，然後按一下 [**購買**。
    - **釘選至儀表板**︰ 如果您想要直接從儀表板存取，請選取。 這是如果您不熟悉尚未入口網站瀏覽特別有用。
    
    以下螢幕擷取畫面只是一個範例的方式，您可以設定 MySQL 資料庫。  
    ![Azure 中建立 MySQL 資料庫-設定](./media/store-php-create-mysql-database/create-db-2-configure.png)

3. 當您完成設定，請按一下 [**建立**]。

    ![Azure 中建立 MySQL 資料庫-建立](./media/store-php-create-mysql-database/create-db-3-create.png)

    您會看到您知道已開始部署快顯通知禁止。

    ![Azure-中建立 MySQL 資料庫，進行中](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    您會收到另一個快顯之後已成功部署。 入口網站也會自動開啟您 MySQL 資料庫刀。

<a name="connect"></a>
## <a name="connect-to-your-mysql-database"></a>連線到 MySQL 資料庫

若要查看您的新 MySQL 資料庫的連線資訊，只要按一下 web 應用程式的刀中的**內容**。
    
![Azure-MySQL 資料庫刀中建立 MySQL 資料庫](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

您現在可以在任何 web app 中使用的連線資訊。 顯示如何使用簡單的 PHP 應用程式中的連線資訊的範例是使用[以下](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql)。

## <a name="connect-a-laravel-web-app-from-the-php-get-started-tutorial"></a>連線 Laravel web app （從 PHP 取得開始教學課程）

假設您剛結束教學課程[建立、 設定和部署 Azure PHP web 應用程式](./app-service-web/app-service-web-php-get-started.md)，而且有 Azure 中執行的[Laravel](https://www.laravel.com/) web 應用程式。 您可以輕鬆地將 Laravel 應用程式資料庫功能加入。 只要遵循下列步驟︰

>[AZURE.NOTE] 下列步驟假設您已完成教學課程[建立、 設定和部署 Azure PHP web 應用程式](./app-service-web/app-service-web-php-get-started.md)。

1. 在您指向 MySQL 資料庫的本機的開發環境中設定 Laravel 應用程式。 若要這麼做，請開啟`.env`您 Laravel 應用程式的根目錄並設定 MySQL 資料庫的選項。

        DB_CONNECTION=mysql
        DB_HOST=<HOSTNAME_from_properties_blade>
        DB_PORT=<PORT_from_properties_blade>
        DB_DATABASE=<see_note_below>
        DB_USERNAME=<USERNAME_from_properties_blade>
        DB_PASSWORD=<PASSWORD_from_properties_blade>

    >[AZURE.NOTE] 在**屬性**刀，MySQL 資料庫的名稱會可能，或可能不是 [**資料庫名稱**] 欄位中顯示的項目。 最好檢查資料庫中的參數的**連線字串**欄位。 
    >
    >![Azure-中建立 MySQL 資料庫，進行中](./media/store-php-create-mysql-database/connect-db-1-database-name.png)

2. 若要確認您現在可以 MySQL 存取最快速的方法是使用[Laravel 的預設驗證臨時平台](https://laravel.com/docs/5.2/authentication#authentication-quickstart)。 在命令列 terminal 中，請從 Laravel 應用程式的根目錄中執行下列命令︰

         php artisan migrate
         php artisan make:auth

    Azure 基礎中預先定義的移轉中的第一個命令所建立的資料表`database/migrations`目錄和第二個命令 scaffolds 的基本檢視和使用者註冊和驗證路由。

3. 立即執行開發伺服器︰

        php artisan serve

4. 在瀏覽器，瀏覽至 http://localhost:8000 和註冊新使用者，如下圖所示︰

    ![連線到 MySQL 資料庫中 Azure-註冊的使用者](./media/store-php-create-mysql-database/connect-db-2-development-server.png)

    請遵循 UI 提示完成註冊。 註冊完成後，您將身分登入。
    
    ![連線到 MySQL 資料庫中 Azure-註冊的使用者](./media/store-php-create-mysql-database/connect-db-3-registered-user.png)

    您現在開發 Azure 中 MySQL 資料庫應用程式。

5. 現在，您只需要複製您`.env`Azure web 應用程式的設定。 執行下列 Azure CLI 命令︰

        azure site appsetting add DB_CONNECTION=mysql
        azure site appsetting add DB_HOST=<HOSTNAME_from_properties_blade>
        azure site appsetting add DB_PORT=<PORT_from_properties_blade>
        azure site appsetting add DB_DATABASE=<Database_param_from_CONNECTION_INFO_from_properties_blade>
        azure site appsetting add DB_USERNAME=<USERNAME_from_properties_blade>
        azure site appsetting add DB_PASSWORD=<PASSWORD_from_properties_blade>

    找出此[設定 Azure web 應用程式](./app-service-web/app-service-web-php-get-started.md#configure)中的運作方式。

6. 接下來，請確認並推入 Azure 本機做的變更之前執行`php artisan make:auth`。

        git add .
        git commit -m "scaffold auth views and routes"
        git push azure master

7. 瀏覽至 Azure web 應用程式。

        azure site browse

8. 使用您先前建立的使用者認證登入。

    ![連線到 MySQL 資料庫中 Azure-瀏覽至 Azure web 應用程式](./media/store-php-create-mysql-database/connect-db-4-browse-azure-webapp.png)

    在您登入之後，您應該會看到 [易記] 後登入畫面。
    
    ![連線到 MySQL 資料庫中 Azure-登入](./media/store-php-create-mysql-database/connect-db-5-logged-in.png)

    恭喜您，PHP web 應用程式中 Azure 從 MySQL 資料庫中立即存取資料。 

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[PHP 開發人員中心](/develop/php/)。
