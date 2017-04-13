<properties
 pageTitle="Azure VM 中建立 HPC 套件標頭節點 |Microsoft Azure"
 description="瞭解如何使用 Azure 入口網站與資源管理員部署模型中 Azure VM 建立 Microsoft HPC 套件的標頭節點。"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/17/2016"
 ms.author="danlep"/>

# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>服務商場圖像 Azure VM 中建立 HPC 套件叢集主節點


若要建立 HPC 叢集主節點使用[Microsoft HPC 套件虛擬機器圖像](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)從 Azure Marketplace 和 Azure 入口網站。 此 HPC 套件 VM 圖像根據 Windows Server 2012 R2 資料中心 HPC Pack 2012 R2 更新 3 已預先安裝。 使用此主節點證明 HPC 套件 Azure 中的概念部署。 您可以新增至執行 HPC 負載叢集運算節點。



>[AZURE.TIP]若要部署完整的 HPC 套件叢集，包括標頭節點以及運算節點的 Azure 中，我們建議您使用自動化的方法。 選項包括[HPC 套件 IaaS 部署指令碼](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)和[Windows 工作負載的 HPC 套件叢集](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)資源管理員範本。 其他的範本，請參閱[HPC 套件叢集 Azure 中的選項](virtual-machines-windows-hpcpack-cluster-options.md)。 


## <a name="planning-considerations"></a>規劃考量

下圖所示，您會部署 HPC 套件主節點中 Azure 虛擬網路中的 Active Directory 網域。

![HPC 套件標頭節點][headnode]

* **Active Directory 網域**為 HPC 套件 VM 上開始 HPC 服務之前，必須在 Azure Active Directory 網域加入標頭節點。 在本文中的概念部署證明所示，您可以將升級的 VM 您建立主節點網域控制站之前啟動 HPC 服務。 部署的另一個網域控制站和 Azure 加入標頭節點 VM 中的樹系為另一個選項。

* **Azure 虛擬網路**-當您使用的資源管理員部署模型部署主節點時，您指定或建立 Azure 虛擬網路。 如果您要加入至現有的 Active Directory 網域的標頭節點，您可以使用虛擬網路。 您也需要稍後新增到叢集運算節點 Vm。

    
## <a name="steps-to-create-the-head-node"></a>若要建立主節點的步驟

以下是使用資源管理員部署模型建立 HPC 套件標頭節點 Azure VM 使用 Azure 入口網站的步驟。 


1. 如果您想要建立新的 Active Directory 樹系 Azure 中不同的網域控制站 Vm，其中一個選項就是使用[資源管理員範本](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/)。 若是概念部署簡單證明，則可以略過此步驟，並設定網域控制站的標頭節點 VM 本身。 這個選項，本文稍後的描述。
    
2. 在[Windows Server 2012 R2 頁面上的 HPC 套件 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) Azure Marketplace 中，按一下 [**建立虛擬機器**]。 

3. 在入口網站中，**在 Windows Server 2012 R2 HPC 套件 2012 R2**在頁面上，選取**資源管理員**部署模型，然後按一下 [**建立**]。

    ![HPC 套件圖像][marketplace]

4. 若要設定的設定，並建立 VM 使用入口網站。 如果您是新 Azure，請遵循教學課程中[建立 Windows 虛擬機器中 Azure 入口網站](virtual-machines-windows-hero-tutorial.md)。 概念部署證明，您通常可以接受預設值或建議的設定。

    >[AZURE.NOTE]如果您想要加入至現有的 Active Directory 網域 Azure 中的主節點，請確定您在建立 VM 時，指定該網域的虛擬網路。
       
4. 建立 VM 後，VM 正在執行遠端桌面[連線至 VM](virtual-machines-windows-connect-logon.md) 。 

5. 加入 VM 現有的網域樹系，或建立網域樹系 VM 本身上。

    * 如果您在 Azure 虛擬網路中使用現有的網域樹系建立 VM，加入至樹系 VM 使用標準的伺服器管理員] 或 [Windows PowerShell 工具。 然後重新啟動。

    * 如果您 VM 在中建立新的虛擬網路 （不含現有的網域樹系），然後將 VM 升級為網域控制站。 使用安裝並設定 Active Directory 網域服務角色在標準的步驟。 如需詳細步驟，請參閱[安裝新 Windows Server 2012 Active Directory 樹系](https://technet.microsoft.com/library/jj574166.aspx)。

5. VM 正在執行，並在 Active Directory 樹系加入之後，啟動 HPC 套件服務，如下所示︰

    。 連線至主節點 VM 使用網域的帳戶的本機管理員群組的成員。 例如，使用您在建立主節點 VM 時，您將設定的系統管理員帳戶。

    b。 預設主節點設定，請以系統管理員身分啟動 Windows PowerShell，並輸入以下︰

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    它可能需要幾分鐘，若要開始 HPC 套件服務。

    使用其他的標頭節點設定選項，請輸入`get-help HPCHNPrepare.ps1`。


## <a name="next-steps"></a>後續步驟

* 您現在可以使用 HPC 套件叢集的主節點。 例如，啟動 HPC 叢集管理員，並完成[部署待辦事項清單](https://technet.microsoft.com/library/jj884141.aspx)。
* 如果您想要增加叢集計算容量視雲端服務中新增[Azure 突發節點](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md)。 

* 請嘗試執行測試工作量叢集上。 例如，請參閱[快速入門指南](https://technet.microsoft.com/library/jj884144)HPC 套件。

<!--Image references-->
[headnode]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/marketplace.png
