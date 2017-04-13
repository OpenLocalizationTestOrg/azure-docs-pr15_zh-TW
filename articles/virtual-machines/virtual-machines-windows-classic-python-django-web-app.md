<properties
    pageTitle="Python 網頁 Django 應用程式 |Microsoft Azure"
    description="本教學課程會指導您如何以主控 Azure Django 型網站使用 Windows Server 2012 R2 資料中心虛擬機器使用傳統的部署模型。"
    services="virtual-machines-windows"
    documentationCenter="python"
    authors="huguesv"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>


<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="web" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="08/04/2015" 
    ms.author="huvalo"/>


# <a name="django-hello-world-web-application-on-a-windows-server-vm"></a>在 Windows Server VM Django Hello World web 應用程式

> [AZURE.SELECTOR]
- [Windows](virtual-machines-windows-classic-python-django-web-app.md)
- [Mac/Linux](virtual-machines-linux-python-django-web-app.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

本教學課程說明如何主控 Django 為基礎的網站上使用 Windows Server 虛擬機器的 Microsoft Azure。 本教學課程假設您有使用 Azure 沒有先前經驗。 完成後本教學課程中，您必須設定 Django 為基礎的應用程式並在雲端中執行。

您將學習如何︰

* 設定主機 Django Azure 虛擬機器。 本教學課程說明如何在 Windows Server 完成這項作業，同時也與 Linux VM 裝載於 Azure 進行相同。
* 從 Windows 中建立新的 Django 應用程式。

依照本教學課程中，您會建立一個簡單的什麼 web 應用程式。 應用程式會裝載 Azure 虛擬機器中。

在已完成的應用程式的螢幕擷取畫面顯示下一步。

![在瀏覽器視窗，顯示 Azure 認識全球頁面][1]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>建立及設定主機 Django Azure 虛擬機器

1. 依照指示指定[以下](virtual-machines-windows-classic-tutorial.md)建立 Windows Server 2012 R2 資料中心分配的 Azure 虛擬機器。

1. 指示 Azure，將連接埠 80 流量導向的連接埠 80 虛擬機器上︰
 - 瀏覽至您新建立的虛擬機器中 Azure 傳統入口網站，然後按一下 [**結束點**] 索引標籤。
 - 按一下畫面底部的 [**新增**] 按鈕。
    ![新增端點](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-addendpoint.png)

 - 開啟**TCP**通訊協定設定為**私人的連接埠 80**的**公用連接埠 80** 。
![][port80]
1. 從 [**儀表板**] 索引標籤中，按一下 [**連線**到遠端登入新建立的 Azure 虛擬機器使用**遠端桌面**]。  

**重要附註︰**下列所有指示假設您登入虛擬機器正確和發行那里命令，而不是您的本機電腦。

## <a id="setup"></a>安裝 Python Django，WFastCGI

**附註︰**若要下載使用 Internet Explorer，您可能必須設定 IE ESC (開始/系統管理工具/伺服器管理員/本機伺服器，然後按一下 [ **IE 增強的安全性設定**、 設定為 [關閉)。

1. 從[python.org][]安裝最新的 Python 2.7 或 3.4。
1. 安裝使用 pip wfastcgi 和 django 套件。

    針對 Python 2.7 使用下列命令。

        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django

    針對 Python 3.4 使用下列命令。

        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="installing-iis-with-fastcgi"></a>透過 FastCGI 安裝 IIS

1. 安裝 IIS FastCGI 支援。  這可能需要幾分鐘的時間執行。

        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="creating-a-new-django-application"></a>建立新的 Django 應用程式

1.  從*C:\inetpub\wwwroot*，輸入下列命令以建立新的 Django 專案︰

    針對 Python 2.7 使用下列命令。

        C:\Python27\Scripts\django-admin.exe startproject helloworld

    針對 Python 3.4 使用下列命令。

        C:\Python34\Scripts\django-admin.exe startproject helloworld

    ![新增 AzureService 指令的結果](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-cmd-new-azure-service.png)

1.  [ **Django 管理員**] 命令會產生的基本結構 Django 為基礎的網站︰

  -   **helloworld\manage.py**可協助您開始主機服務，並停止 Django 為基礎的網站
  -   **helloworld\helloworld\settings.py**包含 Django 設定您的應用程式。
  -   **helloworld\helloworld\urls.py**包含每個 url] 與 [其檢視之間的對應程式碼。

1.  建立名為**views.py** *C:\inetpub\wwwroot\helloworld\helloworld*目錄中的新檔案。 這將會包含呈現 「 好 」 頁面的檢視。 啟動編輯器，然後輸入下列資訊︰

        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

1.  以下列取代 urls.py 檔案的內容。

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )

## <a name="configuring-iis"></a>設定 IIS

1. 解除鎖定全域 applicationhost.config 處理常式一節。  這會讓您 web.config 中 python 處理常式的使用。

        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers

1. 啟用 WFastCGI。  這會將全域 applicationhost.config 參照可執行您 Python 解譯和 wfastcgi.py 指令碼的應用程式。

    Python 2.7:

        c:\python27\scripts\wfastcgi-enable

    Python 3.4:

        c:\python34\scripts\wfastcgi-enable

1. 建立*C:\inetpub\wwwroot\helloworld*web.config 檔案。  值的`scriptProcessor`屬性應該相符的輸出的上一個步驟。  請參閱更多開啟 wfastcgi 設定 pypi [wfastcgi][]的頁面。

    Python 2.7:

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

    Python 3.4:

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

1. 更新的 IIS 預設網站指向 django 專案資料夾的位置。

        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"

1. 最後，載入網頁瀏覽器中。

![在瀏覽器視窗，顯示 Azure 認識全球頁面][1]


## <a name="shutting-down-your-azure-virtual-machine"></a>關閉 Azure 虛擬機器

當您完成本教學課程時，關閉及/或移除您新建立的 Azure 虛擬機器以釋出資源的其他教學課程，並避免支付 Azure 使用費用。

[1]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi
