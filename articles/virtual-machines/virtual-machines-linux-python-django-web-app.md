<properties 
    pageTitle="Python web 應用程式上 Linux Django |Microsoft Azure" 
    description="瞭解如何主控 Azure 使用 Linux 虛擬機器 Django 式 web 應用程式。" 
    services="virtual-machines-linux" 
    documentationCenter="python" 
    authors="huguesv" 
    manager="wpickett" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="web" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="11/17/2015" 
    ms.author="huvalo"/>
    
# <a name="django-hello-world-web-application-on-a-linux-vm"></a>Linux VM Django Hello World web 應用程式

> [AZURE.SELECTOR]
- [Windows](virtual-machines-windows-classic-python-django-web-app.md)
- [Mac/Linux](virtual-machines-linux-python-django-web-app.md)

<br>

本教學課程說明如何主控 Django 為基礎的網站上使用 Linux 虛擬機器的 Microsoft Azure。 本教學課程假設您有使用 Azure 沒有先前經驗。 完成本指南，您會有一個 Django 型應用程式設定和雲端中執行。

您將學習如何︰

* 設定主機 Django Azure 虛擬機器。 本教學課程說明如何在**Linux**完成這項作業，同時也與 Windows Server VM 裝載於 Azure 進行相同。 
* 從 Linux 中建立新的 Django 應用程式。

依照本教學課程中，您會建立一個簡單的什麼 web 應用程式。 應用程式會裝載 Azure 虛擬機器中。

下方，為已完成的應用程式的螢幕擷取畫面︰

![在瀏覽器視窗，顯示 Azure 認識全球頁面](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>建立及設定主機 Django Azure 虛擬機器

1. 依照指示指定[以下](virtual-machines-linux-quick-create-portal.md)建立*Ubuntu 伺服器 14.04 LTS*分配的 Azure 虛擬機器。  如果您想要的話，您可以選擇，而不是 SSH 公開金鑰密碼驗證。

1. 編輯以連接埠 80 使用的指示的內送 http 傳送流量網路安全性群組[以下](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)。

1. 根據預設，新的虛擬機器沒有的完整的網域名稱 (FQDN)。  您可以建立一個按照指示[以下](virtual-machines-linux-portal-create-fqdn.md)。  此為選用步驟，完成本教學課程。

## <a id="setup"></a>設定的開發環境

**附註︰**如果您需要安裝 Python，或想要使用用戶端文件庫，請參閱[Python 安裝指南](../python-how-to-install.md)。

Ubuntu Linux VM 已經隨附 Python 2.7 已預先安裝，但沒有 Apache 或 django 安裝。  請遵循這些步驟來連線到您 VM，安裝 Apache 和 django。

1.  啟動新的**終端機**視窗。
    
1.  輸入下列命令以連線至 Azure VM。  如果您沒有建立 FQDN，您可以使用顯示摘要 Azure 傳統入口網站中虛擬機器中的公用 IP 位址連線。

        $ ssh yourusername@yourVmUrl

1.  輸入下列命令以安裝 django:

        $ sudo apt-get install python-setuptools python-pip
        $ sudo pip install django

1.  輸入下列命令以安裝 mod wsgi Apache:

        $ sudo apt-get install apache2 libapache2-mod-wsgi


## <a name="creating-a-new-django-application"></a>建立新的 Django 應用程式

1.  開啟您在使用前一節 ssh 到您 VM**終端機**視窗。
    
1.  輸入下列命令以建立新的 Django 專案︰

        $ cd /var/www
        $ sudo django-admin.py startproject helloworld

    **Django admin.py**指令碼會產生的基本結構 Django 為基礎的網站︰
    -   **helloworld/manage.py**可協助您開始主機服務，並停止 Django 為基礎的網站
    -   **helloworld/helloworld/settings.py**包含 Django 設定您的應用程式。
    -   **helloworld/helloworld/urls.py**包含每個 url] 與 [其檢視之間的對應程式碼。

1.  建立名為**views.py** **/var/www/helloworld/helloworld**目錄中的新檔案。 這將會包含呈現 「 好 」 頁面的檢視。 啟動編輯器，然後輸入下列資訊︰
        
        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

1.  現在取代**urls.py**檔案的內容下列動作︰

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )


## <a name="setting-up-apache"></a>設定 Apache

1.  建立 Apache 虛擬主機設定檔案**/etc/apache2/sites-available/helloworld.conf**。 將內容設定為下列步驟，並以*yourVmName*取代實際 （適用於範例*pyubuntu*)，您會使用的電腦的名稱。

        <VirtualHost *:80>
        ServerName yourVmName
        </VirtualHost>
        WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
        WSGIPythonPath /var/www/helloworld

1.  啟用下列命令，網站︰

        $ sudo a2ensite helloworld

1.  Apache 並重新啟動下列命令︰

        $ sudo service apache2 reload

1.  最後，載入網頁瀏覽器中︰

    ![在瀏覽器視窗，顯示 Azure 認識全球頁面](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)


## <a name="shutting-down-your-azure-virtual-machine"></a>關閉 Azure 虛擬機器

當您完成此教學課程、 關閉及/或移除您新建立的 Azure 虛擬機器以釋出資源的其他教學課程，並避免支付 Azure 使用費用。
