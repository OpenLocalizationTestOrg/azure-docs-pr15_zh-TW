<properties
 pageTitle="OpenFOAM HPC 套件執行上執行 Linux Vm |Microsoft Azure"
 description="部署上 Azure Microsoft HPC 套件叢集以及 OpenFOAM 工作上執行多個 Linux 運算節點 RDMA 網路上。"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="run-openfoam-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>執行 Microsoft HPC pack 的 OpenFoam Linux RDMA 叢集 Azure 中

本文會說明執行 OpenFoam Azure 虛擬機器中的其中一個方法。 在這裡，您部署 Linux 運算節點的 Microsoft HPC 套件叢集 Azure 及執行[OpenFoam](http://openfoam.com/)工作與 Intel MPI。 您可以使用 RDMA 簡訊 Azure Vm 運算節點，，以便運算節點通訊 Azure RDMA 網路上。 若要執行 OpenFoam Azure 中的其他選項中服務商場，例如 UberCloud 的[OpenFoam 2.3 上 CentOS 6](https://azure.microsoft.com/marketplace/partners/ubercloud/openfoam-v2dot3-centos-v6/)，並執行[Azure](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/)批次包含完全設定商業圖像。 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

OpenFOAM （適用於開啟 [欄位] 作業和操作） 是用於廣泛工程與科學，商業和學年兩種組織中的計算流暢 dynamics 開啟來源 (CFD) 軟體套件。 其包含 meshing，尤其是 snappyHexMesh，複雜的 CAD 幾何，以及前與後置處理平行化 mesher 的工具。 同時，讓使用者可以利用完整電腦硬體自行執行幾乎所有的程序。  

Microsoft HPC 套件提供大型 HPC 以及平行應用程式，包括 MPI 應用程式]，在 Microsoft Azure 虛擬機器叢集上的執行的功能。 HPC 套件也支援執行 Linux HPC Linux 上的應用程式計算 Vm 部署 HPC 套件叢集節點。 使用 Linux 運算節點 HPC pack 簡介資訊，請參閱[開始使用 Linux 運算叢集的節點 HPC 套件 Azure 中](virtual-machines-linux-classic-hpcpack-cluster.md)。

>[AZURE.NOTE] 本文說明如何使用 HPC 套件執行 Linux MPI 工作量。 假設您有一些易用與 Linux 系統管理，以及與執行 MPI 負載 Linux 叢集上。 如果您使用的 MPI 和 OpenFOAM 不同的本文中所顯示的項目版本，您可能修改一些安裝與設定的步驟。 

## <a name="prerequisites"></a>必要條件

*   **使用 RDMA 簡訊 Linux HPC 套件叢集運算節點**-部署 HPC 套件叢集與大小 A8、 A9、 H16r 或 H16rm Linux 運算節點使用[Azure 資源管理員範本](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)或[Azure PowerShell 指令碼](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)。 請參閱[開始使用 Linux 運算叢集的節點 HPC 套件 Azure 中](virtual-machines-linux-classic-hpcpack-cluster.md)的必要條件和任一選項的步驟。 如果您選擇 PowerShell 指令碼部署] 選項，請參閱本文結尾的範例檔案中的範例設定檔案。 使用此設定部署大小 A8 Windows Server 2012 R2 標頭節點及 2 大小 A8 SUSE Linux Enterprise Server 12 運算節點組成 Azure 基礎 HPC 套件叢集。 取代您的訂閱與服務名稱適當的值。 

    **其他須知事項**

    *   Linux RDMA 網路 Azure 中的必要條件，請參閱[H 數列和運算密集的數列 Vm](virtual-machines-windows-a8-a9-a10-a11-specs.md)。

    *   如果您使用 Powershell 指令碼部署] 選項，部署使用 RDMA 網路連線的一個雲端服務中的所有 Linux 運算節點。

    *   部署 Linux 節點之後，連線 SSH 執行任何其他的系統管理工作。 尋找 Azure 入口網站中每個 Linux VM SSH 連線詳細資料。  
        
