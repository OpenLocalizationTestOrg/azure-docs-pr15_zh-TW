<properties
    pageTitle="使用上 Azure Linux Docker VM 副檔名"
    description="說明 Docker 和 Azure 虛擬機器擴充功能，並示範如何以程式設計方式建立是從命令列使用 Azure CLI docker 主機 Azure 虛擬機器。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="08/29/2016"
    ms.author="rasquill"/>

# <a name="using-the-docker-vm-extension-from-the-azure-command-line-interface-azure-cli"></a>使用命令列 Azure Docker VM 副檔名介面 (Azure CLI)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]



本主題說明如何建立 VM Docker VM 副檔名從 Azure CLI 中任何平台上的服務管理 (asm) 模式。 [Docker](https://www.docker.com/)是其中一個最常用的區隔資料並計算共用資源的方式使用[Linux 容器](http://en.wikipedia.org/wiki/LXC)，而不是虛擬機器虛擬化方法。 若要建立 Docker VM 裝載您的應用程式上 Azure 容器的任何數字，您可以使用副檔名為 Docker VM 和[Azure Linux 代理程式](virtual-machines-linux-agent-user-guide.md)。 若要查看高層級討論的容器及他們的優點，請參閱[Docker 高等級白板](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard)。


##<a name="how-to-use-the-docker-vm-extension-with-azure"></a>如何使用 Azure Docker VM 副檔名
若要使用 Azure Docker VM 副檔名，您必須安裝新版[Azure 的命令列介面](https://github.com/Azure/azure-sdk-tools-xplat)(Azure CLI) 高於 0.8.6 （如下圖所撰寫的目前使用的是 0.10.0）。 您可以在 Mac、 Linux 和 Windows 上安裝 Azure CLI。


Azure 上使用 Docker 完成程序相當簡單︰

+ 從您要控制 Azure 的電腦上安裝 Azure CLI 和其相依性 （在 Windows 上，這是以虛擬機器執行 Linux 分配）
+ 若要建立的 VM Docker 主機 Azure 中使用 Azure CLI Docker 命令
+ 若要管理您的 Docker 容器中 Azure 中您 Docker VM 使用本機 Docker 命令。


### <a name="install-the-azure-command-line-interface-azure-cli"></a>安裝 Azure 命令列介面 (Azure CLI)

若要安裝並設定 Azure CLI，請參閱[如何安裝 Azure 命令列介面](../xplat-cli-install.md)。 若要確認安裝，請輸入`azure`哪在命令提示字元中，您應該會看到 Azure CLI ASCII 圖案隨後簡短清單基本命令，您可以使用。 如果安裝正常運作，您應該可以輸入`azure help vm`，並查看其中一個列出的命令所 「 docker 」。

> [AZURE.NOTE] Docker 有 Windows [Docker 電腦](https://docs.docker.com/installation/windows/)，您也可以使用自動建立的可用來使用作為 docker 主機 Azure Vm docker 用戶端工具。

### <a name="connect-the-azure-cli-to-to-your-azure-account"></a>連線至 Azure 帳戶的 Azure CLI
您可以使用 Azure CLI 之前必須將您的 Azure 帳戶認證與 Azure CLI 產生在平台上。 [如何連線至您訂閱的 Azure](../xplat-cli-connect.md) ] 區段中說明如何下載及匯入**.publishsettings**檔案或建立您的 Azure CLI 組織的識別碼之間的關聯。

> [AZURE.NOTE] 使用下列其中一或其他方法的驗證，因此務必確定閱讀文件上方了解不同的功能時，有一些不同之處，在 [行為。

### <a name="install-docker-and-use-the-docker-vm-extension-for-azure"></a>安裝 Docker 和 Azure 使用 Docker VM 副檔名
依照在本機電腦上安裝 Docker [Docker 安裝指示](https://docs.docker.com/installation/#installation)。

若要使用 Docker 與 Azure 虛擬機器，用於 VM Linux 圖像必須安裝[Azure Linux VM 代理程式](virtual-machines-linux-agent-user-guide.md)。 目前，有只有兩種類型的圖像，提供此︰

+ 從 Azure 圖像庫 Ubuntu 圖像或

+ 您建立具有 Azure Linux VM 代理程式的自訂 Linux 圖像安裝和設定。 如需如何建立自訂 Linux VM Azure VM 代理程式的相關資訊，請參閱[Azure Linux VM 代理程式](virtual-machines-linux-agent-user-guide.md)。

### <a name="using-the-azure-image-gallery"></a>使用 [Azure 圖像庫

從艦隊或終端機工作階段中，使用下列 Azure CLI 命令使用，請輸入 VM 庫中找到的最新的 Ubuntu 圖像

`azure vm image list | grep Ubuntu-14_04`

選取其中一個圖像的名稱，例如`b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB`，並使用下列命令以建立新的 VM 使用的圖像。

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB" <username> <password>
```

位置︰

+ * &lt;vm cloudservice 名稱&gt;*VM 變成 Docker 容器主機電腦 Azure 中的名稱

+  *&lt;使用者名稱&gt;*VM 的預設根使用者的使用者名稱

+ *&lt;密碼&gt;*是符合複雜的標準版 Azure 的*使用者名稱*帳戶的密碼

> [AZURE.NOTE] 目前密碼必須至少 8 個字元，包含一個小寫、 一個大寫字元、 數字及特殊字元，例如下列字元︰ `!@#$%^&+=`。 否，結尾的前一個句子期間沒有特殊字元。

如果命令已成功，您應該會看到類似於下，視精確的引數與您所使用的選項而定︰

![](./media/virtual-machines-linux-classic-cli-use-docker/dockercreateresults.png)

> [AZURE.NOTE] 建立虛擬機器可能需要幾分鐘，但之後將其佈 (狀態該值`ReadyRole`) Docker 精靈 （Docker 服務） 開始，您可以連線至 Docker 容器 host （主機）。

若要測試 Docker VM 建立 Azure 中，輸入

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

位置*&lt;vm--您-使用名稱&gt;*是在您的來電至中所使用的虛擬機器名稱`azure vm docker create`。 您應該會看到類似下列，這表示您 Docker 主機 VM 是設定和 Azure 中執行，等待您命令。 

現在您可以嘗試以取得使用您的 docker 用戶端連線 (在某些 Docker 用戶端設定，例如的 Mac 上，您可能必須使用`sudo`):

    sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
    Password:
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
    Pool Name: docker-8:1-131781-pool
    Pool Blocksize: 65.54 kB
    Backing Filesystem: extfs
    Data file: /dev/loop0
    Metadata file: /dev/loop1
    Data Space Used: 1.821 GB
    Data Space Total: 107.4 GB
    Data Space Available: 28 GB
    Metadata Space Used: 1.479 MB
    Metadata Space Total: 2.147 GB
    Metadata Space Available: 2.146 GB
    Udev Sync Supported: true
    Deferred Removal Enabled: false
    Data loop file: /var/lib/docker/devicemapper/devicemapper/data
    Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
    Library Version: 1.02.77 (2012-10-15)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-28-generic
    Operating System: Ubuntu 14.04.3 LTS
    CPUs: 1
    Total Memory: 1.637 GiB
    Name: testsshasm
    WARNING: No swap limit support

只要以確定是所有的工作，您可以檢查 Docker 分機 VM:

    azure vm extension get testsshasm
    info: Executing command vm extension get
    + Getting virtual machines
    data: Publisher Extension name ReferenceName Version State
    data: -------------------- --------------- ------------------------- ------- ------
    data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
    info: vm extension get command OK

### <a name="docker-host-vm-authentication"></a>Docker 主機 VM 驗證

除了建立 Docker VM`azure vm docker create`指令也會自動建立必要的憑證，若要允許 Docker 用戶端電腦連線到使用 HTTPS Azure 容器主機和用戶端和主應用程式在電腦上，視儲存憑證。 在後續嘗試，是重複使用現有的憑證，並將其與新的主應用程式共用中。

根據預設，憑證會放在`~/.docker`，且 Docker 會設定為在連接埠**2376年**上執行。 如果您想要使用不同的連接埠或目錄，那麼您可以使用下列其中一`azure vm docker create`命令列選項，以設定您的 Docker 容器主控 VM 來連接用戶端使用不同的連接埠或不同的憑證︰

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

接聽和驗證用戶端連線在指定的連接埠使用所產生的憑證設定主機上的 Docker 精靈`azure vm docker create`] 命令。 在用戶端電腦必須 Docker 主機存取這些憑證。

> [AZURE.NOTE] 執行這些憑證不網路的主機就會很容易可以連接到電腦的任何人。 修改預設設定之前，請確定您瞭解您的電腦和應用程式的風險。

## <a name="next-steps"></a>後續步驟

* 您準備好移至[Docker 使用者指南]並使用您 Docker VM。 若要建立新的入口網站的 Docker 啟用 VM，請參閱[如何使用 Docker VM 副檔名入口網站]。

* Azure Docker VM 副檔名也支援 Docker 撰寫時，會在任何環境的開發人員模式化應用程式並產生一致的部署使用宣告式 YAML 檔案。 請參閱[開始使用 Docker 與撰寫來定義並執行 Azure 虛擬機器上的多重容器應用程式]。  

<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Next steps]: #next-steps

[How to use the Docker VM Extension with Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]: #Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
[如何使用 Docker VM 副檔名入口網站]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Docker 使用者指南]: https://docs.docker.com/userguide/
 
[開始使用 Docker 與撰寫定義和 Azure 虛擬機器上執行多容器的應用程式]:virtual-machines-linux-docker-compose-quickstart.md