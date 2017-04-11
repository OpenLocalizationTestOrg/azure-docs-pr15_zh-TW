<properties 
    pageTitle="Azure 應用程式服務中建立 PHP MySQL web 應用程式並部署使用 FTP" 
    description="示範如何建立 PHP web 應用程式中 Azure MySQL 與使用 FTP 部署儲存資料的教學課程。" 
    services="app-service\web" 
    documentationCenter="php" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>


#<a name="create-a-php-mysql-web-app-in-azure-app-service-and-deploy-using-ftp"></a>Azure 應用程式服務中建立 PHP MySQL web 應用程式並部署使用 FTP

本教學課程教您如何建立 PHP MySQL web 應用程式，以及如何將其使用 FTP 部署。 本教學課程，假設您有[PHP][install-php]， [MySQL][install-mysql]，網頁伺服器，以及 FTP 用戶端電腦上安裝。 在任何作業系統，包括 Windows、 Mac 和 Linux，就可以之後本教學課程中的指示進行。 完成本指南，您必須在 Azure 中執行的 PHP/MySQL web 應用程式。
 
您將瞭解︰

* 如何建立 web 應用程式，使用 [Azure 入口網站 MySQL 資料庫。 因為 PHP 預設會啟用 Web 應用程式中，選擇性執行任何動作，才能執行碼。
* 如何發佈您的應用程式使用 FTP Azure。
 
依照本教學課程中，您會建立簡單的註冊 web app 中 PHP。 應用程式會裝載在 Web 應用程式。 下方，為已完成的應用程式的螢幕擷取畫面︰

![Azure PHP 網站][running-app]

>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 沒有所需的信用卡，沒有承諾。 


##<a name="create-a-web-app-and-set-up-ftp-publishing"></a>建立 web 應用程式，並設定 FTP 發佈

請遵循這些步驟來建立 web 應用程式和 MySQL 資料庫︰

1. 登入[Azure 入口網站][management-portal]。
2. 按一下左上 Azure 入口網站頂端的 [ **+ 新增**] 圖示。

    ![建立新的 Azure 網站][new-website]

3. 在搜尋] 中，輸入**Web 應用程式 + MySQL** ，然後按一下 [在**Web 應用程式 + MySQL**]。

    ![自訂建立新的網站][custom-create]

4. 按一下 [**建立**]。 輸入唯一的應用程式服務名稱 」、 「 資源群組和新的服務方案有效的名稱。

    ![設定資源群組名稱][resource-group]


6. 輸入新的資料庫，包括同意法律字詞的值。

    ![建立新的 MySQL 資料庫][new-mysql-db]
    
7. 建立 web 應用程式之後，您會看到新的應用程式服務刀。


6. 按一下 [**設定** > **部署認證**。 

    ![設定部署認證][set-deployment-credentials]

7. 若要啟用 FTP 發佈，您必須提供使用者名稱和密碼。 儲存認證，並記下的使用者名稱與您所建立的密碼。

    ![建立發佈的認證][portal-ftp-username-password]

##<a name="build-and-test-your-app-locally"></a>建立並測試您的應用程式至本機

登錄的是簡單的 PHP 應用程式，可讓您註冊的事件提供您的名稱和電子郵件地址。 上一個開放的相關資訊會顯示在表格中。 註冊資訊會儲存於 MySQL 資料庫。 應用程式包含兩個檔案︰

* **index.php**︰ 顯示註冊和資料表，其中包含 registrant 資訊的表單。
* **createtable.php**︰ 建立應用程式的 MySQL 資料表。 一次只會用這個檔案。

若要建立並在本機上執行應用程式，請遵循下列步驟。 請注意，這些步驟是假設您有 PHP，MySQL，且網頁伺服器上您的本機電腦，且您設定已啟用[MySQL PDO 副檔名][pdo-mysql]。

1. 建立 MySQL 資料庫稱為`registration`。 您可以從 MySQL 命令提示字元中，使用這個命令來執行此動作︰

        mysql> create database registration;

2. 在您的網頁伺服器的根目錄，建立一個名為資料夾`registration`並建立兩個檔案中的一個名為`createtable.php`和一個名為`index.php`。

3. 開啟`createtable.php`文字編輯器或 IDE 文件中的檔案，並新增下列程式碼。 將此程式碼會用來建立`registration_tbl`表格中`registration`資料庫。

        <?php
        // DB connection info
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        try{
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            $sql = "CREATE TABLE registration_tbl(
                        id INT NOT NULL AUTO_INCREMENT, 
                        PRIMARY KEY(id),
                        name VARCHAR(30),
                        email VARCHAR(30),
                        date DATE)";
            $conn->query($sql);
        }
        catch(Exception $e){
            die(print_r($e));
        }
        echo "<h3>Table created.</h3>";
        ?>

    > [AZURE.NOTE] 
    > 您需要更新的值<code>$user</code>和<code>$pwd</code>使用本機 MySQL 使用者名稱和密碼。

