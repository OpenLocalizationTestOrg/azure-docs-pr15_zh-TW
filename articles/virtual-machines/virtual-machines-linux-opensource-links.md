<properties
    pageTitle="Linux] 和 [開啟來源電腦 Azure |Microsoft Azure"
    description="列出 Linux 並開啟來源電腦上的文章 Azure，包括基本 Linux 的使用狀況、 執行或上傳 Linux 圖像 Azure，以及其他特定技術和最佳化的相關的內容相關的一些基本概念。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="06/27/2016"
    ms.author="rasquill"/>



# <a name="linux-and-open-source-computing-on-azure"></a>Linux] 和 [開啟來源上 Azure 運算

尋找您要建立及管理 Linux 型虛擬機器傳統部署模型中的所有文件。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="get-started"></a>快速入門
- [在 Azure Linux 的簡介](virtual-machines-linux-intro-on-azure.md)
- [常見問題集問題有關 Azure 虛擬機器建立傳統的部署模型](virtual-machines-linux-classic-faq.md)
- [關於虛擬機器的圖像](virtual-machines-linux-classic-about-images.md)
- [上傳 Distro 圖像](virtual-machines-linux-classic-create-upload-vhd.md)（並指示可使用[Azure-Endorsed 通訊群組](virtual-machines-linux-endorsed-distros.md)）
- [登入 Linux VM 使用 Azure 傳統入口網站](virtual-machines-linux-mac-create-ssh-keys.md)

## <a name="set-up"></a>設定

- [安裝 Azure 命令列介面 (Azure CLI)](../xplat-cli-install.md)


## <a name="tutorials"></a>教學課程

