<properties
 pageTitle="設定 Windows RDMA 叢集執行 MPI 應用程式 |Microsoft Azure"
 description="瞭解如何建立 Windows HPC 套件叢集 H16r、 H16mr、 A8 或 A9 Vm 用 Azure RDMA 網路 MPI 應用程式的大小。"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/20/2016"
 ms.author="danlep"/>

# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>設定 Windows RDMA 叢集 HPC pack 執行 MPI 應用程式

設定 Windows RDMA 叢集 Azure [Microsoft HPC 套件](https://technet.microsoft.com/library/cc514029)與[H 數列] 或 [運算密集的數列執行個體](virtual-machines-windows-a8-a9-a10-a11-specs.md)中執行平行郵件傳遞介面 (MPI) 應用程式。 當您設定 RDMA 簡訊、 Windows 伺服器為基礎的 HPC 套件叢集節點時，MPI 應用程式將有效率地通訊，低延遲，高處理量遠端直接記憶體存取 (RDMA) 技術為基礎的 Azure 中的網路上。

如果您想要 Linux Vm 存取 Azure RDMA 網路上執行 MPI 負載時，請參閱[設定 Linux RDMA 叢集執行 MPI 應用程式](virtual-machines-linux-classic-rdma-cluster.md)。


## <a name="hpc-pack-cluster-deployment-options"></a>HPC 套件叢集部署選項
Microsoft HPC 套件已工具提供免費建立 HPC 叢集內部部署或 Azure 執行 Windows 或 Linux HPC 應用程式。 HPC 套件包含 Microsoft 實作的郵件傳遞介面 for Windows (MS MPI) 在執行階段環境。 用於 RDMA 能夠執行支援的 Windows 伺服器作業系統的執行個體，HPC 套件提供有效執行 Windows MPI 應用程式存取 Azure RDMA 網路的選項。 

本文會介紹設定 Winodws RDMA 叢集與 Microsoft HPC 套件的詳細指示兩個案例和連結。 

* 情況 1。 部署運算密集工作者角色執行個體 (PaaS)

* 案例 2。 部署運算節點中運算密集 Vm (IaaS)

若要使用 Windows 的運算密集執行個體的一般必要條件，請參閱[H 數列和運算密集的數列 Vm](virtual-machines-windows-a8-a9-a10-a11-specs.md) 。



## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>情況 1。 部署運算密集工作者角色執行個體 (PaaS)

從現有 HPC 套件叢集，新增額外的計算資源 Azure 工作者角色執行個體 （Azure 節點） 在雲端服務 (PaaS) 中執行。 此功能，也稱為 「 傳送至 Azure 」 從 HPC 套件支援工作者角色執行個體範圍的大小。 Azure 節點時，只需指定其中一個支援 RDMA 的大小。

以下是考量和步驟，以傳送至 RDMA 簡訊 Azure 從現有的執行個體 （通常是內部部署） 叢集。 使用類似的程序將工作者角色執行個體新增到已部署在 Azure VM HPC 套件標頭節點。

>[AZURE.NOTE] 若要傳送至 Azure HPC pack 的教學課程，請參閱[設定混合式叢集 HPC pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)。 請注意下列步驟中，以便於 RDMA 簡訊套用考量 Azure 節點。

![Azure 的傳送][burst]

### <a name="steps"></a>步驟

4. **部署及設定 HPC 套件 2012 R2 標頭節點**

    從[Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=49922)下載最新的 HPC 套件安裝套件。 需求及準備 Azure 突發部署的指示，請參閱[HPC 套件入門指南](https://technet.microsoft.com/library/jj884144.aspx)和[尖峰 Microsoft HPC pack Azure 工作者執行個體](https://technet.microsoft.com/library/gg481749.aspx)。

5. **Azure 訂閱中設定管理憑證**

    設定主節點和 Azure 之間的連線的安全性憑證。 選項與程序，請參閱[設定 Azure 管理憑證 HPC 套件的案例](http://technet.microsoft.com/library/gg481759.aspx)。 測試部署 HPC 套件安裝預設 Microsoft HPC Azure 管理憑證您可以快速地上傳至 Azure 訂閱。

6. **建立新的雲端服務和儲存帳戶**

    若要建立位置可支援 RDMA 的執行個體中的雲端服務及部署儲存帳戶使用 Azure 傳統入口網站。

7. **建立可 Azure 節點的範本**

    使用建立節點範本精靈 HPC 叢集管理員中的商務連絡人。 步驟，請參閱[建立 Azure 節點範本](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ)中 」 步驟來部署 Azure 節點與 Microsoft HPC 套件 」。

    初始測試，建議您在範本中設定手動可用性原則。

8. **新增到叢集節點**

    使用新增節點] 精靈中 HPC 叢集管理員。 如需詳細資訊，請參閱[Windows HPC 叢集新增 Azure 節點](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add)。

    指定時節點的大小，請選取其中一個支援 RDMA 的執行個體大小。
    
    >[AZURE.NOTE]在每個突發 Azure 部署與運算密集執行個體，HPC 套件會自動為 proxy 節點，除了您指定 Azure 工作者角色執行個體部署最小值 （例如 A8) 2 RDMA 能夠執行個體。 Proxy 節點使用核心配置給訂閱，會造成以及 Azure 工作者角色執行個體的費用。

9. **開始 （提供） 節點並將他們線上執行的工作**

    選取節點並使用 [**開始**] 動作 HPC 叢集管理員中的商務連絡人。 佈建完成後，請選取節點，並使用**[連線**的動作 HPC 叢集管理員中的商務連絡人。 節點已經準備好執行的工作。

10. **提交到叢集的工作**

    使用 HPC 套件工作送出工具來執行叢集工作。 請參閱[Microsoft HPC 套件︰ 工作管理](http://technet.microsoft.com/library/jj899585.aspx)。

11. **停止 (deprovision) 節點**

    當您完成時執行的工作時，離線節點，並使用 [**停止**] 動作 HPC 叢集管理員中的商務連絡人。





## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>案例 2。 部署運算節點中運算密集 Vm (IaaS)

在此案例中，您部署 HPC 套件標頭節點並叢集計算節點 Vm Azure 虛擬網路中的 Active Directory 網域上。 HPC 套件提供[Azure Vm 的部署選項](virtual-machines-linux-hpcpack-cluster-options.md)，包括自動的部署指令碼和 Azure 快速入門範本。 作為範例，執行下列步驟和考量事項引導您使用[HPC 套件 IaaS 部署指令碼](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)來自動化大部分的此程序。

![Azure Vm 中的叢集][iaas]



### <a name="steps"></a>步驟

1. **建立叢集標頭節點並計算節點 Vm 用戶端電腦上執行 HPC 套件 IaaS 部署指令碼**

    從[Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=49922)下載 HPC 套件 IaaS 部署指令碼套件。

    若要準備用戶端電腦，請建立指令碼的設定檔，並執行指令碼，請參閱[建立 HPC 套件 IaaS 部署指令碼 HPC 叢集](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)。 
    
    若要部署 RDMA 簡訊運算節點，請注意下列其他考量事項︰
    
    * **虛擬網路**-指定新的虛擬網路中您想要使用的 RDMA 能夠執行個體大小提供的區域。

    * **Windows 伺服器作業系統**-支援 RDMA 連線，指定 Windows Server 2012 R2 或 Windows Server 2012 的作業系統運算節點 Vm。

    * **雲端服務**-我們建議您部署主節點一個雲端服務中的和您在不同的雲端服務中的計算節點。

    * **不對節點大小**-這個案例中，請考慮大小至少 A4 （額外大） 的標頭的節點。

    * **HpcVmDrivers 副檔名**部署指令碼會自動安裝 Azure VM 代理程式和 HpcVmDrivers 副檔名，當您部署 A8 或 A9 計算的是 Windows 伺服器作業系統節點的大小。 HpcVmDrivers 運算節點 Vm 上安裝驅動程式，讓他們可以連線至 RDMA 網路。

    * **叢集網路設定**的部署指令碼] 會自動設定 HPC 套件叢集拓撲 5 （企業網路上的所有節點）。 此拓撲時需要 Vm 中的所有 HPC 套件叢集部署。 不會變更叢集網路拓撲更新版本。

2. **若要執行的工作將運算節點**

    選取節點並使用**[連線**的動作 HPC 叢集管理員中的商務連絡人。 節點已經準備好執行的工作。

3. **提交到叢集的工作**

    連線至主節點提交工作，或將內部部署電腦設定為執行此動作。 資訊，請參閱[送出到 HPC 叢集 Azure 中的工作](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)。

4. **離線節點並停止 （解除配置） 它們**

    當您完成時執行的工作時，需要節點離線 HPC 叢集管理員中的商務連絡人。 然後，使用 Azure 管理工具來關閉。



## <a name="run-mpi-applications-on-the-cluster"></a>在 [叢集執行 MPI 應用程式

### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>範例︰ 執行 mpipingpong HPC 套件叢集上

若要確認 RDMA 能夠執行個體的 HPC 套件部署，請執行 HPC 套件**mpipingpong**命令叢集上。 **mpipingpong**傳送封包，即成對的節點重複計算延遲和處理量度量單位和統計值為 RDMA 啟用應用程式的網路之間的資料。 此範例顯示一般模式執行 MPI 工作 （在此例中， **mpipingpong**） 使用 [叢集**mpiexec** ] 命令。

假設您新增 Azure 節點的 「 突發至 Azure 」 設定 ([案例 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-(PaaS) in this article)。 如果您部署 HPC 套件的 Azure Vm 叢集上時，您會需要修改命令語法指定不同的節點群組，並設定其他環境變數，將網路流量導向 RDMA 網路。


若要執行 mpipingpong 叢集上︰


1. 在主節點或經妥善設定的用戶端電腦上，開啟命令提示字元。

2. 若要估計的 4 節點的 Azure 突發部署中的節點組之間的延遲，輸入下列命令以提交的小型封包大小與大次數執行 mpipingpong 工作︰

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```

    命令傳回的工作會送出的識別碼。

    如果您部署 HPC 套件叢集部署 Azure Vm 指定包含的節點群組計算 Vm 部署單一雲端服務中的節點並修改**mpiexec** ] 命令，如下所示︰

    ```
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```

3. 工作完成時，若要檢視輸出 （在此例中的任務 1 工作的輸出），輸入下列

    ```
    task view <JobID>.1
    ```

    位置&lt; *JobID* &gt;工作送出的識別碼。

    輸出中會包含類似以下的延遲結果。

    ![偵測 （ping） pong 延遲][pingpong1]

4. 若要估計間 Azure 突發節點的處理量，輸入下列命令以提交大的封包大小與小型次數執行**mpipingpong**工作︰

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```

    命令傳回的工作會送出的識別碼。

    Azure Vm 上部署 HPC 套件叢集，修改命令，如在步驟 2 中所述。

5. 工作完成時，若要檢視輸出 （在此例中的任務 1 工作的輸出），輸入以下資料︰

    ```
    task view <JobID>.1
    ```

  輸出中會包含類似以下的效能結果。

  ![偵測 （ping） pong 處理量][pingpong2]


### <a name="mpi-application-considerations"></a>MPI 應用程式考量


以下是考量 HPC pack Azure 中執行 MPI 應用程式。 部分只適用於部署 Azure 節點 （工作者角色執行個體新增 「 突發至 Azure 」 設定）。

* 雲端服務中的 [背景工作角色執行個體會定期 reprovisioned 不另行通知，Azure （例如系統維護]，或執行個體失敗的大小寫）。 如果執行個體 reprovisioned 執行 MPI 工作時，執行個體將會遺失其資料，並返回狀態，它第一次部署時，這可能會造成 MPI 工作失敗。 更多的節點工作時，都需要使用單一 MPI 工作，然後再執行，很可能有一個執行個體會 reprovisioned 工作時執行。 您也必須考量這如果您指定為檔案伺服器的部署中的單一節點。


* 若要執行 MPI 工作 Azure 中，您沒有使用 RDMA 能夠執行個體。 您可以使用任何支援的 HPC 套件的執行個體大小。 不過，RDMA 能夠執行個體被建議執行的是區分延遲和連接節點的網路頻寬較大型 MPI 工作。 如果您使用其他大小以執行延遲和頻寬區分 MPI 工作時，我們建議您執行小型工作，單一任務只幾個節點執行。

* 部署至 Azure 執行個體的應用程式與主旨，相關應用程式授權條款。 請與廠商的雲端中執行的授權或其他限制的任何商業應用程式。 並非所有的廠商提供 pay-as-you-go 授權。


* Azure 執行個體需要進一步的安裝程式，來存取內部部署節點、 共用與授權伺服器。 例如，若要啟用 Azure 節點存取內部部署授權伺服器，您可以設定網站-Azure 虛擬網路。


* Azure 執行個體上執行 MPI 應用程式，登錄每個 MPI 應用程式的 Windows 防火牆的執行個體上執行**hpcfwutil**命令。 這個選項可讓 MPI 通訊的防火牆動態指定的連接埠。

    >[AZURE.NOTE] 尖峰 Azure 部署，您也可以設定防火牆例外狀況命令新增至您的叢集的所有新 Azure 節點上自動執行。 在執行**hpcfwutil**命令，並確認您的應用程式運作後，將命令新增至您的 Azure 節點的啟動指令碼。 如需詳細資訊，請參閱[使用啟動指令碼 Azure 節點](https://technet.microsoft.com/library/jj899632.aspx)。



* HPC 套件使用 CCP_MPI_NETMASK 叢集環境變數指定可接受的地址 MPI 通訊的範圍。 開始 HPC 套件 2012 r2，CCP_MPI_NETMASK 叢集環境變數只會影響網域叢集運算節點之間的 MPI 通訊 (其中一個內部部署或 Azure Vm)。 加入一組中 Azure 設定的節點會忽略該變數。


* 在不同的雲端服務 （例如，在不同的節點範本，或在多個雲端服務中部署 Azure VM 運算節點 Azure 部署突發） 中部署 Azure 執行個體無法執行 MPI 工作。 如果您有多個不同的節點範本入門的 Azure 節點部署時，必須執行只有一組 Azure 節點 MPI 工作。


* 當您新增至叢集 Azure 節點，並使上線時，HPC 工作排程器服務會立即嘗試節點上啟動工作。 如果只在 Azure 上執行的工作量的部分，請確定您更新，或建立工作範本，以定義工作類型可以執行 Azure 上。 例如，若要確保工作送出工作範本只在 Azure 節點上執行，新增節點群組屬性至工作範本並選取 AzureNodes 為必要的值。 若要建立自訂群組，您 Azure 節點，請使用新增 HpcGroup HPC PowerShell 指令程式。


## <a name="next-steps"></a>後續步驟

* 其他方法來使用 HPC 套件，開發 Azure 批次服務上受管理的集區的運算節點 Azure 中執行 MPI 應用程式。 請參閱[使用多個執行個體工作執行 Azure 批次中的郵件傳遞介面 (MPI) 應用程式](../batch/batch-mpi.md)。

* 如果您想要執行 Linux MPI 存取 Azure RDMA 網路的應用程式，請參閱[設定 Linux RDMA 叢集執行 MPI 應用程式](virtual-machines-linux-classic-rdma-cluster.md)。

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/burst.png
[iaas]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/iaas.png
[pingpong1]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/pingpong1.png
[pingpong2]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/pingpong2.png