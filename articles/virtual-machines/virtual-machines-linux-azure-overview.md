 <properties
   pageTitle="Azure 和 Linux |Microsoft Azure"
   description="說明與 Linux 虛擬機器 Azure 計算、 儲存及網路服務。"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines-linux"
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/14/2016"
   ms.author="v-livech"/>

# <a name="azure-and-linux"></a>Azure 和 Linux

Microsoft Azure 是成長的整合式公用集合雲端服務，包括分析，虛擬機器資料庫，行動網路、 儲存及網頁，最理想的主機服務方案。  Microsoft Azure 提供可讓您只是每年支付您所使用的調整電腦平台，當您想要-不需投資在內部部署的硬體。  Azure 是好的當您準備能夠拓展您的方案，並查看任何縮放您需要服務客戶的需求。

如果您熟悉 Amazon 的各種功能 AWS，您可以檢查 Azure 與 AWS[定義對應文件](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/)。


## <a name="regions"></a>區域
Microsoft Azure 資源分散到世界各地的多個地理區域。  「 地區 」 代表單一的地理區域中的多個資料中心。  2016 年 1 月 1 日到包括︰ 在 [美國，歐洲 6 亞太地區，大陸 china （中國） 的第 2 和 3 在印度 2 8。  如果您想所有 Azure 區域的完整清單，我們維護的現有和新清單本項區域。

- [Azure 區域](https://azure.microsoft.com/regions/)

## <a name="availability"></a>顯示狀態
為了讓您的部署合格我們 99.95 VM 服務等級協定，您需要部署兩個或多個 Vm 執行您的工作量內可用性設定。 這樣就能確定您的 Vm 是分散到多個錯誤網域，在我們的資料中心，以及部署至不同的維護 windows 主機。 完整的[Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/)說明保證可使用 Azure 整個。

## <a name="azure-virtual-machines--instances"></a>Azure 虛擬機器與執行個體
Microsoft Azure 支援執行數個常用 Linux 分配，提供由和維護協力廠商的數字。  您會看到紅色角色企業、 等 CentOS、 Debian、 Ubuntu、 CoreOS、 RancherOS、 FreeBSD，在 Azure Marketplace 散佈。 我們正在使用各種 Linux 社群至[Azure 背書 Linux Distros](virtual-machines-linux-endorsed-distros.md) ] 清單中新增更多的類別。

如果您慣用的 Linux distro 所選擇的不是目前出現在圖庫中，您就可以 」 將您自己 Linux 」 VM 藉由[建立並上傳 Linux VHD Azure 中](virtual-machines-linux-create-upload-generic.md)。

Azure 虛擬機器可讓您部署大範圍的運算解決方案敏捷式的方式。 您可以佈署幾乎任何工作量及任何語言幾乎在任何作業系統上的 Windows，Linux，或自訂建立一個其中一個合作夥伴我們持續增加清單。 仍看不到您要尋找什麼？  別擔心，您也可以從內部部署來開啟您自己的影像。

## <a name="vm-sizes"></a>VM 大小
當您部署 VM Azure 中的時，您要選取其中一個大小適合您的工作量我們系列中的虛擬記憶體大小。 大小也會影響虛擬機器處理 power、 記憶體和儲存空間容量。 是向您收費根據 VM 執行，並使用其配置的資源的時間量。 [虛擬機器大小](virtual-machines-linux-sizes.md)的完整清單。

以下是一些基本指導方針選取其中一個 （A、 D、 DS、 G 和 GS） 我們數列的虛擬記憶體大小。

* A 數列 Vm 是我們價格的精簡的工作量及開發/測試方案入門 Vm 的值。 他們廣泛用於在所有區域內和可以連線，使用所有的標準資源虛擬機器使用。
* A 數列大小 (A8-A11) 是特殊的計算密集設定適用於高效能運算叢集應用程式。
* D 數列 Vm 被用來執行要求較高的運算能力和暫時磁碟效能的應用程式。 D 數列 Vm 的暫存的磁碟，提供更快速的處理器，較高的記憶體-核心比例，solid-state 的磁碟機 (SSD)。
* Dv2 系列，我們 D 數列的最新版本，功能更強大的 CPU。 Dv2 數列 CPU 是關於 35%的速度比 D 數列 CPU。 為基礎的最新的產生 2.4 GHz Intel Xeon® E5 2673 v3 (Haskell) 處理器，並利用 Intel 快速增量技術 2.0，可以移最 3.2 GHz。 Dv2 數列具有相同的記憶體和磁碟設定為 [D 系列。
* G 數列 Vm 提供最多記憶體，並執行含有 Intel Xeon E5 V3 家庭處理器主機上。

附註︰ DS 數列和 GS 數列 Vm 存取進階版儲存空間，我們 SSD 備份高效能、 低延遲 I/O 大量工作負載的儲存空間。 在特定區域中有進階版儲存空間。 如需詳細資訊，請參閱︰

- [Azure 虛擬機器工作負載的進階版儲存空間︰ 高效能儲存空間](../storage/storage-premium-storage.md)

## <a name="automation"></a>自動化
若要達到適當 DevOps 文化特性，所有的基礎結構必須程式碼。  當所有基礎結構放在 [程式碼可輕鬆地重新建立 （鳳凰伺服器）。  Azure 適用於所有主要的自動化工具 Ansible、 主廚、 SaltStack 及傀儡等。  Azure 也會有自己的自動化工具︰

- [Azure 範本](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

- [Azure VMAccess](virtual-machines-linux-using-vmaccess-extension.md)

Azure 推出支援[雲端初始化](http://cloud-init.io/)，跨支援的大部分 Linux Distros。  目前 Canonical 的 Ubuntu Vm 部署與雲端初始化預設為啟用。  RedHats RHEL CentOS，與 Fedora 支援雲端初始化，不過 Azure 圖像維護 RedHat 沒有雲端初始化安裝。  若要使用雲端初始化 RedHat 家庭 OS 上，您必須使用雲端初始化安裝建立的自訂的圖像。

- [Azure Linux Vm 上使用雲端初始化](virtual-machines-linux-using-cloud-init.md)

## <a name="quotas"></a>配額
每個 Azure 訂閱都有預設配額限制在可能會影響大量 Vm 專案的部署的位置。 每個訂閱基礎目前限制為每個地區 20 Vm。  可能引發配額限制，以納入要求限制增加的支援票證。  如需配額限制的詳細資訊︰

- [Azure 訂閱服務限制](../azure-subscription-service-limits.md)


## <a name="partners"></a>合作夥伴

Microsoft 密切與我們的合作夥伴，以確保圖像會更新，且最佳化 Azure 執行階段。  如需詳細資訊，在我們的合作夥伴核取下列其服務商場頁面。

- [在 Azure 背書散佈 Linux](virtual-machines-linux-endorsed-distros.md)

Redhat- [Azure Marketplace-RedHat 企業 Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)

標準- [Azure Marketplace-Ubuntu 伺服器 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)

Debian- [Azure Marketplace-Debian 8 」 潔 」](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)

FreeBSD- [Azure Marketplace-FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)

CoreOS- [Azure Marketplace-CoreOS （穩定）](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)

RancherOS- [Azure Marketplace-RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)

Bitnami- [Bitnami 文件庫的 Azure](https://azure.bitnami.com/)

Mesosphere- [Azure Marketplace-Mesosphere DC/OS 上 Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)

Docker- [Azure Marketplace-Docker 廣域 Azure 容器服務](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)

Jenkins- [Azure Marketplace-CloudBees Jenkins 平台](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)


## <a name="getting-setup-on-azure"></a>快速入門 Azure 設定
若要開始使用 Azure 必須 Azure 帳戶、 Azure CLI 安裝及一組 SSH 公開及私密金鑰金鑰。

## <a name="sign-up-for-an-account"></a>登入的帳戶
使用 Azure 雲端的第一個步驟是註冊 Azure 帳戶。  移至 [ [Azure 帳戶註冊](https://azure.microsoft.com/pricing/free-trial/)] 頁面，即可開始使用。

## <a name="install-the-cli"></a>安裝 CLI
使用您的新 Azure 帳戶，您可以使用快速入門立即 Azure 入口網站中，這是網頁式管理員控制台。  若要管理 Azure 雲端透過命令列，請您安裝`azure-cli`。  在 Mac 或 Linux 工作站上安裝[Azure CLI ](../xplat-cli-install.md)。

## <a name="create-an-ssh-key-pair"></a>建立 SSH 組
現在您擁有 Azure 帳戶、 Azure web 入口網站和 Azure CLI。  下一步是建立 SSH 組用來 SSH 到 Linux 不使用密碼。  若要啟用密碼的情況登入和較高的安全性[建立 SSH 上的按鍵 Linux 和 Mac](virtual-machines-linux-mac-create-ssh-keys.md) 。

## <a name="getting-started-with-linux-on-microsoft-azure"></a>在 Microsoft Azure Linux 快速入門
與您 Azure 帳戶設定、 Azure CLI 安裝 SSH 鍵建立您現在可以準備開始建立 Azure 雲端基礎結構。  第一個任務是，建立幾個 Vm。

## <a name="create-a-vm-using-the-cli"></a>建立使用 CLI VM
建立使用 CLI Linux VM 是的快速部署 VM，而不需離開終端機您正在處理。  您可以指定在入口網站上的所有項目是透過的命令列標幟或切換提供使用。  

- [建立使用 CLI Linux VM](virtual-machines-linux-quick-create-cli.md)

## <a name="create-a-vm-in-the-portal"></a>在管理入口網站建立 VM
Azure web 入口網站中建立 Linux VM 方法輕鬆點，然後按一下，前往部署各種不同的選項。  而不是使用的命令列標幟或參數，您就可以檢視的各種選項和設定好 web 版面配置。  可透過命令列介面的所有項目也會出現在入口網站的。

- [建立 Linux VM 使用入口網站](virtual-machines-linux-quick-create-portal.md)

## <a name="login-using-ssh-without-a-password"></a>使用 SSH 不使用密碼登入
Azure 立即執行 VM 並準備好要登入。  使用密碼以登入透過 SSH 是安全且費時。  使用 SSH 鍵是最安全的方法，同時登入最快速的方法。  當您建立您 Linux VM 透過入口網站或 CLI 時，您會有兩個驗證選項。  如果您選擇的 SSH 的密碼，Azure 就會設定為允許透過密碼登入 VM。  如果您選擇使用 SSH 公開金鑰，Azure 就會設定為僅允許透過 SSH 索引鍵的登入 VM，並停用密碼登入。 若要安全只允許 SSH 金鑰登入您 Linux VM 使用 SSH 公用索引鍵] 選項中的入口網站或 CLI VM 建立期間。

- [設定 SSHD 來停用您 Linux VM SSH 密碼](virtual-machines-linux-mac-disable-ssh-password-usage.md)

## <a name="related-azure-components"></a>相關的 Azure 元件

## <a name="storage"></a>儲存空間

- [Microsoft Azure 儲存體簡介](../storage/storage-introduction.md)

- [使用 azure cli Linux vm 新增磁碟](virtual-machines-linux-add-disk.md)

- [如何將資料磁碟附加到 Linux VM Azure 入口網站中](virtual-machines-linux-attach-disk-portal.md)

## <a name="networking"></a>網路

- [虛擬網路概觀](../virtual-network/virtual-networks-overview.md)

- [Azure 中的 IP 位址](../virtual-network/virtual-network-ip-addresses-overview-arm.md)

- [Azure 中開啟 Linux vm 的連接埠](virtual-machines-linux-nsg-quickstart.md)

- [建立在 Azure 入口網站的完整網域名稱](virtual-machines-linux-portal-create-fqdn.md)


## <a name="containers"></a>容器

- [虛擬機器和 Azure 中的容器](virtual-machines-linux-containers.md)

- [Azure 容器服務簡介](../container-service/container-service-intro.md)

- [部署 Azure 容器服務叢集](../container-service/container-service-deployment.md)

## <a name="next-steps"></a>後續步驟

現在，您可以概略瞭解 Linux Azure 上。  下一步是深入瞭解並建立幾個 Vm ！

- [Azure 使用入口網站上建立 Linux VM](virtual-machines-linux-quick-create-portal.md)

- [建立使用 CLI Linux VM Azure](virtual-machines-linux-quick-create-cli.md)
