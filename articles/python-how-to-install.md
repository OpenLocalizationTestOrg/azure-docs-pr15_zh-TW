<properties
    pageTitle="安裝 Python 和 SDK-Azure"
    description="瞭解如何安裝 Python 和 SDK 與 Azure 搭配使用。"
    services=""
    documentationCenter="python"
    authors="lmazuel"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="lmazuel"/>

# <a name="installing-python-and-the-sdk"></a>安裝 Python 和 SDK

Python 很容易在 Windows 上安裝和 Mac、 Linux，以及[在 Windows 版被](https://msdn.microsoft.com/commandline/wsl/about)隨附預先安裝。 本指南逐步引導您進行安裝及取得您的電腦與 Azure 可供使用。

## <a name="whats-in-the-python-azure-sdk"></a>什麼是在 Python Azure SDK？

Azure SDK python 包含元件，可讓您開發、 部署及管理 Azure Python 應用程式。 具體來說，Azure SDK python 包括下列︰

* **管理文件庫**。 這些課程文件庫提供管理 Azure 的資源，例如儲存帳戶時，虛擬機器介面。

* **執行階段文件庫**。 這些課程文件庫的存取 Azure 的功能，例如儲存與服務匯流排提供介面。

## <a name="which-python-and-which-version-to-use"></a>哪些 Python，若要使用哪個版本

有數種類別的 Python 解譯器-範例包括︰

* CPython-標準和最常用 Python 手語翻譯
* PyPy-快速、 標準替代 CPython 實作
* IronPython-.Net/CLR 執行的 Python 手語翻譯
* Jython-執行上 Java 虛擬機器中的 Python 手語翻譯

**CPython** v2.7 或 v3.3 + 和 PyPy 5.4.0，測試，以及支援 Python Azure SDK。

## <a name="where-to-get-python"></a>可在何處取得 Python？

有數種方式可取得 CPython:

* 直接從[www.python.org][]
* 從例如[www.continuum.io][]、 [www.enthought.com][]或[www.activestate.com][]信譽 distro
* 建立來源 ！

除非您有特定的需求，我們建議您先兩個選項。

## <a name="sdk-installation-on-windows-linux-and-macos-client-libraries-only"></a>SDK 安裝 Windows、 Linux，以及 MacOS （用戶端只有文件庫）

如果您已安裝的 Python，您可以使用 pip 現有 Python 2.7 或 Python 3.3 + 環境中安裝套件的所有用戶端文件庫。 這會從[Python 套件索引][](PyPI) 下載的套件。

您可能需要系統管理員權限︰

- 使用 Linux 和 MacOS，`sudo`命令︰ `sudo pip install azure-mgmt-compute`。
- Windows︰ 以管理員身分開啟您的 PowerShell/命令提示字元

您可以安裝個別的每個 Azure 服務每個文件庫︰

```console
   $ pip install azure-batch          # Install the latest Batch runtime library
   $ pip install azure-mgmt-scheduler # Install the latest Storage management library
```

可以使用安裝預覽套件`--pre`標幟︰

```console
   $ pip install --pre azure-mgmt-compute # will install only the latest Compute Management library
```

您也可以在單一條線，使用安裝 Azure 文件庫的一組`azure`中繼套件。 之後，在此中繼套件中的並非所有封包發佈為穩定`azure`中繼套件仍會在預覽。 不過，核心套件，從 [程式碼品質/完整性檢視方塊可以視為 「 穩定 」 這一次
- 它會正式標示為同步處理其他語言的越快越。 我們不之前的計劃上的任何進一步的主要變更。

因為預覽版本，您需要使用`--pre`標幟︰

```console
   $ pip install --pre azure
```
   
或直接

```console
   $ pip install azure==2.0.0rc6
```

## <a name="getting-more-packages"></a>取得更多封包

[Python 套件索引][](PyPI) 具有豐富的選取範圍 Python 文件庫。  如果您選擇安裝 Distro，您會已經有最感興趣的位元從 web 開發技術電腦的各種情況。


## <a name="python-tools-for-visual-studio"></a>Visual Studio Python 工具

[Visual Studio Python 工具][](PTVS) 是 microsoft 與變成完備的 Python IDE OS 空閒外掛程式︰

![how-要-安裝-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

使用 PTVS 是選擇性的但建議您為其可讓您 Python 和 Web 專案方案支援、 偵錯、 分析、 互動式視窗、 範本編輯及 Intellisense。

PTVS 也可讓您輕鬆地將 Microsoft Azure 部署部署到[雲端服務][]和[網站][]的支援。

PTVS 適用於您現有的 Visual Studio 2013 或 2015年安裝。  如需文件、 下載與討論區，請參閱[Python 工具 Visual Studio]。  

## <a name="python-azure-scenarios-for-linux-and-macos"></a>Python Linux 和 MacOS Azure 分析藍本

如果 Linux 或 MacOS，主要支援的 Azure 案例︰

1. 使用用戶端程式庫 python 分成 Azure 服務

2. 在 Linux VM 執行您的應用程式

3. 開發和發行使用給 Azure 網站

第一種情況，可讓您作者利用 Azure REST api Azure PaaS 功能，例如[blob 儲存體][]、[佇列中的儲存空間][]、[資料表儲存體][]等透過 Pythonic 包裝函式的豐富的 web 應用程式。 這些適用於相同 Windows、 Mac 和 Linux。  您也可以使用這些用戶端文件庫，從您的本機開發電腦或 Linux VM Azure 上執行。

VM 案例中，您只要開始 （Ubuntu、 CentOS Suse） 您所選擇的 Linux VM 及執行/管理像什麼。  作為範例，您可以在您的 Windows/Mac/Linux 電腦上執行[IPython][]複寫/筆記本，並指向 Linux 或 Windows 多處理序 VM Azure 上執行 IPython 引擎的瀏覽器。 請參閱[上 Azure IPython 筆記本][]教學課程，如需詳細資訊。

如需如何設定 Linux VM 的資訊，請參閱[建立虛擬機器執行 Linux][]教學課程。

使用給部署，您就可以開發 Python web 應用程式，並將它發佈到 Azure 網站中，從任何作業系統。  當您將存放庫推入 Azure 時，它會自動建立一個虛擬環境，並 pip 安裝您所需的封裝。

如需有關開發和發行 Azure 網站的詳細資訊，請參閱教學課程[Django 建立網站][]、[多建立網站][]，以及[建立網站與 Flask][]。 更多一般使用任何 WSGI 相容的架構的詳細資訊，請參閱[使用 Azure 網站的設定 Python][]。


## <a name="additional-software-and-resources"></a>其他軟體與資源︰

* [Azure SDK Python ReadTheDocs](http://azure-sdk-for-python.readthedocs.io/en/latest/)
* [Azure SDK Python Github](https://github.com/Azure/azure-sdk-for-python)
* [Python 正式 Azure 範例](https://azure.microsoft.com/documentation/samples/?platform=python)
* [連續分析 Python 通訊群組][]
* [Enthought Python 通訊群組][]
* [ActiveState Python 通訊群組][]
* [SciPy-科學 Python 文件庫的套件][]
* [NumPy-python 數字文件庫][]
* [Django Project-成熟的 web 架構 CMS][]
* [IPython-Python 進階複寫/筆記本][]
* [在 Azure IPython 筆記本][]
* [Visual Studio 上 GitHub Python 工具][]
* [Python 開發人員中心](/develop/python/)

[連續分析 Python 通訊群組]: http://continuum.io
[Enthought Python 通訊群組]: http://www.enthought.com
[ActiveState Python 通訊群組]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy-科學 Python 文件庫的套件]: http://www.scipy.org
[NumPy-python 數字文件庫]: http://www.numpy.org
[Django Project-成熟的 web 架構 CMS]: http://www.djangoproject.com
[IPython-Python 進階複寫/筆記本]: http://ipython.org
[IPython]: http://ipython.org
[在 Azure IPython 筆記本]: virtual-machines-linux-jupyter-notebook.md
[雲端服務]: cloud-services-python-ptvs.md
[網站]: web-sites-python-ptvs-django-mysql.md
[Visual Studio Python 工具]: http://aka.ms/ptvs
[Visual Studio 上 GitHub Python 工具]: https://github.com/microsoft/ptvs
[Python 套件索引]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[建立執行 Linux 虛擬機器]: virtual-machines-linux-quick-create-cli.md
[使用 Django 建立網站]: web-sites-python-create-deploy-django-app.md
[使用多建立網站]: web-sites-python-create-deploy-bottle-app.md
[使用 Flask 建立網站]: web-sites-python-create-deploy-flask-app.md
[設定 Python 使用 Azure 網站]: web-sites-python-configure.md
[資料表儲存體]: storage-python-how-to-use-table-storage.md
[佇列中的儲存空間]: storage-python-how-to-use-queue-storage.md
[blob 儲存體]: storage-python-how-to-use-blob-storage.md