4. 開啟網頁瀏覽器，瀏覽至[http://localhost/registration/createtable.php][localhost-createtable]。 這會建立`registration_tbl`資料庫中的資料表。

5. 在文字編輯器] 或 [IDE 開啟**index.php**檔案，並新增基本 HTML 和 CSS 網頁程式碼 （PHP 程式碼會在稍後步驟中新增）。

        <html>
        <head>
        <Title>Registration Form</Title>
        <style type="text/css">
            body { background-color: #fff; border-top: solid 10px #000;
                color: #333; font-size: .85em; margin: 20; padding: 20;
                font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
            }
            h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
            h1 { font-size: 2em; }
            h2 { font-size: 1.75em; }
            h3 { font-size: 1.2em; }
            table { margin-top: 0.75em; }
            th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
            td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
        </style>
        </head>
        <body>
        <h1>Register here!</h1>
        <p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
        <form method="post" action="index.php" enctype="multipart/form-data" >
              Name  <input type="text" name="name" id="name"/></br>
              Email <input type="text" name="email" id="email"/></br>
              <input type="submit" name="submit" value="Submit" />
        </form>
        <?php

        ?>
        </body>
        </html>

6. 內 PHP 標籤中，新增 PHP 需連線至資料庫的程式碼。

        // DB connection info
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        // Connect to database.
        try {
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
        }
        catch(Exception $e){
            die(var_dump($e));
        }

    > [AZURE.NOTE]
    > 同樣地，您需要更新的值<code>$user</code>和<code>$pwd</code>使用本機 MySQL 使用者名稱和密碼。

7. 下列資料庫連線程式碼中，將程式碼的插入資料庫中的註冊資訊。

        if(!empty($_POST)) {
        try {
            $name = $_POST['name'];
            $email = $_POST['email'];
            $date = date("Y-m-d");
            // Insert data
            $sql_insert = "INSERT INTO registration_tbl (name, email, date) 
                           VALUES (?,?,?)";
            $stmt = $conn->prepare($sql_insert);
            $stmt->bindValue(1, $name);
            $stmt->bindValue(2, $email);
            $stmt->bindValue(3, $date);
            $stmt->execute();
        }
        catch(Exception $e) {
            die(var_dump($e));
        }
        echo "<h3>Your're registered!</h3>";
        }

8. 最後，下列上述的程式碼，新增 [從資料庫中擷取資料的程式碼。

        $sql_select = "SELECT * FROM registration_tbl";
        $stmt = $conn->query($sql_select);
        $registrants = $stmt->fetchAll(); 
        if(count($registrants) > 0) {
            echo "<h2>People who are registered:</h2>";
            echo "<table>";
            echo "<tr><th>Name</th>";
            echo "<th>Email</th>";
            echo "<th>Date</th></tr>";
            foreach($registrants as $registrant) {
                echo "<tr><td>".$registrant['name']."</td>";
                echo "<td>".$registrant['email']."</td>";
                echo "<td>".$registrant['date']."</td></tr>";
            }
            echo "</table>";
        } else {
            echo "<h3>No one is currently registered.</h3>";
        }

您可以立即瀏覽至[http://localhost/registration/index.php] [localhost-index]測試應用程式。

##<a name="get-mysql-and-ftp-connection-information"></a>取得 MySQL 和 FTP 連線資訊

若要連線到 MySQL 資料庫執行的 Web 應用程式，您會需要的連線資訊。 若要取得 MySQL 連線資訊，請遵循下列步驟︰

1. 從應用程式服務 web 應用程式刀按一下 [資源群組] 連結︰

    ![選取 [資源] 群組][select-resourcegroup]

1. 從您 [資源] 群組中，按一下 [資料庫︰

    ![選取資料庫][select-database]

2. 從摘要資料庫中，選取 [**設定** > **屬性**。

    ![選取 [內容]][select-properties]
    
2. 請記下的值`Database`， `Host`， `User Id`，及`Password`。

    ![附註的內容][note-properties]

3. 從您 web 應用程式中，按一下頁面右上角的 [**下載發佈的設定檔**] 連結︰

    ![下載發佈的設定檔][download-publish-profile]

4. 開啟`.publishsettings`XML 編輯器中的檔案。 

3. 尋找`<publishProfile >`的項目`publishMethod="FTP"`看起來像這樣︰

        <publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
            ...
        </publishProfile>
    
請記下`publishUrl`， `userName`，及`userPWD`屬性。

##<a name="publish-your-app"></a>發佈您的應用程式

您已測試您的應用程式本機之後，您可以將其發佈至 web 應用程式使用 FTP。 不過，您必須以更新應用程式中的資料庫連線資訊。 較舊版本 （在**取得 MySQL 與 FTP 連線資訊**] 區段中），使用您取得資料庫連線資訊更新**同時**中的下列資訊`createdatabase.php`和`index.php`檔案以適當的值︰

    // DB connection info
    $host = "value of Data Source";
    $user = "value of User Id";
    $pwd = "value of Password";
    $db = "value of Database";

現在您已經準備好要發佈您的應用程式使用 FTP。

1. 開啟您的 FTP 用戶端的選項。

2. 輸入 [*主機名稱部分*的`publishUrl`您記下到您的 FTP 用戶端上方的屬性。

3. 輸入`userName`和`userPWD`您記下上方的屬性不會變更到您的 FTP 用戶端。

4. 建立的連線。

您已連線之後，您可以上傳及下載檔案，視需要。 請務必上載檔案的根目錄，也就是`/site/wwwroot`。

同時上傳後`index.php`和`createtable.php`，瀏覽至要建立 MySQL 資料表，讓應用程式，然後瀏覽至要開始使用應用程式的**http://[site name].azurewebsites.net/index.php** **http://[site name].azurewebsites.net/createtable.php** 。
 
## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[PHP 開發人員中心](/develop/php/)。

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-ftp/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-ftp/create_web_mysql.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-ftp/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-ftp/select_webapp.png
[set-deployment-credentials]: ./media/web-sites-php-mysql-deploy-use-ftp/set_credentials.png
[portal-ftp-username-password]: ./media/web-sites-php-mysql-deploy-use-ftp/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-ftp/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-ftp/create_wa.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-ftp/select_database.png
[select-resourcegroup]: ./media/web-sites-php-mysql-deploy-use-ftp/select_resourcegroup.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/note-properties.png

[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://portal.azure.com
[download-publish-profile]: ./media/web-sites-php-mysql-deploy-use-ftp/download_publish_profile_3.png
 
