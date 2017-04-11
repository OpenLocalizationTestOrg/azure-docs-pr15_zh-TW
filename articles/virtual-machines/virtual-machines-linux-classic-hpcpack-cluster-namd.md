<properties
 pageTitle="Microsoft HPC pack 上 Linux Vm NAMD |Microsoft Azure"
 description="部署 Microsoft HPC 套件叢集上 Azure 及 NAMD 模擬 charmrun 執行上執行多個 Linux 運算節點"
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
 ms.date="10/13/2016"
 ms.author="danlep"/>

# <a name="run-namd-with-microsoft-hpc-pack-on-linux-compute-nodes-in-azure"></a>Microsoft HPC pack linux 執行的 NAMD 運算 Azure 中的節點

本文將說明 Azure 虛擬機器上執行 Linux 高效能運算 (HPC) 工作負載的其中一個方法。 這裡，您將 Linux 運算節點的[Microsoft HPC 套件](https://technet.microsoft.com/library/cc514029)叢集 Azure 上設定和執行[NAMD](http://www.ks.uiuc.edu/Research/namd/)模擬以計算，並以視覺化方式呈現大型 biomolecular 系統的結構。  

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

* **NAMD**（適用於 Nanoscale 屬於分子 Dynamics 程式） 是專為高效能模擬的大型 biomolecular 系統包含多達數百萬個原子平行屬於分子 dynamics 套件。 這些系統包括病毒、 儲存格結構及大型蛋白質。 NAMD 縮放下數百個一般模擬的核心並超過 500000 人核心的最大的模擬。

* **Microsoft HPC 套件**提供大型 HPC 以及平行應用程式執行叢集的內部部署電腦或 Azure 虛擬機器中的功能。 原本的 Windows HPC 負載，HPC 套件解決方案開發現在 Linux 上執行 Linux HPC 應用程式的支援計算節點 Vm HPC 套件叢集部署。 簡介資訊，請參閱[開始使用 Linux 運算叢集的節點 HPC 套件 Azure 中](virtual-machines-linux-classic-hpcpack-cluster.md)。

若要執行 Linux HPC 負載 Azure 中其他選項，請參閱[技術資源批次和高效能運算](../batch/batch-hpc-solutions.md)。




## <a name="prerequisites"></a>必要條件

* **使用 Linux HPC 套件叢集運算節點**-部署上使用[Azure 資源管理員範本](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)或[Azure PowerShell 指令碼](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)Azure Linux 運算節點 HPC 套件叢集。 請參閱[開始使用 Linux 運算叢集的節點 HPC 套件 Azure 中](virtual-machines-linux-classic-hpcpack-cluster.md)的必要條件和任一選項的步驟。 如果您選擇 PowerShell 指令碼部署] 選項，請參閱本文結尾的範例檔案中的範例設定檔案。 此檔案設定 Windows Server 2012 R2 的標頭節點及四個大小大型 CentOS 6.6 運算節點組成 Azure 基礎 HPC 套件叢集。 自訂此檔案，視您的環境。


