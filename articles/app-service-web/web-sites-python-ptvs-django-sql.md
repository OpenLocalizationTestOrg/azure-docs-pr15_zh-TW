<properties 
    pageTitle="Django 和在 Visual studio Python 工具 2.2 Azure SQL 資料庫" 
    description="瞭解如何使用 Visual studio Python 工具來建立 Django web 應用程式存放在 SQL 資料庫執行個體中的資料，並將其部署到 Azure 應用程式服務 Web 應用程式。" 
    services="app-service\web" 
    tags="python"
    documentationCenter="python" 
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="07/07/2016"
    ms.author="huguesv"/>




# <a name="django-and-sql-database-on-azure-with-python-tools-22-for-visual-studio"></a>Django 和在 Visual studio Python 工具 2.2 Azure SQL 資料庫 

在本教學課程中，我們將使用[Visual Studio Python 工具]建立簡單的投票 web 應用程式使用其中一個 PTVS 範例範本。 本教學課程也是以[視訊](https://www.youtube.com/watch?v=ZwcoGcIeHF4)形式提供。

我們將學習如何使用裝載於 Azure SQL 資料庫、 如何設定 web 應用程式使用 SQL 資料庫，以及如何將 web 應用程式發佈到[Azure 應用程式服務 Web 應用程式](http://go.microsoft.com/fwlink/?LinkId=529714)。

請參閱更多文章涵蓋 Azure 應用程式服務 Web 應用程式與 PTVS Azure 資料表儲存體、 MySQL 和 SQL 資料庫服務中使用多 Flask 與 Django web 架構的開發[Python 開發人員中心]。 本文著重於應用程式服務，同時步驟會類似開發[Azure Cloud Services]時。

## <a name="prerequisites"></a>必要條件

 - Visual Studio 2015
 - [Python 2.7 32 位元]
 - [Python 的 Visual Studio 工具 2.2]
 - [Python 的 Visual Studio 範例 VSIX 工具 2.2]
 - [Azure SDK 工具與 2015]
 - Django 1.9 或更新版本

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

## <a name="create-the-project"></a>建立專案

在此區段中，我們會建立 Visual Studio 專案使用範例範本。 我們會建立一個虛擬環境，並安裝所需的封裝。 我們會建立使用 sqlite 的本機資料庫。 然後，我們會在本機上執行的 web 應用程式。

1.  在 Visual Studio 中，選取**檔案**，**新的專案**。

1.  下**Python****範例**可從[Python 工具 2.2 for Visual Studio 範例 VSIX]專案範本。 選取 [**投票 Django Web 專案**]，然後按一下 [確定] 以建立專案]。

    ![新增專案] 對話方塊](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  系統會提示安裝外部套件。 選取 [**將在虛擬環境安裝**]。

    ![外部套件] 對話方塊](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  選取**Python 2.7**做為基底手語翻譯。

    ![加入虛擬環境] 對話方塊](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  在**方案總管]**中，以滑鼠右鍵按一下 [專案] 節點並選取**Python**，然後選取**Django 移轉**。  然後選取 [ **Django 建立 Superuser**。

1.  這會開啟 Django 管理主控台，並在 [專案] 資料夾中建立 sqlite 資料庫。 遵循提示，建立使用者。

1.  確認應用程式運作按<kbd>F5</kbd>。

1.  在頂端導覽列中，按一下 [**登入**]。

    ![網頁瀏覽器](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  在同步處理資料庫時所建立的使用者輸入認證。

    ![網頁瀏覽器](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  按一下 [**建立範例投票**]。

    ![網頁瀏覽器](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  按一下 [投票] 和進行投票。

    ![網頁瀏覽器](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

## <a name="create-a-sql-database"></a>建立 SQL 資料庫

資料庫中，我們會建立 Azure SQL 資料庫。

您可以依照下列步驟來建立資料庫。

1.  登入[Azure 入口網站]。

1.  在功能窗格底部，按一下 [**新增**。 按一下 [**資料 + 的儲存空間** > **SQL 資料庫**。

1.  建立新的資源群組來設定新的 SQL 資料庫，然後選取適當的位置，]。

1.  SQL 資料庫建立後，按一下 [資料庫刀在 [ **Visual Studio 中開啟**]。
2.  按一下 [**設定您的防火牆**。
3.  在**防火牆設定**刀中，新增**開始 IP**與**結束 IP**防火牆規則設為您的部署電腦的公用 IP 位址。 按一下 [**儲存**]。

    這可讓資料庫伺服器的連線，從您的部署電腦。

4.  回資料庫刀中，按一下 [**屬性**]，然後按一下 [**顯示資料庫連線字串**。 

2.  使用 [複製] 按鈕，將**ADO.NET**的值放在剪貼簿]。

## <a name="configure-the-project"></a>設定專案

在此區段中，我們會設定我們的 web 應用程式，使用我們剛剛建立的 SQL 資料庫。 我們也會安裝 SQL 資料庫使用 Django 所需的額外 Python 套件。 然後，我們會在本機上執行的 web 應用程式。

1.  在 Visual Studio 中，開啟**settings.py**，從 [*專案名稱*] 資料夾。 在編輯器]，暫時貼上的連接字串。 連接字串是此格式︰

        Server=<ServerName>,<ServerPort>;Database=<DatabaseName>;User ID=<UserName>;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

編輯的定義`DATABASES`使用上述的值。

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<DatabaseName>',
                'USER': '<UserName>',
                'PASSWORD': '{your_password_here}',
                'HOST': '<ServerName>',
                'PORT': '<ServerPort>',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': 'True',
                }
            }
        }

1.  在方案總管] 底下的 [ **Python 環境**中，以滑鼠右鍵按一下在虛擬環境並選取**安裝 Python 套件**。

1.  安裝套件`pyodbc`使用**pip**。

    ![安裝 Python 套件] 對話方塊](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  安裝套件`django-pyodbc-azure`使用**pip**。

    ![安裝 Python 套件] 對話方塊](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  在**方案總管]**中，以滑鼠右鍵按一下 [專案] 節點並選取**Python**，然後選取**Django 移轉**。  然後選取 [ **Django 建立 Superuser**。

    這會建立我們在前一節中所建立的 SQL 資料庫資料表。 遵循提示，建立使用者沒有符合 sqlite 資料庫建立第一節中的使用者。

1.  執行應用程式與`F5`。 建立投票送出的資料與**建立範例投票**的投票序列化 SQL 資料庫中。


## <a name="publish-the-web-app-to-azure-app-service"></a>將 web 應用程式發佈到 Azure 應用程式服務

Azure.NET SDK 提供輕鬆 Azure 應用程式服務 Web 應用程式部署網頁 web 應用程式。

1.  在**方案總管]**中，以滑鼠右鍵按一下專案節點，然後選取 [**發佈]**。

    ![發佈 Web] 對話方塊](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)

1.  按一下 [ **Microsoft Azure Web 應用程式**。

1.  按一下 [**新增**]，建立新 web 應用程式。

1.  填寫下列欄位，然後按一下 [**建立**]。
    -   **Web 應用程式名稱**
    -   **應用程式服務計劃**
    -   **資源群組**
    -   **區域**
    -   將**資料庫伺服器**設定為 [**無資料庫**

1.  接受所有其他的預設值，然後按一下 [**發佈**]。

1.  發佈的 web 應用程式會自動開啟網頁瀏覽器。 您應該會看到正常運作，web 應用程式使用裝載於 Azure **SQL**資料庫。

    恭喜您 ！

    ![網頁瀏覽器](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

## <a name="next-steps"></a>後續步驟

請遵循這些連結，瞭解更多關於 Python 工具 Visual Studio、 Django 和 SQL 資料庫。

- [Visual Studio 文件的 Python 工具]
  - [Web 專案]
  - [雲端服務的專案]
  - [Microsoft Azure 遠端偵錯]
- [Django 文件]
- [SQL 資料庫]

## <a name="whats-changed"></a>變更的項目
* 若要變更的指南，從網站應用程式服務請參閱︰ [Azure 應用程式服務與程式影響現有 Azure 服務](http://go.microsoft.com/fwlink/?LinkId=529714)


<!--Link references-->
[Python 開發人員中心]: /develop/python/
[Azure 雲端服務]: ../cloud-services-python-ptvs.md

<!--External Link references-->
[Azure 入口網站]: https://portal.azure.com
[Visual Studio Python 工具]: http://aka.ms/ptvs
[Python 的 Visual Studio 工具 2.2]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python 的 Visual Studio 範例 VSIX 工具 2.2]: http://go.microsoft.com/fwlink/?LinkID=624025
[Azure SDK 工具與 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32 位元]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Visual Studio 文件的 Python 工具]: http://aka.ms/ptvsdocs
[Microsoft Azure 遠端偵錯]: http://go.microsoft.com/fwlink/?LinkId=624026
[Web 專案]: http://go.microsoft.com/fwlink/?LinkId=624027
[雲端服務的專案]: http://go.microsoft.com/fwlink/?LinkId=624028
[Django 文件]: https://www.djangoproject.com/
[SQL 資料庫]: /documentation/services/sql-database/
