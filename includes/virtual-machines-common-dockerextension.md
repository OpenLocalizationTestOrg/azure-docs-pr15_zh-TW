

[Docker](https://www.docker.com/)是其中一個最常用的隔離應用程式的資料，並計算共用資源的方式使用[Linux 容器](http://en.wikipedia.org/wiki/LXC)，而不是虛擬機器的虛擬化方法。 若要建立 Docker VM 裝載您的應用程式上 Azure 容器的任何數字，您可以使用[Azure Linux 代理程式](../articles/virtual-machines/virtual-machines-linux-agent-user-guide.md) [Azure Docker VM 副檔名](https://github.com/Azure/azure-docker-extension/blob/master/README.md)。

本主題說明︰

+ [Docker 和 Linux 容器]
+ [如何使用 Azure Docker VM 副檔名]
+ [Linux 和 Windows 的虛擬機器副檔名]

若要立即建立 Docker 啟用 Vm，請參閱︰

+ [如何使用 Docker VM 副檔名從 Azure 命令列介面 (Azure CLI)]
+ [如何使用 Docker VM 副檔名 Azure 傳統入口網站]
+ [如何快速開始使用 Docker 在 Azure Marketplace]

若要瞭解副檔名為和運作方式的詳細資訊，請參閱[Docker 副檔名使用者指南](https://github.com/Azure/azure-docker-extension/blob/master/README.md)。

## <a name="docker-and-linux-containers"></a>Docker 和 Linux 容器
[Docker](https://www.docker.com/)是其中一個最常用的虛擬化方法，使用[Linux 容器](http://en.wikipedia.org/wiki/LXC)，而不是虛擬機器的區隔資料並計算共用資源的方式，並提供可讓您建立或快速組合應用程式，並將其分散其他 Docker 容器之間的其他服務。

Docker 和 Linux 容器不[Hypervisors](http://en.wikipedia.org/wiki/Hypervisor) Windows HYPER-V 等[KVM](http://www.linux-kvm.org/page/Main_Page) linux （有許多其他範例）。 Hypervisors 虛擬化基礎的作業系統，才能完成 （稱為*虛擬機器*） hypervisor 好像應用程式中執行的作業系統。

Docker 和其他*容器*的方法有極大減少耗用的啟動時間和處理和負荷使用此程序所需的儲存空間及檔案系統隔離公開僅否則核心功能隔離容器 Linux 核心功能。

下表說明更高的層級的功能差異在於 hypervisors 和 Linux 容器類型。 附註有些功能可能較多或較少需要根據您自己的應用程式的需求。

|   功能      | Hypervisors | 容器  |
| :------------- |-------------| ----------- |
| 處理程序隔離 | 更多或較少完成 | 如果取得根時，可能會遭到盜用容器 host （主機） |
| 所需的磁碟上的記憶體 | 完成 OS 加上應用程式 | 僅限應用程式需求 |
| 若要啟動的時間 | OS 加上應用程式載入的大幅更長的時間︰ 開機 | 大幅短︰ 僅應用程式需要啟動，因為核心正在執行  |
| 容器自動化 | 廣泛異 OS 和應用程式 | [Docker 圖像庫](https://registry.hub.docker.com/)。其他人

若要查看高層級討論的容器及他們的優點，請參閱[Docker 高等級白板](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard)。

如需有關 Docker 功能，它是真正的運作方式的詳細資訊，請參閱[Docker 是什麼？](https://www.docker.com/whatisdocker/)

#### <a name="docker-and-linux-container-security-best-practices"></a>Docker 和 Linux 容器安全性的最佳作法

因為容器執行共用主機電腦的核心存取，即可獲得根惡意程式碼時，也可以存取專業主機電腦，但其他容器。 預設設定， [Docker 建議](https://docs.docker.com/articles/security/)使用加入的群組原則或[角色為基礎的安全性](http://en.wikipedia.org/wiki/Role-based_access_control)，例如[SELinux](http://selinuxproject.org/page/Main_Page)或[AppArmor](http://wiki.apparmor.net/index.php/Main_Page)，，例如，以及減少盡可能授與容器核心功能比非常安全性容器系統。 此外，還有許多其他文件在網際網路上的說明使用容器 Docker 等安全性的方法。

## <a name="how-to-use-the-docker-vm-extension-with-azure"></a>如何使用 Azure Docker VM 副檔名

Docker VM 副檔名是安裝在您建立本身安裝 Docker 引擎，並管理遠端通訊與 VM VM 執行個體中的元件。 有兩種方式安裝 VM 分機號碼︰ 您可以使用管理入口網站您 VM 或建立您可以建立從 Azure 命令列介面 (Azure CLI)。

您可以將 Docker VM 分機號碼新增至任何相容 Linux VM 使用入口網站 （目前僅支援的圖像是 Ubuntu 14.04 LTS 圖像比年 7 月更新）。 使用 Azure CLI 命令列，不過，您可以安裝 Docker VM 擴充和建立並上傳您 Docker 通訊憑證所有同時當您建立的 VM 執行個體。

若要立即建立 Docker 啟用 Vm，請參閱︰

+ [如何使用 Docker VM 副檔名從 Azure 命令列介面 (Azure CLI)]
+ [如何使用 Docker VM 副檔名 Azure 傳統入口網站]

## <a name="virtual-machine-extensions-for-linux-and-windows"></a>Linux 和 Windows 的虛擬機器副檔名
[Azure Docker VM 副檔名](https://github.com/Azure/azure-docker-extension/blob/master/README.md)只是其中幾個 VM 擴充功能，在特殊行為，且更多開發中。 例如，數個[Linux VM 代理程式擴充](../articles/virtual-machines/virtual-machines-linux-agent-user-guide.md)功能可讓您修改和管理虛擬機器，包括安全性功能，核心和網路功能，以及等等。 VMAccess 副檔名，例如可讓您重設管理員密碼或 SSH 鍵。

完整清單，請參閱[Azure VM 副檔名](../articles/virtual-machines/virtual-machines-windows-extensions-features.md)。

<!--Anchors-->
[如何使用 Docker VM 副檔名從 Azure 命令列介面 (Azure CLI)]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[如何使用 Docker VM 副檔名 Azure 傳統入口網站]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/
[如何快速開始使用 Docker 在 Azure Marketplace]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-ubuntu-quickstart/
[Docker 和 Linux 容器]: #Docker-and-Linux-Containers
[如何使用 Azure Docker VM 副檔名]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Linux 和 Windows 的虛擬機器副檔名]: #Virtual-Machine-Extensions-For-Linux-and-Windows