* **NAMD 軟體和教學課程檔**-下載 NAMD 軟體 Linux [NAMD](http://www.ks.uiuc.edu/Research/namd/)網站 （需要註冊）。 本文根據 NAMD 2.10，版，並使用[Linux x86_64 (64 位元 Intel/AMD 與乙太網路)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310)封存。 下載[NAMD 教學課程的檔案](http://www.ks.uiuc.edu/Training/Tutorials/#namd)。 下載.tar 檔案，而您需要 Windows 工具來擷取叢集主節點上的檔案。 若要擷取的檔案，請遵循本文稍後的指示進行。 

* **VMD** （可省略）-若要查看您的 NAMD 工作的結果上下載及安裝屬於分子視覺效果程式[VMD](http://www.ks.uiuc.edu/Research/vmd/)您所選擇的電腦。 目前使用的是 1.9.2。 請參閱下載網站，開始 VMD。  


## <a name="set-up-mutual-trust-between-compute-nodes"></a>設定 [運算節點之間的相互信任
多個 Linux 節點上執行跨節點工作需要信任彼此 （由**rsh**或**ssh**） 節點。 當您建立 HPC 套件叢集與 Microsoft HPC 套件 IaaS 部署指令碼時，指令碼自動設定您指定的系統管理員帳戶的永久相互信任。 對於您建立叢集的網域中的非系統管理員使用者，您必須設定暫時節點之間的相互信任工作配置給他們。 完成工作後，然後 destroy 關聯性。 若要這樣做為每位使用者，提供叢集 HPC 套件用它來建立信任關係 RSA 組。 請依照下列指示進行。

### <a name="generate-an-rsa-key-pair"></a>產生 RSA 組
您可以輕鬆產生 RSA 組，其中包含有公開及私密金鑰，藉由執行 Linux **ssh-keygen**命令。

1.  登入 Linux 電腦。

2.  執行下列命令︰

    ```bash
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE] 若要使用的預設設定，直到已完成] 命令，按下**enter 鍵**。 不要輸入 [複雜密碼。出現提示時輸入密碼，只要按下**enter 鍵**。

    ![產生 RSA 組][keygen]

3.  變更目錄 ~/.ssh 目錄。 私密金鑰會儲存於 id_rsa 和 id_rsa.pub 公開金鑰。

    ![私人及公用的按鍵][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>新增組 HPC 套件叢集
1.  當您部署叢集 (例如，hpc\clusteradmin) 提供[的遠端桌面連線](virtual-machines-windows-connect-logon.md)至主節點 VM 使用網域認證您。 您可以管理叢集從主節點。

2. 使用 Windows Server 的標準程序建立叢集的 Active Directory 網域中的網域的使用者帳戶。 例如，Active Directory 使用者和電腦上使用工具主節點。 本文中的範例，假設您建立名為 hpcuser hpclab 網域 (hpclab\hpcuser) 中的網域使用者。

3. 新增網域使用者到 HPC 套件叢集叢集使用者。 如需相關指示，請參閱[新增或移除叢集使用者](https://technet.microsoft.com/library/ff919330.aspx)。

2.  建立一個名為 C:\cred.xml 檔案，然後將 RSA 索引鍵資料複製到其。 您可以在本文結尾的範例檔案中尋找範例。

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

3.  開啟命令提示字元，然後輸入下列命令以設定 hpclab\hpcuser 帳戶認證資料。 您可以使用**extendeddata**參數傳遞 C:\cred.xml 您建立的檔案的索引鍵的資料的名稱。

    ```command
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    此命令順利完成不輸出。 設定您需要執行工作的使用者帳戶的認證之後, 儲存 cred.xml 檔案在安全的位置，或將其刪除。

5.  如果您在其中一個您 Linux 節點產生 RSA 組，請記得要完成使用後刪除機碼。 如果找到現有 id_rsa 檔案或 id_rsa.pub 檔案，HPC 套件不會不會將相互信任設定。

>[AZURE.IMPORTANT] 不建議您執行 Linux 工作叢集管理員的身分共用叢集，因為 Linux 節點上執行根帳號] 之下，由系統管理員送出工作。 在工作送出非系統管理員使用者的使用者帳戶執行本機 Linux 使用相同的工作的使用者名稱。 在此情況下，HPC 套件設定此 Linux 使用者相互信任的工作分派的所有節點。 您可以設定 Linux 上的使用者手動 Linux 節點之前執行的工作，或 HPC 套件使用者會自動建立時工作會送出]。 如果 HPC 套件建立的使用者，HPC 套件後，會刪除該工作完成。 若要降低的安全性威脅，節點上完成作業之後，會移除按鍵。

## <a name="set-up-a-file-share-for-linux-nodes"></a>設定檔案共用 Linux 節點

現在設定 SMB 檔案共用]，並裝載允許存取常見路徑 NAMD 檔案 Linux 節點的所有 Linux 節點上的 [共用] 資料夾。 以下是裝載在的共用的資料夾的步驟。 共用建議分配，例如 CentOS 6.6 目前不支援的檔案 Azure 服務。 如果您 Linux 節點支援 Azure 檔案共用，請參閱[如何使用與 Linux Azure 檔案儲存空間](../storage/storage-how-to-use-files-linux.md)。 共用與 HPC 套件] 選項的其他檔案，請參閱[開始使用 Linux 運算叢集的節點 HPC 套件 Azure 中](virtual-machines-linux-classic-hpcpack-cluster.md)。

1.  在主節點，建立資料夾，並設定讀/寫權限給所有人共用檔案。 在此範例中， \\ \\CentOS66HN\Namd 是 CentOS66HN 對主節點的主機名稱的所在的資料夾的名稱。

2. 建立一個名為 [共用] 資料夾中的 namd2 子。 在 namd2，建立名為 namdsample 的另一個子資料夾。

3. 解壓縮 NAMD 檔案的資料夾中使用.tar 封存**tar**的 Windows 版本的運作方式的其他 Windows 公用程式。 
    * 展開至 [NAMD tar 封存\\ \\CentOS66HN\Namd\namd2。
    
    * 擷取下教學課程的檔案\\ \\CentOS66HN\Namd\namd2\namdsample。

4. 開啟 Windows PowerShell 視窗，然後執行下列命令以裝載 Linux 節點上的 [共用] 資料夾。

    ```command
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2

    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

第一個命令會建立一個名為 [LinuxNodes] 群組中的所有節點 /namd2 資料夾。 第二個命令裝載 dir_mode 資料夾至共用的資料夾 //CentOS66HN/Namd/namd2 和 file_mode 位元設為 777。 *使用者名稱*和*密碼*命令中應該在使用者的認證。

>[AZURE.NOTE]「\`「 第二個命令中的符號是 PowerShell 逸出符號。 「\`，「 表示 「，」 （逗號字元）] 命令的一部分。


## <a name="create-a-bash-script-to-run-a-namd-job"></a>建立艦隊指令碼執行 NAMD 工作

您 NAMD 工作需要**charmrun**來判斷啟動 NAMD 程序時要使用的節點數目的*節點清單*檔案。 您使用的艦隊指令碼，會產生節點清單檔案，並執行**charmrun**處理此節點清單檔案。 您可以再提交 NAMD 工作中 HPC 叢集管理員呼叫此指令碼。

使用您所選擇的文字編輯器，/namd2 包含檔案的資料夾 NAMD 程式中建立艦隊指令碼，並將其命名為 [hpccharmrun.sh。 概念快速證明，複製結尾的本文所提供的範例 hpccharmrun.sh 指令碼並移至 [[送出 NAMD 工作](#submit-a-namd-job)。

>[AZURE.TIP] 儲存為文字檔案與 Linux 指令碼行尾 （僅限 LF、 不 CR LF）。 如此一來，可確保正常 Linux 節點。

以下是這個艦隊指令碼功能的詳細資料。 

1.  定義一些變數。

    ```bash
    #!/bin/bash

    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    # Charmrun command
    CHARMRUN=${SCRIPT_PATH}/charmrun
    # Argument of ++nodelist
    NODELIST_OPT="++nodelist"
    # Argument of ++p
    NUMPROCESS="+p"
    ```

2.  從環境變數取得節點資訊。 $NODESCORES 儲存從 $CCP_NODES_CORES 分割文字的清單。 $COUNT 是 $NODESCORES 的大小。
    ```
    # Get node information from the environment variables
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    ```    
    
    為 $CCP_NODES_CORES 變數格式如下所示︰

    ```
    <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
    ```

    此變數列出的總數節點、 節點名稱，以及每個節點配置給工作的核心數目。 例如，如果工作需要執行的 10 個核心，價值 $CCP_NODES_CORES 非常類似︰

    ```
    3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
    ```
        
3.  如果沒有設定 $CCP_NODES_CORES 變數，請直接開始**charmrun** 。 （這應該只會直接在您 Linux 節點上執行這個指令碼時。）

    ```
    if [ ${COUNT} -eq 0 ]
    then
        # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
        #echo ${CHARMRUN} $*
        ${CHARMRUN} $*
    ```

4.  或建立**charmrun**的節點清單檔案。

    ```
    else
        # Create the nodelist file
        NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

        # Write the head line
        echo "group main" > ${NODELIST_PATH}

        # Get every node name and number of cores and write into the nodelist file
        I=1
        while [ ${I} -lt ${COUNT} ]
        do
            echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
            let "I=${I}+2"
        done
```
5.  執行**charmrun**節點清單檔案，取得其傳回的狀態，並移除節點清單結尾的檔案。

    ${CCP_NUMCPUS} 是另一個環境變數，設定 HPC 套件標頭節點。 它會儲存到這個工作分派的總核心數目。 我們會用它來指定 charmrun 的程序的數目。

    ```
    # Run charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
    fi

    ```
6.  結束與**charmrun**傳回狀態。

    ```
    exit ${RTNSTS}
    ```



以下是在節點清單檔案] 指令碼會產生的資訊︰

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

例如︰

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```

## <a name="submit-a-namd-job"></a>提交 NAMD 工作

現在您已經準備好提交 NAMD 工作 HPC 叢集管理員中的商務連絡人。

1.  連線至叢集主節點，並開始 HPC 叢集管理員。

2.  在 [**資源管理**] 下，確定 Linux 運算節點處於**線上**狀態。 如果不是加以選取，然後按一下**[連線**。

2.  在 [**工作管理**] 下，按一下 [**新工作**]。

3.  輸入工作，例如*hpccharmrun*的名稱。

    ![新的 HPC 工作][namd_job]

4.  在 [**工作詳細資料**頁面，在**工作資源**] 之下的資源類型選取做為**節點**並設定**最小值**為 3。 我們三個 Linux 節點上執行作業，而且每個節點都有四個核心。

    ![工作資源][job_resources]

5. 在左側導覽畫面中，按一下 [**編輯的工作**，然後按一下 [**新增**]，將任務新增至工作。    


6. 在**工作詳細資料] 與 [I/O 重新導向**頁面上，設定為以下值︰

    * **命令列** -
`/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`

        >[AZURE.TIP] 上述命令列是單一命令，但不含分行符號。 本出現在多行底下**命令列**上。

    * **使用目錄**-/namd2

    * **最小值**-3

    ![工作詳細資料][task_details]

    >[AZURE.NOTE] 您設定的工作目錄以下因為**charmrun**嘗試瀏覽至每個節點的同一個工作目錄。 如果未設定工作目錄，HPC 套件會建立一個 Linux 節點的隨機命名資料夾中啟動命令。 這樣其他節點上下列錯誤︰`/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.`若要避免此問題，指定的可存取所有節點，為工作目錄資料夾路徑。

5.  按一下**[確定]** ，然後按一下 [**送出**執行此工作。

    根據預設，HPC 套件送出您目前的登入的使用者帳戶的工作。 對話方塊可能會提示您輸入使用者名稱和密碼之後您按一下 [**提交**]。

    ![工作的認證][creds]

    在某些情況下 HPC 套件會記住您之前輸入，並不會顯示這個對話方塊的使用者資訊。 若要再次顯示 HPC 套件，輸入下列命令，在命令提示字元，然後送出工作。

    ```command
    hpccred delcreds
    ```

6.  工作需要幾分鐘才能完成。

7.  尋找工作記錄在\\ <headnodeName>\Namd\namd2\namd2_hpccharmrun.log 及輸出檔案\\ <headnodeName>\Namd\namd2\namdsample\1-2-sphere\.

8.  或者，您可以開始 VMD 若要檢視您的工作結果。 視覺化 NAMD 的步驟輸出檔案 （在此例 ubiquitin 蛋白質分子中水球體） 都不在本文的範圍之內。 如需詳細資訊，請參閱[NAMD 教學課程](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf)。

    ![工作結果][vmd_view]

## <a name="sample-files"></a>範例檔案

### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>部署叢集 PowerShell 指令碼範例 XML 設定檔案

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
      <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS66HN</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
    <ServiceName>MyLnxCNService</ServiceName>
     <VMSize>Large</VMSize>
     <NodeCount>4</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
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

### <a name="sample-hpccharmrunsh-script"></a>範例 hpccharmrun.sh 指令碼

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
    #echo ${CHARMRUN} $*
    ${CHARMRUN} $*
else
    # Create the nodelist file
    NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    # Write the head line
    echo "group main" > ${NODELIST_PATH}

    # Get every node name & cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/keygen.png
[keys]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/keys.png
[namd_job]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/namd_job.png
[job_resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/job_resources.png
[creds]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/creds.png
[task_details]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/task_details.png
[vmd_view]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/vmd_view.png
