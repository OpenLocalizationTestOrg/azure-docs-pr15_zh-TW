<properties
   pageTitle="MATLAB 叢集虛擬機器上 |Microsoft Azure"
   description="若要建立執行您運算密集平行 MATLAB 工作負載的 MATLAB 分散運算伺服器叢集使用 Microsoft Azure 虛擬機器"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mscurrell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Windows"
   ms.workload="infrastructure-services"
   ms.date="05/09/2016"
   ms.author="markscu"/>

# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Azure Vm 上建立 MATLAB 分散運算伺服器叢集 

使用 Microsoft Azure 虛擬機器建立執行您運算密集平行 MATLAB 工作負載的一或多個 MATLAB 分散運算伺服器叢集。 在使用為基礎的圖像，並使用 Azure 快速入門範本或 Azure PowerShell 指令碼 （提供[GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)） 來部署及管理叢集 VM 安裝 MATLAB 分散運算伺服器軟體。 部署之後，連線到叢集執行您的工作量。 

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>關於 MATLAB 和 MATLAB 分散運算伺服器 

[MATLAB](http://www.mathworks.com/products/matlab/)平台最適合解決工程和科學記號的問題。 使用大型模擬與資料處理工作的 MATLAB 使用者可以使用 MathWorks 平行運算產品設定其運算密集負載充分利用運算叢集及格線服務。 [平行運算工具箱](http://www.mathworks.com/products/parallel-computing/)可讓 MATLAB 使用者平行應用程式並利用多核心處理器，Gpu，計算叢集。 [MATLAB 分散式運算伺服器](http://www.mathworks.com/products/distriben/)可讓 MATLAB 使用者使用叢集中的許多電腦。 


藉由使用 Azure 虛擬機器，您可以建立的所有相同可用送出平行公司內部部署叢集，例如互動式工作、 工作、 獨立工作，以及通訊的工作機制 MATLAB 分散式運算伺服器叢集。 使用 Azure 搭配 MATLAB 平台有許多優點，比較佈建和使用傳統內部部署的硬體︰ 虛擬機器範圍大小，建立叢集視需要，您是每年支付只計算資源您使用，還可將測試在模型。  

## <a name="prerequisites"></a>必要條件

* **用戶端電腦**-您需要 Windows 型的用戶端電腦部署之後 Azure 與 MATLAB 分散運算伺服器叢集通訊。 

* **PowerShell 的 azure** -請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)在用戶端電腦上加以安裝。 

* **Azure 訂閱**-如果您沒有訂閱，您可以建立的[免費帳戶](https://azure.microsoft.com/free/)在幾分鐘。 如需較大的叢集，請考慮 pay-as-you-go 訂閱或其他購買選項。 

* **核心配額**-您可能需要增加部署大型叢集或多個 MATLAB 分散式運算伺服器叢集核心配額。 若要增加配額] 中，[開啟線上客戶支援要求](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)免費。 

* **MATLAB、 平行運算工具箱] 和 [MATLAB 分散式運算伺服器授權**-指令碼假設[MathWorks 主控授權管理員](http://www.mathworks.com/products/parallel-computing/mathworks-hosted-license-manager/)用於所有授權。  

* **MATLAB 分散運算伺服器軟體**-將會安裝，則會使用基底 VM 圖像作為叢集 Vm VM 上。 


## <a name="high-level-steps"></a>高等級的步驟

若要使用您的 MATLAB 分散運算伺服器叢集 Azure 虛擬機器，，則需要下列高層級的步驟。 隨附的快速入門範本和指令碼[GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)上的文件的詳細的指示。

1. **建立基本的 VM 圖像**  
    * 下載並安裝到這個 VM MATLAB 分散式運算伺服器軟體。 

    >[AZURE.NOTE]此程序可能需要幾個小時，但您只需要進行此動作一旦您使用的 MATLAB 每個版本。   
    
2. **建立一或多個叢集**  
    * 使用提供的 PowerShell 指令碼，或使用快速入門] 範本來建立叢集從基底 VM 圖像。   
    * 管理使用所提供的 PowerShell 指令碼可讓您的清單、 暫停、 繼續叢集，然後刪除叢集。 
 
## <a name="cluster-configurations"></a>叢集設定 

目前，叢集建立指令碼和範本可讓您建立的單一 MATLAB 分散式運算伺服器拓撲。 如果您想，建立一或多個其他叢集有不同的數字的工作者 Vm，使用不同的 VM 大小，每個叢集等等。 

### <a name="matlab-client-and-cluster-in-azure"></a>MATLAB 用戶端和 Azure 中叢集 

MATLAB 用戶端節點 MATLAB 工作排程器節點與 MATLAB 分散式運算伺服器 「 工作 」 的節點所有設定為 Azure Vm 在虛擬網路，如下圖所示。 

![叢集拓撲](./media/virtual-machines-windows-matlab-mdcs-cluster/mdcs_cluster.png)

* 若要使用叢集，連線遠端桌面用戶端的節點。 用戶端節點執行 MATLAB 用戶端。 

* 用戶端節點具有的所有同事可以存取的檔案共用。

* MathWorks 主控授權管理員適用於所有 MATLAB 軟體授權檢查項目。 

* 根據預設，工作者 Vm，就會建立一個 MATLAB 分散式運算伺服器工作者，每個核心，但您可以指定的任何數字。 


## <a name="use-an-azure-based-cluster"></a>使用 Azure 基礎叢集 

為其他類型的 MATLAB 分散運算伺服器叢集，您需要使用叢集設定檔管理員 MATLAB 用戶端 （在用戶端 VM） 中建立 MATLAB 工作排程器叢集設定檔。

![叢集設定檔管理員](./media/virtual-machines-windows-matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>後續步驟

* 若要部署及管理 MATLAB 分散式運算伺服器叢集 Azure 中的詳細指示，請參閱[GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)存放庫包含的範本和指令碼。 

* 移至[MathWorks 網站](http://www.mathworks.com/)的詳細 MATLAB 和 MATLAB 分散式運算伺服器的文件。
