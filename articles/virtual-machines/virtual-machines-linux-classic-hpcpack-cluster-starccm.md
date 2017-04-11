<properties
 pageTitle="執行星星-CCM + 上 Linux Vm HPC pack |Microsoft Azure"
 description="部署上 Azure Microsoft HPC 套件叢集和執行星星-CCM + 工作上多個 Linux 運算節點 RDMA 網路上。"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="xpillons"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="09/13/2016"
 ms.author="xpillons"/>

# <a name="run-star-ccm-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>執行星星-CCM + Microsoft HPC pack 上 Linux RDMA 叢集 Azure 中
本文將示範如何部署 Azure 及執行 Microsoft HPC 套件叢集[CD adapco 星形-CCM +](http://www.cd-adapco.com/products/star-ccm%C2%AE)在多個互相 InfiniBand 的 Linux 運算節點的工作。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Microsoft HPC 套件提供功能以執行各種大型 HPC 和平行應用程式，包括 MPI 應用程式]，在 Microsoft Azure 虛擬機器叢集上。 HPC 套件也支援 Linux 運算節點 Vm HPC 套件叢集中部署執行 Linux HPC 應用程式。 使用 Linux 的簡介運算 HPC pack 節點，請參閱[開始使用 Linux 運算叢集的節點 HPC 套件 Azure 中](virtual-machines-linux-classic-hpcpack-cluster.md)。

## <a name="set-up-an-hpc-pack-cluster"></a>設定 HPC 套件叢集
從[下載中心](https://www.microsoft.com/en-us/download/details.aspx?id=44949)下載 HPC 套件 IaaS 部署指令碼，並展開本機。

Azure PowerShell 是的必要條件。 如果您的本機電腦上未設定 PowerShell，請閱讀文件[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。

在撰寫時，從 Azure Marketplace （其中包含 InfiniBand 驅動程式的 Azure） Linux 圖像是用於 SLES 12 CentOS 6.5 與 CentOS 7.1。 本文根據 SLES 12 的使用方式。 若要擷取的支援服務商場 HPC 所有 Linux 圖像的名稱，您可以執行下列 PowerShell 命令︰

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

輸出會列出這些影像可位置和影像名稱供稍後使用部署範本中的 (**ImageName**)。

部署叢集之前，必須先建立 HPC 套件部署範本檔案。 因為我們目標小型叢集，將網域控制站主節點，並將其主控本機的 SQL 資料庫。

下列範本部署主節點，建立名為**MyCluster.xml**，XML 檔案和**SubscriptionId**、 **StorageAccount**、**位置**、 **VMName**，以及**ServiceName**的值取代為您的帳戶。

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

啟動不對節點建立以執行提高權限的命令提示字元中的 PowerShell 命令︰

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

20 至 30 分鐘之後，應該可以主節點。 您可以連線至該從 Azure 入口網站的虛擬機器的 [**連線**] 圖示，即可。

最後，您可能必須修正 DNS 轉寄站。 若要這麼做，啟動 [DNS 管理員]。

1.  以滑鼠右鍵按一下 [DNS 管理員] 中的伺服器名稱，選取 [**內容**]，然後按一下 [**轉寄站**] 索引標籤。

2.  按一下 [若要移除任何轉寄站，[**編輯**] 按鈕，然後按一下**[確定]**。

3.  請確定選取 [**如果有任何轉寄站，不可提供使用根目錄提示**] 核取方塊，，然後按一下**[確定]**。

## <a name="set-up-linux-compute-nodes"></a>設定 Linux 運算節點
您可以使用您用來建立主節點的相同部署範本，以部署 Linux 運算節點。

複製主節點，從您的本機檔案**MyCluster.xml** ，並以您想要部署的節點數目更新**NodeCount**標籤 (< = 20)。 請小心有足夠的可用核心您 Azure 配額] 中，因為每個 A9 執行個體所要使用的 16 的核心，在您的訂閱。 如果您想要使用更多 Vm 中相同的預算，您可以使用，而不是 A9 A8 執行個體 （8 個核心）。

在主節點，複製 HPC 套件 IaaS 部署指令碼。

提高權限的命令提示字元中，執行下列 PowerShell 的 Azure 命令︰

1.  執行**新增 AzureAccount**連線到您 Azure 的訂閱。

2.  如果您有多個訂閱，請執行**取得 AzureSubscription**他們。

3.  設定預設訂閱執行**選取 AzureSubscription SubscriptionName xxxx-預設**] 命令。

4.  執行**.\New-HPCIaaSCluster.ps1-ConfigFile MyCluster.xml**開始部署 Linux 運算節點。

    ![在 [巨集指令的不對節點部署][hndeploy]

開啟 [HPC 套件叢集管理員工具。 幾分鐘之後，Linux 運算節點定期會出現在清單中的叢集運算節點。 使用傳統的部署模式時，會依序建立 IaaS Vm。 因此，如果重要的節點數目，使其所有部署可能需要大量時間。

![Linux 節點中 HPC 套件叢集管理員][clustermanager]

所有的節點叢集，都上手，還有其他的基礎結構設定，讓。

## <a name="set-up-an-azure-file-share-for-windows-and-linux-nodes"></a>設定 Windows 和 Linux 節點 Azure 檔案共用
您可以使用 Azure 檔案服務來儲存指令碼、 應用程式套件和資料檔案。 Azure 檔案做為常設存放區提供 CIFS 功能上方 Azure Blob 儲存體。 請注意，這不是最可調整的解決方案，但它的最簡單的項目，而不需要專用的 Vm。

建立 Azure 檔案共用，請參閱[開始使用 windows Azure 檔案儲存空間](..\storage\storage-dotnet-how-to-use-files.md)的指示執行。

保留您儲存的帳戶的名稱為**saname**、 檔案共用名稱為**共用名稱**，以及儲存帳戶金鑰] 以**sakey**。

### <a name="mount-the-azure-file-share-on-the-head-node"></a>在主節點裝載 Azure 檔案共用
開啟提高權限的命令提示字元]，然後執行下列命令以在本機電腦保存庫中儲存認證︰

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

然後，若要裝載 Azure 檔案共用，請執行︰

```
    net use Z: \\<saname>.file.core.windows.net\<sharename> /persistent:yes
```

### <a name="mount-the-azure-file-share-on-linux-compute-nodes"></a>在 Linux 運算節點裝載 Azure 檔案共用
一個實用的工具 HPC 套件隨附是 clusrun 工具。 若要在一組運算節點的同時執行相同的命令，您可以使用這個命令列工具。 在我們的情況下，它用來裝載 Azure 檔案共用，並保存不受重新啟動電腦。
在提高權限命令提示字元，請執行下列命令。

若要建立裝載目錄︰

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

若要裝載 Azure 檔案共用︰

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

若要保存裝載共用︰

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## <a name="install-star-ccm"></a>安裝星星-CCM +
Azure VM 執行個體 A8 和 A9 提供 InfiniBand 支援及 RDMA 功能。 啟用這些功能的核心驅動程式，可用於 Windows Server 2012 R2 與 SUSE 12、 CentOS 6.5 中 Azure Marketplace 的 CentOS 7.1 圖像。 Microsoft MPI 和 Intel MPI （版本 5.x） 是兩個 MPI 文件庫中 Azure 支援這些驅動程式。

CD adapco 星星-CCM + 放開 11.x，並且稍後會搭配使用 Intel MPI 版本 5.x，因此包含 Azure InfiniBand 支援。

取得 Linux64 星星-CCM + 封裝[成光碟 adapco 入口網站](https://steve.cd-adapco.com)。 在此例中，我們會使用版本 11.02.010 混合的精確度。

在主節點，在**/hpcdata** Azure 檔案共用]，建立名為 [與下列內容**setupstarccm.sh** shell 指令碼。 設定 [星形各個運算節點上執行這個指令碼-CCM + 本機。

#### <a name="sample-setupstarcmsh-script"></a>範例 setupstarcm.sh 指令碼
```
    #!/bin/bash
    # setupstarcm.sh to set up STAR-CCM+ locally

    # Create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # Copy the STAR-CCM package from the file share to the local directory
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # Extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # Start a silent installation of STAR-CCM without the FLEXlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # Update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
現在若要設定星星-CCM + 所有您 linux 運算節點，開啟提高權限的命令提示字元並執行下列命令︰

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

執行命令時，您可以監視 CPU 使用率使用熱力圖的叢集管理員。 幾分鐘之後，所有的節點應該會正確設定。

## <a name="run-star-ccm-jobs"></a>執行星星-CCM + 工作
HPC 套件用於其工作排程器功能才能執行星星-CCM + 工作。 若要這麼做，我們需要的支援的幾個的指令碼，可用來啟動工作，並執行星星-CCM +。 輸入的資料保留 Azure 檔案共用上第一個簡單的。

使用下列 PowerShell 指令碼佇列星形-CCM + 工作。 採用三個引數︰

*  模型名稱

*  若要使用的節點數目

*  若要使用的每個節點的核心的數量

因為星星-CCM + 可以填滿的記憶體頻寬，最好是使用較少的核心每運算節點，並新增新的節點。 每個節點的核心確切數目而定的處理器系列和相互連線速度。

節點配置專用的工作，而且不能與其他工作共用。 工作無法直接開始以 MPI 工作。 **Runstarccm.sh**命令介面指令碼就會開始 MPI 啟動器。

輸入的模型和**runstarccm.sh**指令碼會儲存在先前裝載**/hpcdata**共用。

記錄檔命名的工作識別碼，並且儲存在**/hpcdata 共用**，以及星形-CCM + 輸出檔案。


#### <a name="sample-submitstarccmjobps1-script"></a>範例 SubmitStarccmJob.ps1 指令碼
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # Create a new job; this will give us the job ID that's used to identify the name of the uploaded package in Azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # Submit the job    
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
使用您慣用的星形取代**runner.java** -CCM + Java 模型啟動器和記錄程式碼。

#### <a name="sample-runstarccmsh-script"></a>範例 runstarccm.sh 指令碼
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set the mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
        let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run STAR-CCM with the hostfile argument
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
        -power -podkey "<yourkey>" -rsh ssh \
        -mpi intel -fabric UDAPL -cpubind bandwidth,v \
        -mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
        -batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

在我們的測試，我們會使用 Power 指定授權權杖。 取得該憑證，您必須設定為**$CDLMD_LICENSE_FILE**環境變數**1999@flex.cd-adapco.com**和**-podkey** ] 選項中的命令列鍵。

某些初始化之後，指令碼起- **$CCP_NODES_CORES**環境變數 HPC 套件設定，來建立 MPI 啟動器使用 hostfile 節點的清單。 此 hostfile 將包含運算節點名稱的工作，每行名稱所使用的清單。

**$CCP_NODES_CORES**的格式遵循此模式︰

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

位置︰

* `<Number of nodes>`是分派給此工作的節點數目。

* `<Name of node_n_...>`是配置此工作給每個節點的名稱。

* `<Cores of node_n_...>`為核心配置此工作給的節點數目。

也計算核心 (**$NBCORES**) 的數量根據節點 (**$NBNODES**) 數] 及 [核心每個 （參數**$NBCORESPERNODE**為提供） 的節點數目。

對於 MPI 的選項，Intel MPI Azure 上所使用的是︰

*   `-mpi intel`若要指定 Intel MPI。

*   `-fabric UDAPL`若要使用 Azure InfiniBand 動詞。

*   `-cpubind bandwidth,v`若要最佳化星形 MPI 頻寬-CCM +。

*   `-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0"`若要使用 Azure InfiniBand Intel MPI，若要設定所需的每個節點的核心數目。

*   `-batch`若要開始星星-CCM + 批次模式中沒有使用者介面。


最後，若要開始工作，請確定您的節點會執行線上叢集管理員中的文字。 然後如果出現 PowerShell 命令提示字元中，請執行此︰

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## <a name="stop-nodes"></a>停止節點
稍後您完成您的測試之後，您可以使用下列 HPC 套件 PowerShell 命令停止和開始節點︰

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## <a name="next-steps"></a>後續步驟
請嘗試執行其他 Linux 的工作量。 例如，請參閱︰

* [Microsoft HPC pack linux 執行的 NAMD 運算 Azure 中的節點](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [執行 Microsoft HPC pack 的 OpenFOAM Linux RDMA 叢集 Azure 中](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)


<!--Image references-->
[hndeploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/hndeploy.png
[clustermanager]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/ClusterManager.png