- [安裝燈堆疊 Linux 中 Azure 虛擬機器](virtual-machines-linux-create-lamp-stack.md)
- [Azure VM 滑軌 Web 應用程式上的 [注音標示](linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)
- [如何︰ 安裝 Apache Qpid Proton-C AMQP 和服務匯流排](../service-bus-messaging/service-bus-amqp-apache.md)

### <a name="databases"></a>資料庫
- [最佳化效能 MySQL Azure 上](virtual-machines-linux-classic-optimize-mysql.md)
- [MySQL 叢集](virtual-machines-linux-classic-mysql-cluster.md)
- [Azure 上執行 Linux Cassandra 和從 Node.js 存取](virtual-machines-linux-classic-cassandra-nodejs.md)
- [建立多重母片的 MariaDbs 叢集](virtual-machines-linux-classic-mariadb-mysql-cluster.md)

### <a name="hpc"></a>HPC
- [快速入門 Linux 運算叢集的節點 HPC 套件 Azure 中](virtual-machines-linux-classic-hpcpack-cluster.md)
- [Microsoft HPC pack linux 執行的 NAMD 運算 Azure 中的節點](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
- [設定 Linux RDMA 叢集執行 MPI 應用程式](virtual-machines-linux-classic-rdma-cluster.md)

### <a name="docker"></a>Docker
- [使用命令列 Azure Docker VM 副檔名介面 (Azure CLI)](virtual-machines-linux-classic-cli-use-docker.md)
- [使用 Docker VM 副檔名從 Azure 入口網站](virtual-machines-linux-classic-portal-use-docker.md)
- [如何使用 docker 電腦上 Azure](virtual-machines-linux-docker-machine.md)

### <a name="ubuntu"></a>Ubuntu
- [如何︰ MySQL 叢集](virtual-machines-linux-classic-mysql-cluster.md)
- [如何︰ Node.js 和 Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md)

### <a name="opensuse"></a>OpenSUSE
- [如何︰ 安裝及執行 MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)

### <a name="coreos"></a>CoreOS
- [如何︰ 使用 Azure CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html)


## <a name="planning"></a>規劃
- [Azure 基礎結構服務實作指導方針](virtual-machines-linux-infrastructure-subscription-accounts-guidelines.md)
- [選取 Linux 使用者名稱](virtual-machines-linux-usernames.md)
- [如何設定為在傳統的部署模型中的虛擬機器可用性](virtual-machines-linux-classic-configure-availability.md)
- [如何排程預定進行的維護作業 Azure Vm](virtual-machines-linux-planned-maintenance-schedule.md)
- [管理虛擬機器的可用性](virtual-machines-linux-manage-availability.md)
- [預定進行的維護 Linux Azure 中的虛擬機器](virtual-machines-linux-planned-maintenance.md)


## <a name="deployment"></a>部署
- [建立自訂的虛擬機器執行 Linux](virtual-machines-linux-classic-createportal.md)
- [基本概念︰ 擷取 Linux VM，讓範本](virtual-machines-linux-classic-capture-image.md)
- [非背書散佈的資訊](virtual-machines-linux-create-upload-generic.md)


## <a name="management"></a>管理

- [SSH](virtual-machines-linux-mac-create-ssh-keys.md)
- [如何重設密碼] 或 [SSH 內容 Linux](virtual-machines-linux-classic-reset-access.md)
- [使用根](virtual-machines-linux-use-root-privileges.md)


## <a name="azure-resources"></a>Azure 資源

- [Azure Linux 代理程式](virtual-machines-linux-agent-user-guide.md)
- [Azure VM Extensions 和功能](virtual-machines-windows-extensions-features.md)
- [若要使用雲端初始化 VM 將插入自訂資料](virtual-machines-windows-classic-inject-custom-data.md)


## <a name="storage"></a>儲存空間

- [將資料磁碟附加至 Linux VM](virtual-machines-linux-classic-attach-disk.md)
- [中斷連結 Linux VM 資料磁碟](virtual-machines-linux-classic-detach-disk.md)
- [RAID](virtual-machines-linux-configure-raid.md)


## <a name="networking"></a>網路
- [如何設定傳統的虛擬機器 Azure 中的端點](virtual-machines-linux-classic-setup-endpoints.md)


## <a name="troubleshooting"></a>疑難排解
- [疑難排解安全命令介面 (SSH) 連線到 Linux 型 Azure 虛擬機器](virtual-machines-linux-troubleshoot-ssh-connection.md)
- [建立新的 Linux 虛擬機器中 Azure 傳統部署問題進行疑難排解](virtual-machines-linux-classic-troubleshoot-deployment-new-vm.md)  
- [疑難排解重新啟動，或調整大小現有的 Linux 虛擬機器中 Azure 傳統部署問題](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md) 


## <a name="reference"></a>參照

- [Azure 服務管理 (asm) 模式中的 azure CLI 命令](../virtual-machines-command-line-tools.md)
- [Azure 服務管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)




## <a name="general-links"></a>一般的連結
下列連結是 Microsoft 部落格、 Technet 頁面和外部網站，而非 Azure.com 上方的文件。 以 [同時 Azure 開啟來源運算世界快速移動目標，則幾乎可以特定下列連結會已過期，*雖然*我們應該執行我們持續新增較新的主題和移除過時的文件的最佳的事實。 如果我們已未接一個，請讓我們知道中註解，或提交提取[GitHub repo](https://github.com/Azure/azure-content/)我們的要求。

- [Linux 使用 Docker 容器上執行 ASP.NET 5](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
- [部署 OpenLogic CentOS VM 圖像的方式](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
- [SUSE 更新基礎結構](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
- [SAP 雲端應用程式庫 SUSE Linux Enterprise Server](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)
- [建置高度可用 Linux 上 Azure 12 的步驟](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
- [自動化佈建的 Linux Azure CLI、 node.js、 jhawk Azure 上](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
- [在 Azure GlusterFS](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

### <a name="freebsd"></a>FreeBSD
- [Azure 中執行 FreeBSD](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
- [輕鬆部署 FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
- [部署自訂的 FreeBSD 圖像](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
- [Kaspersky AV Linux 檔案伺服器](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

### <a name="nosql"></a>NoSQL

- [Azure 8 開啟來源 NoSql 資料庫](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
- [使用上 Azure CouchDb Slideshare (MSOpenTech): 體驗](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
- [執行 CouchDB--的-服務 node.js、 CORS，與步兵](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)

- [在 Windows Azure 意指快取服務 redis](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
- [宣佈使用意指預覽版本 ASP.NET 工作階段狀態提供者](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)

- [部落格︰ RavenHQ 現在可在 Azure Marketplace](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### <a name="big-data"></a>顯示較大的資料
- [Azure Linux Vm 上安裝 Hadoop](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
- [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/)

### <a name="relational-database"></a>關聯式資料庫
- [Microsoft Azure 中的 MySQL 高可用性架構](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
- [透過 corosync，使用 ILB pg_bouncer 安裝 Postgres](https://github.com/chgeuer/postgres-azure)

### <a name="linux-high-performance-computing-hpc"></a>Linux 高效能運算 (HPC)

- [快速入門範本︰ 微調 SLURM 叢集](https://github.com/Azure/azure-quickstart-templates/tree/master/slurm)（和[部落格文章](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)）
- [快速入門範本︰ 建立 HPC 叢集 Linux 運算節點](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

### <a name="devops-management-and-optimization"></a>Devops、 管理和最佳化

Devops 全球，管理和最佳化是相當以免受到擴充並快速變更，您應該考慮下方的起點的清單。

- [影片︰ Azure 虛擬機器︰ 使用主廚傀儡與管理 Linux Vm Docker](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)

- [完成與 CoreOS 逐漸自動 Kubernetes 叢集部署指南](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
- [Kubernetes 視覺化檢視](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)

- [Jenkins 從屬 Azure 外掛程式](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
- [GitHub repo︰ 外掛程式]，Azure Jenkins 儲存空間](https://github.com/jenkinsci/windows-azure-storage-plugin)

- [第三方︰ Hudson 從屬 Azure 外掛程式](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
- [Azure 外掛程式的協力廠商︰ Hudson 儲存空間](https://github.com/hudson3-plugins/windows-azure-storage-plugin)

- [影片︰ 什麼是主廚，以及如何運作？](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

- [影片︰ 如何使用 Linux Vm Azure 自動化](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)

- [部落格︰ 如何 Powershell DSC Linux 的](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
- [GitHub: Docker 用戶端 DSC](https://github.com/anweiss/DockerClientDSC)

- [Azure packer 外掛程式](https://github.com/msopentech/packer-azure)
