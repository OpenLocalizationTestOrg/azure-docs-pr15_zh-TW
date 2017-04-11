<properties
 pageTitle="若要執行 MPI 應用程式的 Linux RDMA 叢集 |Microsoft Azure"
 description="建立 Linux 叢集的大小 H16r、 H16mr、 A8 或 A9 Vm 用 Azure RDMA 網路 MPI 應用程式"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>設定 Linux RDMA 叢集執行 MPI 應用程式


瞭解如何設定在[H 數列] 或 [運算密集的數列 Vm](virtual-machines-linux-a8-a9-a10-a11-specs.md)與 Azure Linux RDMA 叢集執行平行郵件傳遞介面 (MPI) 應用程式。 本文提供步驟準備叢集上執行 Intel MPI Linux HPC 圖像。 然後，您部署叢集的 Vm 使用這個影像，其中一項 RDMA 簡訊 Azure VM 大小 （目前 H16r、 H16mr、 A8 或 A9）。 若要執行的有效率地通訊低的延遲 MPI 應用程式使用叢集，高處理量網路基礎遠端直接記憶體存取 (RDMA) 技術。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="cluster-deployment-options"></a>叢集部署選項

以下是您可以用來建立 Linux RDMA 叢集，不論是否有工作排程器方法。


* **Azure CLI 指令碼**-為稍後顯示在本文中，使用[Azure 命令列介面](../xplat-cli-install.md)(CLI) 叢集 RDMA 簡訊 Vm 的部署的指令碼。 服務管理模式中的 CLI 建立叢集節點循序傳統部署模型中，讓部署許多運算節點可能需要幾分鐘。 若要啟用 RDMA 網路連線，當您使用的傳統部署模型，部署 Vm 相同的雲端服務中。

* **Azure 資源管理員範本**-您也可以使用資源管理員部署模型部署 RDMA 簡訊 Vm 叢集 RDMA 網路連線。 您可以[建立自己的範本](../resource-group-authoring-templates.md)，或存回[Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)由 Microsoft 或社群部署您要的方案的範本。 資源管理員範本可以提供部署 Linux 叢集快速和可靠的方法。 若要啟用 RDMA 網路連線，當您使用的資源管理員部署模型，部署 Vm 在相同的可用性設定。

* **HPC 套件**-Azure 中建立 Microsoft HPC 套件叢集，並新增 RDMA 能夠執行支援的 Linux 分配存取 RDMA 網路的運算節點。 請參閱[開始使用 Linux 運算叢集的節點 HPC 套件 Azure 中](virtual-machines-linux-classic-hpcpack-cluster.md)。

## <a name="sample-deployment-steps-in-classic-model"></a>傳統的模型中的範例部署步驟

下列步驟說明如何使用 Azure CLI 部署 SUSE Linux Enterprise Server (SLES) 12 SP1 HPC VM 從 Azure Marketplace、 自訂，並建立自訂的 VM 圖像。 然後，指令碼叢集 RDMA 簡訊 Vm 的部署使用圖像。 

