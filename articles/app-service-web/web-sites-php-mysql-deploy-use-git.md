<properties
    pageTitle="Azure 應用程式服務中建立 PHP MySQL web 應用程式並部署使用給"
    description="示範如何建立資料儲存在 MySQL PHP web 應用程式，並使用 Azure 給部署教學課程。"
    services="app-service\web"
    documentationCenter="php"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="mysql"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-php-mysql-web-app-in-azure-app-service-and-deploy-using-git"></a>Azure 應用程式服務中建立 PHP MySQL web 應用程式並部署使用給

本教學課程教您如何建立 PHP MySQL web 應用程式，以及如何將其部署到[應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714)使用給。 您會使用[PHP][install-php]，MySQL 命令列工具 (部分[MySQL][install-mysql])，及[給][install-git]您電腦上安裝。 在任何作業系統，包括 Windows、 Mac 和 Linux，就可以之後本教學課程中的指示進行。 完成本指南，您必須在 Azure 中執行的 PHP/MySQL web 應用程式。

您將瞭解︰

* 如何建立 web 應用程式，並使用[Azure 入口網站]MySQL 資料庫[management-portal]。 因為 PHP 預設會啟用[應用程式服務 Web 應用程式](http://go.microsoft.com/fwlink/?LinkId=529714)中，選擇性執行任何動作，才能執行碼。
* 如何發佈並重新發佈 Azure 使用給您的應用程式。
* 如何啟用 [編輯器] 延伸到自動化編輯器工作在每個`git push`。

依照本教學課程中，您會建立簡單的註冊 web app 中 PHP。 應用程式會裝載在 Web 應用程式。 下方，為已完成的應用程式的螢幕擷取畫面︰

![Azure PHP 網站][running-app]

## <a name="set-up-the-development-environment"></a>設定的開發環境

本教學課程，假設您有[PHP][install-php]，MySQL 命令列工具 (部分[MySQL][install-mysql])，及[給][install-git]您電腦上安裝。

<a id="create-web-site-and-set-up-git"></a>
## <a name="create-a-web-app-and-set-up-git-publishing"></a>建立 web 應用程式，並設定給發佈

請遵循這些步驟來建立 web 應用程式和 MySQL 資料庫︰

1. 登入[Azure 入口網站][management-portal]。
2. 按一下 [**新增**] 圖示。

3. 按一下 [**查看所有****服務商場**旁。 

4. 按一下 [ **Web + Mobile**，再**Web 應用程式 + MySQL**]。 然後按一下 [**建立**]。

4. 輸入資源群組的有效的名稱。

    ![設定資源群組名稱][resource-group]

5. 輸入新的 web 應用程式的值。

    ![建立 web 應用程式][new-web-app]

6. 輸入新的資料庫，包括同意法律字詞的值。

    ![建立新的 MySQL 資料庫][new-mysql-db]

7. 建立 web 應用程式之後，您會看到新的 web 應用程式刀。

7. 在 [**設定**在**連續部署**中，按一下，然後按一下_設定所需的設定_。

    ![設定給發佈][setup-publishing]

8. 您可以選取 [**本機給存放庫**的來源。

    ![設定給存放庫][setup-repository]


9. 若要啟用給發佈，您必須提供使用者名稱和密碼。 請記下的使用者名稱與您所建立的密碼。 （如果您已設定之前就可以給存放庫，此步驟會略過。）

    ![建立發佈的認證][credentials]


## <a name="get-remote-mysql-connection-information"></a>取得遠端 MySQL 連線資訊

若要連線到 MySQL 資料庫執行的 Web 應用程式，您會需要的連線資訊。 若要取得 MySQL 連線資訊，請遵循下列步驟︰

1. 從您 [資源] 群組中，按一下 [資料庫︰

    ![選取資料庫][select-database]

2. 從資料庫的 [**設定**] 中，選取 [**內容**]。

    ![選取 [內容]][select-properties]

2. 請記下的值`Database`， `Host`， `User Id`，及`Password`。

    ![附註的內容][note-properties]

## <a name="build-and-test-your-app-locally"></a>建立並測試您的應用程式至本機

現在，您建立 web 應用程式，您可以開發應用程式，然後將其部署測試後。

登錄的是簡單的 PHP 應用程式，可讓您註冊的事件提供您的名稱和電子郵件地址。 上一個開放的相關資訊會顯示在表格中。 註冊資訊會儲存於 MySQL 資料庫。 此應用程式包含一個檔案 （如下所示複製/貼上程式碼）︰

* **index.php**︰ 顯示註冊和資料表，其中包含 registrant 資訊的表單。

若要建立並在本機上執行應用程式，請遵循下列步驟。 請注意，這些步驟是假設您有 PHP 和您的本機電腦上設定 MySQL 命令列工具 （MySQL 的一部分），而且您已啟用[MySQL PDO 副檔名][pdo-mysql]。

1. 連線到遠端 MySQL 伺服器，使用的值`Data Source`， `User Id`， `Password`，及`Database`您擷取較舊版本︰

        mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. 將會出現 MySQL 命令提示字元︰

        mysql>

3. 貼上下列的`CREATE TABLE`] 命令，建立`registration_tbl`資料庫中的表格︰

        CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. 在您的本機應用程式] 資料夾的根目錄建立**index.php**檔案。

5. 開啟**index.php**檔案在文字編輯器] 或 [IDE 和下列程式碼，並完成必要的變更標示`//TODO:`註解。


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
            // DB connection info
            //TODO: Update the values for $host, $user, $pwd, and $db
            //using the values you retrieved earlier from the Azure Portal.
            $host = "value of Data Source";
            $user = "value of User Id";
            $pwd = "value of Password";
            $db = "value of Database";
            // Connect to database.
            try {
                $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
                $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            }
            catch(Exception $e){
                die(var_dump($e));
            }
            // Insert registration info
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
            // Retrieve data
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
        ?>
        </body>
        </html>

4.  在 terminal 中移至您的應用程式] 資料夾，並輸入以下命令︰

        php -S localhost:8000

您可以立即瀏覽至**http://localhost:8000 /**測試應用程式。


## <a name="publish-your-app"></a>發佈您的應用程式

您已測試您的應用程式本機之後，您可以使用給的 Web 應用程式發佈。 您會初始化您本機給存放庫並發佈應用程式]。

> [AZURE.NOTE]
> 這些是 Azure 入口網站結尾，建立 web 應用程式並設定設定上述發佈給一節中所示的相同步驟。

1. （選用） 如果您已遺失或遺忘給遠端 repostitory URL，瀏覽至 Azure 入口網站上的 web 應用程式屬性。

1. 開啟 GitBash (或 terminal 中，如果是給您`PATH`) 將目錄變更為您的應用程式的根目錄，請執行下列命令︰

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    系統會提示您先前建立的密碼。

    ![初始的推入到 Azure 透過給][git-initial-push]

2. 瀏覽至要開始使用應用程式 （這項資訊會儲存在您帳戶的儀表板） **http://[site name].azurewebsites.net/index.php** :

    ![Azure PHP 網站][running-app]

發佈您的應用程式之後，您可以開始進行變更，就可以給發佈給。

## <a name="publish-changes-to-your-app"></a>將變更發佈至您的應用程式

若要將變更發佈至您的應用程式中，請遵循下列步驟︰

1. 變更您的應用程式至本機。
2. 開啟 GitBash (或 terminal 中，it 給位於您`PATH`) 將目錄變更為您的應用程式的根目錄，請執行下列命令︰

        git add .
        git commit -m "comment describing changes"
        git push azure master

    系統會提示您先前建立的密碼。

    ![將網站變更至 Azure 發送給透過][git-change-push]

3. 瀏覽至**http://[site name].azurewebsites.net/index.php**可查看您的應用程式與您所做的變更︰

    ![Azure PHP 網站][running-app]

>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

<a name="composer"></a>
## <a name="enable-composer-automation-with-the-composer-extension"></a>啟用編輯器自動化編輯器副檔名

根據預設，在應用程式服務給部署程序不會執行任何 composer.json，如果您有一個 PHP 專案。 您可以啟用 composer.json 處理期間`git push`啟用編輯器副檔名。

1. 在您 PHP 網頁在[Azure 入口網站]應用程式的刀[management-portal]，按一下 [**工具]** > **副檔名**。

    ![編輯器延伸模組設定][composer-extension-settings]

2. 按一下 [**新增**]，然後按一下 [**編輯器**]。

    ![新增編輯器副檔名][composer-extension-add]
    
3. 按一下**[確定**] 以接受法律條款。 按一下**[確定**]，將延伸。

    **已安裝副檔名**刀現在會顯示編輯器副檔名。  
    ![編輯器副檔名檢視][composer-extension-view]
    
4. 現在，請執行`git add`， `git commit`，及`git push`像在前一節中。 現在，您會看到編輯器安裝在 composer.json 中定義的相依性。

    ![編輯器副檔名成功][composer-extension-success]

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[PHP 開發人員中心](/develop/php/)。

<!-- URL List -->

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[install-mysql]: http://dev.mysql.com/downloads/mysql/
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[management-portal]: https://portal.azure.com
[sql-database-editions]: http://msdn.microsoft.com/library/windowsazure/ee621788.aspx

<!-- IMG List -->

[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/create_web_mysql.png
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-git/select_webapp.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-git/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-git/create_wa.png
[setup-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_deploy.png
[setup-repository]: ./media/web-sites-php-mysql-deploy-use-git/select_local_git.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-git/select_database.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-git/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-git/note-properties.png

[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png

[composer-extension-settings]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-settings.png
[composer-extension-add]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-add.png
[composer-extension-view]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-view.png
[composer-extension-success]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-success.png