*   **Intel MPI** -執行 SLES 12 HPC OpenFOAM 運算 Azure 中的節點，您需要安裝 Intel MPI 文件庫 5 runtime [Intel.com 網站](https://software.intel.com/en-us/intel-mpi-library/)。 （以 Intel MPI 5 已預先安裝 CentOS 型 HPC 圖像上。） 在接下來的步驟，如有必要，Intel MPI 上安裝您 Linux 運算節點。 若要準備此步驟中，您在註冊與 Intel 後，請遵循相關的網頁的確認電子郵件中的連結。 然後，將複製的適當的 Intel MPI 版本.tgz 檔案的下載連結。 本文根據 Intel MPI 版本 5.0.3.048。

*   **OpenFOAM 來源套件**-從[OpenFOAM Foundation 網站](http://openfoam.org/download/2-3-1-source/)Linux 適用的下載 OpenFOAM 來源套件軟體。 本文根據 OpenFOAM 2.3.1.tgz 來源套件版本 2.3.1，可供下載。 依照解壓縮並在 Linux 運算節點編譯 OpenFOAM 本文稍後的指示。

*   **EnSight**（選擇性）-若要查看您的 OpenFOAM 模擬的結果，下載並安裝[EnSight](https://www.ceisoftware.com/download/)視覺效果及分析程式。 授權及下載的資訊是 EnSight 網站。


## <a name="set-up-mutual-trust-between-compute-nodes"></a>設定 [運算節點之間的相互信任

多個 Linux 節點上執行跨節點工作需要信任彼此 （由**rsh**或**ssh**） 節點。 當您建立 HPC 套件叢集與 Microsoft HPC 套件 IaaS 部署指令碼時，指令碼自動設定您指定的系統管理員帳戶的永久相互信任。 您建立叢集的網域中的非系統管理員的使用者，您來設定工作配置給他們，暫時節點之間的相互信任並完成工作後 destroy 關聯性。 若要建立的每個使用者的信任，提供 HPC 套件用於信任關係叢集 RSA 組。

### <a name="generate-an-rsa-key-pair"></a>產生 RSA 組

您可以輕鬆產生 RSA 組，其中包含有公開及私密金鑰，藉由執行 Linux **ssh-keygen**命令。

1.  登入 Linux 電腦。

2.  執行下列命令︰

    ```
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE] 若要使用的預設設定，直到已完成] 命令，按下**enter 鍵**。 不要輸入 [複雜密碼。出現提示時輸入密碼，只要按下**enter 鍵**。

    ![產生 RSA 組][keygen]

3.  變更目錄 ~/.ssh 目錄。 私密金鑰會儲存於 id_rsa 和 id_rsa.pub 公開金鑰。

    ![私人及公用的按鍵][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>新增組 HPC 套件叢集
1.  請使用您 HPC 套件的系統管理員帳戶 （您執行的部署指令碼時，您將設定的系統管理員帳戶） 主節點的遠端桌面連線。

2. 使用 Windows Server 的標準程序建立叢集的 Active Directory 網域中的網域的使用者帳戶。 例如，Active Directory 使用者和電腦上使用工具主節點。 本文中的範例，假設您建立名為 hpclab\hpcuser 的網域使用者。

3.  建立一個名為 C:\cred.xml 檔案，然後將 RSA 索引鍵資料複製到其。 範例 cred.xml 檔案位於本文結尾處。

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

4.  開啟命令提示字元，然後輸入下列命令以設定 hpclab\hpcuser 帳戶認證資料。 您可以使用**extendeddata**參數傳遞 C:\cred.xml 檔案所建立的索引鍵的資料的名稱。

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    此命令順利完成不輸出。 設定您需要執行工作的使用者帳戶的認證之後, 儲存 cred.xml 檔案在安全的位置，或將其刪除。

5.  如果您在其中一個您 Linux 節點產生 RSA 組，請記得要完成使用後刪除機碼。 如果 HPC 套件找到現有 id_rsa 檔案或 id_rsa.pub 檔案，它不會設定相互信任。

>[AZURE.IMPORTANT] 不建議您執行 Linux 工作叢集管理員的身分共用叢集，因為 Linux 節點上執行根帳號] 之下，由系統管理員送出工作。 不過，在工作送出非系統管理員使用者的使用者帳戶執行本機 Linux 使用相同的工作的使用者名稱。 在此情況下，HPC 套件設定此 Linux 使用者相互信任跨工作配置節點。 您可以設定 Linux 上的使用者手動 Linux 節點之前執行的工作，或 HPC 套件使用者會自動建立時工作會送出]。 如果 HPC 套件建立的使用者，HPC 套件後，會刪除該工作完成。 若要降低安全性威脅，HPC 套件移除作業完成後的按鍵。

## <a name="set-up-a-file-share-for-linux-nodes"></a>設定檔案共用 Linux 節點

現在設定標準的 SMB 共用資料夾上主節點。 若要允許存取應用程式檔案常見路徑 Linux 節點，裝載 Linux 節點上的 [共用] 資料夾。 如果您想，您可以使用另一個共用選項，例如 [Azure 檔案共用-建議，為多個案例-或 NFS 共用的檔案。 請參閱共用資訊和詳細的步驟，以[開始使用 Linux 運算叢集的節點 HPC 套件 Azure 中](virtual-machines-linux-classic-hpcpack-cluster.md)的檔案。

1.  在主節點，建立資料夾，並設定讀/寫權限給所有人共用檔案。 例如，在為共用 C:\OpenFOAM \\ \\SUSE12RDMA HN\OpenFOAM。 以下， *SUSE12RDMA HN*是主節點的主機名稱。

2.  開啟 Windows PowerShell 視窗，然後執行下列命令︰

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

    clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

第一個命令會建立一個名為 [LinuxNodes] 群組中的所有節點 /openfoam 資料夾。 第二個命令與 dir_mode Linux 節點上裝載共用的資料夾 //SUSE12RDMA-HN/OpenFOAM 和 file_mode 位元設為 777。 *使用者名稱*和*密碼*命令中應該在使用者的認證。

>[AZURE.NOTE]「\`「 第二個命令中的符號是 PowerShell 逸出符號。 「\`，「 表示 「，」 （逗號字元）] 命令的一部分。

## <a name="install-mpi-and-openfoam"></a>安裝 MPI 和 OpenFOAM

若要執行以 MPI 工作的 OpenFOAM RDMA 網路上，您需要進行編譯 OpenFOAM Intel MPI 文件庫。 

首先，請執行幾個**clusrun**命令 （如果尚未安裝） 安裝 Intel MPI 文件庫及 OpenFOAM 您 Linux 節點。 使用主節點共用先前設定為共用 Linux 節點間的安裝檔案。

>[AZURE.IMPORTANT]這些安裝及編譯的步驟是範例。 您必須知道 Linux 系統管理，以確保正確安裝相依編譯器和文件庫。 您可能需要修改某些環境變數或 Intel MPI OpenFOAM 您版本的其他設定。 如需詳細資訊，請參閱您的環境[Intel MPI 文件庫的 Linux 安裝指南](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html)和[OpenFOAM 來源套件安裝](http://openfoam.org/download/2-3-1-source/)。


### <a name="install-intel-mpi"></a>安裝 Intel MPI

儲存下載的安裝套件的 Intel MPI (在此範例中 l_mpi_p_5.0.3.048.tgz) C:\OpenFoam 在以便在 Linux 節點可以從 /openfoam 存取此檔案。 然後再執行**clusrun**所有 Linux 節點上安裝 Intel MPI 文件庫。

1.  下列命令複製安裝套件，然後將它解壓縮至每個節點 /opt/intel。

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel

    clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/

    clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
    ```

2.  若要自動安裝 Intel MPI 文件庫，請使用 silent.cfg 檔案。 您可以在本文結尾的範例檔案中尋找範例。 將此檔案中共用的資料夾 /openfoam。 如需 silent.cfg 檔案的詳細資訊，請參閱[Intel Linux 安裝指南無訊息安裝的 MPI 文件庫](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html#silentinstall)。

    >[AZURE.TIP]請確定您將 silent.cfg 檔案儲存為 Linux 的文字檔案行尾 （僅限 LF、 不 CR LF）。 此步驟可確保正常 Linux 節點。

3.  無訊息模式安裝 Intel MPI 文件庫。
 
    ```
    clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
    ```
    
### <a name="configure-mpi"></a>設定 MPI

以進行測試，您應該新增至每個 Linux 節點 /etc/security/limits.conf 的下列行︰


    clusrun /nodegroup:LinuxNodes echo "*               hard    memlock         unlimited" `>`> /etc/security/limits.conf
    clusrun /nodegroup:LinuxNodes echo "*               soft    memlock         unlimited" `>`> /etc/security/limits.conf


更新 limits.conf 檔案之後，請重新啟動 Linux 節點。 例如，使用下列**clusrun**命令︰

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

重新啟動後，請確定 [共用] 資料夾已裝載為 /openfoam。

### <a name="compile-and-install-openfoam"></a>編譯並安裝 OpenFOAM

儲存下載的安裝套件 OpenFOAM 來源套件 (OpenFOAM-在此範例中的 2.3.1.tgz) 至 C:\OpenFoam 在 Linux 節點可以從 /openfoam 存取此檔案。 執行在所有 Linux 節點編譯 OpenFOAM **clusrun**命令。


1.  建立資料夾 /opt/OpenFOAM 每個 Linux 節點、 到這個資料夾中，複製來源套件及那里擷取它。

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM

    clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/
    
    clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
    ```

2.  若要編譯 OpenFOAM 與 Intel MPI 文件庫，請先設定環境變數 Intel MPI 和 OpenFOAM 兩者均適用。 若要設定變數使用呼叫 settings.sh 艦隊指令碼。 您可以在本文結尾的範例檔案中尋找範例。 在共用的資料夾 /openfoam 將此檔案 （Linux 行尾儲存）。 此檔案也包含您稍後使用執行 OpenFOAM 工作 MPI 和 OpenFOAM 執行階段設定。

3. 安裝編譯 OpenFOAM 所需的相依套件。 根據您 Linux 通訊群組，您可能必須將存放庫。 執行**clusrun**命令如下︰

    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
    
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
    
    必要時，若要執行的命令，以確認，他們也能正常執行的每個 Linux 節點 SSH。

4.  執行下列命令以編譯 OpenFOAM。 需要一些時間來完成編譯程序，並產生大量的記錄資訊至標準輸出，因此使用**/ 交錯格式**選項來顯示輸出交錯格式。

    ```
    clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
    ```
    
    >[AZURE.NOTE]「\`」 命令中的符號是 PowerShell 逸出符號。 「\`& 」 表示 「 & 」 命令的一部分。

## <a name="prepare-to-run-an-openfoam-job"></a>若要執行 OpenFOAM 工作準備

現在準備好執行兩個 Linux 節點上呼叫 sloshingTank3D，也就是其中一個 OpenFoam 範例，MPI 工作。 

### <a name="set-up-the-runtime-environment"></a>設定執行階段環境

在執行階段環境 MPI 和 OpenFOAM 上設定 Linux 節點，請在 Windows PowerShell 的視窗中執行下列命令。 （此命令僅適用於 SUSE Linux。）

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### <a name="prepare-sample-data"></a>準備範例資料

使用您先前設定共用檔案 Linux 節點 （裝載為 /openfoam） 之間的標頭節點共用。

1.  其中一個您 Linux SSH 運算節點。

2.  如果您還沒有尚未這麼做，請執行下列命令以設定 OpenFOAM 執行階段環境。

    ```
    $ source /openfoam/settings.sh
    ```
    
3.  將 sloshingTank3D 範例複製到 [共用] 資料夾，然後瀏覽至該。

    ```
    $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/

    $ cd /openfoam/sloshingTank3D
    ```

4.  當您使用的預設參數，此範例時，可能需要數十分鐘，若要執行，因此您可能會想要修改某些參數，使其執行速度更快。 修改時間步驟變數 deltaT 和 writeInterval 系統/controlDict 檔案中的是一個簡單的選擇。 此檔案會儲存所有相關的時間和讀取和寫入解決方案資料控制項的輸入的資料。 例如，您可能會變更為 0.5 從 0.05 deltaT 的值和值為 0.5 從 0.05 writeInterval。

    ![修改步驟變數][step_variables]

5.  系統/decomposeParDict 檔案中，指定所要的值的變數。 此範例使用兩個 Linux 節點每個 8 個核心，因此設定 numberOfSubdomains 16 和 n 至 hierarchicalCoeffs 的 (1 1 16)，這表示執行 OpenFOAM 平行 16 的程序。 如需詳細資訊，請參閱[OpenFOAM 使用者指南︰ 3.4 執行的應用程式，平行](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4)。

    ![分解程序][decompose]

6.  從 sloshingTank3D 目錄準備範例資料，請執行下列命令。

    ```
    $ . $WM_PROJECT_DIR/bin/tools/RunFunctions

    $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict

    $ runApplication blockMesh

    $ cp 0/alpha.water.org 0/alpha.water

    $ runApplication setFields  
    ```
    
7.  在主節點，您應該會看到範例資料檔案會複製到 C:\OpenFoam\sloshingTank3D。 （C:\OpenFoam 是在 [共用] 資料夾）。

    ![在資料檔案][data_files]

### <a name="host-file-for-mpirun"></a>Host （主機） mpirun 檔案

在此步驟中，您可以建立**mpirun**命令使用的主檔案 （運算節點清單）。

1.  在其中一個 Linux 節點，建立名為 hostfile /openfoam，底下，讓所有 Linux 節點 /openfoam/hostfile 可以連線到此檔案的檔案。

2.  將此檔案中撰寫您 Linux 節點的名稱。 在此範例中，檔案會包含下列名稱︰
    
    ```       
    SUSE12RDMA-LN1
    SUSE12RDMA-LN2
    ```
    
    >[AZURE.TIP]您也可以在 C:\OpenFoam\hostfile 建立此檔案，在。 如果您選擇這個選項，請將其儲存為 Linux 的文字檔案行尾 （僅限 LF、 不 CR LF）。 如此一來，可確保正常 Linux 節點。

    **被指令碼包裝紙**

    如果您有許多 Linux 節點，並想讓您在只有某些上執行的工作，這不是建議使用固定 host （主機） 檔案，因為您不知道哪些節點將會配置給您的工作。 在此情況下，撰寫艦隊指令碼包裝函式**mpirun**自動建立主檔案。 您可以尋找稱為 hpcimpirun.sh 結尾的本文中，範例艦隊指令碼包裝紙，並將其儲存為 /openfoam/hpcimpirun.sh。 這個範例指令碼執行下列動作︰

    1.  若要透過 RDMA 網路執行 MPI 工作設定**mpirun**，以及一些加法命令參數的環境變數。 在此情況下，它會將下列變數︰

        *   I_MPI_FABRICS = shm:dapl
        *   I_MPI_DAPL_PROVIDER = ofa-v2-ib0
        *   I_MPI_DYNAMIC_CONNECTION = 0

    2.  建立主檔案根據環境變數 $CCP_NODES_CORES，啟動工作時，會將 HPC 標頭節點的設定。

        $CCP_NODES_CORES 的格式遵循此模式︰

        ```
        <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
        ```

        位置

        * `<Number of nodes>`-配置此工作給的節點數目。  
        
        * `<Name of node_n_...>`-配置此工作給每個節點的名稱。
        
        * `<Cores of node_n_...>`-核心配置此工作給的節點數目。

        例如，如果工作需要執行兩個節點，$CCP_NODES_CORES 是類似
        
        ```
        2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
        ```
        
    3.  呼叫 [ **mpirun** ] 命令，並將兩個參數附加至命令列。

        * `--hostfile <hostfilepath>: <hostfilepath>`-指令碼會建立主檔案的路徑

        * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}`-環境變數設定 HPC 套件主節點，儲存到這個工作分派的總核心數目。 在此情況下，指定**mpirun**的程序的號碼。


## <a name="submit-an-openfoam-job"></a>提交 OpenFOAM 工作

現在您可以送出工作 HPC 叢集管理員中的商務連絡人。 您需要在命令列中的指令碼 hpcimpirun.sh 傳遞的部分工作的工作。

1. 連線至叢集主節點，並開始 HPC 叢集管理員。

2. **在 [資源管理] 中**，確定 Linux 運算節點的**線上**狀態。 如果不是加以選取，然後按一下**[連線**。

3.  在 [**工作管理**] 下，按一下 [**新工作**]。

4.  輸入工作，例如_sloshingTank3D_的名稱。

    ![工作詳細資料][job_details]

5.  在**工作資源**，選擇做為 「 節點 」 的資源類型，然後設定為 2 的最小值]。 這項設定，每一個都有八個核心在此範例中的兩個 Linux 節點上執行的工作。

    ![工作資源][job_resources]

6. 在左側導覽畫面中，按一下 [**編輯的工作**，然後按一下 [**新增**]，將任務新增至工作。 新增四個工作，使用下列命令列及設定的工作。

    >[AZURE.NOTE]執行`source /openfoam/settings.sh`設定 OpenFOAM 和 MPI 執行階段環境中，讓每個下列工作呼叫它之前 OpenFOAM] 命令。

    *   **任務 1**。 執行**decomposePar**產生的同時執行**interDyMFoam**資料檔案。
    
        *   分派給任務的一個節點

        *   **命令列** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
    
        *   **使用目錄**-/ openfoam/sloshingTank3D
        
        請參閱下圖。 同樣地，您設定剩餘任務。

        ![任務 1 詳細資料][task_details1]

    *   **任務 2**。 若要計算樣本同時執行**interDyMFoam** 。

        *   分派給任務的兩個節點

        *   **命令列** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`

        *   **使用目錄**-/ openfoam/sloshingTank3D

    *   **任務 3**。 執行**reconstructPar**若要將一組合併的時間目錄，從每個 processor_N_ 目錄集。

        *   分派給任務的一個節點

        *   **命令列** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`

        *   **使用目錄**-/ openfoam/sloshingTank3D

    *   **任務 4**。 若要將 OpenFOAM 結果檔案轉換成 EnSight 格式，並將 EnSight 檔案放在目錄中名為 「 Ensight 的大小寫的目錄中的同時執行**foamToEnsight** 。

        *   分派給任務的兩個節點

        *   **命令列** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`

        *   **使用目錄**-/ openfoam/sloshingTank3D

6.  將這些工作，以遞增順序任務相依性。

    ![任務相依性][task_dependencies]

7.  按一下 [**送出**執行此工作]。

    根據預設，HPC 套件送出您目前的登入的使用者帳戶的工作。 按一下 [**送出**後，您可能會看到對話方塊中，提示您輸入使用者名稱和密碼。

    ![工作的認證][creds]

    在某些情況下 HPC 套件會記住您之前輸入，並不會顯示這個對話方塊的使用者資訊。 若要再次顯示 HPC 套件，輸入下列命令，在命令提示字元，然後送出工作。

    ```
    hpccred delcreds
    ```

8.  工作會從數十分鐘數個小時，根據您所設定的範例參數。 在熱力圖，您會看到 Linux 節點上執行的工作。 

    ![熱力圖][heat_map]

    在每個節點，啟動八個程序。

    ![Linux 程序][linux_processes]

9.  完成工作之後，請 C:\OpenFoam\sloshingTank3D，與在 C:\OpenFoam 記錄檔] 下的資料夾中尋找工作結果。


## <a name="view-results-in-ensight"></a>檢視結果 EnSight

您也可以使用[EnSight](https://www.ceisoftware.com/)視覺化及分析 OpenFOAM 工作的結果。 如需關於的視覺效果和 EnSight 中的動畫，請參閱此[視訊指南](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html)。

1.  您在安裝 EnSight 之後，請啟動它。

2.  開啟 C:\OpenFoam\sloshingTank3D\EnSight\sloshingTank3D.case。

    您會看到油箱檢視器中。

    ![在 [EnSight 槽][tank]

3.  從**internalMesh**，建立**Isosurface** ，然後選擇 [變數**alpha_water**。

    ![建立 isosurface][isosurface]

4.  設定**Isosurface_part**在上一個步驟中建立的色彩。 例如，將它設定為水藍色。

    ![編輯 isosurface 色彩][isosurface_color]

5.  **Iso 大量**從**牆**選取建立**牆面**[**組件**] 面板中，然後按一下 [工具列中的 [ **Isosurfaces** ] 按鈕。

6.  在對話方塊中，選取**Isovolume** **類型**， **Isovolume 範圍**的最小值設為 0.5。 若要建立 isovolume，按一下 [**建立與所選的部分**。

7.  設定**Iso_volume_part**在上一個步驟中建立的色彩。 例如，將它設定為 [深層水藍色。

8.  設定**牆**的色彩。 例如，將它設定為透明白色。

9. 現在，按一下 [**播放**] 來查看模擬的結果。

    ![油箱結果][tank_result]

## <a name="sample-files"></a>範例檔案

### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>部署叢集 PowerShell 指令碼範例 XML 設定檔案

 ```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>suse12rdmavnet</VNetName>
    <SubnetName>SUSE12RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>SUSE12RDMA-HN</VMName>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <NodeCount>2</NodeCount>
      <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

### <a name="sample-credxml-file"></a>範例 cred.xml 檔案

```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```
### <a name="sample-silentcfg-file-to-install-mpi"></a>若要安裝 MPI 範例 silent.cfg 檔案

```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no


```

### <a name="sample-settingssh-script"></a>範例 settings.sh 指令碼

```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```


###<a name="sample-hpcimpirunsh-script"></a>範例 hpcimpirun.sh 指令碼

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
    ${MPIRUN} $*
else
    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

    # Get every node name and write into the hostfile file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the mpirun with hostfile arg
    ${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/keygen.png
[keys]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/keys.png
[step_variables]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/step_variables.png
[data_files]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/data_files.png
[decompose]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/decompose.png
[job_details]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/job_details.png
[job_resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/job_resources.png
[task_details1]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/task_details1.png
[task_dependencies]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/task_dependencies.png
[creds]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/creds.png
[heat_map]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/heat_map.png
[tank]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/tank.png
[tank_result]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/tank_result.png
[isosurface]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/isosurface.png
[isosurface_color]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/isosurface_color.png
[linux_processes]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/linux_processes.png