>[AZURE.TIP]部署叢集 RDMA 簡訊 Vm Azure Marketplace 在其他支援 HPC 影像為基礎的使用類似的步驟。 一些步驟可能稍有不同，如下所述。 例如，Intel MPI 包含而且中只有部分這些圖像的設定。 然後，如果部署，而不是 SLES 12 SP1 HPC VM SLES 12 HPC VM，您需要更新 RDMA 驅動程式。 如需詳細資訊，請參閱[關於 A8、 A9、 A10 及 A11 運算密集執行個體](virtual-machines-linux-a8-a9-a10-a11-specs.md#rdma-driver-updates-for-sles-12)。


### <a name="prerequisites"></a>必要條件

*   **用戶端電腦**-您需要 Mac、 Linux 或 Windows 型的用戶端電腦與 Azure 進行通訊。 這些步驟是假設您使用的 Linux 用戶端。

*   **Azure 訂閱**-如果您沒有訂閱，您可以建立的[免費帳戶](https://azure.microsoft.com/free/)在幾分鐘。 如需較大的叢集，請考慮 pay-as-you-go 訂閱或其他購買選項。 

*   **VM 大小可用性**-目前下列執行個體大小是 RDMA 簡訊︰ H16r、 H16mr、 A8 及 A9。 檢查 Azure 區域中的可用性[區域所提供的產品](https://azure.microsoft.com/regions/services/)。 

*   **核心配額**-您可能需要增加部署的運算密集 Vm 叢集核心的配額。 例如，您需要至少 128 個核心，如果您想要部署 8 A9 Vm 本文中所示。 您的訂閱也可能會限制您可以將部署中的特定 VM 大小系列，包括 H 數列的核心數目。 若要要求配額增加，免費的 [[開啟線上客戶支援要求](../azure-supportability/how-to-create-azure-support-request.md)。 

*   **Azure CLI** - [安裝](../xplat-cli-install.md)Azure CLI 和用戶端電腦上的 [[連線至 Azure 訂閱](../xplat-cli-connect.md)。


### <a name="step-1-provision-a-sles-12-sp1-hpc-vm"></a>步驟 1。 佈建 SLES 12 SP1 HPC VM

後登入與 Azure CLI Azure 執行`azure config list`確認輸出顯示服務管理模式。 如果尚未選取，請執行此命令以設定模式︰


    azure config mode asm


輸入下列命令以清單授權您使用的所有訂閱︰


    azure account list

目前作用中的訂閱會用來識別`Current`設定為`true`。 如果此訂閱不是您要用來建立叢集，請將適當的訂閱 Id 設定為作用中的訂閱︰

    azure account set <subscription-Id>

若要查看可公開使用 SLES 12 SP1 HPC 圖像 Azure 中的，執行類似下列命令，假設您命令介面的環境支援**grep**:


    azure vm image list | grep "suse.*hpc"

現在佈建 SLES 12 SP1 HPC 圖像 RDMA 簡訊 VM 執行類似下列命令︰

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

位置

* 大小 (在此範例中 A9) 是其中一個 RDMA 簡訊 VM 大小。

* 外部 SSH 連接埠號碼 (在此範例中，這是 SSH 預設 22) 為任何有效的連接埠號碼。 內部 SSH 連接埠號碼會設定為 22。

* 指定位置 Azure 區域中建立新的雲端服務。 指定您選擇的虛擬記憶體大小提供的位置。

* SLES 12 SP1 圖像目前可以名稱`b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824`或`b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824`SUSE 優先順序支援 （套用額外費用）。

### <a name="step-2-customize-the-vm"></a>步驟 2。 自訂 VM

VM 完成佈建使用 VM 的外部 IP 位址 （或 DNS 名稱） 的 vm SSH 外部連接埠設定，您的數字，並加以自訂之後。 連線的詳細資訊，請參閱[如何登入虛擬機器執行 Linux](virtual-machines-linux-mac-create-ssh-keys.md)。 除非根目錄存取，才能完成步驟，請在 VM 上設定的使用者執行命令。

>[AZURE.IMPORTANT]Microsoft Azure 不提供 Linux Vm 至根存取。 若要取得系統管理存取權的使用者的 vm 連線時，執行命令使用`sudo`。

* **更新**-使用**zypper**安裝更新。 您也可能會想要安裝 NFS 公用程式。 

    >[AZURE.IMPORTANT]在 SLES 12 SP1 HPC VM，我們建議您不要套用核心更新，可能會導致問題的 Linux RDMA 驅動程式。

* **Intel MPI** -Intel MPI SLES 12 SP1 HPC VM 上安裝完成，執行下列命令︰

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm

* **鎖定記憶體**-的 MPI 代碼的 RDMA 鎖定可用的記憶體、 新增或變更 /etc/security/limits.conf 檔案中的下列設定。 （您需要編輯此檔案的根權限）。 

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

    >[AZURE.NOTE]供測試之用，您也可以設定 memlock 限制。 例如︰ `<User or group name>    hard    memlock unlimited`。 如需詳細資訊，請參閱[最佳已知設定鎖定記憶體大小的方法](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size)。

* **SLES Vm SSH 快速鍵**-產生 SSH 鍵以執行 MPI 工作時，建立您的使用者帳戶之間運算節點的信任 SLES 叢集。 （如果您部署 CentOS 型 HPC VM，請不要依照此步驟。 請參閱本文稍後的相關指示，設定叢集節點間的 passwordless SSH 信任您擷取圖像，並部署叢集後）。 

    執行下列命令以建立 SSH 索引鍵。 當系統提示您輸入時，按 Enter，不要設定複雜密碼的預設位置會產生的金鑰。

        ssh-keygen

    將公開金鑰附加為已知公開金鑰 authorized_keys 檔案。

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    在 ~/.ssh 目錄中，編輯或建立 「 設定 」 檔案。 提供您打算使用 Azure (在此範例中 10.32.0.0/16) 中的私人網路的 IP 位址範圍︰

        host 10.32.0.*
        StrictHostKeyChecking no

    或者，] 清單中的每個 VM 叢集中的私人網路 IP 位址，如下所示︰

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

    >[AZURE.NOTE]設定`StrictHostKeyChecking no`未指定在特定的 IP 位址或範圍時，可以建立潛在安全性風險。

* **應用程式**的安裝任何應用程式，您需要在此 VM 或之前擷取圖像，請執行其他自訂作業。

### <a name="step-3-capture-the-image"></a>步驟 3。 擷取圖像

若要擷取圖像，第一次 Linux VM 中執行下列命令。 這個命令 deprovisions VM，但會維持使用者帳戶和您設定的 SSH 鍵。

```
sudo waagent -deprovision
```

用戶端電腦，然後執行下列 Azure CLI 命令擷取圖像。 如需詳細資訊，請參閱[如何擷取傳統 Linux 虛擬機器為圖像](virtual-machines-linux-classic-capture-image.md)。  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

執行下列命令之後，您用於擷取 VM 圖像並且 VM 會刪除。 現在您已準備好部署叢集自訂圖像。

### <a name="step-4-deploy-a-cluster-with-the-image"></a>步驟 4。 部署叢集使用圖像

修改下列艦隊指令碼，使用適當的值，為您的環境，然後從用戶端電腦上執行。 Azure 部署傳統部署模型中，依序 Vm，因為需要幾分鐘，才能部署 8 A9 Vm 建議在這個指令碼。

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>CentOS HPC 叢集的考量

如果您想要設定叢集的 HPC 根據 CentOS 型 HPC 中的圖像，而不是 SLES 12 Azure Marketplace 的其中一個，請遵循前一節中的一般步驟。 當您佈建並設定 VM 時，請注意差異如下︰

1. Intel MPI 已安裝 VM 佈建後，從 CentOS 型 HPC 的圖像。 

2. 鎖定記憶體設定已新增的 VM /etc/security/limits.conf 檔案中。

2. 不會產生 SSH 擷取您佈建的 VM 上索引鍵。 部署 cluser 之後我們建議使用者驗證設定。 請參閱下一節。  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>設定叢集 passwordless SSH 信任

在 CentOS 型 HPC 叢集，有兩種方法可以建立運算節點之間的信任︰ 主機驗證及使用者驗證。 Host （主機） 驗證超出本文的範圍，以及通常必須完成透過副檔名指令碼部署期間。 使用者驗證方便部署之後建立信任，且需要產生與共用 SSH 鍵之間運算節點的叢集。 這個方法通常稱為 passwordless SSH 登入並時，需要執行 MPI 工作。 

從社群提供的範例指令碼會提供[GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh)啟用 CentOS 型 HPC 叢集上的輕鬆使用者驗證。 下載並使用這個指令碼使用下列步驟。 您也可以修改這個指令碼，或使用其他任何方法來建立叢集運算節點之間的 passwordless SSH 驗證。

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh
    
若要執行指令碼，您必須知道您子網路 IP 位址的前置字元。 上一個叢集節點執行下列命令，以取得前置字元。 您的輸出看起來應該像 10.1.3.5，和前置詞是 10.1.3 部分。

    ifconfig eth0 | grep -w inet | awk '{print $2}'

立即執行使用三個參數的指令碼︰ 常見的使用者名稱，在運算節點、 運算節點，並從上一個命令傳回的子網路前置詞該使用者的常見密碼。


    ./user_authentication.sh <myusername> <mypassword> 10.1.3

這個指令碼執行下列動作︰

* 名為.ssh，所需之 passwordless 登入主機節點上建立目錄。 
* 建立設定檔.ssh 目錄中的指示 passwordless 叢集允許從任何節點的登入登入。 
* 建立包含節點名稱及叢集中的所有節點的節點 IP 位址的檔案。 以供日後參考執行指令碼之後，會保留這些檔案。 
* 建立的私人及公用組包括主機節點的每個叢集節點與建立 authorized_keys 檔案中的項目。

>[AZURE.WARNING]執行這個指令碼，可以建立潛在安全性風險。 請確定不散發 ~/.ssh 公開金鑰資訊。


## <a name="configure-intel-mpi"></a>設定 Intel MPI

若要在 Azure Linux RDMA 執行 MPI 應用程式，您需要設定特定 Intel MPI 某些環境變數。 以下是範例艦隊指令碼來設定執行應用程式所需的變數。 視您安裝的 Intel MPI mpivars.sh 變更的路徑。

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

Host （主機） 檔案的格式如下所示。 在您的叢集新增一行的每一個節點。 指定從 VNet 私用的 IP 位址，定義不 DNS 名稱。 例如，兩個主機 10.32.0.1 和 10.32.0.2 的 IP 位址的檔案包含下列內容︰

```
10.32.0.1:16
10.32.0.2:16
```

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>執行基本的兩個節點叢集 MPI

如果您尚未這麼做，第一次設定環境 Intel MPI。 

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-a-simple-mpi-command"></a>執行簡單的 MPI 命令

在其中一個運算節點顯示 MPI 的安裝正確，而且能以無聲之間至少兩個運算節點執行簡單的 MPI] 命令。 下列**mpirun**命令會執行 [**主機名稱**] 命令，在兩個節點。

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
您的輸出應該清單的名稱，做為輸入您傳送的所有節點`-hosts`。 例如，兩個節點**mpirun** ] 命令會傳回輸出類似下列︰

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>執行 MPI 基準

下列 Intel MPI 命令執行 pingpong 基準以確認叢集設定與 RDMA 網路連線。

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

在工作叢集上兩個節點，您應該會看到類似以下的輸出。 在 Azure RDMA 的網路上的預期延遲或低於 3 毫秒的郵件大小 512 個位元組。

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## <a name="next-steps"></a>後續步驟

* 嘗試部署並上 Linux 叢集執行您 Linux MPI 應用程式。

* 請參閱在 Intel MPI 的指導方針的[Intel MPI 文件庫文件](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/)。

* 若要建立 Intel Lustre 叢集使用 CentOS 型 HPC 圖像，請嘗試[快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos)。 如需詳細資訊，請參閱此[部落格文章](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/)。
