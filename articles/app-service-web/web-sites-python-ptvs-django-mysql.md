<properties 
    pageTitle="Django 和 Azure Python 工具 2.2 Visual studio 上 MySQL" 
    description="瞭解如何使用 Visual studio Python 工具來建立 Django web 應用程式儲存 MySQL 資料庫執行個體中的資料，並將其部署到 Azure 應用程式服務 Web 應用程式。" 
    services="app-service\web" 
    documentationCenter="python" 
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python"
    ms.topic="get-started-article" 
    ms.date="07/07/2016"
    ms.author="huvalo"/>

# <a name="django-and-mysql-on-azure-with-python-tools-22-for-visual-studio"></a>Django 和 Azure Python 工具 2.2 Visual studio 上 MySQL 

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

在本教學課程中，您會使用[Visual Studio Python 工具](PTVS) 若要建立簡單的投票 web 應用程式使用其中一個 PTVS 範例範本。 您將學習如何使用裝載於 Azure MySQL 服務、 如何設定 web 應用程式使用 MySQL，以及如何將 web 應用程式發佈到[Azure 應用程式服務 Web 應用程式](http://go.microsoft.com/fwlink/?LinkId=529714)。

> [AZURE.NOTE] 在此教學課程中所包含的資訊也適用於以下影片的︰
> 
> [MySQL PTVS 2.1: Django 應用程式][video]

請參閱更多文章涵蓋 Azure 應用程式服務 Web 應用程式與 PTVS Azure 資料表儲存體、 MySQL 和 SQL 資料庫服務中使用多 Flask 與 Django web 架構的開發[Python 開發人員中心]。 本文著重於應用程式服務，同時開發[Azure 雲端服務]時的步驟是類似。

## <a name="prerequisites"></a>必要條件

 - Visual Studio 2015
 - [Python 2.7 32 位元]或[Python 3.4 32 位元]
 - [Python 的 Visual Studio 工具 2.2]
 - [Python 的 Visual Studio 範例 VSIX 工具 2.2]
 - [Azure SDK 工具與 2015]
 - Django 1.9 或更新版本

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<!-- This note should not render as part of the the previous include. -->

> [AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 沒有信用卡，而且沒有承諾是必要。

## <a name="create-the-project"></a>建立專案

在此區段中，您會建立 Visual Studio 專案使用範例範本。 您會建立一個虛擬環境，並安裝所需的封裝。 您可以建立使用 sqlite 的本機資料庫。 然後您會在本機上執行應用程式。

1. 在 Visual Studio 中，選取**檔案**，**新的專案**。

1. 下**Python****範例**可從[Python 工具 2.2 for Visual Studio 範例 VSIX]專案範本。 選取 [**投票 Django Web 專案**]，然後按一下 [確定] 以建立專案]。

    ![新增專案] 對話方塊](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1. 系統會提示安裝外部套件。 選取 [**將在虛擬環境安裝**]。

    ![外部套件] 對話方塊](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1. 選取**Python 2.7**或**Python 3.4**做為基底手語翻譯。

    ![加入虛擬環境] 對話方塊](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1. 在**方案總管]**中，以滑鼠右鍵按一下 [專案] 節點並選取**Python**，然後選取**Django 移轉**。  然後選取 [ **Django 建立 Superuser**。

1. 這會開啟 Django 管理主控台，並在 [專案] 資料夾中建立 sqlite 資料庫。 遵循提示，建立使用者。

1. 確認 [應用程式的運作方式按`F5`。

1. 在頂端導覽列中，按一下 [**登入**]。

    ![Django 導覽列](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1. 在同步處理資料庫時所建立的使用者輸入認證。

    ![登入表單](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1. 按一下 [**建立範例投票**]。

    ![建立範例投票](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1. 按一下 [投票] 和進行投票。

    ![範例投票的投票](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

## <a name="create-a-mysql-database"></a>建立 MySQL 資料庫

資料庫，您會在 Azure 建立 ClearDB MySQL 裝載的資料庫。

或者，您可以建立自己的虛擬機器 Azure 中執行，然後安裝並管理您自己的 MySQL。

您可以遵循下列步驟，使用免費的方案建立資料庫。

1. [Azure 入口網站]登入。

1. 在功能窗格頂端，按一下 [**新增**]，然後按一下 [**資料 + 的儲存空間**]，然後按一下**MySQL 資料庫**。 

1. 建立新的資源群組來設定新的 MySQL 資料庫，然後選取適當的位置，]。

1. MySQL 資料庫建立後，請按一下 [資料庫刀中的 [**屬性**]。

1. 使用 [複製] 按鈕，將的**連接字串**值放在剪貼簿]。

## <a name="configure-the-project"></a>設定專案

在此區段中，您會設定為使用您剛剛建立的 MySQL 資料庫我們 web 應用程式。 您也會安裝使用 Django MySQL 資料庫所需的額外 Python 套件。 然後您會在本機上執行的 web 應用程式。

1. 在 Visual Studio 中，開啟**settings.py**，從 [*專案名稱*] 資料夾。 在編輯器]，暫時貼上的連接字串。 連接字串是此格式︰

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    變更預設資料庫**引擎**使用 MySQL，並設定的**連接字串**的**名稱**、**使用者**、**密碼**及**host （主機）**值。

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<Database>',
                'USER': '<User Id>',
                'PASSWORD': '<Password>',
                'HOST': '<Data Source>',
                'PORT': '',
            }
        }


1. 在方案總管] 底下的 [ **Python 環境**中，以滑鼠右鍵按一下在虛擬環境並選取**安裝 Python 套件**。

1. 安裝套件`mysqlclient`使用**pip**。

    ![安裝套件] 對話方塊](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1. 在**方案總管]**中，以滑鼠右鍵按一下 [專案] 節點並選取**Python**，然後選取**Django 移轉**。  然後選取 [ **Django 建立 Superuser**。

    這會建立您在前一節中建立 MySQL 資料庫資料表。 遵循提示，建立沒有符合使用者在本文的第一節所建立的 sqlite 資料庫中的使用者。

1. 執行應用程式與`F5`。 建立投票送出的資料與**建立範例投票**的投票序列化 MySQL 資料庫中。

## <a name="publish-the-web-app-to-azure-app-service"></a>將 web 應用程式發佈到 Azure 應用程式服務

Azure.NET SDK 提供簡單的方法將您的 web 應用程式部署到 Azure 應用程式服務。

1. 在**方案總管]**中，以滑鼠右鍵按一下專案節點，然後選取 [**發佈]**。

    ![發佈 Web] 對話方塊](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1. 按一下 [ **Microsoft Azure 應用程式**服務。

1. 按一下 [**新增**]，建立新 web 應用程式。

1. 填寫下列欄位，然後按一下 [**建立**]:
    - **Web 應用程式名稱**
    - **應用程式服務計劃**
    - **資源群組**
    - **區域**
    - 將**資料庫伺服器**設定為 [**無資料庫**

1. 接受所有其他的預設值，然後按一下 [**發佈**]。

1. 發佈的 web 應用程式會自動開啟網頁瀏覽器。 您應該會看到未能正常運作，使用裝載於 Azure **MySQL**資料庫的 web 應用程式。

    ![網頁瀏覽器](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

    恭喜您 ！ 您已成功發佈至 Azure 的 MySQL 式 web 應用程式。

## <a name="next-steps"></a>後續步驟

請遵循這些連結，進一步瞭解 Python 工具 for Visual Studio 與 Django MySQL。

- [Visual Studio 文件的 Python 工具]
  - [Web 專案]
  - [雲端服務的專案]
  - [Microsoft Azure 遠端偵錯]
- [Django 文件]
- [MySQL]

如需詳細資訊，請參閱[Python 開發人員中心](/develop/python/)。

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
[Python 3.4 32 位元]: http://go.microsoft.com/fwlink/?LinkId=517191
[Visual Studio 文件的 Python 工具]: http://aka.ms/ptvsdocs
[Microsoft Azure 遠端偵錯]: http://go.microsoft.com/fwlink/?LinkId=624026
[Web 專案]: http://go.microsoft.com/fwlink/?LinkId=624027
[雲端服務的專案]: http://go.microsoft.com/fwlink/?LinkId=624028
[Django 文件]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/
[video]: http://youtu.be/oKCApIrS0Lo
